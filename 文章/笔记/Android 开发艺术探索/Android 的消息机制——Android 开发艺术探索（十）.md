Android 的消息机制主要指 Handler 的运行机制，Handler 的运行需要底层的 MessageQueue 和 Looper 的支撑。

MessageQueue 是消息队列，以队列的形式对外提供插入和删除的工作，用于存储消息。其内部数据结构并不是队列而是单链表。

Looper 以无限循环的方式去查找消息，如果有的话就处理消息，否则的话就一直等着。

ThreadLocal 可以在不同的线程中互不干扰地存储并提供数据，通过 ThreadLooper 可以轻松获取每个线程的 Looper。主线程创建时会初始化 Looper，其他线程默认是没有 Looper 的。

## 10.1 Android 的消息机制概述

Android 中的 UI 控件不是线程安全的，所以 Android 规定访问 UI 只能在主线程中进行，验证是在 ViewRootImpl 中的 checkThread 方法完成的。然而 UI 线程又不能进行耗时操作，否则会出现 ANR 。所以系统提供了 Handler 来解决在子线程中无法访问 UI 线程的问题。

Handler 在创建时会采用当前线程的 Lopper 来构建内部的消息循环系统，如果当前线程没有 Looper，则需要创建 Looper。可以通过 Handler 的 post 方法将一个 runnable 投递到 Handler 内部的 Looper 中去处理，也可以通过 send 方法发送一个消息，这个消息同样会在 Looper 中进行处理。

当 Handler 调用 post 方法后，，它会调用 MessageQueue 的 enqueueMessage 方法将这个消息放入消息队列中，当 Looper 发现新消息时就会处理这个消息，最终消息中的 Runnable 或者 Handler 中的 handlerMessage 方法会被调用。因为 Looper 是运行在创建 Handler 所在线程中，所以 Handler 中的业务逻辑就切换到 Handler 所在的线程中执行了。

## 10.2 Android 的消息机制分析

### 10.2.1 ThreadLocal 的工作原理

##### 使用

ThreadLocal 是线程内的数据存储类，他可以在指定的线程中存储数据，并且只能在指定的线程中读取。当某些数据以线程为作用域，并且不同线程有不同的副本的时候，就可以采用 ThreadLocal，比如 Looper 、ActivityThread 以及 AMS 中。

代码示例：
```
final ThreadLocal<Boolean> booleanThreadLocal=new InheritableThreadLocal<>();
        booleanThreadLocal.set(false);
        Log.i(TAG, "thread main: "+booleanThreadLocal.get());
        
        new Thread(new Runnable() {
            @Override
            public void run() {
                booleanThreadLocal.set(true);
                Log.i(TAG, "thread1 run: "+booleanThreadLocal.get());
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {

                Log.i(TAG, "thread2 run: "+booleanThreadLocal.get());
            }
        }).start();
```

```
05-15 15:06:55.460 3003-3003/? I/hhhandler: thread main: false
05-15 15:06:55.460 3003-3066/? I/hhhandler: thread1 run: true
05-15 15:06:55.460 3003-3067/? I/hhhandler: thread2 run: false
```
可以看到，同一个对像，在不同的线程中的到值是不一样的。

##### 源码分析
在 Thread 类中有两个变量，包含了 ThreadLocal 中的静态类 ThreadLocalMap，用于存储数据

```
/* ThreadLocal values pertaining to this thread. This map is maintained
 * by the ThreadLocal class. */
ThreadLocal.ThreadLocalMap threadLocals = null;
```


ThreadLocal 的部分源码:

```
private final int threadLocalHashCode = nextHashCode();

public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
    
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

```
ThreadLocalMap 是一个静态内部类，其 set 方法

```
private Entry[] table;

private void set(ThreadLocal<?> key, Object value) {
    // We don't use a fast path as with get() because it is at
    // least as common to use set() to create new entries as
    // it is to replace existing ones, in which case, a fast
    // path would fail more often than not.
    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len-1);
    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        ThreadLocal<?> k = e.get();
        if (k == key) {
            e.value = value;
            return;
        }
        if (k == null) {
            replaceStaleEntry(key, value, i);
            return;
        }
    }
    tab[i] = new Entry(key, value);
    int sz = ++size;
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
}
```

根据源码可看出，在静态内部类 ThreadLocalMap 中维持着一个数组 Entry[]，这个数组用于存储 ThreadLocal 和他的值，当我们对 ThreadLocal 操作时，实际上是对 ThreadLocalMap 进行操作，而这个 ThreadLocalMap 又是由 Thread 持有的，所以能保证所有线程对 ThreadLocal 的读写操作仅限于自己线程内部。

### 10.2.2 消息队列的工作原理

MessageQueue 通过一个单链表来维护消息队列，其主要包含两个操作：插入（queueMessage）和读取（next）。其中 next 方法是一个无限循环，如果队列中没有消息，next 会一直阻塞，如果有消息，next 方法会返回这条消息，并从队列中删除。

### 10.2.3 Lopper 的工作原理

Looper 的使用

```
  class LooperThread extends Thread {
      public Handler mHandler;

      public void run() {
          Looper.prepare();

          mHandler = new Handler() {
              public void handleMessage(Message msg) {
                  // process incoming messages here
              }
          };

          Looper.loop();
      }
  }
```

使用 prepare 方法创建 Looper；使用 loop 方法来启动消息循环；quit 可以直接退出 Looper ；quitSafely 可以添加一个退出标记，当处理完消息队列中的已有的消息后会再退出。getMainLooper 可以在任何时候获取到主线程的 Looper。在子线程中创建了 Looper 后一定要记得 quit。

loop 方法
```
public static void loop() {
    final Looper me = myLooper();
    if (me == null) {
        throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
    }
    final MessageQueue queue = me.mQueue;
    // Make sure the identity of this thread is that of the local process,
    // and keep track of what that identity token actually is.
    Binder.clearCallingIdentity();
    final long ident = Binder.clearCallingIdentity();
    for (;;) {
        Message msg = queue.next(); // might block
        if (msg == null) {
            // No message indicates that the message queue is quitting.
            return;
        }
        
        ...
        
        final long start = (slowDispatchThresholdMs == 0) ? 0 : SystemClock.uptimeMillis();
        final long end;
        try {
            msg.target.dispatchMessage(msg);
            end = (slowDispatchThresholdMs == 0) ? 0 : SystemClock.uptimeMillis();
        } finally {
            if (traceTag != 0) {
                Trace.traceEnd(traceTag);
            }
        }
        
        ...
        
        msg.recycleUnchecked();
    }
}
```

Looper 的 loop 方法中调用 MessageQueue 的 next 方法来获取消息。当没有消息时，next 方法会被阻塞，此时 loop 方法也会被阻塞；当消息被 quit 方法标记为退出状态时，next 会返回 null，此时会跳出循环退出；当有新消息时调用 msg.target.dispatchMessage(msg) 方法处理，msg.target 就是发送这条消息 Handler 对象，所以最后效果是在创建 Handler 的 Looper 中调用了 Handler 的 dispatchMessage 方法。

### 10.2.4 Handler 的工作原理

##### post 和 send 方法源码

```
    public final boolean post(Runnable r)
    {
       return  sendMessageDelayed(getPostMessage(r), 0);
    }
    
    public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
        MessageQueue queue = mQueue;
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        return enqueueMessage(queue, msg, uptimeMillis);
    }
    
    private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }

```
可以看出 post 方法也是使用 sendMessage 方法实现的，而 send 的一系列方法最终又都是由 sendMessageAtTime 方法实现的，在此方法中只是向 MessageQueue 中插入了一条消息。

##### dispatchMessage 方法源码

```

    public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }
    
```
当 msg 的 callback 不为空时执行 handleCallback 方法；然后检查 Handler 的 mCallback 是否为空，并执行其 handleMessage 方法；最后调用 Handler 自己的 handleMessage 方法进行处理。这三种情况分别对应一下三种 handler 的使用方式


```
    Handler handler1=new Handler();
    handler1.post(new Runnable() {
        @Override
        public void run() {

        }
    });

    Handler handler2=new Handler(new Handler.Callback() {
        @Override
        public boolean handleMessage(Message msg) {
            return false;
        }
    });

    Handler handler3=new Handler(){
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
        }
    };
```

## 10.3 主线程的消息循环

Android 主线程就是 ActivityThread ，其中入口方法 main 中创建了 Looper 和 MessageQueue 并开启了消息循环。其中有 ActivityThread.H extend Handler 用于处理四大组件的启动停止等过程。

AMS 完成 ActivityThread 的请求后，通过进程间通信回调 ApplicationThread 中的 Binder，然后 AppicationThread 会向 H 发送消息，H 把 AppicationThread 逻辑切换到 ActivityThread 执行。