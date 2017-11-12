---
title: 一次 dump 分析的复盘
categories: PROGRAMMING
date: 2017-11-12 23:16:58
tags: [windbg, crash-dump, postmortem]
---
首先有主站的运营同学反馈某个用户的投稿工具一选择上传视频就崩溃，100% 重现。

要到 crash dump 之后挂上 windbg，首先用 `lmvm bililive` 检查一下用户使用的版本：

```
Image path: D:\ugc_assistant\2.0.0.1054\bililive.dll
Image name: bililive.dll
Browse all global symbols  functions  data
Timestamp:        Fri Sep  1 17:53:54 2017 (59A92E32)
CheckSum:         00A12C47
ImageSize:        00A34000
File version:     2.0.0.1054
Product version:  2.0.0.1054
File flags:       0 (Mask 17)
File OS:          4 Unknown Win32
File type:        1.0 App
File date:        00000000.00000000
Translations:     0409.04b0
CompanyName:      bilibili
ProductName:      bilibili投稿工具
InternalName:     bililive_dll
OriginalFilename: bililive.dll
```

拿到版本之后从构建机上拷出对应的 PDB，设置路径，重新加载符号。

因为投稿工具有大量的工作线程，所以紧接着用 `~#` 检查发生异常的线程：

```
. 18  Id: 1a68.2620 Suspend: 0 Teb: fff72000 Unfrozen
      Start: bililive!base::`anonymous namespace'::ThreadFunc (5da3abd0)
      Priority: 0  Priority class: 32  Affinity: f
```

崩溃发生在一个工作线程，接下来用 `~18s` 将这个线程设置为当前线程，并且用 `.ecxr` 切换到异常 context 上。

因为已经有 PDB 了，所以直接用 `kPL` 把 callback 附加对应的源码参数显示出来，同时不显示对应的源码文件和行号（因为对源码比较熟悉，定位到第一现场后可以直接调到源文件，全都打印出来反而容易干扰输出）：

```
 # ChildEBP RetAddr
00 084ff4c0 5dea879f bililive!_invalid_parameter_noinfo(void)+0xc
01 084ff4d8 5da6c8d9 bililive!rand_s(
			unsigned int * _RandomValue = 0x084ff59c)+0x82
02 084ff5a0 5da6c936 bililive!`anonymous namespace'::RandUint32(void)+0x19
03 084ff5a8 5da51f65 bililive!base::RandUint64(void)+0x6
04 084ff670 5da52014 bililive!base::RandGenerator(
			unsigned int64 range = 0x80000000)+0x95
05 084ff73c 5da1c54e bililive!base::RandInt(
			int min = 0n0,
			int max = 0n2147483647)+0x84
06 084ff8a8 5da1bfa3 bililive!bililive::VideoUploadTask::DoUpload(void)+0x22e
07 084ff9b0 5da36c46 bililive!bililive::VideoUploadTask::DoStart(void)+0x3b3
08 084ffaf4 5da3611d bililive!base::MessageLoop::RunTask(
			struct base::PendingTask * pending_task = 0x084ffb44)+0x346
09 084ffb88 5da56fba bililive!base::MessageLoop::DoWork(void)+0x1cd
0a 084ffb94 5da579dd bililive!base::MessagePumpForIO::DoRunLoop(void)+0x7a
0b 084ffbb4 5da3686d bililive!base::MessagePumpWin::Run(
			class base::MessagePump::Delegate * delegate = 0x084ffcc8)+0x3d
0c 084ffc7c 5da380e3 bililive!base::MessageLoop::RunInternal(void)+0x9d
0d 084ffc84 5da367a6 bililive!base::RunLoop::Run(void)+0x13
0e 084ffca8 5da3afeb bililive!base::MessageLoop::Run(void)+0x16
0f 084ffcb0 5da3b462 bililive!base::Thread::Run(
			class base::MessageLoop * message_loop = 0x084ffcc8)+0xb
10 084ffd7c 5da3ac25 bililive!base::Thread::ThreadMain(void)+0xd2
11 084ffd90 751633ca bililive!base::`anonymous namespace'::ThreadFunc(
			void * params = 0x000006ec)+0x55
12 084ffd9c 771d9ed2 kernel32!BaseThreadInitThunk+0xe
13 084ffddc 771d9ea5 ntdll!__RtlUserThreadStart+0x70
14 084ffdf4 00000000 ntdll!_RtlUserThreadStart+0x1b
```

发现异常是由 `base::RandUint64()` 内部调用的 CRT 的 `rand_s()` 触发的（这里 CRT 被静态链接了），并且看 `_invalid_parameter_noinfo` 的名字，很像是不满足函数参数一类的。

根据 MSDN 这里的[描述](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/rand-s)，`rand_s()` 仅会在参数指针为空时，才会往外抛异常，然而这里我们明显能看出来，参数不为空。

既然这样，那我们就跳到 stack frame 00 对应的源码去看看，然而......

正值某国开会，对境外站点的访问遭到了封锁，不能从微软的服务器拖对应的 PDB....

死马当活马医，既然如此就考虑通过反汇编入手，看看能否找到 root cause。

先利用 `uf bililive!rand_s` 反汇编整个函数，还好不是很长

```asm
0:018> uf bililive!rand_s
bililive!rand_s [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 60]:
   60 5dea871d 55              push    ebp
   60 5dea871e 8bec            mov     ebp,esp
   60 5dea8720 51              push    ecx
   60 5dea8721 53              push    ebx
   60 5dea8722 56              push    esi
   61 5dea8723 ff359c171a5e    push    dword ptr [bililive!g_pfnRtlGenRandom (5e1a179c)]
   61 5dea8729 ff154c64fd5d    call    dword ptr [bililive!_imp__DecodePointer (5dfd644c)]
   61 5dea872f 8bd8            mov     ebx,eax
   62 5dea8731 8b4508          mov     eax,dword ptr [ebp+8]
   62 5dea8734 85c0            test    eax,eax
   62 5dea8736 7516            jne     bililive!rand_s+0x31 (5dea874e)  Branch

bililive!rand_s+0x1b [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 62]:
   62 5dea8738 e87297ffff      call    bililive!_errno (5dea1eaf)
   62 5dea873d 6a16            push    16h
   62 5dea873f 5e              pop     esi
   62 5dea8740 8930            mov     dword ptr [eax],esi
   62 5dea8742 e8eb61ffff      call    bililive!_invalid_parameter_noinfo (5de9e932)
   62 5dea8747 8bc6            mov     eax,esi
   62 5dea8749 e9cf000000      jmp     bililive!rand_s+0x100 (5dea881d)  Branch

bililive!rand_s+0x31 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 63]:
   63 5dea874e 832000          and     dword ptr [eax],0
   63 5dea8751 57              push    edi
   65 5dea8752 85db            test    ebx,ebx
   65 5dea8754 0f85a3000000    jne     bililive!rand_s+0xe0 (5dea87fd)  Branch

bililive!rand_s+0x3d [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 75]:
   75 5dea875a 8b352464fd5d    mov     esi,dword ptr [bililive!_imp__LoadLibraryExW (5dfd6424)]
   75 5dea8760 6800080000      push    800h
   75 5dea8765 53              push    ebx
   75 5dea8766 bb4067135e      mov     ebx,offset bililive!`string' (5e136740)
   75 5dea876b 53              push    ebx
   75 5dea876c ffd6            call    esi
   77 5dea876e 8b3d5464fd5d    mov     edi,dword ptr [bililive!_imp__GetLastError (5dfd6454)]
   77 5dea8774 8945fc          mov     dword ptr [ebp-4],eax
   77 5dea8777 85c0            test    eax,eax
   77 5dea8779 7528            jne     bililive!rand_s+0x86 (5dea87a3)  Branch

bililive!rand_s+0x5e [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 77]:
   77 5dea877b ffd7            call    edi
   77 5dea877d 83f857          cmp     eax,57h
   77 5dea8780 750e            jne     bililive!rand_s+0x73 (5dea8790)  Branch

bililive!rand_s+0x65 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 81]:
   81 5dea8782 6a00            push    0
   81 5dea8784 6a00            push    0
   81 5dea8786 53              push    ebx
   81 5dea8787 ffd6            call    esi
   81 5dea8789 8945fc          mov     dword ptr [ebp-4],eax
   84 5dea878c 85c0            test    eax,eax
   84 5dea878e 7513            jne     bililive!rand_s+0x86 (5dea87a3)  Branch

bililive!rand_s+0x73 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 86]:
   86 5dea8790 e81a97ffff      call    bililive!_errno (5dea1eaf)
   86 5dea8795 6a16            push    16h
   86 5dea8797 5e              pop     esi
   86 5dea8798 8930            mov     dword ptr [eax],esi
   86 5dea879a e89361ffff      call    bililive!_invalid_parameter_noinfo (5de9e932)
   86 5dea879f 8bc6            mov     eax,esi
   86 5dea87a1 eb79            jmp     bililive!rand_s+0xff (5dea881c)  Branch

bililive!rand_s+0x86 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 89]:
   89 5dea87a3 68c862105e      push    offset bililive!`string' (5e1062c8)
   89 5dea87a8 50              push    eax
   89 5dea87a9 ff152864fd5d    call    dword ptr [bililive!_imp__GetProcAddress (5dfd6428)]
   89 5dea87af 8bd8            mov     ebx,eax
   90 5dea87b1 85db            test    ebx,ebx
   90 5dea87b3 7522            jne     bililive!rand_s+0xba (5dea87d7)  Branch

bililive!rand_s+0x98 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 92]:
   92 5dea87b5 e8f596ffff      call    bililive!_errno (5dea1eaf)
   92 5dea87ba 8bf0            mov     esi,eax
   92 5dea87bc ffd7            call    edi
   92 5dea87be 50              push    eax
   92 5dea87bf e8fe96ffff      call    bililive!_get_errno_from_oserr (5dea1ec2)
   92 5dea87c4 59              pop     ecx
   92 5dea87c5 8906            mov     dword ptr [esi],eax
   92 5dea87c7 e86661ffff      call    bililive!_invalid_parameter_noinfo (5de9e932)
   92 5dea87cc ffd7            call    edi
   92 5dea87ce 50              push    eax
   92 5dea87cf e8ee96ffff      call    bililive!_get_errno_from_oserr (5dea1ec2)
   92 5dea87d4 59              pop     ecx
   92 5dea87d5 eb45            jmp     bililive!rand_s+0xff (5dea881c)  Branch

bililive!rand_s+0xba [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 94]:
   94 5dea87d7 8b356462fd5d    mov     esi,dword ptr [bililive!_imp__EncodePointer (5dfd6264)]
   94 5dea87dd 53              push    ebx
   94 5dea87de ffd6            call    esi
   95 5dea87e0 6a00            push    0
   95 5dea87e2 8bf8            mov     edi,eax
   95 5dea87e4 ffd6            call    esi
  100 5dea87e6 b99c171a5e      mov     ecx,offset bililive!g_pfnRtlGenRandom (5e1a179c)
  100 5dea87eb 8739            xchg    edi,dword ptr [ecx]
  100 5dea87ed 3bf8            cmp     edi,eax
  100 5dea87ef 7409            je      bililive!rand_s+0xdd (5dea87fa)  Branch

bililive!rand_s+0xd4 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 109]:
  109 5dea87f1 ff75fc          push    dword ptr [ebp-4]
  109 5dea87f4 ff152c64fd5d    call    dword ptr [bililive!_imp__FreeLibrary (5dfd642c)]

bililive!rand_s+0xdd [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 109]:
  109 5dea87fa 8b4508          mov     eax,dword ptr [ebp+8]

bililive!rand_s+0xe0 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 113]:
  113 5dea87fd 6a04            push    4
  113 5dea87ff 50              push    eax
  113 5dea8800 ffd3            call    ebx
  113 5dea8802 85c0            test    eax,eax
  113 5dea8804 7514            jne     bililive!rand_s+0xfd (5dea881a)  Branch

bililive!rand_s+0xe9 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 115]:
  115 5dea8806 e8a496ffff      call    bililive!_errno (5dea1eaf)
  115 5dea880b c7000c000000    mov     dword ptr [eax],0Ch
  116 5dea8811 e89996ffff      call    bililive!_errno (5dea1eaf)
  116 5dea8816 8b00            mov     eax,dword ptr [eax]
  116 5dea8818 eb02            jmp     bililive!rand_s+0xff (5dea881c)  Branch

bililive!rand_s+0xfd [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 118]:
  118 5dea881a 33c0            xor     eax,eax

bililive!rand_s+0xff [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 118]:
  118 5dea881c 5f              pop     edi

bililive!rand_s+0x100 [f:\dd\vctools\crt\crtw32\misc\rand_s.c @ 118]:
  118 5dea881d 5e              pop     esi
  118 5dea881e 5b              pop     ebx
  119 5dea881f 8be5            mov     esp,ebp
  119 5dea8821 5d              pop     ebp
  119 5dea8822 c3              ret
```

那么发生问题的是哪一行呢？往回看调用栈第一个栈帧

```
00 084ff4c0 5dea879f bililive!_invalid_parameter_noinfo(void)+0xc
```

**5dea879f** 是第一个栈帧的 return address，也就是这个函数返回到父函数后要执行的下一条指令的地址，于是就能定位到

```
   86 5dea879a e89361ffff      call    bililive!_invalid_parameter_noinfo (5de9e932)
   86 5dea879f 8bc6            mov     eax,esi
```

因为汇编的特殊性，比较好的选择是从问题的指令开始，往回倒推，着重小心涉及 branching 的指令。

NOTE：为了专注问题，这里跳过其他非相关的汇编代码

```asm
   86 5dea8790 e81a97ffff      call    bililive!_errno (5dea1eaf)
   86 5dea8795 6a16            push    16h
   86 5dea8797 5e              pop     esi
   86 5dea8798 8930            mov     dword ptr [eax],esi
```

这里往 `eax` 指向的内存写入了 0x16，根据上下文，我猜 函数 `bililive!_errno()` 返回了 CRT errno 的地址，于是存到了 `eax`；翻一下文档，0x16 错误值代表的刚好是 `EINVAL`。

根据

```
   84 5dea878c 85c0            test    eax,eax
   84 5dea878e 7513            jne     bililive!rand_s+0x86 (5dea87a3)  Branch
```

我们知道，在此之前 `eax` 等于 0（否则已经跳转到另外一个地址，就不会执行到出错的地方了）。

```
   81 5dea8782 6a00            push    0
   81 5dea8784 6a00            push    0
   81 5dea8786 53              push    ebx
   81 5dea8787 ffd6            call    esi
```

前面 `eax` 为 0 是 `esi` 的执行结果，大概率是失败了（FALSE）；这句直接把刚才 `eax` 分析的重要性给抹杀了，orz...

这里 `esi` 应该是某个函数指针，目前看有三个参数，最后两个都是 0 （别忘了 cdecl calling convention 下，参数从右往左压栈）

```
   77 5dea877b ffd7            call    edi
   77 5dea877d 83f857          cmp     eax,57h
   77 5dea8780 750e            jne     bililive!rand_s+0x73 (5dea8790)  Branch
```

这段比较有意思，因为无论 `jne` 是不是要跳，两个目的地都是我们刚才执行过的指令，这意味着，这里 `eax` 可能为 57h，也可能不是，不过还好不影响我们的主逻辑。

```
   75 5dea875a 8b352464fd5d    mov     esi,dword ptr [bililive!_imp__LoadLibraryExW (5dfd6424)]
   75 5dea8760 6800080000      push    800h
   75 5dea8765 53              push    ebx
   75 5dea8766 bb4067135e      mov     ebx,offset bililive!`string' (5e136740)
   75 5dea876b 53              push    ebx
   75 5dea876c ffd6            call    esi
   77 5dea876e 8b3d5464fd5d    mov     edi,dword ptr [bililive!_imp__GetLastError (5dfd6454)]
   77 5dea8774 8945fc          mov     dword ptr [ebp-4],eax
   77 5dea8777 85c0            test    eax,eax
   77 5dea8779 7528            jne     bililive!rand_s+0x86 (5dea87a3)  Branch
```

首先 `jne` 不跳，因为目标地址不在执行范围内，这意味着 `eax` 为 0。

同时可以发现 `edi` 保存的是 `GetLastError()` 的地址；并且这里的 `edi` 和上一段汇编代码的 `edi` 是一个

`esi` 保存了 `LoadLibraryExW()` 的地址，并且这个调用失败了（因为 `eax` 为 0）。

并且可以发现，上面分析过的有段汇编，同样执行了一次 `esi`。

这里 `LoadLibraryEx()` 加载的函数名字的地址是 **5e136740**，用 `db` 查看一下发现是

```
5e136740  41 00 44 00 56 00 41 00-50 00 49 00 33 00 32 00  A.D.V.A.P.I.3.2.
5e136750  2e 00 44 00 4c 00 4c 00-00 00 00 00 00 00 00 00  ..D.L.L.........
```

在往上看，函数就到头了，而且没有直接跳到异常上下文的指令，所以基本上可以确定这个异常由 `LoadLibraryEx()` 调用失败引发

整理一下逻辑就是：

首先调用 `bililive!_imp__DecodePointer` 失败，然后进行一系列的 fallback，例如利用 `LoadLibraryEx()` 加载 *ADVAPI32.DLL*

如果第一次调用失败且 Last-Error-Code 是 57h，则换一套参数在调用一次 `LoadLibraryEx()`，否则就直接失败

但是这里无论是不是 57H，结果都一样，失败了...

既然是调用失败，那有必要看一下具体的 last error code 是啥。

直接用 `!teb` 看这个线程 TEB 的数据，里面就有 last-error-code:

```
0:018> !teb
TEB at fff72000
    ExceptionList:        084fed8c
    StackBase:            08500000
    StackLimit:           084fe000
    SubSystemTib:         00000000
    FiberData:            00001e00
    ArbitraryUserPointer: 00000000
    Self:                 fff72000
    EnvironmentPointer:   00000000
    ClientId:             00001a68 . 00002620
    RpcHandle:            00000000
    Tls Storage:          03ef1910
    PEB Address:          fffde000
    LastErrorValue:       126
    LastStatusValue:      c0000135
    Count Owned Locks:    0
    HardErrorMode:        0
```

126 意味着 *Error code: (Win32) 0x7e (126) - The specified module could not be found.*

用 `lmo` 可以看到

```
756a0000 75740000   advapi32   (deferred)
    Mapped memory image file: d:\dbgsymbols\sys_symbols\advapi32.dll\4CE7B706a0000\advapi32.dll
    Image path: C:\Windows\SysWOW64\advapi32.dll
    Image name: advapi32.dll
```

advapi32 被延迟加载了，但是系统还是能够找到的。这种情况下的无法加载，我能想到的大概只有：

1. 这个 DLL 某个依赖的 DLL 找不到
2. 加载行为被 HOOK 干掉了

至于具体是哪个就不得而知了。

不过起码我们找到了问题不是么.....

完整的 dump 和 PDB 包可以在这里下载：链接: https://pan.baidu.com/s/1i5xjGt3 密码: nnsh

不要问我为什么是百度网盘。。。现在能够方便做外链的免费网盘没几家了啊
