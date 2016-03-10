---
title: 'Android学习笔记（2）:  Activity'
date: 2016-02-01 06:00:30
tags: Android | study
categories: "Android学习笔记"	
---


#  Android学习笔记（2）:  Activity
**1. Activity的概念**
Activity 是用户接口程序，原则上它会提供给用户一个交互式的接口功能。它是 android 应用程序的基本功能单元。Activity 本身是没有界面的。所以activity类创建了一个窗口，开发人员可以通过setContentView(View)接口把UI放到activity创建的窗口上，当activity指向全屏窗口时，也可以用其他方式实现：作为漂浮窗口（通过windowIsFloating的主题集合），或者嵌入到其他的activity（使用ActivityGroup）。activity是单独的，用于处理用户操作。几乎所有的activity都要和用户打交道。

**2. Activity 的生命周期**

`onCreate()`,必须要实现的方法。当我们的Activity创建的时候，系统会调用此方法。在我们实现当中，需要初始化activity当中重要的组件。需要强调的是，在这个方法里，需要调用`setContentView()`来为activity的用户界面调用布局。
注意点：不要做太耗时的事情，否则影响启动速度，用户体验不好。
当Activity处于可见状态的时候就会调用`onStart()`方法(不常用，做一了解)
当Activity可以得到用户焦点的时候就会调用`onResume()`方法(很常用，可以做许多处理（如访问网络，打开文件等))
当Activity没有被销毁的时候重新调用这个Activity就会调用`onRestart()`方法(不常用)
当Activity被遮挡住的时候就会调用`onPause方法`(多用于保存数据)
当Activity处于不可见状态的时候就会调用`onStop()`方法（Activity处于后台时的状态，可用于停止界面刷新，暂停动画等操作）
当Activity被销毁时会调用`onDestory()`方法（Activity生命周期结束，可做一些回收处理，对象释放，关闭文件流数据库等，一般通过调用activity的`finish()`方法触发）。

**3. Activity的跳转与传值**
在Android中，如果我们要通过一个Activity来启动另一个Activity，可以使用 `startActivity(Intent intent)`方法来传入一个Intent对象，这个Intent对象我们可以精确的指定我们需要跳转的Activity上，或者通过Intent对象来指定我们要完成的一个action操作。
①.通过setClass方法来指定我们要跳转的Activity
```java
Intent intent = new Intent();
intent.setClass(MainActivity.this, SecondActivity.class);

```
②.通过setAction方法来我们要完成的一个action操作
```java
Intent  intent = new Intent();
intent.setAction("com.xiaoluo.android_intent.second");

```
通过这种方式可以来指定我们的Intent对象要完成某个操作，这个操作可以是启动一个Activity，我们可以在AndroidManifest.xml中在 <Activity> 元素下指定一个 <intent-filter> 对象，然后其子元素声明一个 <action> 元素，这样我们可以将这个action动作绑定到了这个Activity上，即Android操作系统会去找与intent对象中指定的action名字的 <intent-filter>对象，然后执行相应的动作，例如：
```xml
<activity 
   android:name="com.xiaoluo.android_intent.SecondActivity"
   android:label="SecondActivity">
        <intent-filter>
          <action android:name="com.xiaoluo.android_intent.second"/>
          <category android:name="android.intent.category.DEFAULT"/>
        </intent-filter>
</activity>

```
这样我们的Intent对象，在启动时，就会找到名字为 com.xiaoluo.android_intent.second 的<intent-filter>对象，来启动我们的SecondActivity。
传值的方式详见MainActivity和SecondActivity两个文件。

**4.Activity其他（注册，样式）**
Activity一定要在AndroidManifest.xml中注册。否则会报异常。
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"          package="com.xiaoluo.android_activity"    
android:versionCode="1"    
android:versionName="1.0" >
    <uses-sdk
        android:minSdkVersion="8"        
        android:targetSdkVersion="18" />
    <application
        android:allowBackup="true"        
        android:icon="@drawable/ic_launcher"                                                                                                             
        android:label="@string/app_name"        
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.wingtech.android_activity.MainActivity"             
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.xiaoluo.android_activity.SecondActivity"            
            android:label="SecondActivity" >
        </activity>
</application>
</manifest>

```
样式：
```java
android:theme="@android:style/Theme.NoTitleBar"    //删除标题栏
android:theme="@android:style/Theme.NoTitleBar.Fullscreen"    //删除标题栏和电池栏

```
(上述两条不能同时使用)


```xml
android:theme="@android:style/Theme.Dialog"        //窗口Activity
android:theme="@android:style/Theme.Light"         //背景为白色
android:theme="@android:style/Theme.Wallpaper"     //背景为桌面壁纸

```





实例代码：
**MainActivity.java**
```java
public class MainActivity extends Activity
{
    private Button button;
    
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        button = (Button)findViewById(R.id.button);
        button.setOnClickListener(new ButtonListener());
    }

    class ButtonListener implements OnClickListener
    {
        @Override
        public void onClick(View v)
        {
//          Intent intent = new Intent();
//          intent.setClass(MainActivity.this, SecondActivity.class);//第一种跳转方式，指定包名类名
            Intent  intent = new Intent();
            intent.setAction("com.wingtech.android_intent.second");//通过action
            //以下是传值（键值对）
            intent.putExtra("com.wingtech.android_intent.age", 20);　// 第一个参数为key（可自己命名），后面是各种类型的数据类型
            intent.putExtra("com.wingtech.android_intent.name", "wingtech");
            Bundle bundle = new Bundle();    //Bundle的底层是一个HashMap<String, Object>
            bundle.putString("hello", "world");
            intent.putExtra("bundle", bundle);
            startActivity(intent);
        }
    }
    
    @Override
    public boolean onCreateOptionsMenu(Menu menu)
    {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.main, menu);
        return true;
    }

}

```

**SecondActivity.java**
```java
public class SecondActivity extends Activity
{
    private TextView textView;
    private final String TAG = "SecondActivity";
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.second);
        
        textView = (TextView)findViewById(R.id.textView);
        
        //得到跳转到该Activity的Intent对象
        Intent intent = getIntent();
        
        int age = intent.getIntExtra("com.wingtech.android_intent.age", 10);
        String name = intent.getStringExtra("com.wingtech.android_intent.name");
        Bundle bundle = intent.getBundleExtra("bundle");
        String world = bundle.getString("hello");
        Log.i(TAG, age + ", " + name + ", " + world);
        textView.setText(name + " : " + age + ", " + world);
    }
}

```
