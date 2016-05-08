---
title: 记一次被 Android 进程复用坑的经历
date: 2016-05-08 15:42:18
categories: PROGRAMMING
tags: [android]
---

因为需求需要，把某个功能拆分成一个独立的服务，并由一个全局的 `service manager` 去控制这个服务；服务对客户端暴露的实现也是通过 `service manager`。

因为服务不需要运行在一个独立进程，manager 和 service 直接通过一个包含服务对象的 local binder 相互通信，看上去大概就这样：

```java
public class LocalService extends Service {

    public class LocalBinder extends Binder {
        LocalService getService() {
            return LocalService.this;
        }
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }

    private final IBinder mBinder = new LocalBinder();

}

// This class is in fact a singleton.
public class ServiceManager {
    private LocalService.LocalBinder mServiceBridge;

    private ServiceConnection mConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className, IBinder service) {
            mServiceBridge = (LocalService.LocalBinder)service;
        }

        public void onServiceDisconnected(ComponentName className) {
            mServiceBridge = null;
        }
    };
    
    public void startService() {
        bindService(...);
    }
    
    public void stopService() {
        unbindService(...);
    }
}
```

考虑到语义的逻辑，我一开始选择了在自定义的 `MainApplication` 的 `onCreate` 中启动服务；并且为了避免不必要的消耗，在用户从主 activity 退出时，会停止这个服务。

过了几天，有同事在测试过程中反馈说，每次正常从 app 退出后，再次打开 app 运行涉及服务的功能都会抛 `mServiceBridge` 的空指针访问异常，但是下一次打开 app，相关功能又是好的。

我一开始觉得莫名其妙，因为对象空指针只能意味着服务没有被启动，但是启动服务是在 `Application.onCreate` 中进行的，这说不过去。

后来我不小心瞥到 DDMS 的进程列表，发现即使关闭了所有的 activity 退出了应用，相应的进程并没有退出...正如牛逼顿被苹果砸到的那个瞬间，一个想法一闪而过。

经过 demo 验证，问题出在 android 的进程复用上。

和传统 PC 上的做法不同，考虑到移动设备的特殊环境，即使所有的 activity 退出了，系统也不会马上回收 app 所在的进程。进程的资源、状态仍被保留，只是会进入一个不稳定状态：系统随时都能回收这个进程。

而如果在进程被系统回收前又打开了 app，那么系统会复用之前的进程。因为进程并没有被重新创建，所以 `onCreate` 函数被跳过了。在上面的环境下，就会跳过启动服务，导致 `mServiceBridge` 对象为空。

解决方案有两个：

- 把启动服务的操作放到某个 activity 启动中，例如 the infamous splash activity
- 主 activity 结束后强行退出进程

另外，根据上面的情况起码要明白，如果某个行为（例如一些设施的初始化）要放在 `Application.onCreate` 中完成，那么语义上这个操作形成的作用是跟随整个进程生命周期的；而进程的生命周期在 android 上是不可知的，所以相应的，这个行为的设计也要考虑到这点。