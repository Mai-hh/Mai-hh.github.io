# Broadcast和Service知识整理

## 1. 广播Broadcast

* 广播来源：系统、程序
* 广播分类：标准广播（发散型发送）、有序广播（链表型发送）

### 1.1 广播的监听：BroadcastReceiver的注册

* 创建BroadcastReceiver的继承类

  ~~~java
  public class BootCompleteReceiver extends BroadcastReceiver {
      //接收到对应广播时执行
      @Override
      public void onReceive(Context context, Intent intent) {
      }
  }
  ~~~

* 动态注册（在程序运行时动态注册）

  java代码实现，利用隐式Intent

* 静态注册（无需运行，程序安装后就注册）

  在AndroidManifest.xml中注册控件

  ~~~xml
  <!--声明权限，可以拿到开机广播-->        
  <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
  <!--指定静态注册的类名-->   
  <receiver
            android:name=".BootCompleteReceiver"
            android:enabled="true"
            android:exported="true">
      <intent-filter>
          <action android:name="android.intent.action.BOOT_COMPLETED"/>
      </intent-filter>
  </receiver>
  ~~~

### 1.2 广播的发送

* 发送标准广播

  ~~~java
  Intent intent = new Intent("com.maihao.broadcast.MY_BROADCAST");
  //发送这个标准广播（类比启动Activity）
  sendBroadcast(intent);
  ~~~

* 发送有序广播

  ~~~java
  Intent intent = new Intent("com.maihao.broadcast.MY_BROADCAST");
  sendOrderedBroadcast(intent, null);
  ~~~

  有序广播特性：

  1. 优先级：动态接收器利用IntentFilter的方法，静态接收器直接设置priority属性
  2. 阻断：一个接收器调用abortBroadcast()方法后则阻断广播

* 本地广播

  主要使用LocalBroadcastManager类下的方法

  只能动态注册（本地广播不能，也不需要静态注册）

  ~~~java
  //类比本地发送，取消注册的方法都是LocalBroadcastManager下的版本
  localBroadcastManager.registerReceiver(localReceiver, intentFilter)
  ~~~



## 2. Android 中的多线程

在Android中利用Java的子线程刷新UI是不被允许的，但是这种接收耗时任务刷新UI的需求是存在的

### 2.1 异步消息处理

* Message：线程之间传递的消息，可以携带一个what字段，两个arg字段携带整形数据，和一个obj字段携带一个对象
* Handler：sendMessage()发送信息，handleMessage()处理信息
* MessageQueue：存放子线程发回来的Message队列
* Looper：管理消息队列，loop()取出消息队列的消息给handleMessage()

### 2.2 AsyncTask解决方案

~~~java
class task extends AsyncTask<Void, Void, Void> {
    //三个泛型分别是，传入的参数，DoInBackground返回进度的参数，onProgreeUpdate返回的结果参数
    //初始化数据
    @Override
    protected void onPreExecute() {
        super.onPreExecute();
    }
    //耗时操作，return值会传给onProgressUpdate方法，过程值需要使用publishProgress(progress)方法来提交给onProgressUpdate
    @Override
    protected Void doInBackground(Void... voids) {
        return null;
    }
    //处理结果操作，更新UI等操作放在这里进行
    @Override
    protected void onProgressUpdate(Void... values) {
        super.onProgressUpdate(values);
    }
    //线程执行完毕的收尾操作
    @Override
    protected void onPostExecute(Void unused) {
        super.onPostExecute(unused);
    }
}
//执行异步线程
new task().execute();
~~~

## 3. 服务Service

服务也有自己的生命周期，程序利用Binder接口访问并操作服务

* 启动方式（和其他几大组件类似）

  1. 其他组件调用Context的 **startService()** 方法可以启动一个Service，并回调服务中的**onStartCommand()**。如果该服务之前还没创建，那么回调的顺序是**onCreate()->onStartCommand()**。服务启动了之后会一直保持运行状态，直到**stopService()或stopSelf()**方法被调用，服务停止并回调**onDestroy()**。

  2. 其它组件调用Context的**bindService()**可以绑定一个Service，并回调服务中的**onBind()**方法。类似地，如果该服务之前还没创建，那么回调的顺序是**onCreate()->onBind()**。之后，调用方可以获取到**onBind()**方法里返回的IBinder对象的实例，从而实现和服务进行通信。只要调用方和服务之间的连接没有断开，服务就会一直保持运行状态，直到调用了**unbindService()**方法服务会停止，回调顺序**onUnBind()->onDestroy()**。

  ps：这两种启动方法并不冲突，当使用startService()启动Service之后，还可再使用bindService()绑定，只不过需要同时调用 stopService()和 unbindService()方法才能让服务销毁掉。

* 普通服务：优先级低，易被回收

* 前台服务：

  1. 跟随程序运行而一直运行，程序销毁才销毁
  2. startForegroundService(intent)启动，5秒之内开启通知栏的提示

* **绑定Service的方法**

  1. 扩展 Binder 类：Service仅供本地应用使用，且无需跨进程工作，在
  2. 使用 Messenger：服务与远程进程通信，则可使用 Messenger 为Service提供接口
  3. AIDL：跨客户端

