# 第一章 Activity 的生命周期和启动模式
## 1.1 Activity 的生命周期全面分析
### 1.1.1 典型情况下

##### 生命周期配对
Activity 的生命周期都是两两一组的，前面一个调用了，后面的也必会调用，例如在 onStart 中调用了 finish ，则执行顺序为：onCreate -> onStart -> onStop -> onDeatroy

* onCreate 和 onDestroy 标识着 Acticity 的创建和销毁
* onStart（onRestart） 和 onStop 标识着 Activity 是否可见，当从其他 Activity 返回此 Activity 时还会调用 要在 onStart 之前调用 onRestart
* onResume 和 onPause 标识 Activity 是否在前台，是否可操作
 
##### onStart 和 onResume、onPause 和 onStop 的区别
onStart 表示是否可见，onResume 表示是否在前台，即是否可交互。所以当在这个 Activity 上打开的新 Activity 采用了透明主题，这时本 Activity 都处于可见但不可操作的状态，所以只会调用 onPause 而不会调用 onStop。

##### 当打开新 Activity 时何时调用

当打开新的 Activity 时，旧 Activity 先调用 onPause ，再启动新的 Activty。所以我们不能在 onPause 中做耗时操作，否则会影响新 Activity 的显示。

![](http://ww1.sinaimg.cn/mw690/64ddfd2bgy1fp592hwfvkj20wz08qq6e.jpg)

### 1.1.2 异常情况下

#### 情况 1. 资源相关的系统配置发生改变导致 Activity 被杀死并重新创建

资源相关的系统配置包括屏幕方向，系统语言等，这些配置发生变化后系统需要根据情况重新加载资源文件，默认情况下，Activity 会被销毁并重建。

##### onSaveInstanceState 保存状态和恢复

当 Activity 异常退出时，会调用 onSaveInstanceState 保存当前的 Acitivty 的状态，调用时机在 onStop 之前，和 onPause 之间没有明确顺序。恢复 Activity 有两个方法: onCreate 和 onRestoreInstanceState。onCreate 中需要判断 Bundle 是否为空，onRestoreInstanceState 只在异常退出后恢复的时候调用，调用时机在 onStart 之后。

在 onSaveInstanceState 和 onRestoreInstanceState 中，系统自动为我们做了一定的恢复工作。其保存了当前 Activity 的视图结构，并在重启后恢复这些数据。比如文本框中用户输入的数据，ListView 滚动的位置等。每个 View 都有 onSaveInstanceState 和 onRestoreInstanceState 两个方法，可以自己查看源码。

#### 情况 2. 资源内存不足导致低优先级的 Activity 被杀死

##### Activity 优先级
1. 前台 Activity
2. 可见但非前台 Activity，执行了onPause
3. 后台 Activity，执行了 onStop 

脱离了四大组件的进程很容易被杀死，所以后台工作要放入 Service 中保证有一定的优先级，才不会轻易被杀死。

##### 当资源配置改变时不重新创建
 Activity 中的 configChange 属性可以置顶当某项系统设置改变后不进行重建。常用的有 locale、orientaion、keyboardHidden 三个选项。
 ![](http://ww1.sinaimg.cn/mw690/64ddfd2bgy1fp5q8k11r7j20z20pktnz.jpg)
 
 ## 1.2 Activity 的启动模式
 ### 1.2.1 Activity 的 LaunchMode
 ##### 四种启动模式
 
 1. standard：标准模式。每次启动一个 Activity 都会重新创建一个新的实例，并添加进启动这个 Activity 的 Activity 的栈。如果使用非 Activity 类型的 Context 来启动 Activity，因为它本身没有所谓的任务栈，所以这种情况必须为 Activity 指定 FLAG_ACTIVITY_NEW_TASK 标记，这样启动的时候就会为他创建一个新的任务栈。
 2. singleTop：栈顶复用模式。如果新的 Activity 已经位于栈顶，那么此 Activity 不会被重新创建，而是调用其 onNewIntent 方法，以取出当前的请求信息。其方法调用顺序为 onPause -> onNewItent -> onResume 。
 3. singleTask：栈内复用模式。只要 Activity 在一个栈中存在，就不会重新创建实例，而是回调其 onNewIntent，并把它上面的其他 Activity 出栈销毁。所以与其说是调用，更像是从栈顶 Activity 依次返回此 Activity，所以这个时候还会调用 onRestart 和 onStart ，当然，如果其本来就在栈顶，其效果和 singgleTop 就一样了。
 4. singleInstance：单实例模式。此 Activity 只能单独位于一个任务栈中，后续的请求也只会复用这一个 Activity 。
 
##### 任务栈

每个 Activity 都有一个参数 TaskAffinity 标识其所需要的任务栈，默认情况下这个参数就是应用的包名，我们也可以为 Activity 单独指定。

当 TaskAffinity 和 singleTask 配对使用时，新的 Activity 会运行在 TaskAffinity 任务栈中，如果没有这个任务栈，就会创建这个任务栈再把 Acitivity 实例入栈。

当TaskAffinity 和 allowTaskReparenting 结合。如果一个 Activity allowTaskReparenting 属性为 true，那么它允许放入其他任务栈中，但是当系统发现其指定的任务栈存在的时候，就会直接转移到其指定的任务栈中。例如应用 A 启动了应用 B 的一个 Activity，此时这个 Activity 在应用 A 的任务栈中，而其 TaskAffinity 应该是 B 的包名，此时如果从桌面再启动 B，B 会创建自己的任务栈，则此 Activity 就会回到 B 的任务栈中。

##### 指定启动模式

两种方式：
1. 在 AndroidMenifest 中为 Activity 指定，这个方式指定的是默认启动方式
    
```
<activity android:name=".Main2Activity"
            android:launchMode="singleTask">
        </activity>
```
2. 在 Intent 中指定，这种方式优先级比第一种方式高，可以单独为某次启动指定启动方式。

```
startActivity(new Intent(MainActivity.this,MainActivity.class));
                Intent intent=new Intent(MainActivity.this,Main2Activity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
                startActivity(intent);
```

### 1.2.2 Activity 的Flags

常用的 Flags
* FLAG_ACTIVITY_NEW_TASK 指定 shingleTask 启动模式
* FLAG_ACTIVITY_SINGLE_TOP 指定 singleTop 启动模式
* FLAG_ACTIVITY_CLEAR_TOP 任务栈中位于他之上的所有 Activity 都要出栈，一般和 FLAG_ACTIVITY_NEW_TASK 配合使用；如果被启动的 Activity 是 standard 模式，则它以及其上的所有 Activity 都会出栈，然后重新创建新的 Activity 入栈。
* FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS 指定 Activity 属性 android:excludeFromRecents="true" 。这意味着这个 Activity 不会出现在 Activity 的历史列表中，这时候用户无法通过历史列表返回此 Activity 。


## 1.3 IntentFilter 的匹配规则

Intent 隐式调用时需要能够匹配目标组件的 IntenFilter 中设置的过滤信息。IntentFilter 中的过滤信息有 action、category、data。他们的匹配规则：

1. action：一个过滤规则可以有多个 action，Intent 必须制定一个 action，只要 Intent 中的 action 能和其中任意一个匹配上就算匹配成功。action 区分大小写。
2. category：Intent 中的每一个 category 都必须是过滤规则中已经定义了的 category，如果 Intent 中没有设置 category，会默认带有一个 android.intent.category.DEFAULT。
3. data：data 分为mimeType 和 URL 两部分，mimeType 表示媒体类型，其他参数拼成一个 URL，其结构为
    
    ```
    <scheme>://<host>:<port>/[<path>|<pathPrefix>|<pathPattern>]
    ```
    示例
    ```
    http://122.112.238.143:8080/web/system.vm
    content://com.example.project:200/folder/subfolder/etc
    ```
    其中 scheme 默认值是 content 和 file ；path 表示完整路径；pathPattern 是可以添加 '*' 通配符的路径；pathPrefix 表示前缀信息。
    匹配 data 需要 mimeType 和 URL 都匹配才可以。
    
##### 判断是否有 Activity 匹配 Intent

当使用隐式调用的时候如果没有 Activity 能匹配，就会报错，所以调用的时候可以判断一下是否有 Activity 能匹配此 Intent。有以下几种方法：

```
intent.resolveActivity(getPackageManager());
        getPackageManager().resolveActivity(intent, PackageManager.MATCH_DEFAULT_ONLY);
        getPackageManager().queryIntentActivities(intent,PackageManager.MATCH_DEFAULT_ONLY);
```
