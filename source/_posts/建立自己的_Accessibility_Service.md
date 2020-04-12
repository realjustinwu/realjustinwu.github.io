---
title: Android Accessibility (2) —— 创建自己的 Accessibility Service
date: 2018-10-7 20:27:00
categories: Android
tags: [Accessibility, 辅助]
blog: true
---



> 在分析 Accessibility Service 源码之前，我们先看一下 Accessibility Service 是如何创建的，为后续继续分析源码做准备。[参考文档](https://developer.android.com/guide/topics/ui/accessibility/services)

Android 系统给我们提供了标准的 accessibility 服务，如 Talkback 和 Select to Speak。一些 APP 也为我们提供了 Accessibility 功能，如 绿色保护、MacroDroid、Tasker、Vysor 等，当然我们也可以自定义我们自己的服务。

## 初始化

1. 声明 Accessibility 服务

    <!-- more -->

    声明一个 Service 并添加到 Manifest 文件中，需要权限和 filter

    ```xml
    <service
        android:name=".MyAccessibilityService"
        android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE">
        ...
        <intent-filter>
            <action android:name="android.accessibilityservice.AccessibilityService" />
        </intent-filter>
        <!-- 设置 configuration -->
        <meta-data
            android:name="android:accessibilityservice"
            android:resource="@xml/accessibility" />
    </service>
    ```

2. 添加 accessibility 配置文件

    ```xml
    <accessibility-service xmlns:android="http://schemas.android.com/apk/res/android"
        android:description="@string/accessibility_service_description"
        android:packageNames="com.example.android.apis"
        android:accessibilityEventTypes="typeAllMask"
        android:accessibilityFlags="flagDefault"
        android:accessibilityFeedbackType="feedbackSpoken"
        android:notificationTimeout="100"
        android:canRetrieveWindowContent="true"
        android:settingsActivity="com.example.android.accessibility.ServiceSettingsActivity"
    />
    ```

3. 参数说明

| 字段名 | 字段说明 |
    | -- | -- |
    | android:packageNames | 关注哪些应用发出的事件，多个应用包名之间用逗号分隔，如果不填，则关注手机上所有应用发出的事件 |
    | android:accessibilityEventType | 关注的事件类型，TYPE_VIEW_FOCUSED，TYPE_WINDOW_STATE_CHANGED，TYPE_NOTIFICATION_STATE_CHANGED 等 |
    | android:accessibilityFlags | 辅助服务额外的flag信息。例如FLAG_REPORT_VIEW_IDS可以使回调的事件带上view的ID |
    | android:accessibilityFeedbackType | 事件的反馈类型，例如声音反馈、触觉反馈、视觉反馈等。 |
    | android:notificationTimeout | 两个同样类型的辅助事件发给辅助服务的最小时间间隔 |
    | android:canRetrieveWindowContent | 是否可以获取窗口内容 |
    | android:description | 打开 Accessibility 之后的描述信息 |
    | android:settingsActivity | 打开 Accessibility 之后，点击 Settings 跳转的 Activity |

## 实现 AccessibilityService

1. 需要重写 AccessibilityService 的方法

    1. onServiceConnected() 可选。系统启动这个 Service 的时候，可以在这里调用setServiceInfo()进行配置AccessibilityServiceInfo
    2. onAccessibilityEvent() 必选。接收系统发来的 AccessibilityEvent
    3. onInterrupt() 必选。系统想要中断 AccessibilityService 返给的响应时会调用。在整个生命周期里会被调用多次。
    4. onUnbind() 可选。系统关闭这个 Service 的时候，做一些释放工作

2. 过滤 Accessibility 事件

    1. 过滤需要的事件，可以通过两种方式
        - 包名: 通过 android:packageNames 中定义
        - 事件类型: 通过 android:accessibilityEventTypes 设置

    2. 注意： 系统会把 accessibility 事件发送给多个 Service，如果他们的 feedback Types 不一样，如果是一样的，会发送给先注册的那个

3. 设置音量

    ```kotlin
    private val mAudioManager = getSystemService(AUDIO_SERVICE) as AudioManager
    override fun onAccessibilityEvent(accessibilityEvent: AccessibilityEvent) {
        if (accessibilityEvent.source.text == "Increase volume") {
            mAudioManager.adjustStreamVolume(AudioManager.STREAM_ACCESSIBILITY, ADJUST_RAISE, 0)
        }
    }
    ```

4. 使用 Accessibility 按钮(在 Navigation Bar 右侧, Android 8.0)

    可以在 Service 启动的时候，检查并监听这个按钮。可以查看 Select to Speck 这个 Accessibility Service

    ```kotlin
    override fun onServiceConnected() {
        if (accessibilityButtonController?.isAccessibilityButtonAvailable) { // 需要判断能否注册这个 button
            registerAccessibilityButton();
        } else {
            // Can't register
        }
    }

    private fun registerAccessibilityButton() {
        accessibilityButtonController?.registerAccessibilityButtonCallback(
            object: AccessibilityButtonCallback() {
                override fun onClicked(controller: accessibilityButtonController) {
                    // 被点击了
                }
                override fun onAvailablityChanged(controller: accessibilityButtonController, available: Boolean) {
                    if (controller == accessibilityButtonController) {
                    }
                }
            }, null)
    }
    ```

5. 获取指纹识别手势

    如果要使用这个功能，应该提供给用户开关控制这个功能。如果多个 Accessibility Service 都使用指纹手势功能，可能导致冲突。

    ```xml
    <accessibility-service xmlns:android="http://schemas.android.com/apk/res/android"
    ...
    android:accessibilityFlags=" ... |flagRequestFingerprintGestures"
    android:canRequestFingerprintGestures="true"
    ... />
    ```

    ```kotlin
    override fun onCreate() {
        mGestureController = fingerprintGestureController
        mIsGestureDetectionAvailable = mGestureController?.isGestureDetectionAvailable ?: false
    }
    override fun onServiceConnected() {
        if (mFingerprintGestureCallback != null || !mIsGestureDetectionAvailable) return
        mFingerprintGestureCallback =
                object : FingerprintGestureController.FingerprintGestureCallback() {
                    override fun onGestureDetected(gesture: Int) {
                        when (gesture) {
                            FINGERPRINT_GESTURE_SWIPE_DOWN -> moveGameCursorDown()
                            FINGERPRINT_GESTURE_SWIPE_LEFT -> moveGameCursorLeft()
                            FINGERPRINT_GESTURE_SWIPE_RIGHT -> moveGameCursorRight()
                            FINGERPRINT_GESTURE_SWIPE_UP -> moveGameCursorUp()
                            else -> Log.e(MY_APP_TAG, "Error: Unknown gesture type detected!")
                        }
                    }
                    override fun onGestureDetectionAvailabilityChanged(available: Boolean) {
                        mIsGestureDetectionAvailable = available
                    }
                }
        mFingerprintGestureCallback?.also {
            mGestureController?.registerFingerprintGestureCallback(it, null)
        }
    }
    ```

6. 多国语言的支持

7. 监听手势(Android 4.1)

    设置 `serviceInfo.flags = AccessibilityServiceInfo.FLAG_REQUEST_TOUCH_EXPLORTATION_MODE`

8. 执行手势操作

    右滑然后下滑
    ```kotlin
    private fun doRightThenDownDrag() {
        val dragRightPath = Path().apply { moveTo(200, 200) lineTo(400, 200) }
        val rightDuration = 500L
        val dragDownPath = Path().apply { moveTo(400, 200) lineTo(400, 400) }
        val downtDuration = 500L
        val rightThenDownDrag = GestureDexcription.StrokeDescription(dragRightPath, 0L, rightDuration, true).apply {
            continueStroke(dragDownPath, rightDuration, downtDuration, false)
        }
    }
    ```

9. 使用 Accessibility 动作(Android 4.1)

    使用  performAction() 执行特殊动作。
    ```kt
    override fun onAccessibilityEvent(event: AccessibilityEvent) {
        event.source?.apply {
            // 使用 event 和 层级信息 source 决定执行啥动作
            performAction(AccessibilityNodeInfo.ACTION_SCROLL_FORWARD)
            // 也可以使用 performGlobalAction() 执行全局操作，如跳转到首页
            recycle()
        }
    }
    ```

### 其他方法

1. 使用焦点

    Accessibility 焦点跟输入焦点是不一样的。Accessibility 焦点是为了让 Accessibility 能能够使用非选中的点。使用 `AccessibilityNodeInfo.findFocus()` 或者 `focusSearch()` 查找元素，然后使用 `perfomAction(ACTION_SET_ACCESSIBILITY_FOCUS)` 获取焦点

2. 获取布局文件

    使用 `AccessibilityEvent.getSource()` 获取布局层级，需要设置 `canRetrieveWindowContent = true`

3. 获取焦点文件

    使用 `AccessibilityNodeInfo.getChild()` 和 `getParent()` 获取焦点元素

4. 获取 Hint Text

    `getHintText()` 即使 hint 没有显示也能获取到

5. 获取元素的位置

    使用 refreshWithExtraData(EXTRA_DATA_TEXT_CHARACTER_LOCATION_KEY, resultBundle)
