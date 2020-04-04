---
title: How To Design a URL Shortening Service
categories: PROGRAMMING
date: 2020-04-04 17:32:38
tags: [system design, url shortening, tinyurl, bit.ly]
---
## 1. Encoding Actual URL

The path part, which we would call it _key_, of an encoded URL can consist of 6 ~ 8 digits in base62, up to desired uniqueness rate.

### Randomly Generated

We can randomly generate those digits each within the range [0, 61].

Then we must check if the generated key is unique by consulting our key storage.

> Aside: this checking process can be optimized with data structures like bloom-filter, provided we have to deal hundres of million records.

If we are unlucky, we may have a key collision; in this case, just re-generate another key until its unique.

To make encoding more efficient, we can have a standalone _Key Generation Service(KGS)_ that generates those random key beforehand, and store them.
<!-- more -->
**Pros**

- Key is not guessable (not predictable)

**Cons:**

- Must deal with key collision.

### ID Generator

We need an auto-increment id generator, like

1. the primary key of a table in MySQL
2. `INCR` a counter in Redis
3. .etc

Each time we gain a new id, then encode it in base62, the reuslt is our key.

Because the uniqueness of an id can easily be secured, we don't have much to worry.

We can further have distributed id generation services: suppose we have 10 instances of the service, identified as `kgs_d, 1 <= d <= 9`, each generates id suffixed with `d`.

E.g

- `kgs_1` generates id: 1, 11, 21, ..., 91, 101, 111, ...
- `kgs_2` generates id: 2, 12, 22, ..., 92, 102, 112, ...
- ...

**Pros**

- No need to handle uniqueness explicitly

**Cons**

- The link pattern is easy to be detected and links are predictable, provided users find out links are encoded in base62.

## 2. Redirecting

If the full URL is found, issue an `HTTP 301 Moved Permanently` or an `HTTP 302 Found` status back to the browser, passing the stored URL in the `Location` field of the response header, such as

```
HTTP/1.1 301 Moved Permanently
Location: https://tools.ietf.org/html/rfc7231#section-6.5.4
Cache-Control: max-age=0, no-cache, private
```

If no matched URL found, issue an `HTTP 404 Not Found` status back, instead.

### 301 or 302 ?

On HTTP's semantics:

- 301 means that the resource(page) is moved permanently to a new location. The client should not attempt to request the original location from now on.

- 302 means that the resource(page) is temporarily located somewhere else, and the client should continue requesting the original url.

By default, most modern browsers would cache 301 response, while would skip caching 302 response; however, their cache policy can be changed by using either `cache-control` or `expire` fields, explicitly.

For search engine spiders:

- for 301 redirect, they would treat the original URL no longer exist, and replace its index data (like page-rank) with the new URL

- for 302 redirect, they would index both the original URL and the new one.

To be noted, both TinyURL and bit.ly use `HTTP 301 Moved Permanently` for page redirecting, and they also state cache control explicitly.

```
$curl -I https://tinyurl.com/wn3bh5r

HTTP/1.1 301 Moved Permanently
Location: https://tools.ietf.org/html/rfc7231#section-6.5.4
Cache-Control: max-age=0, no-cache, private

$curl -I https://bit.ly/3bzxXk0

HTTP/1.1 301 Moved Permanently
Location: https://tools.ietf.org/html/rfc7231#section-6.5.4
Cache-Control: private, max-age=90
```

### 302 Found or 302 Moved Temporarily

The HTTP/1.0 spec initially defined status 302's description phrase `Moved Temporarily`; and HTTP/1.1 spec standardized it as `Found`.

Therefore, `HTTP 302 Moved Temporarily` is just a legacy matter.

## 3. Caching

The mapping of encoded URL to the original one is unlikely to be changed in the future, so we almost don't need to handle cache invalidation.

We can use Redis as our cache server, to avoid every user access hitting our backend storage.

Moreover, application server can have their own in-memory cache, using LRU eviction policy.

## 4. Notes

TinyURL & Bit.ly would generate one same short url for an original url when applied multiple times.

TinyURL has made the key part unpredictable; while Bit.ly simply use auto-increment id in base62 as final key.
