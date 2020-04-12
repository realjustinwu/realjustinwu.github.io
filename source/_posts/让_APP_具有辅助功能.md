---
title: Android Accessibility (1) —— 让 APP 具有辅助功能
date: 2018-9-17 21:03:25
categories: Android
tags: [Accessibility, 辅助]
blog: true
---



Android Accessibility 目标是让所有的人都能使用 App，包括盲人、色弱患者、聋人等，还有一些正常人，如正在做饭，没有第三只手来操作 APP 的人。

为了让我们的 APP 更好地支持 Accessibility，需要在 App 中进行一些处理，如一些 ImageButton 添加 label，当获取焦点的时候，Talkback  读的不仅仅是 “unlabeled, button, Double-tap to activate” 而是 “Sigin in, button, ouble-tap to activate”。

让 App 支持 Accessibility，需要下面 9 个步骤

<!-- more -->

## 1. 为 UI 元素添加 Label

给元素添加 label 之后，当元素获取焦点时，Talkback 读的内容是 “label + 元素类型 + 当前状态”，默认情况下，TextView 和 Button 的 label 就是其中的文字，通常不需要更改。其他的 UI 元素，如果能被选中，一般都需要为他添加 label。给元素添加 label 有两种方式

1. 在 XML 中添加 label

    在 XML 中添加 label，通常是给不会在运行过程中改变的元素。如登录按钮，返回按钮等。可以在 XML 中添加 `android:contentDescription="@string/back"`

2. 在 Java 代码中添加 label

    如果运行过程中内容会更改的元素，如 ListView 中的其中一个 Itam，可以在代码中获得这个 View 然后 `itemView.setContentDescription(itemTitle + " " + itemDescription)`

对于 EditText，当内容为空的时候，会吧 hint 当成 label，当内容不为空是，把内容当成 label。

如果不想让元素读 ”Double-tap to activate” 或者 “Double-tap And hold to long press”，可以分布设置 clickable 和 longClickable 属性。

## 2. 把多个元素当成一个组

一个 ListView 中的一个 item 通常有好几个 UI 元素，比如联系人列表，会包含 姓名，电话，还有一个呼叫按钮。正常情况下，我们希望整个 item 获取焦点，右滑一次就可以进入下一个 item，而不是先 focus 到姓名，然后 focus 到电话，最后 focus 到呼叫按钮。这时候需要把整个 item 当成一个组。如何一整个 item 当成一个组呢？可以在根元素 ViewGroup 中添加 `android:focusable="true"`，如下

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_below="@id/header"
    android:orientation="horizontal"
    android:focusable="true">

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_below="@id/header"
        android:layout_weight="1"
        android:orientation="vertical">

        <TextView
            android:id="@+id/contact_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Name">

        <TextView
            android:id="@+id/phone_number"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="10086">

    </LinearLayout>

    <ImageButton
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/call_icon">
</LinearLayout>
```

## 3. 自定义提示语

有时候一些提示语并不是因为元素获取焦点才发出的，比如发送消息时，需要读 “sending”，发送成功之后读 "sent"，发送失败读 "sent failed"。可以通过一些 UI 元素来发送提示语，如

```kotlin
val sendBtn = this.findViewById(R.id.send_button)
sendBtn.setOnClickListener {
    sendBtn.announceForAccessibility(getText(R.string.sending))
}
```

或者

```kotlin
val event = AccessibilityEvent.obtain(AccessibilityEvent.TYPE_ANNOUNCEMENT)
event.packageName = view.getContext().getPackageName()
event.className = view.getClass().getName()
event.source = view
event.text.add(getText(R.string.sending))
view.parent.requestSendAccessibilityEvent(view, event)
```

## 4. 启用焦点导航

1. 默认 Button, CheckBox, Clickable 对象都可以自动获得 Accessibility 焦点

2. 如果要为一个特殊的 View 设置 Accessibility 焦点
    - xml 中设置: `android:importantForAccessibility="yes"`，可以设置 no, auto, noHideDescendants(不能获取焦点，子 View 也不行)
    - 代码中设置 ViewCompat.setImportantForAccessibility(targetView, ViewCompat.IMPORTANT_FOR_ACCESSIBILITY_YES);

3. 可以控制焦点顺序
    - nextFocusDown
    - nextFocusUp
    - nextFocusLeft
    - nextFocusRight

4. 自动给某个 View 获取 Accessibility 焦点

    ```kotlin
    view.postDelayed(() -> {
        view.sendAccessibilityEvent(AccessibilityEvent.TYPE_VIEW_HOVER_ENTER)
    }, 1000)
    ```

## 5. 易于理解的导航

1. 可以使用键盘或者手势进行跳转
2. 避免让 UI 元素淡出，或者在某个时刻改变 UI 元素
3. 对于用户的每一次交互都应该给予反馈

## 6. 增大控件的触摸区域

UI 元素的触摸区域不应该小于 48dp，可以使用 padding，或者 TouchDelegate 来增加触摸区域

## 7. 提高对比度

可以使用 [Accessibility Scanner](https://play.google.com/store/apps/details?id=com.google.android.apps.accessibility.auditor&hl=zh-cn) 来检查对比度

1. 对于大于等于 18pt 的正常字体，或者大于等于 14pt 的正常字体，对比度应该在 3.0/1 以上
2. 对于小于 18pt 的正常字体，或者小于 14pt 的正常字体，对比度应该在 4.5/1 以上

## 8. 使用其他提示信息帮助识别元素

使用颜色 + 符号 + 文字标识一个按钮，而不仅仅是一个文字，或者颜色

## 9. 对于多媒体

- 提供给用户暂停、停止、修改音量等操作
- 如果视频很重要，应该提供其他格式的内容

通过上面 9 个步骤，基本上能完成大多数对 Accessibility 支持的需求。然而有一些不是很规范的，需要修改 UI 布局。还有一些自定义的 View 比较麻烦一点，比如 ListView 中添加了 Header View 和 footer View，系统默认的 Talkback 会包含这两个 View，读成 “showing item 1 to 10 of 100”，其实 1 是一个 隐藏的 header View，当然此时总数也不对，应该是 98 个。

还有一些是自定义的 View，需要重写 Accessibility API 的接口才能支持 accessibility。这个会在后面的文章中提到。

**参考**: <https://developer.android.com/guide/topics/ui/accessibility/apps>
