---
title: 'Android学习笔记（4）:  Broadcast'
date: 2016-02-01 06:25:48
tags: Android | study
categories: "Android学习笔记"
---

#  Android学习笔记（4）:  Broadcast

**1. BroadcastReceiver** 

广播接收者（ BroadcastReceiver ）用于接收广播 Intent ，广播 Intent 的发送是通过调用 `Context.sendBroadcast()` ``Context.sendOrderedBroadcast()`、或 Context.sendStickyBroadcast()方法来实现的。通常一个广播 Intent 可以被订阅了此 Intent 的多个广播接收者所接收。 
广播是一种广泛运用的在应用程序之间传输信息的机制 。而BroadcastReceiver 是对发送出来的广播进行过滤接收并响应的一类组件；来自普通应用程序，如一个应用程序通知其他应用程序某些数据已经下载完毕。BroadcastReceiver 自身并不实现图形用户界面，但是当它收到某个通知后， BroadcastReceiver 可以启动 Activity 作为响应，或者通过 NotificationMananger 提醒用户，或者启动 Service 等等。
(1).笼统点说就是用来传输数据的，具体点说：1) 实现了不同app之间数据传输与共享，只要是和发送广播的action相同的接收者都能接受到这个广播。典型的app就是android自带的短信，电话等广播，只要实现了他们的action的广播，就能接收他们的数据了，以便做出一些处理，比如说拦截系统短信，拦截骚扰电话等

(2).起到通知作用，比如在service中要通知主程序跟新主程序UI等。service是没有界面的，所以不能直接获得主程序中的控件，这样就只能在主程序中实现一个广播接受者专门用来接受service发来的数据格通知了。

**2. android提供了2种注册广播接受者的形式**

1) 静态注册
 静态注册方式是在AndroidManifest.xml的application里面定义receiver并设置要接收的action。
静态方式的特点：不管该app是否处于活动状态都会进行监听。
example：
```xml
  <receiver android:name="MyReceiver">
      <intent-filter android:priority = "1000">
        <action android:name="MyReceiver_Action"/>
      </intent-filter>
  </receiver>
```

其中，MyReceiver为继承BroadcastReceiver的类，重写了OnReceiver方法，并在OnReceiver方法中对广播进行处理。<intent-filter>标签设置过滤器，接受指定action广播。

2)动态注册
动态注册方式在activity里调用函数来注册和静态的内容差不多。一个形参是Receiver，一个是intentFilter，其中里面是要接收的action。

动态注册的特点:在代码中进行注册后，当app关闭后，就不在进行监听了。

example:
```java
 MyReceiver receiver = new  MyReceiver();
//创建过滤器，并指定action，使之用于接收同action的广播
IntentFilter filter = new IntentFilter("MyReceiver_Action");

//注册广播接收器
 registerReceiver(receiver, filter);

// 指定广播目标Action
Intent intent = new Intent("MyReceiver_Action");
// 可通过Intent携带消息
intent.putExtra("msg", "发送广播");
// 发送广播消息
sendBroadcast(intent);
//注销广播接收器
unregisterReceiver(receiver);
注：
1.一般在onResume()中注册BroadcastReceiver，在onPuase()中注销BroadcastReceiver。
2.一个BroadcastReceiver 对象只有在被调用onReceive(Context, Intent)时才有效，当从该函数返回后，该对象就无效的了，结束生命周期。

```

Android 中广播的生命周期，它并不像 Activity 一样复杂，运行原理很简单

>调用对象->实现onReceive()->结束


生命周期只有十秒左右，如果在` onReceive() `内做超过十秒内的事情，就会报错 。

每次广播到来时 , 会重新创建 BroadcastReceiver 对象 , 并且调用 `onReceive() `方法 , 执行完以后 , 该对象即被销毁 . 当` onReceive()` 方法在 10 秒内没有执行完毕， Android 会认为该程序无响应 . 所以在 
BroadcastReceiver 里不能做一些比较耗时的操作 , 否侧会弹出 `ANR(Application No Response)` 的对话框  。
