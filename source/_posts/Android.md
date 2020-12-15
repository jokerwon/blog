---
title: Android
tag: [Android]
---

## 一、了解 Android

<!-- more -->

### 1.1.1 Android 系统架构

1. Linux 内核层

为 Android设备的各种硬件提供了底层的驱动，如显示驱动、音频驱动、照相机驱动、蓝牙驱动、Wi-Fi驱动、电源管理等。

2. 系统运行库层
3. 应用框架层
4. 应用层

### 1.1.3 开发特色

1. 四大组件

+ 活动（Activity）
+ 服务（Service）
+ 广播接收器（Broadcast Receiver）
+ 内容提供器（Content Provider）

2. 丰富的系统控件
3. SQLite 数据库
4. 强大的多媒体
5. 地理位置定位

## 二、活动

### 2.1 活动是什么

活动（Activity）是最容易吸引用户的地方，它是一种可以包含用户界面的组件，主要用于和用户进行交互。

### 2.2 活动的基本用法

1. 在 src 中创建活动（先不勾选 Generate Layout File 以及 Launcher Activity）。New => Activity => Empty Activity。eg. FirstActivity.java

2. 创建布局。在 res/layout 下，New => Layout resource file。eg.  first_layout.xml
3. 加载布局。在 FirstActivity.onCreate() 中加入代码 setContentView(R.layout.first_layout);
4. 在 AndroidManifest 文件中注册。在 <application></application>中加入子节点 <activity android:name=".FirstActivity"></activity>，并配置为主活动，即在 <activity> 标签的内部加入 <intent-filter> 标签，并在这个标签中加入两个子节点，代码如下：

~~~xml
<activity android:name=".FirstActivity" android:label="This is FirstActivity">
 <intent-filter>
  <action android:name="android.intent.action.MAIN" />
  <category android:name="android.intent.category.LAUNCHER" />
 </intent-filter>
</activity>
~~~

注：

1. 勾选Generate Layout File表示会自动为FirstActivity创建一个对应的布局文件，勾选Launcher
   Activity 表示会自动将 FirstActivity 设置为当前项目的主活动

2. 由于在最外层的 <manifest> 标签中已经通过 package 属性指定了程序的包名是com.example.helloworld，因此在注册活动时这一部分就可以省略了，直接使用 .FirstActivity 就足够了。

### 2.4 活动的生命周期

#### 2.4.2 活动的状态

1. 运行状态

   当一个活动位于返回栈的栈顶时，这时活动就处于运行状态。系统最不愿意回收的就是处于运行状态的活动，因为这会带来非常差的用户体验。

2. 暂停状态

   当一个活动不再处于栈顶位置，但仍然可见时，这时活动就进入了暂停状态。处于暂停状态的活动仍然是完全存活着的，系统也不愿意去回收这种活动（因为它还是可见的，回收可见的东西都会在用户体验方面有不好的影响），只有在内存极低的情况下，系统才会去考虑回收这种活动。

3. 停止状态

   当一个活动不再处于栈顶位置，并且完全不可见的时候，就进入了停止状态。系统仍然会为这种活动保存相应的状态和成员变量，但是这并不是完全可靠的，当其他地方需要内存时，处于停止状态的活动有可能会被系统回收。

4. 销毁状态

   当一个活动从返回栈中移除后就变成了销毁状态。系统会最倾向于回收处于这种状态的活动，从而保证手机的内存充足。

#### 2.4.3 活动的生存期

Activity 类中定义了 **7** 个回调方法，覆盖了活动生命周期的每一个环节。

- onCreate()

  在活动第一次被创建的时候调用。你应该在这个方法中完成活动的初始化操作，比如说加载布局、绑定事件等。

- onStart()

  在活动由不可见变为可见的时候调用。

- onResume()

  在活动准备好和用户进行交互的时候调用。**此时的活动一定位于返回栈的栈顶，并且处于运行状态。**

- onPause()

  在系统准备去启动或者恢复另一个活动的时候调用。

- onStop()

  在活动完全不可见的时候调用。它和 onPause() 方法的主要区别在于，**如果启动的新活动是一个对话框式的活动，那么 onPause() 方法会得到执行，而 onStop() 方法并不会执行。**

- onDestroy()

  在活动被销毁之前调用，之后活动的状态将变为销毁状态。

- onRestart

  在活动由停止状态变为运行状态之前调用，也就是活动被重新启动了。

以上 7 个方法中除了 onRestart() 方法，其他都是两两相对的，从而又可以将活动分为 3种生存期。

- 完整生存期

  活动在 onCreate() 方法和 onDestroy() 方法之间所经历的，就是完整生存期。一般情况下，一个活动会在 onCreate() 方法中完成各种初始化操作，而在 onDestroy() 方法中完成释放内存的操作。

- 可见生存期

  活动在 onStart() 方法和 onStop() 方法之间所经历的，就是可见生存期。在可见生存期内，活动对于用户总是可见的，即便有可能无法和用户进行交互。我们可以通过这两个方法，合理地管理那些对用户可见的资源。比如在 onStart() 方法中对资源进行加载，而在 onStop() 方法中对资源进行释放，从而保证处于停止状态的活动不会占用过多内存。

- 前台生存期

  活动在 onResume() 方法和 onPause() 方法之间所经历的就是前台生存期。在前台生存期内，活动总是处于运行状态的，此时的活动是可以和用户进行交互的，我们平时看到和接触最多的也就是这个状态下的活动。

![1587625979101](C:\Users\0049003261.ZNV\AppData\Roaming\Typora\typora-user-images\1587625979101.png)

### 2.5 活动的启动模式

- standard
- singleTop
- singleTask
- singleInstance

#### 2.5.1 standard

默认的启动模式。对于使用 standard模式的活动，系统不会在乎这个活动是否已经在返回栈中存在，**每次启动都会创建该活动的一个新的实例。**

#### 2.5.2 singleTop

当活动的启动模式指定为singleTop，在启动活动时如果发现返回栈的栈顶已经是该活动，则认为可以直接使用它，不会再创建新的活动实例。不过当活动并未处于栈顶位置时，这时再启动该活动，还是会创建新的实例的。

修改启动模式：

~~~xml
<!-- 通过 activity 节点的 android:launchMode 属性来指定活动启动模式 -->
<activity
  android:name=".FirstActivity"
  android:launchMode="singleTop"
  android:label="This is FirstActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
~~~

#### 2.5.3 singleTask

当活动的启动模式指定为 singleTask，每次启动该活动时系统首先会在返回栈中检查是否存在该活动的实例，如果发现已经存在则直接使用该实例，并把在这个活动之上的所有活动统统出栈，如果没有发现就会创建一个新的活动实例。

#### 2.5.4 singleInstance

指定为 singleInstance模式的活动会启用一个新的返回栈来管理这个活动（其实如果 singleTask模式指定了不同的 taskAffinity，也会启动一个新的返回栈）。那么这样做有什么意义呢？想象以下场景，假设我们的程序中有一个活动是允许其他程序调用的，如果我们想实现其他程序和我们的程序可以共享这个活动的实例，应该如何实现呢？使用前面 3种启动模式肯定是做不到的，因为每个应用程序都会有自己的返回栈，同一个活动在不同的返回栈中入栈时必然是创建了新的实例。而使用 singleInstance模式就可以解决这个问题，在这种模式下会有一个单独的返回栈来管理这个活动，不管是哪个应用程序来访问这个活动，都共用的同一个返回栈，也就解决了共享活动实例的问题。

![1587636187847](C:\Users\0049003261.ZNV\AppData\Roaming\Typora\typora-user-images\1587636187847.png)
