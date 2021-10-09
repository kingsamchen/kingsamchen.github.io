---
title: Using Headless Chrome to Convert Offline HTML to PDF in Bulk
categories: PROGRAMMING
date: 2021-10-09 16:16:00
tags: [headless, pdf]
---
手里有一批某课程内容的教案但是都是导出的 HTML 文档，因为每次打开都会请求一堆的外部资源（比如JS），导致卡顿几秒钟。

但是这是盗版资源 (shame on me) 所以这些外部请求完全是捣乱；于是想到批量转换为 PDF 就好了。

一开始选择的是 wkhtmltopdf，但是 libpng 会因为源文件的 sRGB 不正确导致失败。看到有个 2017 年报的 issue 到现在还是 Open 状态，遂放弃。

后来想到浏览器可以正常打开，也可以正常 print to pdf，那何不让浏览器批量处理？

恰好 Chrome 支持 headless mode，结合简单的 powershell 就可以做到批量转换：

```powershell
$dest_dir = "/your/dest/dir/"
$src_files = Get-ChildItem "/your/src/dir/"

$env:Path += ";C:\Program Files (x86)\Google\Chrome\Application\"
foreach ($file in $src_files) {
    $output_file = $file.BaseName + ".pdf"
    $dest_file = Join-Path -Path $dest_dir -ChildPath $output_file
    Write-Output "Processing $dest_file"
    chrome.exe --headless --disable-gpu --print-to-pdf-no-header --print-to-pdf=$dest_file $file
    # a simple throttle
    Start-Sleep 5s
}

Write-Output "Congratulations!!!"
```

每次调用都会创建一个 headless chrome 做转换，而一次转换大概需要十几秒；同时这个调用是启动 chrome 之后就立即返回的，并非等到转换结束。

所以为了避免短时间内创建大量的 headless chrome 进程把系统资源耗死，这里简单的用 sleep 做一个节流器

<!-- more -->
