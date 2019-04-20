---
title: Write Your Own DNS Query
categories: PROGRAMMING
date: 2019-04-13 10:49:01
tags: [dns, golang]
---

大部分人应该都知道 DNS 协议，以及它的用处。但是考虑过自己动手写 DNS message transmission 的应该不占多数。

我们不妨考虑如何自己实现一个简单的 DNS client，完成基本的 domain name query and response parsing。

因为 DNS 协议通常使用 UDP 作为其传输层协议，而且数据包是二进制包，所以实现一个这样简单的 demo 考虑用 golang 可能会省事儿不少。

### DNS Message Format

大体上 DNS 不是一个复杂的协议（虽然各类坑实在不少），所有的消息，不管是 query 还是 reply，都共享一个消息格式：

```
+---------------------+
|        Header       |
+---------------------+
|       Question      | the question for the name server
+---------------------+
|        Answer       | Resource Records (RRs) answering the question
+---------------------+
|      Authority      | RRs pointing toward an authority
+---------------------+
|      Additional     | RRs holding additional information
+---------------------+
```

- Header：消息头，包含一些参数、标志位
- Question：包含 query 的信息，例如需要查询的 domain
- Answer：reply 的信息
- Authority：如果有信息，则表明应答的服务器是 ultimate authority server。别忘了 DNS 服务器是树形结构，通常终端用户查询的 DNS 服务器都是 local DNS server。
- Additional：服务器传回的一些额外数据，非用户显式需要的

对于一个 query message，`Answer` section 是空的；而对于一个 reply message，`Question` 保存着对应 query 的数据。

一般而言，终端设备收到的 reply 消息里，`Authority` 和 `Additional` 是空的。所以下面会跳过这两个 section 的描述。
<!-- more -->
另外，读写网络包的时候需要注意，需要按照 big-endian 写入多字节整数！

#### Header

query 和 reply 消息的 header 格式是一样的：

```
0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F  0
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                      ID                       |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|QR|   OPCODE  |AA|TC|RD|RA|   Z    |   RCODE   |	Flags row
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                    QDCOUNT                    |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                    ANCOUNT                    |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                    NSCOUNT                    |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                    ARCOUNT                    |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
```

上面每一行的大小都是 2-byte，即 16-bit

- ID：query 的 ID，用来串联 query - reply。可以是任何内容的数据，通常用16位随机数

- Flags：
  - QR：表明消息类型
    - `0` query 消息
    - `1` reply 消息
  - OPCODE：表明 query 类型
    - `0` standard query
    - `1` inverse query
    - `2` server status request
    - `3` ~ `15` reserved
  - AA：如果为 `1` 则表明这个 reply 是一个 authoritative answer；应答的服务器是 authoritive server。这个 flag 仅在 reply 消息中有用
  - TC：为 `1` 表明消息被截断
  - RD：置 `1` 表明使用 recursive query mode（DNS query 有两种查询模式：recursive 和 iterative），仅用于 query 消息
  - RA：为 `1` 表明 DNS server 支持 recursive query
  - Z：reserved
  - RCODE：
    - `0`：成功，无错误
    - `1`：格式错误；服务器无法解析 query 消息
    - `2`：DNS server error
    - `3`：name error；查询的 domain name 不错在
    - `4`：not implemented；不支持的查询方式
    - `5`：refused；服务器拒绝此查询（根据某种策略）
  - QCOUNT：16-bit unsigned；Question 区中查询的条目数
  - ANCOUNT：16-bit unsigned；Answer 区中的记录数（注意 reply 中可能包含多条记录）
  - NSCOUNT：16-bit unsigned；Authority 区中记录数
  - ARCOUNT：16-bit unsigned；Additional 区中记录数

  实现是需要注意 flags row 某些标志使用了多个 bits，需要运用一些位操作技巧。

  ```go
  func newQueryHeader(packet *bytes.Buffer) (id uint16) {
  	// ID
  	rnd := rand.New(rand.NewSource(time.Now().UnixNano()))
  	id = uint16(rnd.Intn(math.MaxUint16))
  	checkNoFail(binary.Write(packet, binary.BigEndian, id))

  	// Set RD bit flag only.
  	packet.Write([]byte{0x01, 0x00})

  	// QD Count
  	checkNoFail(binary.Write(packet, binary.BigEndian, uint16(1)))

  	// AN Count
  	checkNoFail(binary.Write(packet, binary.BigEndian, uint16(0)))

  	// NS Count
  	checkNoFail(binary.Write(packet, binary.BigEndian, uint16(0)))

  	// AR Count
  	checkNoFail(binary.Write(packet, binary.BigEndian, uint16(0)))

  	if packet.Len() != _HeaderSize {
  		panic(errors.New(fmt.Sprintf("incorrect header size; size=%d", packet.Len())))
  	}

  	return
  }
  ```

#### Question Section

对于一个 DNS query message，我们需要将查询的内容写入 question section。

其格式如下：

```
0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F  0
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                                               |
/                     QNAME                     /
/                                               /
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                     QTYPE                     |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                     QCLASS                    |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
```

QNAME 保存我们要查询的 domain name，并且要首先对 domain name 进行编码：每个用 `.` 分开的部分会编码成一个 label，label 开头是当前 label 的字符数。

例如对域名 `example.com`，编码后的结果如下

- label `example`：`0x07 'e' 'x' 'a' 'm' 'p' 'l' 'e'`
- label `com`：`0x03 'c' 'o' 'm'`

QNAME 结尾用 `0x00` 标记结束

QTYPE：specifies
the type of the query

- 0x0001: A records (host address)
- 0x0002: name server records      (NS)
- 0x000F: mail server records      (MX)

QCLASS：该 query 的类别

- 0x0001：represents
  Internet address

```go
func newQuerySection(domain string, packet *bytes.Buffer) {
	sections := strings.Split(domain, ".")
	for _, section := range sections {
		lenByte := uint8(len(section))
		packet.WriteByte(byte(lenByte))
		packet.WriteString(section)
	}
	packet.WriteByte(0x00)

	// QTYPE = A record
	checkNoFail(binary.Write(packet, binary.BigEndian, uint16(0x0001)))
	// QCLASS = Internet address
	checkNoFail(binary.Write(packet, binary.BigEndian, uint16(0x0001)))
}
```

组合一下前面的 header 和 question，我们就可以写出整个 query 的部分

```go
func newDNSQuery(domain string) (packet []byte) {
	var buf bytes.Buffer

	_ = newQueryHeader(&buf)
	newQuerySection(domain, &buf)

	packet = buf.Bytes()

	return
}
```

#### Answer Section

这部分保存这服务器返回的结果信息。

格式如下：

```
0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F  0
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                                               |
/                                               /
/                      NAME                     /
|                                               |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                      TYPE                     |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                     CLASS                     |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                      TTL                      |
|                                               |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|                   RDLENGTH                    |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--|
/                     RDATA                     /
/                                               /
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
```

**注意：一个 reply 可能包含多个 answer 记录，每个记录都满足上面的格式要求。解析的时候需要根据 reply 中 header 里的 `ANCOUNT` 来确定到底有多少条记录。**

NAME 保存着我们请求的域名；如果我们请求的域名实际上是一个 alias，则 NAME 里保存着真实域名。

大多数情况下，这部分内容会被 packet compression。因为域名信息前面在 question 已经出现过了，所以没必要重复内容。

压缩机制如下：

如果采用了压缩，则这部分内容的实际二进制格式为

```
0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
| 1 1 |                OFFSET                   |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
```

- 整体是 16-bit 的记录，头两个 bits 是 `11` 代表启用了压缩
- 后面 14 bits 是数据相对于整个 reply message 的 offset

这里的 TYPE 和 CLASS 于 question 的 QTYPE 和 QCLASS 基本一致，除了 TYPE 多了一种类型：`0x0005` 代表 CNAME 记录

TTL：是 32-bit unsigned，代表这条记录能被缓存多少秒

RDLENGTH：RDATA 的长度

RDATA：具体内容视 TYPE 内容而定

- TYPE = 0x0001, for A records, then data is IP address
- TYPE = 0x0005, for CNAME, then the data is the name of alias
- TYPE = 0x0002, for name servers, data is the name of server
- TYPE = 0x000F, for mail servers, the format is PREFERENCE + EXCHANGE

下面是大体的解析框架，略去了部分解析数据的函数：

```go
func parseResponse(resp []byte) (reply *dnsReply, err error) {
	reply = &dnsReply{}

	// Header section first.

	flag1 := resp[2]
	flag2 := resp[3]

	// QR | OPCODE | AA | TC | RD

	if (flag1 & 0x80) == 0 {
		log.Printf("flag1=%x", flag1)
		return nil, errors.New("not a answer reply")
	}

	if ((flag1 >> 2) & 0x1) != 0 {
		reply.AuthorativeAnswer = true
	}

	if ((flag1 >> 1) & 0x1) != 0 {
		log.Printf("flag1=%x", flag1)
		return nil, errors.New("response data was truncated")
	}

	// RA | Z | RCODE

	if (flag2 & 0x80) == 0 {
		log.Printf("flag2=%x", flag2)
		return nil, errors.New("remote server doesn't support recursive query")
	}

	if (flag2 & 0x0F) != 0 {
		log.Printf("flag2=%x", flag2)
		return nil, errors.New("error response")
	}

	ansCount := binary.BigEndian.Uint16(resp[6:8])

	cursor := 0
	for i := _HeaderSize; i < len(resp); i++ {
		if resp[i] == 0x00 {
			cursor = i + 1
			break
		}
	}

	// skip QTYPE & QCLASS
	cursor += 4

	// process answer sections
	for ansCount > 0 {
		if (resp[cursor] & 0xC0) != 0xC0 {
			log.Printf("Name=%x", resp[cursor])
			return nil, errors.New("answer doesn't use compression")
		}

		offset := int(binary.BigEndian.Uint16([]byte{resp[cursor] & 0x3F, resp[cursor+1]}))
		name := resolveSectionDataAsString(resp[offset:])
		cursor += 2

		recordType := int(binary.BigEndian.Uint16(resp[cursor:cursor+2]))
		cursor += 2

		// skip CLASS
		cursor += 2

		ttl := int(binary.BigEndian.Uint32(resp[cursor:cursor+4]))
		cursor += 4

		valueLen := int(binary.BigEndian.Uint16(resp[cursor:cursor+2]))
		cursor += 2

		var value string
		if recordType == _RecordTypeARecord {
			value = resolveSectionDataAsIPAddr(resp[cursor:cursor + valueLen])
		} else if recordType == _RecordTypeCNAME {
			value = resolveSectionDataAsString(resp[cursor:cursor + valueLen])
		}
		cursor += valueLen

		record := &dnsRecord{
			Type: recordType,
			TTL: ttl,
			Name: name,
			Value: value,
		}

		reply.records = append(reply.records, record)

		ansCount--
	}

	return
}
```



### Example DNS Query and Response

为了方便理解，我从某本教材的习题里找了一个 query 和 response 的二进制包数据

#### Query

```
0000000: db42 0100 0001 0000 0000 0000 0377 7777 .B...........www
0000010: 0c6e 6f72 7468 6561 7374 6572 6e03 6564 .northeastern.ed
0000020: 7500 0001 0001                          u.....
```

![](/img/dns_query.png)

#### Response

```
0000000: db42 8180 0001 0001 0000 0000 0377 7777 .B...........www
0000010: 0c6e 6f72 7468 6561 7374 6572 6e03 6564 .northeastern.ed
0000020: 7500 0001 0001 c00c 0001 0001 0000 0258 u..............X
0000030: 0004 9b21 1144                          ...!.D
```

![](/img/dns_response.png)

### Final Demo

完整的代码实现见 [DNSClient/main.go](https://github.com/kingsamchen/Eureka/blob/master/DNSClient/cmd/main.go)

![](/img/dns_client_demo.png)
