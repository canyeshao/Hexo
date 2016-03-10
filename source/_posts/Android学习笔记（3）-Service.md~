---
title: 'Android学习笔记（3）:  Service'
date: 2016-02-01 06:18:36
tags: Android 
categories: "Android学习笔记"
---


一、使用场合
Service通常总是称之为“后台服务”，其中“后台”一词是相对于前台而言的，具体是指其本身的运行并不依赖于用户可视的UI界面，因此，从实际业务需求上来理解，Service的适用场景应该具备以下条件：
1.并不依赖于用户可视的UI界面；
2.具有较长时间的运行特性。
像音乐播放器，下载管理器等。
二、生命周期
1)  可以通过调用`Context.startService()`启动一个Service，这可能会触发Service的`onCreate()`和`onStartCommand()`操作，具体来说即执行`startService()`一定会触发`onStartCommand()`，但如果该Service已经在系统中存在，则`onCreate()`不会被再次调用，**它只在Service第一次启动时触发。**
通过`Context.startService()`启动的Service会一直运行，直到通过`Context.stopService()`或者stopSelf()停止它。多次通过`startService()`启动某个服务并不会生成多个实例，但会导致服务的`onStartCommand()`被多次调用，当然由于只有一个实例，因此无论启动多少次，停止它只需调用一次`Context.stopService()`或`stopSelf()`就可以了。
 
2)  也可以通过`Context.bindService()`来获得一个服务的链接，这个链接是一直会保持到通过`Context.unbindService()`断掉它。如果在连接时系统中还没有该服务，则可能会新创建一个服务，这时Service的onCreate函数也同样会被调用。连接建立时会Service的onBinder会被触发，通过onBinder可以返回连接建立后的IBinder接口对象，使用服务的客户端（比如某个Activity）可以通过IBinder对象和Service交互。
     一个Service如果是通过`bindService()`启动的，那么它会一直存在到没有任何客户端与它保持连接为止，原因是可能有很多客户端与这个服务保持连接，这时如果某个链接被客户端主动断掉只会是Service的链接数减1，当减至0的时候这个Service就会被销毁。
 
3)  一个Service既可以被启动(start)也可以被连接(bind)，这时Service的生命周期取决于它被创建的方式，如果是通过`Context.startService()`创建的则和第一种情况一样，如果是通过`Context.bindService()`使用参数`Context.BIND_AUTO_CREATE`创建的，则情况和第二种一样。
     当然，在Service停止，被销毁时，会触发其`onDestroy()`函数，我们需要在这里完成这个Service相关资源的清理，比如停止其子线程，注销监听器等等。


三、需要注意的地方
1)   Service无论以何种方式创建，都是在应用的主线程里创建的，也就是说创建一个Service并不意味着生成了一个新的线程，Service的创建过程是阻塞式的，因此也需要考虑性能，不能影响界面和逻辑上的后续操作。
 
2)   如果Service自己没有生成新的线程，那它也是运行在应用的主线程里的，因此Service本身并不能提高应用的响应速度和其他的性能，而是说通过这个后台服务生成新的线程来处理比较耗时的操作如大数据的读取等来提高响应，Service自己并不能保证这一点。Service相当于提供了一个这些费时操作的平台，由它在后台创建新线程完成这些任务，以及视各种情况管理这些线程，包括销毁。
 
3)  ` stopService()`和`unbindService()`都可以把Service停掉，但是如果希望明确立刻停掉Service，则使用stopService更安全，因为unbindService实质上是将与Service的连接数减一，当减为0的时候才会销毁该服务实例，stopService的效果相当于将连接数立即减为0，从而关闭该服务，所以在选择关闭方式上要视不同情况而定。
4)StartService与BindService
StartService通过`startService()`进行方法的调用， 调用者与服务之间没有联系，即使调用者退出了服务依然在运行； 
BindService通过`bindService()`来绑定服务， 调用者和绑定者绑在一起， 调用者一旦退出了， 服务就终止了。

四、与Activity交互
1.广播交互
2.共享文件交互(SharedPreferences)
  Server端将当前下载进度写入共享文件中，Client端通过读取共享文件中的下载进度，并更新到主界面上
3.Messenger交互(信使交互)

五、实际应用
以超级省电(PowerSaveManagement) app 介绍一下一下实际应用
```xml
<service android:name="com.wt.powersavemanagement.PowerSaveService" >
    <intent-filter android:priority="1000" >
        <action android:name="com.wt.powersavemanagement.PowerSaveService" />
    </intent-filter>
</service>
```
1）在开机广播里面监听并启动
```java
Intent serviceIntent = new Intent("com.wt.powersavemanagement.PowerSaveService");
serviceIntent.setPackage("com.wt.powersavemanagement");
context.startService(serviceIntent);
```
2）
```java
public void onCreate() {

}
```
3）
```java
public int onStartCommand(Intent intent, int flags, int startId) {

}
```
4）
```java
public void onDestroy() {

}
```

