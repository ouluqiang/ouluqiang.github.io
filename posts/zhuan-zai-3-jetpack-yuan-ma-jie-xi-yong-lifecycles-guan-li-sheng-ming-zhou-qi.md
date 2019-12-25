---
title: '[转载] 3. Jetpack源码解析---用Lifecycles管理生命周期'
date: 2019-10-15 17:09:17
tags: [转载]
published: true
hideInList: false
feature: 
---
[](http://creativecommons.org/licenses/by-sa/4.0/)版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。

本文链接：[https://blog.csdn.net/lyhhj/article/details/91440191](https://blog.csdn.net/lyhhj/article/details/91440191)

<!-- more -->

1\. 背景
======

> 上一篇我门对Jetpack组件中的`Navigation`做了介绍，并且对其做了源码分析，相信看过之后已经对此有了一定的了解，本篇文章我们会对`Lifecycles`进行使用及源码的介绍，还没看上篇的可以看一下：

**系列文章**：

[1\. Jetpack源码解析—看完你就知道Navigation是什么了？](https://blog.csdn.net/lyhhj/article/details/93757755)

[2\. Jetpack源码解析—Navigation为什么切换Fragment会重绘？](https://blog.csdn.net/lyhhj/article/details/94003014)

2\. 基础
======

  

2.1 简介
------

`Lifecycles`是一个持有组件生命周期状态（如Activity、Fragment）信息的类，并允许其他对象观察此状态。可以帮助我们方便的管理`Activity`和`Fragment`的生命周期。

`Lifecycle组件`中通过两个枚举类来跟踪其关联组件的生命周期：  
  
![](https://img-blog.csdn.net/20180610101922250?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsZXh3bGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70#align=left&display=inline&height=381&originHeight=381&originWidth=700&status=done&width=700)

  

2.2 基本使用
--------

> 在我们的日常开发中，经常需要我们在Activity或者Fragment的生命周期方法中手动去管理一下资源的释放等行为。举个简单的例子，当我们做自定义相机扫描的时候，camera相关的资源就需要我们手动的去释放及开启预览；或者我们在使用MVP模式去开发的时候，P的创建和销毁也需要我们在生命周期方法中去操作。

通过`Lifecycles组件`我们可以这样使用：  
  
我们可以定义一个Observer实现`LifecycleObserver`，并且在`Activity`或者`Fragment`中进行观察：

    /**
     * created by ${Hankkin}
     * on 2019-06-10
     */
    
    class MyObserver : LifecycleObserver {
    
        @OnLifecycleEvent(Lifecycle.Event.ON_START)
        fun onStart() {
            Log.e(javaClass.name, "-------onStart")
        }
    
        @OnLifecycleEvent(Lifecycle.Event.ON_CREATE)
        fun onCreate() {
            Log.e(javaClass.name, "-------onCreate")
        }
    
        @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
        fun onResume() {
            Log.e(javaClass.name, "-------onResume")
        }
    
        @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
        fun onPause() {
            Log.e(javaClass.name, "-------onPause")
        }
    
    
        @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
        fun onStop() {
            Log.e(javaClass.name, "-------onStop")
        }
    
    
        @OnLifecycleEvent(Lifecycle.Event.ON_DESTROY)
        fun onDestroy() {
            Log.e(javaClass.name, "-------onDestroy")
        }
    
    }
    

下面是Activity：

    class MainActivity : AppCompatActivity() {
        
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
    
            lifecycle.addObserver(MyObserver())
            Log.e(javaClass.name, "-------onCreate")
    
        }
    
        override fun onStart() {
            super.onStart()
            Log.e(javaClass.name, "-------onStart")
        }
    
        override fun onResume() {
            super.onResume()
            Log.e(javaClass.name, "-------onResume")
        }
    
        override fun onPause() {
            super.onPause()
            Log.e(javaClass.name, "-------onPause")
        }
    
        override fun onStop() {
            super.onStop()
            Log.e(javaClass.name, "-------onStop")
        }
    
        override fun onDestroy() {
            super.onDestroy()
            Log.e(javaClass.name, "-------onDestroy")
        }
    }
    

启动Activity，我们可以看到控制台中的打印日志：

    MainActivity: -------onCreate
    MyObserver: -------onCreate
    MainActivity: -------onStart
    MyObserver: -------onStart
    MainActivity: -------onResume
    MyObserver: -------onResume
    ......
    

通过控制台打印我们可以看到我们的观察者Activity和被观察者的日志均被打印了。具体是怎么实现的呢？

  

3\. 源码分析
========

通过代码我们大概能看出来`Lifecycles`组件是通过观察者模式来实现的，接下来我们具体分析实现原理，我们发现在`Activity`和`Fragment`中可以直接通过`getLifecycle()`方法获取到`Lifecycle`，那么我们就从这里入手:

  

3.1 getLifecycle()
------------------

我们点击进去发现**ComponentActivity**中实现了`LifecycleOwner`接口，`LifecycleOwner`接口中则声明了`getLifecycle()`方法，而**ComponentActivity**中直接返回了**mLifecycleRegistry**：

    public class ComponentActivity extends androidx.core.app.ComponentActivity implements
            LifecycleOwner,
            ViewModelStoreOwner,
            SavedStateRegistryOwner,
            OnBackPressedDispatcherOwner {
    
        private final LifecycleRegistry mLifecycleRegistry = new LifecycleRegistry(this);
        ......
        @NonNull
        @Override
        public Lifecycle getLifecycle() {
            return mLifecycleRegistry;
        }
    

`LifecycleRegistry`是个什么东西呢？原来它继承了**Lifecycle**

    public class LifecycleRegistry extends Lifecycle
    

  

3.2 Lifecycle和LifecycleRegistry
-------------------------------

我们看一下Lifecycle类：

    public abstract class Lifecycle {
    
            //注册LifecycleObserver （比如Presenter）
            public abstract void addObserver(@NonNull LifecycleObserver observer);
            //移除LifecycleObserver 
            public abstract void removeObserver(@NonNull LifecycleObserver observer);
            //获取当前状态
            public abstract State getCurrentState();
    
            public enum Event {
                ON_CREATE,
                ON_START,
                ON_RESUME,
                ON_PAUSE,
                ON_STOP,
                ON_DESTROY,
                ON_ANY
            }
            
           public enum State {
                DESTROYED,
                INITIALIZED,
                CREATED,
                STARTED,
                RESUMED;
    
                public boolean isAtLeast(@NonNull State state) {
                    return compareTo(state) >= 0;
                }
           }
    }
    

**Lifecycle**中就是声明了一些抽象方法和两个状态的枚举类，具体的实现看**LifecycleRegistry**：

    public class LifecycleRegistry extends Lifecycle {
    
    // LifecycleObserver Map，每一个Observer都有一个State
        private FastSafeIterableMap<LifecycleObserver, ObserverWithState> mObserverMap = new FastSafeIterableMap<>();
        // 当前的状态
        private State mState;
        // 生命周期拥有者，ComponentActivity继承了LifecycleOwner
        private final WeakReference<LifecycleOwner> mLifecycleOwner;
    
        //修改State值
        private void moveToState(State next) {
            if (mState == next) {
                return;
            }
            mState = next;
            if (mHandlingEvent || mAddingObserverCounter != 0) {
                mNewEventOccurred = true;
                // we will figure out what to do on upper level.
                return;
            }
            mHandlingEvent = true;
            sync();
            mHandlingEvent = false;
        }
    
        /**
        * 添加LifecycleObserver观察者，并将之前的状态分发给这个Observer,例如我们在onResume之后注册这个Observer，
        * 该Observer依然能收到ON_CREATE事件
        */
        @Override
        public void addObserver(@NonNull LifecycleObserver observer) {
            State initialState = mState == DESTROYED ? DESTROYED : INITIALIZED;
            //新建带有状态的观察者
            ObserverWithState statefulObserver = new ObserverWithState(observer, initialState);
            ObserverWithState previous = mObserverMap.putIfAbsent(observer, statefulObserver);
            ......
            // 例如：Observer初始状态是INITIALIZED，当前状态是RESUMED，需要将INITIALIZED到RESUMED之间的
            // 所有事件分发给Observer
            while ((statefulObserver.mState.compareTo(targetState) < 0
                    && mObserverMap.contains(observer))) {
                pushParentState(statefulObserver.mState);
                statefulObserver.dispatchEvent(lifecycleOwner, upEvent(statefulObserver.mState));
                popParentState();
                targetState = calculateTargetState(observer);
            }
            ......
    }
    
    /**
         * 同步Observer状态，并分发事件
         */
        private void sync() {
            LifecycleOwner lfecycleOwner = mLifecycleOwner.get();
            if (lifecycleOwner == null) {
                Log.w(LOG_TAG, "LifecycleOwner is garbage collected, you shouldn't try dispatch "
                        + "new events from it.");
                return;
            }
            while (!isSynced()) {
                mNewEventOccurred = false;
                // State中，状态值是从DESTROYED-INITIALIZED-CREATED-STARTED-RESUMED增大
                // 如果当前状态值 < Observer状态值，需要通知Observer减小状态值，直到等于当前状态值
                if (mState.compareTo(mObserverMap.eldest().getValue().mState) < 0) {
                    backwardPass(lifecycleOwner);
                }
                Entry<LifecycleObserver, ObserverWithState> newest = mObserverMap.newest();
                // 如果当前状态值 > Observer状态值，需要通知Observer增大状态值，直到等于当前状态值
                if (!mNewEventOccurred && newest != null
                        && mState.compareTo(newest.getValue().mState) > 0) {
                    forwardPass(lifecycleOwner);
                }
            }
            mNewEventOccurred = false;
        }
    
        /**
         * 向前传递事件，对应图中的INITIALIZED -> RESUMED
         * 增加Observer的状态值，直到状态值等于当前状态值
         */
        private void forwardPass(LifecycleOwner lifecycleOwner) {
            Iterator<Entry<LifecycleObserver, ObserverWithState>> ascendingIterator =
                    mObserverMap.iteratorWithAdditions();
            while (ascendingIterator.hasNext() && !mNewEventOccurred) {
                Entry<LifecycleObserver, ObserverWithState> entry = ascendingIterator.next();
                ObserverWithState observer = entry.getValue();
                while ((observer.mState.compareTo(mState) < 0 && !mNewEventOccurred
                        && mObserverMap.contains(entry.getKey()))) {
                    pushParentState(observer.mState);
                    // 分发状态改变事件
                    observer.dispatchEvent(lifecycleOwner, upEvent(observer.mState));
                    popParentState();
                }
            }
        }
    
        /**
         * 向后传递事件，对应图中的RESUMED -> DESTROYED
         * 减小Observer的状态值，直到状态值等于当前状态值
         */
        private void backwardPass(LifecycleOwner lifecycleOwner) {
            Iterator<Entry<LifecycleObserver, ObserverWithState>> descendingIterator =
                    mObserverMap.descendingIterator();
            while (descendingIterator.hasNext() && !mNewEventOccurred) {
                Entry<LifecycleObserver, ObserverWithState> entry = descendingIterator.next();
                ObserverWithState observer = entry.getValue();
                while ((observer.mState.compareTo(mState) > 0 && !mNewEventOccurred
                        && mObserverMap.contains(entry.getKey()))) {
                    Event event = downEvent(observer.mState);
                    // 分发状态改变事件
                    pushParentState(getStateAfter(event));
                    observer.dispatchEvent(lifecycleOwner, event);
                    popParentState();
                }
            }
        }
    

`LifecycleRegistry`代码我门看完了，注释也已经很清楚了，基本作用就是添加观察者，响应生命周期事件，分发生命周期事件的作用。

  

3.3 ReportFragment
------------------

接下来我们继续分析`ComponentActivity`，我们在`onCreate()`声明周期中发现一个比较熟悉`ReportFragment`，这个fragment我以前在做内存泄漏优化的时候经常碰到，在leakcanary中经常会报出这个类，所以这里面看看`ReportFragment.injectIfNeededIn(this);`到底做了什么？

    @RestrictTo(RestrictTo.Scope.LIBRARY_GROUP_PREFIX)
    public class ReportFragment extends Fragment {
        private static final String REPORT_FRAGMENT_TAG = "androidx.lifecycle"
                + ".LifecycleDispatcher.report_fragment_tag";
    
        public static void injectIfNeededIn(Activity activity) {
            // ProcessLifecycleOwner should always correctly work and some activities may not extend
            // FragmentActivity from support lib, so we use framework fragments for activities
            android.app.FragmentManager manager = activity.getFragmentManager();
            if (manager.findFragmentByTag(REPORT_FRAGMENT_TAG) == null) {
                manager.beginTransaction().add(new ReportFragment(), REPORT_FRAGMENT_TAG).commit();
                // Hopefully, we are the first to make a transaction.
                manager.executePendingTransactions();
            }
        }
        private void dispatchCreate(ActivityInitializationListener listener) {
            if (listener != null) {
                listener.onCreate();
            }
        }
    
        private void dispatchStart(ActivityInitializationListener listener) {
            if (listener != null) {
                listener.onStart();
            }
        }
    
        private void dispatchResume(ActivityInitializationListener listener) {
            if (listener != null) {
                listener.onResume();
            }
        }
    
        @Override
        public void onActivityCreated(Bundle savedInstanceState) {
            super.onActivityCreated(savedInstanceState);
            dispatchCreate(mProcessListener);
            dispatch(Lifecycle.Event.ON_CREATE);
        }
    
        @Override
        public void onStart() {
            super.onStart();
            dispatchStart(mProcessListener);
            dispatch(Lifecycle.Event.ON_START);
        }
    
        @Override
        public void onResume() {
            super.onResume();
            dispatchResume(mProcessListener);
            dispatch(Lifecycle.Event.ON_RESUME);
        }
    
        @Override
        public void onPause() {
            super.onPause();
            dispatch(Lifecycle.Event.ON_PAUSE);
        }
    
        @Override
        public void onStop() {
            super.onStop();
            dispatch(Lifecycle.Event.ON_STOP);
        }
    
        @Override
        public void onDestroy() {
            super.onDestroy();
            dispatch(Lifecycle.Event.ON_DESTROY);
            // just want to be sure that we won't leak reference to an activity
            mProcessListener = null;
        }
    
        private void dispatch(Lifecycle.Event event) {
            Activity activity = getActivity();
            if (activity instanceof LifecycleRegistryOwner) {
                ((LifecycleRegistryOwner) activity).getLifecycle().handleLifecycleEvent(event);
                return;
            }
    
            if (activity instanceof LifecycleOwner) {
                Lifecycle lifecycle = ((LifecycleOwner) activity).getLifecycle();
                if (lifecycle instanceof LifecycleRegistry) {
                    ((LifecycleRegistry) lifecycle).handleLifecycleEvent(event);
                }
            }
        }
    

查看源码我们发现，`ReportFragment`在每个生命周期中都做了分发事件的处理，通过调用`getLifecycle().handleLifecycleEvent(event)`来做生命周期的分发。意思也就是在`ComponentActivity`中添加了一个没有页面的`ReportFragment`，当Activity生命周期变化的时候，通过调用`LifecycleRegistry.handleLifecycleEvent()`方法通知LifecycleRegistry改变状态，LifecycleRegistry内部调用`moveToState()`改变状态，并调用每个LifecycleObserver.onStateChange()方法通知生命周期变化。

> 我们通过查看ReportFragment调用，发现还有两个类也调用了它，一个是`LifecycleDispatcher`，一个是`ProcessLifecycleOwner`，这两个究竟是做什么的呢？

\[外链图片转存失败(img-Kmu4o9EL-1562754821909)([http://psv0rasgi.bkt.clouddn.com/jetpack/_image/2019-06-10/屏幕快照 2019-06-11 下午2.19.20 (2).png#align=left&display=inline&height=324&originHeight=648&originWidth=1112&status=done&width=556](http://psv0rasgi.bkt.clouddn.com/jetpack/_image/2019-06-10/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-06-11%20%E4%B8%8B%E5%8D%882.19.20%20%282%29.png#align=left&display=inline&height=324&originHeight=648&originWidth=1112&status=done&width=556))\]

  

3.4 LifecycleDispatcher
-----------------------

    class LifecycleDispatcher {
    
        private static AtomicBoolean sInitialized = new AtomicBoolean(false);
    
        static void init(Context context) {
            if (sInitialized.getAndSet(true)) {
                return;
            }
            ((Application) context.getApplicationContext())
                    .registerActivityLifecycleCallbacks(new DispatcherActivityCallback());
        }
    
        @SuppressWarnings("WeakerAccess")
        @VisibleForTesting
        static class DispatcherActivityCallback extends EmptyActivityLifecycleCallbacks {
    
            @Override
            public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
                ReportFragment.injectIfNeededIn(activity);
            }
    
            @Override
            public void onActivityStopped(Activity activity) {
            }
    
            @Override
            public void onActivitySaveInstanceState(Activity activity, Bundle outState) {
            }
        }
    
        private LifecycleDispatcher() {
        }
    }
    

我们查看源码，发现在`init()`方法中，通过`Application`注册了`DispatcherActivityCallback`，并且在`onActivityCreated`中将`ReportFragment`注入到Activity中。

  

3.5 ProcessLifecycleOwner
-------------------------

    public class ProcessLifecycleOwner implements LifecycleOwner {
    
           private final LifecycleRegistry mRegistry = new LifecycleRegistry(this);
    
        private Runnable mDelayedPauseRunnable = new Runnable() {
            @Override
            public void run() {
                dispatchPauseIfNeeded();
                dispatchStopIfNeeded();
            }
        };
    
        private ActivityInitializationListener mInitializationListener =
                new ActivityInitializationListener() {
                    @Override
                    public void onCreate() {
                    }
    
                    @Override
                    public void onStart() {
                        activityStarted();
                    }
    
                    @Override
                    public void onResume() {
                        activityResumed();
                    }
                };
    
        private static final ProcessLifecycleOwner sInstance = new ProcessLifecycleOwner();
    
       
        public static LifecycleOwner get() {
            return sInstance;
        }
    
        static void init(Context context) {
            sInstance.attach(context);
        }
    
        void activityStarted() {
            mStartedCounter++;
            if (mStartedCounter == 1 && mStopSent) {
                mRegistry.handleLifecycleEvent(Lifecycle.Event.ON_START);
                mStopSent = false;
            }
        }
    
        void activityResumed() {
            mResumedCounter++;
            if (mResumedCounter == 1) {
                if (mPauseSent) {
                    mRegistry.handleLifecycleEvent(Lifecycle.Event.ON_RESUME);
                    mPauseSent = false;
                } else {
                    mHandler.removeCallbacks(mDelayedPauseRunnable);
                }
            }
        }
    
       ......
        private void dispatchStopIfNeeded() {
            if (mStartedCounter == 0 && mPauseSent) {
                mRegistry.handleLifecycleEvent(Lifecycle.Event.ON_STOP);
                mStopSent = true;
            }
        }
    
        private ProcessLifecycleOwner() {
        }
        //监听Application生命周期，并分发给Activity
        void attach(Context context) {
            mHandler = new Handler();
            mRegistry.handleLifecycleEvent(Lifecycle.Event.ON_CREATE);
            Application app = (Application) context.getApplicationContext();
            app.registerActivityLifecycleCallbacks(new EmptyActivityLifecycleCallbacks() {
                @Override
                public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
                    ReportFragment.get(activity).setProcessListener(mInitializationListener);
                }
    
                @Override
                public void onActivityPaused(Activity activity) {
                    activityPaused();
                }
    
                @Override
                public void onActivityStopped(Activity activity) {
                    activityStopped();
                }
            });
        }
    }
    

根据官方注释，我们可以了解到：

*   ProcessLifecycleOwner是用来监听Application生命周期的，因此它只会分发一次ON\_CREATE事件，并且不会分发ON\_DESTROY事件。
*   ProcessLifecycleOwner在Activity的onResume和onStop方法中都采用了Handle.postDelayed()方法，是为了处理Activity重建时比如横竖屏幕切换时，不会发送事件。
*   ProcessLifecycleOwner一般用来判断应用是在前台还是后台。但由于使用了Handle.postDelayed()，因此这个判断不是即时的，有默认700ms的延迟。
*   ProcessLifecycleOwner与LifecycleDispatcher一样，都是通过注册Application.registerActivityLifecycleCallbacks来监听Activity的生命周期回调，来给每个Activity添加ReportFragment的。

看了着两个类我们发现它们的入口均为`init()`,所以看一下谁调用了？

  

3.6 ProcessLifecycleOwnerInitializer
------------------------------------

    public class ProcessLifecycleOwnerInitializer extends ContentProvider {
        @Override
        public boolean onCreate() {
            LifecycleDispatcher.init(getContext());
            ProcessLifecycleOwner.init(getContext());
            return true;
        }
    
        @Nullable
        @Override
        public Cursor query(@NonNull Uri uri, String[] strings, String s, String[] strings1,
                String s1) {
            return null;
        }
    
        @Nullable
        @Override
        public String getType(@NonNull Uri uri) {
            return null;
        }
    
        @Nullable
        @Override
        public Uri insert(@NonNull Uri uri, ContentValues contentValues) {
            return null;
        }
    
        @Override
        public int delete(@NonNull Uri uri, String s, String[] strings) {
            return 0;
        }
    
        @Override
        public int update(@NonNull Uri uri, ContentValues contentValues, String s, String[] strings) {
            return 0;
        }
    }
    

果真，在`ProcessLifecycleOwnerInitializer` 的`onCreate()`中对这两个进行了初始化，看类名可以翻译成_进程生命周期初始化_，到这里我们对该类就找不到调用者或者使用者了，所以不得不百度一下，发现有人说这个类是在`AndroidManifest.xml`中声明的，在构建APK过程中，AS会将多个模块的`AndroidManifest.xml`合并到一起，所以查看build目录，具体路径为`build/intermediates/bundle_manifest/debug/processDebugManifest/bundle-manifest/AndroidManifest.xml`，果真在里面：

    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.hankkin.reading_aac"
        android:versionCode="1"
        android:versionName="1.0" >
    
        <uses-sdk
            android:minSdkVersion="19"
            android:targetSdkVersion="28" />
    
        <application
            android:appComponentFactory="androidx.core.app.CoreComponentFactory"
            android:debuggable="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:roundIcon="@mipmap/ic_launcher_round"
            android:supportsRtl="true"
            android:testOnly="true"
            android:theme="@style/AppTheme" >
            <activity android:name="com.hankkin.reading_aac.ui.LoginActivity" >
            </activity>
            <activity android:name="com.hankkin.reading_aac.MainActivity" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
    
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
    
            <provider
                android:name="androidx.lifecycle.ProcessLifecycleOwnerInitializer"
                android:authorities="com.hankkin.reading_aac.lifecycle-process"
                android:exported="false"
                android:multiprocess="true" />
        </application>
    
    </manifest>
    

到这里整个Lifecycle初始化的过程就结束了。

  

4\. 总结
======

经过上面的源码分析，我们可以大概给整个**Lifecycle组件**分为三部分：

  

4.1 Lifecycle的初始化
-----------------

    通过在manifest中声明provider，`ProcessLifecycleOwnerInitializer`注册Activity和fragment.的声明周期回调并做监听，同时向Activity中添加一个空白的`ReportFragment`，使用它作为生命周期的事件分发。而当Activity和Fragment生命周期状态发生改变时，都通过LifecycleRegistryOwner来处理生命周期状态的改变。
    

  

4.2 Lifecycle的状态改变及状态分发
-----------------------

    在`ReportFragment`中调用`LifecycleRegister.handleLifecycleEvent(Lifecycle.Event)`，通过**获取事件对应的下一个状态**以及**改变当前状态到下一个状态**，同步分发事件；最后将各个观察者的生命周期状态依次移动到正确状态。
    

可以查看下面的时序图：

![](https://img-blog.csdn.net/20180922213240512?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JpZXpoaWh1YQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70#align=left&display=inline&height=185&originHeight=185&originWidth=703&status=done&width=703)

  

4.3 Lifecycle解析生命周期
-------------------

我们声明的**MyObserver**中的方法都是带有注解的，查看`OnLifecycleEvent`：

    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    public @interface OnLifecycleEvent {
        Lifecycle.Event value();
    }
    

注解修饰的方法会通过反射的方式获取，并且保存在`ClassesInfoCache`中，然后在生命周期发生改变的时候再找到对应 Event 的方法，通过反射来调用方法。

  

5\. 参考链接：
=========

[https://juejin.im/post/5cd81634e51d453af7192b87#heading-10](https://juejin.im/post/5cd81634e51d453af7192b87#heading-10)

https://yuqirong.me/2018/07/15/Android%20Architecture%20Component%E4%B9%8BLifecycle%E8%A7%A3%E6%9E%90/