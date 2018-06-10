---
title: Build Your Own HTTP Proxy Server Using Go
categories: PROGRAMMING
date: 2018-06-10 19:54:44
tags: [http, proxy, golang]
---
Go is a quite awesome programming language for building network applications. So I built my own HTTP proxy server using go last week.

To grasp the essence of how HTTP proxy server works, I choosed to implement it on TCP transport layer, forwarding TCP traffic directly.

Essentially, it runs a server, accepting incoming TCP connections and from which the server extracts target host of a request by parsing HTTP protocol messages. The server then establishes a connection to the target host, and finally operates as an intermedia, forwarding traffic from one host to another.

Implementing a TCP server which is able to handle concurrent requests uing go is easier than you thought: just runs a loop to accept requests, launching a new goroutine for each new connection:

```go
type Server struct {
	addr     string
	listener net.Listener
}

func (srv *Server) Start() {
	var err error
	srv.listener, err = net.Listen("tcp", srv.addr)
	if err != nil {
		log.Fatalf("Failed to listen at %s : %v", srv.addr, err)
	}

	log.Printf("Proxy is listening at %s", srv.addr)

	srv.runLoop()
}

func (srv *Server) runLoop() {
	for {
		conn, err := srv.listener.Accept()
		if err != nil {
			log.Printf("Failed to accept an incoming connection : %v", err)
			continue
		}

		go srv.handleConnection(conn)
	}
}

func (srv *Server) handleConnection(conn net.Conn) {
	c := newConnection(conn)
	c.serve()
}
```
<!-- more -->
### Handling HTTP Requests

Parsing requests and forwrading traffic are a bit tricky, we need to handle HTTP and HTTPS requests separately; and to simplify our implementation, we assume our proxy server only handles HTTP 1.1 traffic.

For a HTTP request that is aware of use of proxy, URL in the request line is in full path. We parse the url to obtain the target host, and then establish a TCP connection to the host. The default port is 80 if not given in the url.

To retrieve the request line, we need to read data from the socket, and because of the byte-stream trait of TCP protocol, we must use a buffer to preserve what we have read.

Once the connection to another host is established, we first send the preserved data, then just copy data from one socket to another.

### Handling HTTPS Requests

Because HTTPS traffic is encrypted, a dedicated approach is adopted, that is, traffic tunneling.

A proxy aware HTTPS request will use the `CONNECT` method, and only request header that tells tunnling information is in plain text.

Our proxy server reads target host from tunnling header, connecting to the host, just as handling HTTP requests.

Here then comes the different part: the proxy server sends back `HTTP/1.1 200 Connection Established\r\n\r\n` to the source, implicating that a tunnel is ready to use; after that step, the proxy server interchanges data between two hosts.

One thing to remember: because tunnling header is not part of the original request, we must drain the header before we go to the next step; otherwise we would encounter TLS errors.

```go
func (c *connection) serve() {
	defer c.reqConn.Close()

	poachedData, remoteAddr, secure, err := readRequestInfo(c.reqConn)
	if err != nil {
		log.Printf("WARNING: Unable to read request info from %s : %v", c.reqConn.LocalAddr(), err)
		return
	}

	log.Printf("Establishing connection to %s", remoteAddr)

	remoteConn, err := net.Dial("tcp", remoteAddr)
	if err != nil {
		log.Printf("WARNING: Failed to connect to host %s : %v", remoteAddr, err)
		return
	}

	defer remoteConn.Close()

	// If we are going to proxy a https request, we just simply discard `poachedData`, which contains
	// the entire connect header;
	// Otherwise, we forward `poachedData` alone with remaining header and body to the remote host.
	if secure {
		c.reqConn.Write([]byte("HTTP/1.1 200 Connection Established\r\n\r\n"))
	} else {
		_, err = remoteConn.Write(poachedData)
		if err != nil {
			log.Printf("WARNING: Failed to write request header to remote host! %v", err)
			return
		}
	}

	log.Printf("Begin to tunneling connections %s <-> %s", c.reqConn.LocalAddr(), remoteAddr)

	go io.Copy(remoteConn, c.reqConn)

	io.Copy(c.reqConn, remoteConn)

	log.Print("The tunnel is ended")
}
```

Full code can be found at [here](https://github.com/kingsamchen/Eureka/tree/master/HTTPProxy/golang)
