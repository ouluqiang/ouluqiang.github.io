---
title: '[转载] 2. Jetpack源码解析---Navigation为什么切换Fragment会重绘？'
date: 2019-10-15 17:12:29
tags: []
published: true
hideInList: false
feature: 
---
[](http://creativecommons.org/licenses/by-sa/4.0/)版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。

本文链接：[https://blog.csdn.net/lyhhj/article/details/94003014](https://blog.csdn.net/lyhhj/article/details/94003014)

<!-- more -->

> 上篇文章我们简单的介绍了**Navigation组件**的使用，以及深入分析了源码中的具体实现，基本原理我们已经很清晰了。本篇文章主要介绍下我在项目中遇到的问题，以及目前关于**Navigation**实现的一些探讨。还没有看过上篇文章的可以查看一下：

[1\. Jetpack源码解析—看完你就知道Navigation是什么了？](https://blog.csdn.net/lyhhj/article/details/93757755)

1\. 背景
======

先来看一下`Navigation`组件在官方文档上的介绍：

> 今天，我们宣布推出Navigation组件，作为构建您的应用内界面的框架，重点是让单 Activity 应用成为首选架构。利用Navigation组件对 Fragment 的原生支持，您可以获得架构组件的所有好处（例如生命周期和 ViewModel），同时让此组件为您处理 FragmentTransaction 的复杂性。此外，Navigation组件还可以让您声明我们为您处理的转场。它可以自动构建正确的“向上”和“返回”行为，包含对深层链接的完整支持，并提供了帮助程序，用于将导航关联到合适的 UI 小部件，例如抽屉式导航栏和底部导航。

确实经过源码分析我们就可以发现，`Navigation`组件封装了`Menu`菜单栏、`Fragment`的切换、`NavigationView`、`Drawerlayout`等一系列涉及到的组件，为了更方便的让我们使用单Activity多Fragment的架构。

但是我在使用的时候发现，当一个`Fragment`中的布局稍微复杂一些，切换`Fragment`的时候会顿卡，而且如果再配合`DrawrLayout`使用的话，还会闪一下屏，效果体验不是很好，本着这个问题，我又再次对`Navigation`组件进行了分析。

2.Fragment切换
============

通过现象分析，发现当切换`NavigationView`中的menu菜单来切换`Fragment`时，`DrawerLayout`抽屉关闭有一个短暂的动画(具体的这里就不分析了,感兴趣的可以自行查看，但是这不是根本原因)，同时`Fragment`切换，发生顿卡和闪屏的现象。所以…还是看源码吧：

2.1 NavController
-----------------

    private void navigate(@NonNull NavDestination node, @Nullable Bundle args,
                @Nullable NavOptions navOptions, @Nullable Navigator.Extras navigatorExtras) {
            boolean popped = false;
            ....
            Navigator<NavDestination> navigator = mNavigatorProvider.getNavigator(
                    node.getNavigatorName());
            Bundle finalArgs = node.addInDefaultArgs(args);
            NavDestination newDest = navigator.navigate(node, finalArgs,
                    navOptions, navigatorExtras);
           ....
        }
    
    

2.2 FragmentNavigator
---------------------

    public NavDestination navigate(@NonNull Destination destination, @Nullable Bundle args,
                @Nullable NavOptions navOptions, @Nullable Navigator.Extras navigatorExtras) {
            ...
            //根据classname反射获取Fragmnent
            final Fragment frag = instantiateFragment(mContext, mFragmentManager,
                    className, args);
            frag.setArguments(args);
            //获取Fragment事务
            final FragmentTransaction ft = mFragmentManager.beginTransaction();
            //切换动画设置
            int enterAnim = navOptions != null ? navOptions.getEnterAnim() : -1;
            int exitAnim = navOptions != null ? navOptions.getExitAnim() : -1;
            int popEnterAnim = navOptions != null ? navOptions.getPopEnterAnim() : -1;
            int popExitAnim = navOptions != null ? navOptions.getPopExitAnim() : -1;
            if (enterAnim != -1 || exitAnim != -1 || popEnterAnim != -1 || popExitAnim != -1) {
                enterAnim = enterAnim != -1 ? enterAnim : 0;
                exitAnim = exitAnim != -1 ? exitAnim : 0;
                popEnterAnim = popEnterAnim != -1 ? popEnterAnim : 0;
                popExitAnim = popExitAnim != -1 ? popExitAnim : 0;
                ft.setCustomAnimations(enterAnim, exitAnim, popEnterAnim, popExitAnim);
            }
            //切换Fragment
            ft.replace(mContainerId, frag);
            ft.setPrimaryNavigationFragment(frag);
            ......
            
            ft.addToBackStack(generateBackStackName(mBackStack.size(), destId));
            ........
        }
    

看到这里就很清楚了吧，`Fragment`的切换是通过`replace`方式来切换的，并且加入回退栈，也就是说每次切换`Fragment`,都会销毁视图和重新创建视图。至于为什么用这种方式我是真的想不到，也没搞清楚初衷是什么？按照我们目前的开发来说，`Fragment`的切换通常都会使用`hide()`、`show()`，而`replcae()`的方式很少用，替换会把容器中的所有内容全都替换掉，有一些app会使用这样的做法，保持只有一个fragment在显示，减少了界面的层级关系。

> 不仅仅是这样，上篇文章有小伙伴问切换了`Fragment`之后，点击返回按钮，发现之前的`Fragment`重走了`onCreateView`流程，这就意味着之前的状态没了。对于这个问题其实根据上面的分析，也能大概想到是因为什么，但是返回按钮的操作我之前还真没有看过源码，所以这次顺便了解一下：

3\. 返回都做了什么
===========

3.1 onBackPressed
-----------------

我们同样从首页的`onBackPressed`入手：

    override fun onBackPressed() {
            if (drawerLayout.isDrawerOpen(GravityCompat.START)) {
                drawerLayout.closeDrawer(GravityCompat.START)
            } else {
                super.onBackPressed()
            }
        }
    

    public void onBackPressed() {
            mOnBackPressedDispatcher.onBackPressed();
        }
    

最终调用了`mOnBackPressedDispatcher`的`onBackPressed()`方法。我们查看这个类，通过**Debug**调试，我们跟到了`FragmentManagerImpl`类：

    private final OnBackPressedCallback mOnBackPressedCallback =
                new OnBackPressedCallback(false) {
            @Override
            public void handleOnBackPressed() {
                FragmentManagerImpl.this.handleOnBackPressed();
            }
        };
    

发现点击返回按钮之后就走到这个，执行`handleOnBackPressed()`方法。

3.2 FragmentManagerImpl
-----------------------

继续跟踪源码，中间的一些过程我这里就忽略掉了，大部分都是一些`popBackStack`的操作，这里我们直接跟踪到关键点：

    //在BackStackRecords中进行入栈出栈操作。
    private static void executeOps(ArrayList<BackStackRecord> records,
                                       ArrayList<Boolean> isRecordPop, int startIndex, int endIndex) {
            for (int i = startIndex; i < endIndex; i++) {
                final BackStackRecord record = records.get(i);
                final boolean isPop = isRecordPop.get(i);
                if (isPop) {
                    record.bumpBackStackNesting(-1);
                    // Only execute the add operations at the end of
                    // all transactions.
                    boolean moveToState = i == (endIndex - 1);
                    record.executePopOps(moveToState);
                } else {
                    record.bumpBackStackNesting(1);
                    record.executeOps();
                }
            }
        }
    

我们可以看到通过遍历栈数组，对`record`做`executePopOps()`操作，通过cmd来让`FragmentManager`做相关操作。

    void executePopOps(boolean moveToState) {
            for (int opNum = mOps.size() - 1; opNum >= 0; opNum--) {
                final Op op = mOps.get(opNum);
                Fragment f = op.mFragment;
                if (f != null) {
                    f.setNextTransition(FragmentManagerImpl.reverseTransit(mTransition),
                            mTransitionStyle);
                }
                switch (op.mCmd) {
                    case OP_ADD:
                        f.setNextAnim(op.mPopExitAnim);
                        mManager.removeFragment(f);
                        break;
                    case OP_REMOVE:
                        f.setNextAnim(op.mPopEnterAnim);
                        mManager.addFragment(f, false);
                        break;
                    case OP_HIDE:
                        f.setNextAnim(op.mPopEnterAnim);
                        mManager.showFragment(f);
                        break;
                    case OP_SHOW:
                        f.setNextAnim(op.mPopExitAnim);
                        mManager.hideFragment(f);
                        break;
                    case OP_DETACH:
                        f.setNextAnim(op.mPopEnterAnim);
                        mManager.attachFragment(f);
                        break;
                    case OP_ATTACH:
                        f.setNextAnim(op.mPopExitAnim);
                        mManager.detachFragment(f);
                        break;
                    case OP_SET_PRIMARY_NAV:
                        mManager.setPrimaryNavigationFragment(null);
                        break;
                    case OP_UNSET_PRIMARY_NAV:
                        mManager.setPrimaryNavigationFragment(f);
                        break;
                    case OP_SET_MAX_LIFECYCLE:
                        mManager.setMaxLifecycle(f, op.mOldMaxState);
                        break;
                    default:
                        throw new IllegalArgumentException("Unknown cmd: " + op.mCmd);
                }
                if (!mReorderingAllowed && op.mCmd != OP_REMOVE && f != null) {
                    mManager.moveFragmentToExpectedState(f);
                }
            }
            if (!mReorderingAllowed && moveToState) {
                mManager.moveToState(mManager.mCurState, true);
            }
        }
    

同时重新设置`PrimaryNavigationFragment`，add我们的首页`Fragment`，最后执行`moveToState`方法：

    public void addFragment(Fragment fragment, boolean moveToStateNow) {
            if (DEBUG) Log.v(TAG, "add: " + fragment);
            makeActive(fragment);
            if (!fragment.mDetached) {
                if (mAdded.contains(fragment)) {
                    throw new IllegalStateException("Fragment already added: " + fragment);
                }
                synchronized (mAdded) {
                    mAdded.add(fragment);
                }
                fragment.mAdded = true;
                fragment.mRemoving = false;
                if (fragment.mView == null) {
                    fragment.mHiddenChanged = false;
                }
                if (isMenuAvailable(fragment)) {
                    mNeedMenuInvalidate = true;
                }
                if (moveToStateNow) {
                    moveToState(fragment);
                }
            }
        }
    

当我们继续跟踪的时候就会发现，在`moveToState`方法中，`Fragment`的state是`Fragment.CREATED`，并且会执行`performCreateView()`中的`onCreateView()`方法：

        f.mContainer = container;
        f.performCreateView(f.performGetLayoutInflater(f.mSavedFragmentState), container, f.mSavedFragmentState);
    

    void performCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container,
                @Nullable Bundle savedInstanceState) {
            mChildFragmentManager.noteStateNotSaved();
            mPerformedCreateView = true;
            mViewLifecycleOwner = new FragmentViewLifecycleOwner();
            mView = onCreateView(inflater, container, savedInstanceState);
            ....
        }
    

到这里就基本结束了，我只分析了一个大概，可以了解到点击返回按钮，同样也会重新创建视图，也就是`onCreateView`会重新走一遍。

4\. 总结
======

对于`Navigation`组件的这种切换方式，我也很无奈，而且也并没有暴露出来API供我们使用其他切换方式，我也询问了很多大佬，他们也不是很清楚，也有的发现这也是`Navigation`的一个很大的诟病。那么有没有解决办法呢？很遗憾我目前还没有想到比较好的办法。

> 基于`Navigation`用来承载`Fragment`的容器是`NavHostFragment`，所以我们并不能使用`ViewPager+Fragment`的通过`setUserVisibleHint`实现懒加载的方式；同样我们也没办法使用`onHiddenChanged`的方式来实现复杂逻辑的加载；但是你可以在进入`Fragment`的时候先显示一个Loading框，加载完数据之后再渲染布局，这样的话可以减少一些尴尬。

4.1 建议
------

这里我的建议是：如果你的每个`Fragment`真的每次都需要重新绘制的话，你可以考虑使用`Navigation`组件来实现，毕竟通过`Navgation`组件真的很方便帮助我们切换导航，而且虽然布局会重新绘制，但是Google的官方Demo–[SunFlower](https://github.com/googlesamples/android-sunflower)还是使用了这种方式，所以这里面我觉得：官方推荐我们使用Jetpack组件中的**ViewModel**、**LiveData**…等，可以发现**SunFlower**demo中，即便是切换Fragmengt也不会有很明显的卡顿现象，因为每个Fragment即便重新绘制，但是View所对应的**ViewModel**还在，数据并不需要重新加载或者请求，当然这仅仅是我自己的看法啊.

但是如果你没有这种场景的话，建议还是用普通的方式我们自己来控制切换吧，这样无论是基于`Drawerlayout`还是`BottomNaivgationView`的话，我们可以自己实现切换。这块我也不是很确定哈，也希望听取大家的意见和建议。

> 我还发现一个问题，就是Play商店，现在就是这样的情况，抽屉栏中的`Item`每个基本都是重新绘制，而且第一个`Item`**我的应用和游戏**切换的时候就会有很明显的卡顿和闪屏，猜测Google play 商店具体是不是使用的`Navigation`组件不敢确定，但是它很大几率是通过`replace`方式来做的切换。感兴趣的话可以看一下，我这贴一个GIF图，不一定能看清楚，不过确实是这个效果。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS82LzI2LzE2YjkzMTIzMjZlYzRmZDY)

最后，如果有不对的地方或者更好的解决办法，可以一起讨论一下哈！