---
title: 2016-11-7未命名文件 
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---
## 类装载器 DexClassLoader
在Java环境中，有个概念叫做“类装载器”（ClassLoader）,其作用是动态装载Class文件。标准的Java SDK 中有一个ClassLoader类，借助它可以装载想要的Class文件，每个ClassLoader对象在初始化时必须指定Class文件路径。

对于Android应用程序，本质上虽然也是java开发，并且使用标准的Java编译器编译出Class文件，但最终的Apk文件中包含的却是dex类型的文件。dex文件是将所需的所有Class文件重新打包，打包的规则不是简单地压缩，而是完全对Class文件内部的各种函数表，变量表等进行优化，并产生一个新的文件，这就是Dex文件。由于dex文件是一种经过优化的Class文件，因此需要加载这样特殊的Class谁的就需要特殊的类装载器， 这就是DexClassLoader,Android SDK 中提供的DexClassloader类就是出于这个目的。

1. 根据apk或是jar所在的路径（/data/app/com.shenjianli.plugin.apk）dexPaht,解压dex文件存放路径（/data/data/com.shenjianli.pluginhost）dexOutputDir,目标类中c/C++库存放的路径libPath和父装载器创建DexClassLoader对象。
2. 调用DexClassLoader的loadClass()方法装载指定的类，返回一个Class对象。
3. 调用Class对象的newInstance()方法，返回一个真正的PluginClass对象。
4. 通过反射机制去调用PluginClass中的方法Method
5. 调用该Method对象的invoke()方法，得到PluginClass中方法的处理结果
![plugin](/img/dexclassloader001.png)

## 基于类装载器设计一种“插件”架构
1.定义一个interface接口，仅仅定义函数的输入和输出，不定义具体实现，该interface类一方面存在于Plugin项目中，另一方面也存在于Host项目中，即该类同时参与 两个项目的编译


## JNI调用机制
Java Native Interface (JNI)是Java本地接口，所谓本地（native）一般是指c/c++语言。

三种情况需要C语言协助：
1.调用驱动
2.对于大量数据处理的模块，java的效率可能远低于C
3.效率差不多，但已经存在C代码

### 从Java调用访问C
1.在Java中定义某个函数为Native类型
2.编译该JAVA程序文件，生成CLASS
3.用javah -jni命令编译得到头文件
4.本地方法的实现，用c/c++语言编写程序源文件
5.创建一个共享库，用cl编译器编译
6.System.loadLibrary(“test”); 加载so或dll
7.执行java程序
[从Java调用访问C](http://blog.sina.com.cn/s/blog_5a08b1780100cf7i.html)
public native void display(String strName);
NIEXPORT void JNICALL Java_jni_TestJni_display(JNIEnv *env, jobject arg, jstring inString)
当Java调用native时，编译器会向native引擎传递调用者的包名，以及函数名称，还有参数类型，native引擎根据这些信息决定应该具体调用那个本地函数，native引擎中AndroidRuntime类提供了一个registerNativeMethods()函数，可以通过该函数来定义Java native函数和C函数名称的映射关系。

在C函数中，会包含至少两个参数，前者是JNIEnv对象，该对象是一个Java虚拟机（JVM）所运行的环境，相当于Jvm的“管家”，通过它可以访问Jvm内部的各种对象；第二个参数arg是调用该函数的对象
### 从C中访问Java
由于Java中的函数在native引擎中并没有直接的函数指针，java函数只能由java引擎去执行，而不是C。所以，C访问Java不能通过指针，而只能通过通用的参数接口，C调用Java时，也需要把想要访问的类名，函数名，参数传递给Java引擎。

1.获取Java对象的类
cls = env->GetObjectClass(jobject)
2.获取Java函数的id值
jmethodId mid = env->GetMethodId(cls,"method_name","([Ljava/lang/String;]V");
3.找到函数后，就可以调用
env->CallXXXMehod(jobject,mid,ret)
其中XXX代表了函数的返回值类型，Void,Object,Boolean,Byte,Char等

## 异步消息处理线程
对于一个普通线程而言，执行完run()方法内的代码后线程就结束，而异步消息处理线程是指，线程启动后会进入一个无限循环之中，每循环一次，从其内部的消息队列中取出一个消息，并回调相应的消息处理函数，执行完一个消息后则继续循环。如果消息队列为空，线程会暂停，直到消息队列中有新的消息。

当同时有以下两种需求时，可考虑使用异步消息线程
1.任务需要常驻，比如用于处理用户交互的任务
2.任务需要根据外部传递的消息做不同的操作

## Binder
在Android中，Binder用于完成进程间通信（IPC）,即把多个进程“别”在一起，比如，普通应用程序可以调用音乐播放服务提供的播放，暂停，停止等功能！

Binder驱动
重载了transact()方法，主要包括以下内容
1.以线程间消息通信的模式，向服务端发送客户端传递过来的参数
2.挂起当前线程，当前线程正是客户端线程，并等待服务器线程执行完指定服务函数通知（notify）
3.接收到服务端线程通知，然后继续执行客户端线程，并返回到客户端代码区

## 设计Servicer端
1. 继承Binder类新建一个Service类
2. 重载onTransact()方法，从data变量中读出客户端传递的参数（data中的变量的位置需要客户端和服务端进行约定）
3. onTransact中的参数code用于标识客户端期望调用服务端那个函数，需要双方进行约定
4. enforceInterface()是为了某种校验，它与客户端的writeInterfaceToken相对应

## Binder客户端
## 使用Service类
以上Binder服务端和客户端存在两个问题：
1.客户端如何获得服务端的Binder对象引用
2.客户端和服务端必须事先约定好两件事，服务端函数的参数在包裹中的顺序，服务端不同函数的int标识

public boolean bindService(Intent service,ServiceConnection conn,int flags)

这个函数用于绑定一个服务，其中第二个参数interface类定义如下
```
public interface ServiceConnection{
	public void onServiceConnected(ComponentName name ,IBinder service);
    public void onServiceDisconnected(ComponentName name);	
}
```
当客户端请求Ams启动某个Service后，该Service如果正常启动，那么Ams就会远程调用ActivityThread类中的ApplicationThread对象，调用参数中会包含Service的Binder引用，然后在ApplicationThread中会回调bindService中的conn接口。因此，在客户端中，可以在onServiceConnected()方法中将其参数Service保存为一个全局变量，从而在客户端的任何地方都可以调用该远程服务！

## 保证包裹内参数顺序aidl工具的使用
aidl 可以把aidl文件转化为一个Java类文件，这个文件同时重载了transact和onTransact()方法，统一了存入包裹和读取包裹参数

## 系统服务中的Binder对象
getSystemService方法获取一个系统服务时，是如何得到Binder引用的呢？
ServiceManager是一个独立进程，其作用是管理各种系统服务
ServiceManager本身也是一个Service，Framework提供一个函数BinderInteranl.getContextObject()来获取ServiceManager，然后通过ServiceManager的提供的方法获取其他系统Service的Binder引用！

系统中仅暴露一个全局的Binder引用，那就是ServiceManager，而其他系统服务则可以隐藏起来，从而有助于系统服务的扩展，以及调用系统服务的安全检查。

Service001

## FrameWork
Android程序的入口在那里？
框架中包含三个主要部分，分别为服务端，客户端，linux驱动

### 服务端
主要包含两个重要类，WindowManagerService(WmS)和ActivityManagerService(Ams)，Ams主要是用来管理所有应用程序中的Activity

### 客户端
AcitvityThread,这个是应用程序的主线程类，所有的apk程序有且仅有一个ActivityThread类
Activity,为apk程序中一个最小运行单元
PhoneWindow类，继承于Window类，内部包含一个DecorView对象，DecorView的父类是FrameLayout
Window类，提供一组通用的窗口（window）操作api，Wms所管理的并不是Window类，而是一个View或者ViewGroup类
DecorView类，是FrameLayout的子类，并且是PhoneWindow中的一个内部类，DecorView主要是对FrameLayout进行了一定的修饰，如添加Title bar，并响应特定的热键消息
ViewRoot类：Wms管理客户端窗口，继承于Handler，其作用主要是把Wms的IPC调用转化为本地的一个异步调用！
W类，继承于Binder并且是ViewRoot的一个内部类

## APK程序运行过程：
1.ActivityThread从main()函数中开始执行，调用prepareMainLooper()为UI线程创建一个消息队列（MessageQueue）
2.创建一个ActivityThread对象，在AtcitityThread的初始化代码中会创建一个H(Handler)对象和一个ApplicationThread(Binder)对象，其中Binder负责接收远程Ams的IPC调用，接收到调用后，则通过Handler把消息发送到消息队列，UI主线程会异步地从消息队列中读取并执行相应操作，如，start,stop,pause
3.UI线程调用Looper.loop()方法进入消息循环，不断的取消息，处理消息
4.当ActivityThread接收到Ams发送的start某个Activity后，就会创建指定的对象。Activity又会创建PhoneWindow类---->DecorView类--->创建相应的View或者ViewGroup;创建完成后activity需要把创建好的界面显示到屏幕上，于是调用WindowManager类---->创建一个ViewRoot对象和W类--->WindowManager再调用Wms提供的远程接口完成添加一个窗口并且显示到屏幕
5.接下来用户在程序界面上操作，KeyQ线程不断把用户消息存储到QueueEvent队列中，InputDispatcherThread线程逐个取出消息，然后调用Wms中的相应消息处理函数，当Wns发现该消息属于客户端某个窗口时，就会调用相应窗口的w接口。
6.W类是一个Binder，负责接收Wms的IPC调用，并把调用消息传递给ViewRoot，ViewRoot再把消息传递给UI主线程ActivityThread,ActivityThread解析该消息并做相应的处理。在客户端程序中，首先处理消息的是DecorView，如果DecorView不想处理某个消息，则可以将消息传递给其内部包含的子View或者ViewGroup,如果还没有处理，则传递给PhoneWindow,最后再传递给Activity.

## 客户端中的线程
Android Apk程序中都有那些线程？
什么是UI线程
包含有Activity的客户端程序中至少包含三个线程，每个Binder对象都对应一个线程，Activity启动后会创建一个ViewRoot.W对象，同时ActivityThread会创建一个ApplicationThread对象，这两个Binder对象会启动两个线程，负责接收linux Binder驱动发送IPC调用，最后一个程序本身所在的线程，叫主线程，也称UI线程
Thead002

Wms所管理的窗口和Window类没有任何关系

## Context
一个Context意味着一个场景，一个场景就是用户和操作系统交互的一种过程
一个Activity就是一个Context,一个Servcie也是一个Context

Activity类的确是基于Context,而Service类也是基于Context。Activity除了基于Context类外，还实现了一些其他重要接口，从设计的角度来看，interface仅仅是某些功能，而extends才是类的本质。即Activity的本质是一个Context,其所实现的其他接口是是为扩充Context的功能而已，扩充后的类称这为一个Activity或者Service.

## 创建应用窗口
  1.每个应用类窗口都重发一个Activity对象，因此，创建应用类窗口首先需要创建一个Activity对象。当Ams决定启动某个Activity时，会通知客户端进程，而每个客户端进程都对应一个ActivityThread类，任何Activity都必须隶属于一个应用程序，因此启动Activity的任务最终由ActivityThread完成。
  2.调用Activity对象的attach()方法
  3.在attach方法内部，除了进行重要变量赋值处，另一件重要的事情就是为该Activity创建Window对象，这是通过调用PolicyManager的静态方法makeNewWindow()完成的。
  4.创建好Window对象后，需要给Window对象中的mWindowManager变量赋值，该变量的类型是WindowManager类，
  5.配置好了Activity和Window对象后，接下来就需要给该窗口中添加 真正的显示元素View或者ViewGroup.从performLaunchActivity()内部调用callActivityOnCreate()开始的，并会辗转调用Activity的onCreate（）方法中。





