---
title: '[转载] 1. Jetpack源码解析---看完你就知道Navigation是什么了？'
date: 2019-10-07 14:14:33
tags: [Jetpack,Navigation,转载]
published: true
hideInList: false
feature: 
---
[](http://creativecommons.org/licenses/by-sa/4.0/)版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。

本文链接：[https://blog.csdn.net/lyhhj/article/details/93757755](https://blog.csdn.net/lyhhj/article/details/93757755)

<!-- more -->

1\. 背景
======

> 之前已经翻译过了Google官方的CodeLabs上面的教程，教程很详细，代码在Github上也可以找到，本篇文章旨在自己的APP上使用效果及演示Demo，来具体的使用Navigation。并且对其进行源码解析。

基本相关介绍可以查看我之前翻译的文章，基本就是google翻译了一个大概。

[一、Android Jetpack\_Note\_CodeLabs一Navigation](https://juejin.im/post/5cf37caee51d45777621bb2b)

2\. 基本使用
========

> 虽然在之前的文章中已经很详细的介绍了`Navigation`，但是这里也简单的叙述一下我在项目中的具体使用：

2.1 Navigation+DrawerLayout+ToolBar
-----------------------------------

我们可以通过使用`Navigation` 配合`DrawerLayout`侧边栏和`Toolbar`标题来进行工作，不再需要我们去定义点击事件，也不需要我们去管理Fragment做切换，只需要我们做相关的配置和极少量的代码就可以了。

### 2.1.1 DrawerLayout

侧边栏的用法和我们之前的使用一样，配置好我们`NavigationView`里面的`_headerLayout_`_、_`_menu_`_即可；_

> **注意：**这里面的menu有一点和我们之前的不一样，item的id必须要和navigation里面的fragment的id相同，否则点击事件不生效，这里先提一下，下面会详细介绍。

### 2.1.2 ToolBar和NavHostFragment

`DrawerLayout`配置好之后，我们再来配置标题栏，之前我们的用法都是在中间加一个存放`Fragment`的容器，有可能是`FrameLayout`、`ViewPager`等，这里面我们需要配置一个`Fragment`，这个`Fragment`的**name**是`androidx.navigation.fragment.NavHostFragment`，这是一个添加到布局中的特殊部件，**NavHostFragment**通过**navGraph**与**navigation**导航编辑器进行关联。具体代码如下：

    <androidx.drawerlayout.widget.DrawerLayout
                xmlns:tools="http://schemas.android.com/tools"
                android:id="@+id/drawer_layout"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:fitsSystemWindows="true"
                tools:openDrawer="start">
            <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:orientation="vertical">
                <com.google.android.material.appbar.AppBarLayout
                        android:layout_height="wrap_content"
                        android:layout_width="match_parent"
                        android:theme="@style/AppTheme.AppBarOverlay">
    
                    <androidx.appcompat.widget.Toolbar
                            android:id="@+id/toolbar"
                            android:layout_width="match_parent"
                            android:layout_height="?attr/actionBarSize"
                            android:background="?attr/colorPrimary"
                            android:theme="@style/AppTheme.PopupOverlay"
                    />
    
                </com.google.android.material.appbar.AppBarLayout>
    
                <fragment
                        android:id="@+id/fragment_home"
                        android:name="androidx.navigation.fragment.NavHostFragment"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        app:defaultNavHost="true"
                        app:navGraph="@navigation/navigation_main"/>
            </LinearLayout>
    
            <com.google.android.material.navigation.NavigationView
                    app:itemIconTint="@color/nav_item_txt"
                    app:itemTextColor="@color/nav_item_txt"
                    android:id="@+id/nav_view"
                    android:layout_width="wrap_content"
                    android:layout_height="match_parent"
                    android:layout_gravity="start"
                    android:fitsSystemWindows="true"
                    app:headerLayout="@layout/nav_header_main"
                    app:menu="@menu/activity_main_drawer"/>
    
        </androidx.drawerlayout.widget.DrawerLayout>
    

我们可以看到**NavHostFragment**中有两个属性比较特殊：`app:defaultNavHost`和`app:navGraph="@navigation/navigation_main"`，前者就是是否是默认的其实页面，后者就是我们要设计的Navigation布局文件.

### 2.1.3 navigation_main.xml

在`Android Studio`3.2版本以上里面内嵌了`Navigation`的设计面板工具，我们可以在res文件夹下面的`navigation`文件里面对我们的fragment/Activity进行设计。

![WeChatf84276a636246413fd559699a8c1e759.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlLzAvMjAxOS9wbmcvMjc5NTA1LzE1NTk1NTM0NzQ3ODMtNzBlMGI3ODItNDNlYy00YTE3LTkyZTYtZTZkOWZmMDZmOTljLnBuZw#align=left&display=inline&height=152&name=WeChatf84276a636246413fd559699a8c1e759.png&originHeight=202&originWidth=546&size=27542&status=done&width=410)

打开**Desgin**面板，进入设计模式，在里面我们可以新建我们的目标页面。如果你还没创建过一个\*\*Destination，\*\*你可以点击`create a destination`创建一个`Fragmengt/Activity`。当然如果你之前已经创建好了的话，在这里你可以直接选择：

![WeChat224344cdf31a9c4010f08da7cdf8f45e.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlLzAvMjAxOS9wbmcvMjc5NTA1LzE1NTk1NTM3MDU4ODQtNDNkYTg1ZGMtNmQ0OC00NmY0LWFmMjktOGRkZDQwMTZhZmVkLnBuZw#align=left&display=inline&height=429&name=WeChat224344cdf31a9c4010f08da7cdf8f45e.png&originHeight=858&originWidth=1196&size=147391&status=done&width=598)

选择完一个**Destination**之后，在面板中就可以看到了，具体的action、arguments就不介绍了，详细的可以看之前的文章。

打开**Text**模式的xml我们可以看到我们选择的Fragmengt配置信息，当然你也可以不通过面板设计，也可以直接在xml里进行代码编写。  
`startDestination`是APP默认启动的页面，这里面必须要指定，否则会报错crash。这里我的代码所指默认页面是`HomeFragment`，如下：

    <navigation xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:app="http://schemas.android.com/apk/res-auto"
                xmlns:tools="http://schemas.android.com/tools"
                android:id="@+id/navigation_main"
                app:startDestination="@+id/homeFragment"
                tools:ignore="UnusedNavigation">
      
     <fragment android:id="@+id/homeFragment"
                  android:name="com.hankkin.jetpack_note.ui.home.HomeFragment"
                  android:label="@string/menu_home">
            <action android:id="@+id/action_navigationFragment_to_webFragment"
                    app:destination="@id/webFragment"
                    app:enterAnim="@anim/slide_in_right"
                    app:exitAnim="@anim/slide_out_left"
                    app:popEnterAnim="@anim/slide_in_left"
                    app:popExitAnim="@anim/slide_out_right"/>
        </fragment>
    
        <fragment android:id="@+id/codeFragment"
                  android:name="com.hankkin.jetpack_note.ui.CodeFragment"
                  android:label="@string/menu_code"/>
    

我们可以看到上面的布局代码 默认的起始页面是homeFragment，下面还有一个codeFragment，其实这两个fragment也就是对应着在menu中的两个菜单，同时也对应我们侧边栏中的一个**首页**和一个**代码页，**

    <item
                    android:id="@+id/homeFragment"
                    android:icon="@drawable/ic_menu_home"
                    android:title="@string/menu_home"/>
            <item
                    android:id="@+id/codeFragment"
                    android:icon="@drawable/ic_menu_code"
                    android:title="@string/menu_code"/>
    

> 还记得上面说的id要相同吗？就是上面**item的id**要和**navigation_main.xml**中fragment的id相同，否则点击菜单不会切换fragment的。

配置完上面这些信息之后，怎么将他们绑定起来使用呢？

### 2.1.4 NavController

先看下代码：

    		navController = Navigation.findNavController(this, R.id.fragment_home)
            appBarConfiguration = AppBarConfiguration(setOf(R.id.homeFragment, R.id.codeFragment), drawerLayout)
            // Set up ActionBar
            setSupportActionBar(mDataBinding.toolbar)
            setupActionBarWithNavController(navController, appBarConfiguration)
            // Set up navigation menu
            mDataBinding.navView.setupWithNavController(navController)
    

*   我们通过findNavController传入之前定义好的装载fragment的容器id（也就是之前定义的NavHostFragment）找到了Navigation对应的navController；
*   通过配置一个AppBarConfiguration，AppBarConfiguration 里传入了一个id的set集合和drawerlayout，id的集合就是我们在\*\*navigation_main.xml \*\*定义的fragment id
*   最后通过设置setupActionBarWithNavController、setupWithNavController进行关联绑定

> 到此，我们的基本配置就结束了，可以看到我们drawerlayout中的首页和代码按钮点击会切换对应的fragment，同时toolbar的汉堡按钮和返回按钮也会自动切换；当然Navigation还可以配合BottomNavigationView使用。

2.2 BottomNavigationView使用
--------------------------

### 2.2.1 配置文件

和上面的步骤类似：也是配置好 navigation.xml布局以及 BottomNavigationView所对应的menu菜单文件  

### 2.2.2 setupWithNavController

当然BottomNavigationView也提供了扩展方法setupWithNavController去绑定菜单和fragment，这里使用很简单就不具体介绍了。详情可见[BottomNavSampleActivity](https://github.com/Hankkin/JetPack_Note/blob/master/app/src/main/java/com/hankkin/jetpack_note/ui/navigation/BottomNavSampleActivity.kt)。

2.3 Action跳转及传餐
---------------

### 2.3.1 Action跳转

先看一下navigation的Desgin模式：

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlLzAvMjAxOS9wbmcvMjc5NTA1LzE1NTk2MzQ4MzIwNDUtMTZmYzY0ZWItOTI3MS00MTAwLWEyOTMtM2I4MDg5M2UzMWEzLnBuZw#align=left&display=inline&height=348&name=image.png&originHeight=695&originWidth=747&size=159060&status=done&width=374)

可能你会注意到这些线是什么？没错这就是一个一个的**Action**，当你手动将两个Fragment进行连线后，在xml布局里面会对应生成一个标签，例如：

    <action android:id="@+id/action_dashBoardSampleFragment_to_notificationSampleFragment"
                    app:destination="@id/notificationSampleFragment"/>
    

  
它会自动创建好id，id有可能比较长，但是确很清楚，从xtoy的模式，当然如果你不喜欢可以自己改，destination则是我们要跳转到的目标接界面。

action设置好了之后，我们可以执行下面代码进行跳转：

    findNavController().navigate(R.id.action_homeSampleFragment_to_dashBoardSampleFragment_action)
    

### 2.3.2 NavOptions切换动画

当然fragment之间的切换是支持动画的，NavOptions是一个动画管理类，我们可以设置进入和回退的动画，设置的方式有两种：

1.  直接在标签中设置动画

    <action android:id="@+id/action_homeSampleFragment_to_dashBoardSampleFragment_action"
                    app:destination="@id/dashBoardSampleFragment"
                    app:enterAnim="@anim/slide_in_right"
                    app:exitAnim="@anim/slide_out_left"
                    app:popEnterAnim="@anim/slide_in_left"
                    app:popExitAnim="@anim/slide_out_right"/>
    

2.  通过NavOptions设置动画

    val options = navOptions {
                anim {
                    enter = R.anim.slide_in_right
                    exit = R.anim.slide_out_left
                    popEnter = R.anim.slide_in_left
                    popExit = R.anim.slide_out_right
                }
            }
            view.findViewById < Button >(R.id.navigate_destination_button)?.setOnClickListener {
                findNavController().navigate(R.id.flow_step_one_dest, null, options)
            }
    

### 2.3.3 参数传递

fragment之间的切换参数传递的方法也很简单，之前我们可能要通过宿主Activity或者接口等方法，总之挺麻烦的，下面我们看看通过Navigation控制的Fragment之间怎么传递？

我们可以在naviagtion布局中使用标签，

*   name是我们传参的key
*   argType是参数类型
*   defaultValue默认值
*   nullable 是否可空

    <argument
             android:name="deep_args"
             app:argType=""
             android:defaultValue=""
             app:nullable=""/>
    

> **注意：**当然type类型也支持我们自定的实体类，但是需要你填写类的全路径，同时你要保证实体类实现了序列化

我们可以通过把参数传递封装到Bundle中，然后再执行navigate()方法时传递过去，例如：

    val args = Bundle()
    args.putString("link","1")
    args.putString("title","1")
    it.findNavController().navigate(R.id.webFragment, args)
    

当然你在接受是也可以通过getArguments().getString(xxxx)这种方式去获取，但是Navigation组件还提供给了我们更简单的方式，当你设置了标签后，通过编译代码，会自动为我们生成一个**XXXFragmentDirections**类，它里面为我们作了参数的封装，而NavController的navigate()方法同时支持direction类型的传递。

    val direction = HomeFragmentDirections.actionNavigationFragmentToWebFragment(link,title)
    it.findNavController().navigate(direction)
    

同时在我们的目标页面所对应了一个XXXFragmentArgs，我们可以直接拿到navArgs()从这里我们可以直接拿到参数。

    private val args: WebFragmentArgs by navArgs()
    

2.4 Deep Link
-------------

关于Deep Link 是指跳入应用内的一个功能，我就把它翻译成深层链接了，Navigation提供了这样一个功能，使用起来也很简单：

                val args = Bundle()
                args.putString("deep_args",et_deep_link.text.toString())
                val deep = findNavController().createDeepLink()
                    .setDestination(R.id.notificationSampleFragment)
                    .setArguments(args)
                    .createPendingIntent()
    
                val notificationManager =
                    context?.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                    notificationManager.createNotificationChannel(
                        NotificationChannel(
                            "deeplink", "Deep Links", NotificationManager.IMPORTANCE_HIGH)
                    )
                }
                val builder = NotificationCompat.Builder(
                    context!!, "deeplink")
                    .setContentTitle(resources.getString(R.string.app_name))
                    .setContentText("Navigation 深层链接测试")
                    .setSmallIcon(R.mipmap.jetpack)
                    .setContentIntent(deep)
                    .setAutoCancel(true)
                notificationManager.notify(0, builder.build())
    

我们可以创建一个DeepLink，带上参数，通过Notification通知来测试这样的效果，可以直接跳到项目中的该页面。  
具体可查看[SampleNotificationFragment](https://github.com/Hankkin/JetPack_Note/blob/master/app/src/main/java/com/hankkin/jetpack_note/ui/navigation/SampleNotificationFragment.kt)。

3\. 源码解析
========

### 3.1 NavHostFragment

> 官网上是这样介绍它的：NavHostFragment provides an area within your layout for self-contained navigation to occur. 大致意思就是NavHostFragment在布局中提供了一个区域，用于进行包含导航

接下来我们看一下它的源码：

    public class NavHostFragment extends Fragment implements NavHost {
        @CallSuper
        @Override
        public void onAttach(@NonNull Context context) {
            super.onAttach(context);
            if (mDefaultNavHost) {
                requireFragmentManager().beginTransaction()
                        .setPrimaryNavigationFragment(this)
                        .commit();
            }
        }
    }
    

可以看到它就是一个`Fragment`，在`onAttach`生命周期开启事务将它自己设置成了PrimaryFragment了，当然通过`defaultNavHost`条件判断的，这个布尔值看着眼熟吗？没错，就是我们在xml布局中设置的那一个。

    					<fragment
                        android:id="@+id/fragment_home"
                        android:name="androidx.navigation.fragment.NavHostFragment"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        app:defaultNavHost="true"
                        app:navGraph="@navigation/navigation_main"/>
    

接着看它的`onCreate`生命周期

        @CallSuper
        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            final Context context = requireContext();
    
            mNavController = new NavController(context);
            mNavController.getNavigatorProvider().addNavigator(createFragmentNavigator());
    
           	.......
    
            if (navState != null) {
                // Navigation controller state overrides arguments
                mNavController.restoreState(navState);
            }
            if (mGraphId != 0) {
                // Set from onInflate()
                mNavController.setGraph(mGraphId);
            } else {
                // See if it was set by NavHostFragment.create()
                final Bundle args = getArguments();
                final int graphId = args != null ? args.getInt(KEY_GRAPH_ID) : 0;
                final Bundle startDestinationArgs = args != null
                        ? args.getBundle(KEY_START_DESTINATION_ARGS)
                        : null;
                if (graphId != 0) {
                    mNavController.setGraph(graphId, startDestinationArgs);
                }
            }
        }
    

我们看到在`onCreate`生命周期中创建了一个`NavController`，并且为这个`NavController`创建了一个`_Navigator_`_添加了进去，_我们跟踪`createFragmentNavigator`，发现它创建了一个`FragmentNavigator`，这个类是做什么的呢？它继承了Navigator，查看注释我们知道它是为每个Navigation设置策略的，也就是说Fragment之间通过导航切换都是由它来操作的，下面会详细介绍的，这里先简单看下。  
接下来我们看到为`NavController`设置了`setGraph()`，也就是我们xml里面定义的`navGraph`，导航布局里面的`Fragment`及`action`跳转等信息。

还有就是onCreateView、onViewCreated等生命周期方法，基本就是加载布局设置ID的方法了。

下面我们跟到NavController.setGraph()中看下是怎样将我们设计的fragment添加进去的？

### 3.2 NavController

    /**
         * Sets the {@link NavGraph navigation graph} to the specified graph.
         * Any current navigation graph data (including back stack) will be replaced.
         *
         * <p>The graph can be retrieved later via {@link #getGraph()}.</p>
         *
         * @param graph graph to set
         * @see #setGraph(int, Bundle)
         * @see #getGraph
         */
        @CallSuper
        public void setGraph(@NonNull NavGraph graph, @Nullable Bundle startDestinationArgs) {
            if (mGraph != null) {
                // Pop everything from the old graph off the back stack
                popBackStackInternal(mGraph.getId(), true);
            }
            mGraph = graph;
            onGraphCreated(startDestinationArgs);
        }
    

我们看如果设置的graph不为null，它执行了popBackStackInternal，看注释的意思为从之前的就的graph栈弹出所有的graph：

    boolean popBackStackInternal(@IdRes int destinationId, boolean inclusive) {
            .....
            .....
            boolean popped = false;
            for (Navigator navigator : popOperations) {
                if (navigator.popBackStack()) {
                    mBackStack.removeLast();
                    popped = true;
                } else {
                    // The pop did not complete successfully, so stop immediately
                    break;
                }
            }
            return popped;
        }
    

果真remove掉了之前所有的naviagtor。而这个mBackStack是什么时候添加的navigator的呢？查看源码我们发现：

    private void navigate(@NonNull NavDestination node, @Nullable Bundle args,
                @Nullable NavOptions navOptions, @Nullable Navigator.Extras navigatorExtras) {
            boolean popped = false;
            if (navOptions != null) {
                if (navOptions.getPopUpTo() != -1) {
                    popped = popBackStackInternal(navOptions.getPopUpTo(),
                            navOptions.isPopUpToInclusive());
                }
            }
            Navigator<NavDestination> navigator = mNavigatorProvider.getNavigator(
                    node.getNavigatorName());
            Bundle finalArgs = node.addInDefaultArgs(args);
            NavDestination newDest = navigator.navigate(node, finalArgs,
                    navOptions, navigatorExtras);
            if (newDest != null) {
                // 如果NavGraph不在栈内，先拿到父类Navgarph
                ArrayDeque<NavBackStackEntry> hierarchy = new ArrayDeque<>();
                NavGraph parent = newDest.getParent();
                while (parent != null) {
                    hierarchy.addFirst(new NavBackStackEntry(parent, finalArgs));
                    parent = parent.getParent();
                }
                // 现在遍历后堆栈并查看哪些导航图已经在栈内
                Iterator<NavBackStackEntry> iterator = mBackStack.iterator();
                while (iterator.hasNext() && !hierarchy.isEmpty()) {
                    NavDestination destination = iterator.next().getDestination();
                    if (destination.equals(hierarchy.getFirst().getDestination())) {
                        //destination 如果已经在栈顶，不需要再add了
                        hierarchy.removeFirst();
                    }
                }
                // Add all of the remaining parent NavGraphs that aren't
                // already on the back stack
                mBackStack.addAll(hierarchy);
                //添加新的 destination
                NavBackStackEntry newBackStackEntry = new NavBackStackEntry(newDest, finalArgs);
                mBackStack.add(newBackStackEntry);
            }
            if (popped || newDest != null) {
                dispatchOnDestinationChanged();
            }
        }
    

还记得这个方法吗？我们一般手动切换Fragment时可以调用这个方法，最后就是跟踪到这里。

    findNavController().navigate(R.id.bottomNavSampleActivity)
    

同时，切换目标Fragment到栈顶。我们发现最后`dispatchOnDestinationChanged()`这个方法，分发目标界面切换。有必要去跟一下，你可能会发现意想不到的东西：

     /**
         * Dispatch changes to all OnDestinationChangedListeners.
         * <p>
         * If the back stack is empty, no events get dispatched.
         *
         * @return If changes were dispatched.
         */
        @SuppressWarnings("WeakerAccess") /* synthetic access */
        boolean dispatchOnDestinationChanged() {
            // We never want to leave NavGraphs on the top of the stack
            //noinspection StatementWithEmptyBody
            while (!mBackStack.isEmpty()
                    && mBackStack.peekLast().getDestination() instanceof NavGraph
                    && popBackStackInternal(mBackStack.peekLast().getDestination().getId(), true)) {
                // Keep popping
            }
            if (!mBackStack.isEmpty()) {
                NavBackStackEntry backStackEntry = mBackStack.peekLast();
                for (OnDestinationChangedListener listener :
                        mOnDestinationChangedListeners) {
                    listener.onDestinationChanged(this, backStackEntry.getDestination(),
                            backStackEntry.getArguments());
                }
                return true;
            }
            return false;
        }
    

这里面分发了所有实现了`OnDestinationChangedListener`接口的方法，继续跟踪，看看都哪些实现了这个接口呢？

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlLzAvMjAxOS9wbmcvMjc5NTA1LzE1NTk2NjQwMDM5NDUtNGY2NWU4MjgtZWRlOC00M2VlLWI4NmItNjVlZmVkYzMwMTFhLnBuZw#align=left&display=inline&height=817&name=image.png&originHeight=817&originWidth=1941&size=315597&status=done&width=1941)

只有一个类实现了**AbstractAppBarOnDestinationChangedListener**，看一下具体实现：

    @Override
        public void onDestinationChanged(@NonNull NavController controller,
                @NonNull NavDestination destination, @Nullable Bundle arguments) {
            DrawerLayout drawerLayout = mDrawerLayoutWeakReference != null
                    ? mDrawerLayoutWeakReference.get()
                    : null;
            if (mDrawerLayoutWeakReference != null && drawerLayout == null) {
                controller.removeOnDestinationChangedListener(this);
                return;
            }
            CharSequence label = destination.getLabel();
            if (!TextUtils.isEmpty(label)) {
                ......
                ......
                matcher.appendTail(title);
                //设置title
                setTitle(title);
            }
            boolean isTopLevelDestination = NavigationUI.matchDestinations(destination,
                    mTopLevelDestinations);
            if (drawerLayout == null && isTopLevelDestination) {
                //设置icon
                setNavigationIcon(null, 0);
            } else {
                //设置返回箭头状态
                setActionBarUpIndicator(drawerLayout != null && isTopLevelDestination);
            }
        }
    

原来如此，到这里就应该清楚了，当我们切换Fragment时，大概流程如下：

1.  切换目标fragment到栈顶
2.  分发目标Fragment切换状态
3.  设置toolbar的标题、icon状态等
4.  当然setTitle()、setNavigationIcon()等都为抽象方法，具体实现可以看子类里是怎么实现的，具体就不叙述了

> 到这里，基本的几个核心类以及相关实现我们基本了解了，下面我们看一下基本的流程，首先我们从入口进去，一点点跟进

### 3.3 Navigation.findNavController(this, R.id.fragment_home)

我们在最开始会初始化一个NavController：

    @NonNull
        public static NavController findNavController(@NonNull Activity activity, @IdRes int viewId) {
            View view = ActivityCompat.requireViewById(activity, viewId);
            NavController navController = findViewNavController(view);
            .......
            return navController;
        }
    
    @Nullable
        private static NavController findViewNavController(@NonNull View view) {
            while (view != null) {
                NavController controller = getViewNavController(view);
                .........
            }
            return null;
        }
    
    @SuppressWarnings("unchecked")
        @Nullable
        private static NavController getViewNavController(@NonNull View view) {
            Object tag = view.getTag(R.id.nav_controller_view_tag);
            NavController controller = null;
            if (tag instanceof WeakReference) {
                controller = ((WeakReference<NavController>) tag).get();
            } else if (tag instanceof NavController) {
                controller = (NavController) tag;
            }
            return controller;
        }
    

查看代码可以看到是通过一个tag值来找到的，那么什么时候设置的呢？还记得3.1里面介绍的`NavHostFragment`的生命周期`onViewCreated`么？

    @Override
        public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
            super.onViewCreated(view, savedInstanceState);
            .......
            View rootView = view.getParent() != null ? (View) view.getParent() : view;
            Navigation.setViewNavController(rootView, mNavController);
        }
    

在视图创建的时候调用了`Naviagtion.setViewNavController()`。NavController初始化好了之后，接下来将它和**NavigationView**、**ToolBar**、**BottomNavigationView**、**DrawerLayout**进行绑定：

### 3.4 setupActionBarWithNavController

不管是`NavigationView`还是`Bottom``NavigationView`，都会调用这个方法，他是`AppCompatActivity`的一个扩展方法，调用的是NavigationUI这个类：

    public static void setupActionBarWithNavController(@NonNull AppCompatActivity activity,
                @NonNull NavController navController,
                @NonNull AppBarConfiguration configuration) {
            navController.addOnDestinationChangedListener(
                    new ActionBarOnDestinationChangedListener(activity, configuration));
        }
    

可以看到它就是调用了目标切换的那个接口，用来实现标题按钮等状态的改变。查看它的方法实现：

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlLzAvMjAxOS9wbmcvMjc5NTA1LzE1NTk2NjYyNjY1MjItMDFhOTc2YTEtN2I0Ni00OWMwLTljYzEtNmFlZjZjMjNlN2ZhLnBuZw#align=left&display=inline&height=697&name=image.png&originHeight=1393&originWidth=1151&size=265848&status=done&width=576)

我们看到它重载了很多方法，包括我们上面提到的**NavigationView**、**ToolBar**、**BottomNavigationView**、**DrawerLayout**。这样就将组件的状态切换绑定起来了，当fragment切换时，上面提到的接口分发，去切换布局按钮等状态。

### 3.5 navView.setupWithNavController(navController)

    public static void setupWithNavController(@NonNull final NavigationView navigationView,
                @NonNull final NavController navController) {
            navigationView.setNavigationItemSelectedListener(
                    new NavigationView.OnNavigationItemSelectedListener() {
                        @Override
                        public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                            //目标页面是否被选中
                            boolean handled = onNavDestinationSelected(item, navController);
                            if (handled) {
                                //切换菜单状态、关闭抽屉
                                ViewParent parent = navigationView.getParent();
                                if (parent instanceof DrawerLayout) {
                                    ((DrawerLayout) parent).closeDrawer(navigationView);
                                } else {
                                    BottomSheetBehavior bottomSheetBehavior =
                                            findBottomSheetBehavior(navigationView);
                                    if (bottomSheetBehavior != null) {
                                        bottomSheetBehavior.setState(BottomSheetBehavior.STATE_HIDDEN);
                                    }
                                }
                            }
                            return handled;
                        }
                    });
            final WeakReference<NavigationView> weakReference = new WeakReference<>(navigationView);
            navController.addOnDestinationChangedListener(
                    new NavController.OnDestinationChangedListener() {
                        @Override
                        public void onDestinationChanged(@NonNull NavController controller,
                                @NonNull NavDestination destination, @Nullable Bundle arguments) {
                            NavigationView view = weakReference.get();
                            if (view == null) {
                                navController.removeOnDestinationChangedListener(this);
                                return;
                            }
                            Menu menu = view.getMenu();
                            for (int h = 0, size = menu.size(); h < size; h++) {
                                MenuItem item = menu.getItem(h);
                                item.setChecked(matchDestination(destination, item.getItemId()));
                            }
                        }
                    });
        }
    

最后就是状态切换了，当点击menu菜单或者目标Fragment切换的时候，改变状态。

### 3.6 遗留问题

**遗留：**还记得上面说的那个在设置menu菜单栏的item的ID要和navigation.xml里fragment的ID相同么？至于为什么要这么做，我们看上面的第一段代码：跟踪**onNavDestinationSelected()：**

    public static boolean onNavDestinationSelected(@NonNull MenuItem item,
                @NonNull NavController navController) {
           	.......
            .......
            if ((item.getOrder() & Menu.CATEGORY_SECONDARY) == 0) {
                builder.setPopUpTo(findStartDestination(navController.getGraph()).getId(), false);
            }
            NavOptions options = builder.build();
            try {
                //TODO provide proper API instead of using Exceptions as Control-Flow.
                navController.navigate(item.getItemId(), null, options);
                return true;
            } catch (IllegalArgumentException e) {
                return false;
            }
        }
    

我们看到最后还是调用navigate()方法,并且将MenuItem的ID作为参数传递过去：

    public void navigate(@IdRes int resId, @Nullable Bundle args, @Nullable NavOptions navOptions,
                @Nullable Navigator.Extras navigatorExtras) {
            NavDestination currentNode = mBackStack.isEmpty()
                    ? mGraph
                    : mBackStack.getLast().getDestination();
            if (currentNode == null) {
                throw new IllegalStateException("no current navigation node");
            }
            @IdRes int destId = resId;
            ......
            ......
            //根据menu id查询目标页面
            NavDestination node = findDestination(destId);
            if (node == null) {
                final String dest = NavDestination.getDisplayName(mContext, destId);
                throw new IllegalArgumentException("navigation destination " + dest
                        + (navAction != null
                        ? " referenced from action " + NavDestination.getDisplayName(mContext, resId)
                        : "")
                        + " is unknown to this NavController");
            }
            navigate(node, combinedArgs, navOptions, navigatorExtras);
        }
    

`NavDestination node = findDestination(destId)`通过Menu Item的ID查询**NavDestination：**  
**

    @SuppressWarnings("WeakerAccess") /* synthetic access */
        NavDestination findDestination(@IdRes int destinationId) {
            .......
            return currentGraph.findNode(destinationId);
        }
    
    @Nullable
        final NavDestination findNode(@IdRes int resid, boolean searchParents) {
            NavDestination destination = mNodes.get(resid);
            // Search the parent for the NavDestination if it is not a child of this navigation graph
            // and searchParents is true
            return destination != null
                    ? destination
                    : searchParents && getParent() != null ? getParent().findNode(resid) : null;
        }
    

而**mNodes**是一个SparseArrayCompat数组，而**NavDestination**中维护了navigation.xml中的每个fragment的相关信息：

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlLzAvMjAxOS9wbmcvMjc5NTA1LzE1NTk3MDQ2OTM5NjEtZjc0M2Y0ZTMtNDBhOS00ZWY4LWIwODMtMTFmMWMxZWExZGRlLnBuZw#align=left&display=inline&height=383&name=image.png&originHeight=765&originWidth=1208&size=377825&status=done&width=604)

在初始化的时候通过`addDestination()`放到数组mNodes中，而mId则就是我们的MenuItem的ID，所以很清楚了吧。

4\. 总结
------

### 4.1 流程

1.  考虑到我们开始如果直接从**setupWithNavController** 入口进行分析的话，可能不太容易找到怎么创建的graph布局中的fragment，以及**NavHostFragment**到底是什么，所以我们先分析了布局中的**NavHostFragment，**我们发现为什么要在布局中声明了一个**NavHostFragment，**它是用来做什么的，最后发现在它的生命周期中创建了一个NavController，并且添加了FragmentNavigator，同时setGraph了。
2.  紧接着我们通过setGraph进入到了**NavController**类中，通过graph里面设置的初始fragment看到了切换栈内切换Fragment的代码。
3.  在里面我们看到了熟悉的`navigate()`方法，在里面dispatchOnDestinationChanged()吸引了我的注意力，通过查找，发现切换Fragment之后，通过该方法去改变布局的状态，也就是OnDestinationChangedListener接口。
4.  到这里基本的代码实现已经了解的差不多了，然后我回到了入口，通过初始化NavController，调用NavigationUI中的方法绑定**NavigationView**、**ToolBar**、**BottomNavigationView**、**DrawerLayout**等布局，在调用`navigate()`方法后，改变状态，整个流程就走通了。

> 可能有一些不合理的地方，望大家见谅，但是这是我此次的一个基本流程。

### 4.2 类图

[![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlLzAvMjAxOS9wbmcvMjc5NTA1LzE1NTk3MjgwNzI4MDEtZTRlMGMxOTUtZTRkMy00ODdjLTk0NjktYjZlZjc0YjU0OGZjLnBuZw#align=left&display=inline&height=560&name=image.png&originHeight=1024&originWidth=1363&size=146731&status=done&width=746)](http://lc-2hxprqvs.cn-n1.lcfile.com/55a8ef5cb455ed676f77/navigation.png)

### 4.3 分析

#### 4.3.1 **NavHostFragment**

我们在Activity的布局里面设置了**NavHostFragment**,同时设置了navGraph布局，经过上面的分析我们知道**NavHostFragment**中新建了NavController,并且创建了用来管理Fragment事务及切换的**FragmentNavigator**，可以简单的把它理解成连接Fragment和NavController的一个桥梁，同时也提供了包含导航的容器布局。

#### 4.3.2 NavController

NavContorller是整个导航组件的核心，通过它来加载xml中fragment节点转化成**NavDestination**，并保存在栈内，通过navigate()方法切换栈内NavDestination，以做到fragment的切换操作。同时当fragment切换后，下发**OnDestinationChanged**接口，来改变NavgationView、BottomNavgationView、Menu等相关UI操作。

#### 4.3.3 NavigationUI

通过NavgationUI类，为各个View设置接口监听，将View的UI状态和NavController中的切换Fragment做了绑定。

> 到这里整个Navgation组件的源码分析就结束了，大概的流程已经很清晰了，当然没有做到百分百，比如Deep Link部分，感兴趣的可以自行看一下，可以按照这个思路去真的看一下源码，看完之后你真的会对Navgation组件有更深的理解。当然你也可以参考CodeLabs中的Demo以及文档，也可以看我的Jepack_Note的代码，如有不对的地方，还望指出，谅解.