---
title: 利用 chromium net 库的 URLRequest 实现支持断点续传的 URLDownloader
categories: PROGRAMMING
date: 2016-12-18 23:11:30
tags: [chromium, net lib, urlrequest, resumable download]
---
最近因为要实现某直播姬的自动更新功能，于是就要求客户端能够自动下载安装包，所以就要实现一个简单的支持续传的下载功能。

因为用了 chromium 的框架，所以自然是基于 chromium net lib 去实现；稍微翻了一下源码目录，最后决定在 `net::URLRequest` 的基础上自己封装一个 `URLDownloader`。

虽然之前某豹浏览器也有恶意魔改过的下载，但是相关功能一直和我没关系，所以这次基本算是 from scratch，期间也因为资料文档基本没有，绕了一些弯路，全靠一边看源码一遍自己通过demo调试去学习...

为了方便可能的后来人，这里稍作记录。

完整的demo源码可以看这里：[SimpleDownloader](https://github.com/kingsamchen/Eureka/tree/master/SimpleDownloader)

把 `URLDownloader` 的头文件单独摘出来

```c++
#ifndef DOWNLOADER_URL_DOWNLOADER_H_
#define DOWNLOADER_URL_DOWNLOADER_H_

#include <memory>
#include <vector>

#include "base/basictypes.h"
#include "base/files/file_path.h"
#include "base/memory/ref_counted.h"
#include "base/threading/thread_checker.h"
#include "net/base/io_buffer.h"
#include "net/url_request/url_request.h"
#include "net/url_request/url_request_context.h"
#include "url/gurl.h"

namespace downloader {

// The user of URLDownloader must ensure that the construction and operations of an URLDownloader
// instance are performed on a same thread.
class URLDownloader : public net::URLRequest::Delegate {
public:
    class CompleteCallback {
    public:
        virtual ~CompleteCallback() {}

        virtual void OnDownloadSuccess() = 0;

        virtual void OnDownloadFailure() = 0;
    };

    // The caller of this constructor must make sure:
    // - `save_path` is writable;
    // - the object `callback` refers to is alive during the entire lifecycle of URLDownloader, since
    //   the class itself doesn't acquire the ownership for the `callback`.
    URLDownloader(const GURL& url, const base::FilePath& save_path, CompleteCallback* callback);

    ~URLDownloader() = default;

    // Starts or resumes the download.
    void Start();

    // Stops the download.
    void Stop();

private:
    void OnResponseStarted(net::URLRequest* request) override;

    void OnReadCompleted(net::URLRequest* request, int bytes_read) override;

    // Saves received network data (stored in `buf_`) to disk write cache, and writes to disk
    // if necessary.
    // It is legal to call this function with 0 passed in as `bytes_received`, when you want to
    // force flushing cache to disk.
    void SaveReceivedChunk(int bytes_received, bool force_write_to_disk);

    DISALLOW_COPY_AND_ASSIGN(URLDownloader);

private:
    GURL url_;
    base::FilePath save_path_;
    base::FilePath tmp_save_path_;
    CompleteCallback* complete_callback_;
    size_t downloaded_bytes_;
    std::vector<char> disk_write_cache_;
    scoped_refptr<net::IOBuffer> buf_;
    std::unique_ptr<net::URLRequestContext> request_context_;
    std::unique_ptr<net::URLRequest> request_;
    base::ThreadChecker thread_checker_;
};

}   // namespace bililive

#endif  // DOWNLOADER_URL_DOWNLOADER_H_
```

核心只有四个函数：`URLRequest::Start()`, `OnResponseStarted()`, `URLRequest::Read()`，和 `OnReadCompleted()`。

这四个函数的 interactions 大概如下：

- `Start()` 执行后内部会调用 `OnResponseStarted()`
- 在 `OnResponseStarted()` 中，如果 request 和 response 的通信都没有什么问题，则要用 `Read()` 读取接收到的网络 IO 数据，接着调用 `OnReadCompleted()` 切换状态；这个步骤称之为 initial reading
- 在 `OnReadCompleted()` 中，反复利用 `Read()` 读取网络数据

`Read()` 和 `OnReadCompleted()` 之间有一个容易被忽略的细节： 利用 `Read()` 从读取网络 IO 数据时

1. 如果数据立即可用，则 `Read()` 会正常读取数据
2. 如果数据当前不可用，则会触发 asynchronous reading，但是 `Read()` 函数会马上返回；等到底层获取到 IO 数据后，内部自己会调用 `OnReadCompleted()`

除此之外，还要注意 `URLRequest` 对象的**构造和操作**必须在同一个线程。

至于续传，本质就是利用了 HTTP request header 的 range，具体请参考代码。

最后说一句，如果你的目的是实现一套和服务端 RESTFul API 通信的设施，那么直接使用 `net::URLFetcher` 是个更好的选择；因为后者是在 `net::URLRequest` 基础之上针对字符处理加的一个wrapper，屏蔽了具体的 IO data read 细节。