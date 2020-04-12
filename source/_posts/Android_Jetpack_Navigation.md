---
title: Android Jetpack - Navigation
date: 2018-11-8 13:24:10
categories: Android
tags: [Android Jetpack]
blog: true
---



Navigation 目的是为了简化 APP 目的地之间的导航。可视化导航，让页面间的导航一目了然。可以给页面跳转添加过度效果，让页面跳转携带的数据跟简洁。

刚开始看到这个名字的时候，以为可以用它来管理 Activity 之间的跳转，看完之后发现不是这样的，只能管理 Fragment 的跳转。

Navigation 需要在 Activity 中添加一个 NavHostFragment，所有的跳转都在由这个 Fragment 管理，也就是根本出不了 Activity。当然也可以跳到其他的 Activity，跳出去之后就不归 Navigation 管了。

如果要更好的使用 Navigation，需要把多 Activity 的运用，简化成单个 Activity 的运用😢。

<!-- more -->

## 原则

1. 有一个固定的起始位置，退出的时候也应该从这个页面退出
    > 登录页不能作为起始位置

2. 用栈来表示运用的“导航状态”

3. “向上”按钮永远不会退出应用
    > 起点界面中不应该出现向上按钮。
    > 当应用是通过其他应用使用 deeplink 的方式启动时，向上按钮应该将用户带回上层界面而不是当时启动此应用的其他应用。

4. Up和Back在应用程序任务中是等效的
    > 当前用户不处于起点界面，这个时候系统返回键就不会退出应用。

5. DeepLink或者Navigate至相同界面生成相同的堆栈
    > deeplink 启动之后，清除已有导航栈，取而代之的是deeplink的导航栈。
    > 1. 如果 destination 是 start destination，则栈中只有 start destination
    > 2. 如果 destination 不是 start destination，则栈是 start destination + 目标 destination

## 使用Navigation架构组件实现导航

1. Navigation架构组件的目标类型：
    - Fragment - 通常是 Fragment
    - Activity
    - 导航图和子图 - 当目标是导航图或子图时，导航到该图或子图的起始目标
    - 自定义目标类型

2. 配置

    1. Android Studio 必须是 3.2 版本以上，Settings > Experimental > Enable Navigation Editor
    2. 添加依赖

        ```gradle
        // module build.gradle
        dependencies {
            implementation "android.arch.navigation:navigation-fragment-ktx:$nav_version"
            implementation "android.arch.navigation:navigation-ui-ktx:$nav_version"
            androidTestImplementation "android.arch.navigation:navigation-testing-ktx:$nav_version"
        }
        // ## For safe arguments ##
        // project build.gradle
        buildscript{
            dependencies{
                classpath 'android.arch.navigation:navigation-safe-args-gradle-plugin:1.0.0-alpha06'
            }
        }
        // module build.gradle
        apply plugin: "androidx.navigation.safeargs"
        ```

3. 配置 navigation 资源文件

    1. 在 res 目录上， New > Android Resource File > 输入文件名 > 选择文件类型 Navigation

    2. 添加目的地: 视图编辑器 > + 号 > Create blank destination; 或者选择已有的 Fragent& Activity

    3. 在试图编辑器中选中目标之后，可以修改属性
        - Type: 类型 Activity/Fragment 不可改
        - id: 用于指定一个目的地
        - Class: 目的地的 class 类型
        - label: 定义名称
        - layout: 用于目的地的预览显示

    4. 建立目的地之间的链接

        选中目标，拖动右侧的线，可以建立链接

    5. 选中链接线或者目的地，可以修改跳转的动画等

    6. 选中目的地，在右侧面板，可以设置为“起始目的地”

    7. 把多个 destinations 合并成一个 navigation graph

        ctrl + 点击多个，右击 =》 Move to Nested Graph

    8. 把上面的 Nested Graph 移动到另一个文件，使用 `<include>` 引入

4. 让 Activity 管理 Navigation

    Activity 需要在布局文件中添加 NavHost 的实现类，如 NavHostFragment 去管理导航，如下布局

    ```xml
    <fragment
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/my_nav_host_fragment"
        android:name="androidx.navigation.fragment.NavHostFragment"
        app:navGraph="@navigation/nav_graph"
        app:defaultNavHost="true" // 会重写返回行为，也可以从写 onSupportNavigateUp() 自己实现
                                  // findNavController(R.id.nav_host_fragment).navigateUp()
        />
    ```

    代码中创建 NavHostFragment

    ```kotlin
    val finalHost = NavHostFragment.create(R.navigation.example_graph)
    supportFragmentManager.beginTransaction()
        .replace(R.id.nav_host, finalHost)
        .setPrimaryNavigationFragment(finalHost) // 相当于设置 app:defaultNavHost="true"
        .commit()
    ```

5. 获取 NavController，并进行跳转

    1. 获取 NavController，可以通过下面的 3 中方式

        1. NavHostFragment.findNavController(Fragment)
        2. Navigation.findNavController(Activity, @IdRes int viewId)
        3. Navigation.findNavController(View)  // 从父容器中查找

        Kotlin 中给 View 添加了一个方法 finNavController() 同第 3 种方式

    2. 页面跳转

        使用 NavController.navigete(id) 跳转，Navigation 是一个工具类，封装了一些好用的方法，如下

        `button.setOnclickListener(Navigation.createNavigationOnClickLisntener(R.id.next_fragment_action, null))`

    3. 使用 bundle 传递数据

        ```kotlin
        // 携带数据
        var bundle = bundleOf("amount" to amount)
        view.findNavController().navigate(R.id.confirmationAction, bundle)
        // 获取数据
        val tv = view.findViewById(R.id.textViewAmount)
        tv.text = arguments.getString("amount")
        ```

    4. 使用类型安全的方式传递数据

        需要添加插件 `apply plugin: 'androidx.navigation.safeargs'`

        ```kotlin
        /// ???  use ConfirmationFragmentArgs.Builder() ?
        override fun onClick(v: View?) {
            val amountTv: EditText = view!!.findViewById(R.id.editTextAmount)
            val amount = amountTv.text.toString().toInt()
            val action = SpecifyAmountFragmentDirections.confirmationAction(amount)
            action.amount = amount
            v.findNavController().navigate(action)
        }
        override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
            val tv: TextView = view.findViewById(R.id.textViewAmount)
            val amount = ConfirmationFragmentArgs.fromBundle(arguments).amount
            tv.text = amount.toString()
        }
        ```

6. Deep link 到一个目的地

    在 Android Studio 3.1 一下，需要自己在 manifest.xml 中添加 intent-filter 标签。3.2 以后添加下面的标签

    ```xml
    <activity name=".MainActivity">
        <nav-graph androdi:value="@navigation/main_nav" />
    </activity>
    // main_nav
    <fragment
        android:id="@+id/deeplink_dest"
        android:name="com.example.android.codelabs.navigation.DeepLinkFragment"
        tools:layout="@layout/deeplink_fragment">
        <argument
            android:name="myarg"
            android:defaultValue="Android!"/>
        <deepLink app:uri="www.example.com/{myarg}" />
    </fragment>
    ```

    通过 NavController 创建 deeplinkIntent，他会找到 nav-graph 对应的 Activity，然后使用 destination 找到对应的目的地。
    也可以使用 NavDeepLinkBuilder 直接创建 intent

    ```kotlin
    // 创建一个 pending Intent
    val deeplink = findNavController().createDeepLink()
            //.setGraph(R.id.mobile_navigation)  如果没有设置，就查找当前的
            .setDestination(R.id.deeplink_dest)
            .setArguments(args)
            .createPendingIntent()
    // 或者直接跳转到目的地
    findNavController().createDeepLink()
                    .setDestination(R.id.home_dest)
                    .setArguments(args)
                    .createTaskStackBuilder().startActivities()
    // 通过 NavDeepLinkBuilder deeplink
    val deeplink = NavDeepLinkBuilder(context)
            .setGraph(R.navigation.mobile_navigation)
            .setDestination(R.id.android)
            .setArguments(args)
            .createPendingIntent()
    // 通过网页 http://www.example.com/124 直接跳转
    ```

7. 共享元素

    ```xml
    // 第一个 fragment
    <ImageView
        android:id="@+id/image"
        android:transitionName="image"/>
    // 第二个 fragment
    <ImageView
        android:id="@+id/image"
        android:transitionName="image"/>
    ```

    ```kotlin
    // 第一个 fragment
    val imageView = view.findViewById<ImageView>(R.id.image)
    val extras = FragmentNavigatorExtras(imageView to "image")
    Navigation.findNavController(it).navigate(R.id.next_action, null, null, extras)
    // 第二个 Fragment
    override fun onCreateView() {
        sharedElementEnterTransition = ChangeBounds().apply { duration = 750 }
        sharedElementReturnTransition= ChangeBounds().apply { duration = 750 }
    }
    ```

8. NavHostFragment 之外的元素

    1. 监听 destination 改变

        ```kt
        navController.addOnNavigatedListener { navController, destination ->
            textView.setText(destination.label)
        }
        ```

    2. 使用 BottomNavigationView 控制 Navigation

        ```xml
        // layout
        <com.google.android.material.bottomnavigation.BottomNavigationView
            android:id="@+id/bottom_nav_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:menu="@menu/bottom_nav_menu" />
        // bottom_nav_menu
        <menu xmlns:android="http://schemas.android.com/apk/res/android">
            <item
                android:id="@id/home_dest"
                android:icon="@drawable/ic_home"
                android:title="@string/home" />
            <item
                android:id="@id/deeplink_dest"
                android:icon="@drawable/ic_android"
                android:title="@string/deeplink" />
        </menu>
        ```

        ```kotlin
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottom_nav_view)
        bottomNav?.setupWithNavController(navController)  // 把 navController 设置给 bottomNavigationView
        ```

        点击 item 的时候，如果 navigation 文件中，有跟 item ID 相同的 destination id 就会跳转到这个 destination。
