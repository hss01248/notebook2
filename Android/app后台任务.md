# app后台任务

# 起因

直接开启一个子线程来跑任务,然后app切到后台,线程就是后台线程,一直执行吗?

no! 线程会被挂起,等app切换到前台,线程再恢复,任务继续执行.

# 怎么办?

有什么方法可以实现切到后台也继续执行任务?

比如后台播放

后台压缩文件/音视频



参考官方指南里后台定位权限的排查,有如下五种方法:

![image-20210201175320752](https://gitee.com/hss012489/picbed/raw/master/picgo/1612173206444-image-20210201175320752.jpg)

其中,

后台服务8.0后不可用

早期用：AlarmManager + BroadcastReceiver

后面使用: jobscheduler要Android5后才可用. 如果没有升级到Androidx,可以用一用.

目前官方推荐使用jetpack里的workmanager: 可以兼容到Android4.0 ,但是需要升级到Androidx.



## jobscheduler使用方式:



```xml
注册:
 <service android:name=".MyJobService"
            android:permission="android.permission.BIND_JOB_SERVICE"/>
```





```java
@RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
public class MyJobService extends JobService {
    static ExecutorService service;
    static Map<Integer,IDoInBackground> callbacks = new HashMap<>();

     static void setDoInBackground(int jobId, IDoInBackground doInBackground) {
        callbacks.put(jobId,doInBackground);
    }

    static IDoInBackground doInBackground;


    @Override
    public boolean onStartJob(final JobParameters params) {
        PersistableBundle bundle = params.getExtras();
        Log.d("job","MyJobService-onStartJob:"+bundle+", thread:"+Thread.currentThread().getName());
        if(callbacks.containsKey(params.getJobId())){
            callbacks.get(params.getJobId()).run(bundle);
            callbacks.remove(params.getJobId());
        }
        /*if(service == null){
            service = Executors.newSingleThreadExecutor();
        }
        service.execute(new Runnable() {
            @Override
            public void run() {
                if(callbacks.containsKey(params.getJobId())){
                    callbacks.get(params.getJobId()).run(bundle);
                    callbacks.remove(params.getJobId());
                }
            }
        });*/
        return true;
    }
    @Override
    public boolean onStopJob(JobParameters params) {
        Log.d("job","MyJobService-onStopJob:");
        return true;//返回false表示停止后不再重试执行
    }



    public interface IDoInBackground{
        void run(PersistableBundle bundle);
    }








    private static ComponentName mServiceComponent;
    private static  int mJobId;
    static JobScheduler mJobScheduler;



    public static void doInBg(Context context,PersistableBundle bundle,IDoInBackground doInBackground) {
        if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.LOLLIPOP) {
//根据JobService创建一个ComponentName对象
            if(mServiceComponent == null){
                mServiceComponent = new ComponentName(context, MyJobService.class);
            }
            int jobId = mJobId++;
            JobInfo.Builder builder = new JobInfo.Builder(jobId, mServiceComponent);
            //builder.setMinimumLatency(500);//设置延迟调度时间
            //builder.setOverrideDeadline(2000);//设置该Job截至时间，在截至时间前肯定会执行该Job
            builder.setRequiredNetworkType(JobInfo.NETWORK_TYPE_ANY);//设置所需网络类型
            builder.setRequiresDeviceIdle(false);//设置在DeviceIdle时执行Job
            builder.setRequiresCharging(false);//设置在充电时执行Job
           /* PersistableBundle bundle = new PersistableBundle();
            bundle.putString("dir",dir.getAbsolutePath());
            bundle.putString("fileName",fileName);*/
            builder.setExtras(bundle);//设置一个额外的附加项

            JobInfo  mJobInfo = builder.build();

            if(mJobScheduler == null){
                mJobScheduler = (JobScheduler) context.getSystemService(Context.JOB_SCHEDULER_SERVICE);
            }
            MyJobService.setDoInBackground(jobId,doInBackground);

            mJobScheduler.schedule(mJobInfo);//调度Job
            /*mBuilder = new JobInfo.Builder(id,new ComponentName(this, MyJobService.class));

            JobInfo  mJobInfo = mBuilder.build();
            mJobScheduler.schedule(builder.build());//调度Job
            mJobScheduler.cancel(jobId);//取消特定Job
            mJobScheduler.cancelAll();//取消应用所有的Job*/
        }

    }
}
```



```java
// 调用:
 if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.LOLLIPOP) {
            PersistableBundle  bundle = new PersistableBundle();
            bundle.putString("realPath",realPath);
   
            MyJobService.doInBg(getApplicationContext(), bundle, new         MyJobService.IDoInBackground() {
                @Override
                public void run(PersistableBundle bundle) {
                  
                    VideoCompressUtil.doCompressAsync(bundle.getString("realPath"), "", mode,
                            new DefaultDialogCompressListener2(SimpleActivity.this,
                                    new ICompressListener() {
                                        @Override
                                        public void onFinish(String outputFilePath) {

                                        }

                                        @Override
                                        public void onError(String message) {

                                        }
                                    }));
                }
            });
        }
```





# workManager:

直接看官方文档即可:

https://developer.android.com/topic/libraries/architecture/workmanager?hl=zh-cn



![image-20210202202557680](https://gitee.com/hss012489/picbed/raw/master/picgo/1612268757786-image-20210202202557680.jpg)

参考[WorkManager的基本使用](https://zhuanlan.zhihu.com/p/78599394)的总结如下:

**总结**

开发者经常需要处理后台任务，如果处理后台任务所采用的API没有被正确使用，那么很可能会消耗大量设备的电量。Android出于设备电量的考虑，为开发者提供了WorkManager，旨在将一些不需要及时完成的任务交给它来完成。虽然WorkManager宣称，能够保证任务得到执行，但我在真实设备中，发现应用程序彻底退出与重启设备，任务都没有再次执行。查阅了相关资料，发现这应该与系统有关系。我们前面也提到了，WorkManager会根据系统的版本，决定采用JobScheduler或是AlarmManager+Broadcast Receivers来完成任务。但是这些API很可能会受到OEM系统的影响。比如，假设某个系统不允许AlarmManager自动唤起，那么WorkManager很可能就无法正常使用。

而后我在模拟器中进行测试，模拟器采用的是Google原生系统，发现无论是彻底退出应用程序，或是重启设备，任务都能够被执行。所以，WorkManager在真实设备中不能正常使用，很可能就是系统的问题。因此，开发者在使用WorkManager作为解决方案时，一定要慎重。

另外，我还发现，周期任务的实际执行，与所设定的时间差别较大。执行时间看起来并没有太明显的规律。并且在任务执行完成后，WorkInfo并不会收到Success的通知。查阅了相关资料，发现Android认为Success和Failure都属于终止类的通知。意思是，如果发出这类通知，则表明任务彻底结束，而周期任务不会彻底终止，会一直执行下去，所以我们在使用LiveData观察周期任务时，不会收到Success这类的通知。这也是我们需要注意的地方。



## 问题: 半天不执行

![image-20210202205653445](https://gitee.com/hss012489/picbed/raw/master/picgo/1612270613490-image-20210202205653445.jpg)



> 如何让其立刻执行?或者保证尽快执行?

有两个方案

* 在当前线程执行,不要切换线程,不要创建子线程: 在后台新开启的子线程会出于停滞状态
* 用handler抛到主线程执行