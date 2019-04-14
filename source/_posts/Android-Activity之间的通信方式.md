---
title: Android Activity之间的通信方式
date: 2019-04-14 11:37:59
tags: Android
---

### Activity之间通信方式

+ Intent
+ 借助类的静态变量
+ 借助全局变量/Application
+ 借助外部工具
 + 借助SharedPreference 
 + 使用Android数据库SQLite
 + 赤裸裸的使用File
 + Android剪切板

#### Intent
借助四大组件（Activity、Service、BroadcastReceiver、ContentProvider），通过Intent传递数据，进行Activity间通信。
1. Activity跳转传递Intent
2. 广播传递Intent
3. ContentProvider保存数据，B获取数据。
4. Service

#### 借助类的静态变量
B中定义静态变量，A中设置B中的静态变量，B中就能获取到A设置的静态变量，当然该静态变量可以设置在任意地方。

#### 借助全局变量来实现/Application
一般可以把变量放在Applicaiton这个全局的变量中，通过获取Applicaiton来设置和获取数据变量。

也可以设置单例模式。道理同Applicaiton

#### 借助外部工具
1. sp存储
2. 数据库存储
3. 文件存储
4. Android剪贴板等存储方式

### Activity和Service间通信
+ startService
+ bindService
+ 回调接口
+ broadcast(广播)

#### startService
单向的，只能Activity向Service传递数据，该service不与Activity绑定。

#### bindService
双向传递数据，通过bindService向Service传递数据，通过ServiceConnection向Activity传递数据。

#### 回调接口
```java 
public interface OnProgressListener ｛
    void onProgress(int progress);
｝
```

Service 代码如下：
```java
public class MsgService extends Service ｛
    /**
     * 进度条的最大值
     */
    public static final int MAX_PROGRESS = 100;
    /**
     * 进度条的进度值
     */
    private int progress = 0;
    /**
     * 更新进度的回调接口
     */
    private OnProgressListener onProgressListener;
    /**
     * 注册回调接口的方法，供外部调用
     * @param onProgressListener
     */
    public void setOnProgressListener(OnProgressListener onProgressListener) ｛
        this.onProgressListener = onProgressListener;
    ｝
    /**
     * 增加get()方法，供Activity调用
     * @return 下载进度
     */
    public int getProgress() ｛
        return progress;
    ｝
    /**
     * 模拟下载任务，每秒钟更新一次
     */
    public void startDownLoad()｛
        new Thread(new Runnable() ｛
            @Override
            public void run() ｛
                while(progress < MAX_PROGRESS)｛
                    progress += 5;
                    //进度发生变化通知调用方
                    if(onProgressListener != null)｛
                        onProgressListener.onProgress(progress);
                    ｝
                    try ｛
                        Thread.sleep(1000);
                    ｝ catch (InterruptedException e) ｛
                        e.printStackTrace();
                    ｝
                ｝
            ｝
        ｝).start();
    ｝
    /**
     * 返回一个Binder对象
     */
    @Override
    public IBinder onBind(Intent intent) ｛
        return new MsgBinder();
    ｝
    public class MsgBinder extends Binder｛
        /**
         * 获取当前Service的实例
         * @return
         */
        public MsgService getService()｛
            return MsgService.this;
        ｝
    ｝
｝
```


Activity代码如下：

```java
public class MainActivity extends Activity ｛
    private MsgService msgService;
    private ProgressBar mProgressBar;
    @Override
    protected void onCreate(Bundle savedInstanceState) ｛
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //绑定Service
        Intent intent = new Intent("com.example.communication.MSG_ACTION");
        bindService(intent, conn, Context.BIND_AUTO_CREATE);
        mProgressBar = (ProgressBar) findViewById(R.id.progressBar1);
        Button mButton = (Button) findViewById(R.id.button1);
        mButton.setOnClickListener(new OnClickListener() ｛
            @Override
            public void onClick(View v) ｛
                //开始下载
                msgService.startDownLoad();
            ｝
        ｝);
    ｝
    ServiceConnection conn = new ServiceConnection() ｛
        @Override
        public void onServiceDisconnected(ComponentName name) ｛
        ｝
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) ｛
            //返回一个MsgService对象
            msgService = ((MsgService.MsgBinder)service).getService();
            //注册回调接口来接收下载进度的变化
            msgService.setOnProgressListener(new OnProgressListener() ｛
                @Override
                public void onProgress(int progress) ｛
                    mProgressBar.setProgress(progress);
                ｝
            ｝);
        ｝
    ｝;
    @Override
    protected void onDestroy() ｛
        unbindService(conn);
        super.onDestroy();
    ｝
｝
```

#### 通过broadcast(广播)的形式
Activity注册广播，Service发送广播给Activity接收。

### Activity和Fragment间的通信
+ 回调接口
+ getActivity（）
+ 广播
+ handler





