title: Activity详解
date: 2015-10-14 18:26:55
tags: Android
---


## 启动Activity

- `显式Intent`



``` java	
Intent intent = new Intent(MainActivity.class,SecondActivity.class);
startActivity(intent);
```

- `隐式Intent`:并不指定某一个特定的Activity而是通过action和category还有data来指定，通过系统分析启动哪一个Activity.如果不存在相应的Activity则报错,存在多个则可以选择，其中多个action和多个data满足一个就行，category必须全部满足，没有android.intent.category.DEFAULT的Activity无法响应隐式Intent.


``` xml
<intent-filter>
      <action android:name="com.meituan.kevin.ACTION_START"/>
      <category android:name="android.intent.category.DEFAULT"/> //必须加，startActivity()会默认添加这个category
      <category android:name="com.meituan.kevin.second.CATEGORY"/>
</intent-filter>
```

``` java        
Intent intent = new Intent("com.meituan.kevin.ACTION_START");
intent.addCategory("com.meituan.kevin.second.CATEGORY");
startActivity(intent);
```


## 关闭Activity

Activity可以显示调用finish()进行关闭，但是一般不这样做，一般是由Android系统来管理，只有确实不希望用户返回这个实例时才显示调用。


<!--more -->

## Activity的生命周期
![](http://hukai.me/android-training-course-in-chinese/basics/activity-lifecycle/basic-lifecycle-stopped.png)


### 七个回调方法
![](http://developer.android.com/images/activity_lifecycle.png)

- onCreate() 在这个方法中应该初始化Activity所必须的组件，通过setContentView()来加载layout,初始化Activity所需要的数据，一般将只初始化一次的放在这里
- onStart()用户可见，但无法交互，Activity还在后台，当下一个Activity对其显示的数据做出改变时，应在这里进行设置。
- onResume() Activity完全可见，并处于前台，可与用户进行交互，重新初始化onPause中释放的资源
- onPause() 此时可以做一些存储数据、停止动画，释放部分系统西资源等工作，不能太耗时，负责会影响新的Activity的显示
- onRestart()从完全不可见变成可见时调用
- onStop() 可做一些稍微重量级的回收工作，例如对某些资源的释放，因为在stopped状态下，被回收的几率很大，所以有些也需要在这里回收
- onDestroy() 可做回收工作和最终的资源释放,因为引用会被Activity回收时销毁，而线程不会，所以需要在这里清除开启的线程

**除了上面的，我们还有其他的回调方法:onContentChanged， onPostCreate， onPostResume， onConfigurationChanged**

`onContentChanged()`是Activity中的一个回调方法
当Activity的布局改动时，即setContentView()或者addContentView()方法执行完毕时就会调用该方法， 例如，Activity中各种View的findViewById()方法都可以放到该方法中。

`onPostCreate`方法是指onCreate方法彻底执行完毕的回调，`onPostResume`类似，这两个方法官方说法是一般不会重写，现在知道的做法也就只有在使用`ActionBarDrawerToggle`的使用在onPostCreate需要在屏幕旋转时候等同步下状态，Google官方提供的一些实例就是如下做法：



	protected void onPostCreate(Bundle savedInstanceState) {
    	super.onPostCreate(savedInstanceState);
    	// Sync the toggle state after onRestoreInstanceState has occurred.
    	mDrawerToggle.syncState();
	}



- 在Activity第一次启动时，会先后调用 onCreate() -> onStart()-> onResume()
- 当打开新的Activity或切换到桌面时，会调用onPause()-> onStop()
- 当用户再次回到原Acitivty时，onRestart() -> onStart() -> onResume()
- 按back返回时 onPause() -> onStop() -> onDestroy()
- 当打开的Activity采用了透明主题时，会调用onPause和onSaveInstan

如图： 当处于paused状态和stoped状态时，可能会被回收，这时再次打开会从新开始

onStart和onStop是从是否可见的角度上来回调的，而onResume和onPasuse是从Activity是否位于前台这个角度回调的，除此之外在实际使用中没有其他明显区别。

一个Activity启动另一个Activity时两个的先后调用顺序：

![](http://ww3.sinaimg.cn/large/b00f9334jw1ex0ijc5gr8j20ic03dzn7.jpg)

> 只有在调用了前一个Activity的`onPause`后才能调用新Activity的`onResume()`,因此不能在onPause()中做重量级的操作，否则会影响交互，用户可以看到界面，点击屏幕却没有反应。所以应尽量在onStop中做操作。


如果Activity旋转，在Activity不做设置的情况下，调用顺序如下,启动Acticity 旋转屏幕,再转回去的过程：

![](http://ww3.sinaimg.cn/large/b00f9334jw1ex0l8d5eb7j20k40bqwq1.jpg)

###很重要

> 我们可以看到Activity会先销毁在重建，由于这是异常退出的，所以其中`onSaveInstanceState()`会被调用保存当前的状态，保存为Bundle对象，时机是onStop之前，重建后会调用`onRestoreInstanceState()`来恢复，但是一些需要用来显示的数据需要在onCreate里进行恢复,官方推荐`onRestoreInstanceState()`。系统会自动帮我们保存当前Activity的视图结构，大部分view都重写了这两个方法，但是必须设置id，才能恢复。

**注意onSaveInstance会在Activity`可能被销毁且有机会重新显示`的情况下才会被调用，因此调用finish()方法时不会被调用，而onRestoreInstanceState()则只会在之前的Activity`真正的被销毁后`需要重建时才会被调用**

当系统配置发生变化时，Activity会被重建，我们可以设置Activity的configChanges属性，防止重建。例如：

	android:configChanges = "orientation|screenSize"//如果设置多值可以用 | 来连接起来 



### 六种状态
- Created
- Started
- Resumed
- Paused和Stopped状态下一样，在内存不如的情况下可能会被Killded掉
- Stopped
- Destroyed

### 三个生存期
1. 完整的生存期：onCreate()和onDestroy()之间
2. 可见生存期：onStart()和onStop()之间，活动对用户来说是可见的，可在onStart方法中对资源进行加载，在onStop()中对资源进行释放
3. 前台生存期：onResume()和onPause()之间

### 四种启动模式
1. standard:标准模式，也是默认模式。每次启动都会创建新的实例，压在栈顶
2. singeTop:如果启动的Activity就是目前栈顶的Activity时，就不会在建一个新的实例来压栈了。但会调用onnNewIntent()
3. singleTask 如果新启动的Acitivity在栈中存在了，则将其之上的Activity全部出栈，可将MainActivity设置维singleTask模式，在要退出的Activity中跳转到MainActivity，然后再MainActivity中调用finish()达到退出应用的目的。
4. singleInstance 申明为此模式启动的Activity会单独出现在一个新的任务栈中，当其他应用也要新建一个此Activity时，则不用创建，可共享。

> Activity所在的栈的名字是由TaskAffinity决定的，这个参数标识了一个Activity所需要的任务栈的名字，默认是Application的taskAffinity，但是Application的默认taskAffinity是包名，所以默认taskAffinity的值是包名。


设置Activity的启动模式有两种：

- 一种在AndroiMenifest为Activity指定启动模式
- 另一种通过在Intent里添加Flag来为Activity指定启动模式

优先级上第二种优先于第一种

连续点击三次，让其跳转到同一个Activity时我们可以看到：

![](http://ww3.sinaimg.cn/large/b00f9334jw1ex1t60gu92j20fw06sn0b.jpg)
不设置时，可以看到com.meituan.kevin.activitytest这个任务栈里存在4个Activity

![](http://ww1.sinaimg.cn/large/b00f9334jw1ex1t7nucphj20ft04og  ns.jpg)
设置时，可以看到只存在一个

当将MainActivity设置成singTask时，依次压入Second、Third，后跳入Main,回调方法：
![](http://ww1.sinaimg.cn/large/b00f9334jw1ex2rn5fk9sj20jk0a3ajk.jpg)

当Second设置成singleInstance时，A->B->C 按Back回退时，C->A->B

**singTask里的坑**：当A已startActivityForResult启动B时,将ResultCode设置成OK,A为默认时，会收到OK，但是当A为singleTask时，会返回canle..导致结果不一样。如下图：
![](http://ww3.sinaimg.cn/large/b00f9334jw1ex2sscobq5j20ja06ojxt.jpg)
![](http://ww4.sinaimg.cn/large/b00f9334jw1ex2st50520j20j506w0z4.jpg)

>所以尽量少用singleTask和singleInstance，尽量自己控制任务栈。




