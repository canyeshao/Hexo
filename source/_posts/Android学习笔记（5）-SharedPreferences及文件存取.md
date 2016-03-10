---
title: 'Android学习笔记（5）: SharedPreferences及文件存取'
date: 2016-02-01 06:28:20
tags: Android | study
categories: "Android学习笔记"
---

SharedPreferences 以键值对存储数据，非常简单。
直接放实例：

```java
SharedPreferences sharedPreferences = getSharedPreferences("lxt008", Context.MODE_PRIVATE);
Editor editor = sharedPreferences.edit();//获取编辑器
editor.putString("name", "lxt");
editor.putInt("age", 35);
editor.commit();//提交修改
```
生成的lxt008.xml文件内容如下：
```xml
<?xml version=“1.0” encoding=“utf-8” standalone=“yes” ?>
<map>
<string name="name">lxt</string>
<int name="age" value=“30" />
</map>
```

`getSharedPreferences(na, memode)`方法
参数1:指定该文件名称，名称不用带后缀。
参数2:指定文件的操作模式，共有四种操作模式。 
`Context.MODE_PRIVATE`：为默认操作模式，代表该文件是私有数据，只能被应用本身访问，在该模式下，写入的内容会覆盖原文件的内容，如果想把新写入的内容追加到原文件中。可以使用Context.MODE_APPEND
`Context.MODE_APPEND`：模式会检查文件是否存在，存在就往文件追加内容，否则就创建新文件。
`Context.MODE_WORLD_READABLE`和`Context.MODE_WORLD_WRITEABLE`用来控制其他应用是否有权限读写该文件。
`MODE_WORLD_READABLE`：表示当前文件可以被其他应用读取；`MODE_WORLD_WRITEABLE`：表示当前文件可以被其他应用写入。
`getPreferences(mode)`方法操作SharedPreferences，这个方法默认使用当前类不带包名的类名作为文件的名称。


访问SharedPreferences中的数据代码如下：
```java
SharedPreferences sharedPreferences = getSharedPreferences("lxt008", Context.MODE_PRIVATE);

//getString()第二个参数为缺省值，如果preference中不存在该key，将返回缺省值
String name = sharedPreferences.getString("name", "");
int age = sharedPreferences.getInt("age", 1);

```

其他应用要访问上面应用的preference，首先需要创建上面应用的Context，然后通过Context访问preference,访问preference时会在应用所在包下的shared_prefs目录找到preference ：
```java
Context otherAppsContext = createPackageContext(“com.test",Context.CONTEXT_IGNORE_SECURITY);

SharedPreferences sharedPreferences = otherAppsContext.getSharedPreferences(“test", Context.MODE_WORLD_READABLE);

String name = sharedPreferences.getString("name", "");
int age = sharedPreferences.getInt("age", 0);
```


在Android中，除了对应用程序私有文件夹中的文件进行操作之外，还可以从资源文件和Assets中获得输入流读取数据。这些文件分别存放在应用程序的res/raw目录和assets目录下。这些文件将在编译的时候和其他文件一起打包进APK中。
注意，这两个文件夹下的文件只能进行读操作，不能进行写操作。

1.读取res/raw下的文件资源，通过以下方式获取输入流来进行写操作
```java
InputStream is = getResources().openRawResource(R.id.filename);  
```
2.读取assets下的文件资源，通过以下方式获取输入流来进行写操作
```java
AssetManager am = null;  
am = getAssets();  
InputStream is = am.open("filename");  
```


在程序中访问SDCard，你需要申请访问SDCard的权限。
在AndroidManifest.xml中加入访问SDCard的权限如下:
```xml
<!-- 在SDCard中创建与删除文件权限 -->
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>

<!-- 往SDCard写入数据权限 -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
