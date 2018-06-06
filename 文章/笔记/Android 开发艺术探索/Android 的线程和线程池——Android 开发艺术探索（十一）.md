Android 中的线程：
1. AsyncTask，封装了线程池和 Handler ，方便在子线程中更新 UI；
2. HandlerThread，具有消息循环的线程；
3. IntentServer，使用 HandlerThread 来执行任务，利用 Server 的身份来提升其优先级，防止在后台运行时被回收。

线程池通过缓存一定量的线程，避免频繁的创建和回收。

## 11.1 主线程和子线程

Android 中主线程用于运行四大组件以及处理他们和用户的交互，为了保证交互流程，此线程中不允许做耗时操作。网络访问必须放在子线程。

## 11.2 Android 中的线程形态

### 11.2.1 AsyncTask

Asynctask 是一个轻量级的异步任务类，它可以在线程池中执行任务，然后把任务进度和最终结果传递给主线程供其更新 UI 。

Asynctask 有三个泛型参数，分别表示参数的类型、表示任务进度的类型和返回结果的类型。四个核心方法：

注意：
1. Asynctask 必须在主线程创建
2. execute 必须在主线程调用
3. 不要在程序中直接调用 onPreExecute、onPostExecute、doInBackground 和 onProgressUpdate 方法
4. 一个 Asynctask 对象只能执行一次
5. 从 Android 3.0 开始，Asynctask 采用一个线程来串行执行任务，可以调用 executeOnExecutor 来并行执行任务