---
title: Android 自定义View——智能床垫
date: 2016-08-31 16:29:56
tags: [自定义View, ObjectAnimator, Path]
categories: Android
blog: true
---



实现一个如下图所示的自定义View。有点像女孩子的安尔乐。😀

{% img "/images/intelligent_bed.gif" %}

<!-- more  -->

画图如下：

{% img "/images/intelligent_bed_design.png" %}

实现过程:

1. 计算可以上下移动的四个点（如图：A, B, C, D）。
2. 使用属性动画移动点的位置。
3. 使用 Path 绘图，先绘制全部的白色区域，再绘制边边上的线。
4. 圆角使用贝塞尔曲线

### 属性动画

自定义属性动画，

```java
public class MyView {
    private int head;
    private ObjectAnimator[] mAnimators;
    private final String[] PROPERTY = {"head", "xxx", "xxx", "xxx"};
    public int getHead() {
        return head;
    }
    public void setHead(int head) {
        this.head = head;
        invalidate();  // 设置之后，刷新 view
    }
    private void initView() {
        mAnimators = new ObjectAnimator[4];
        for (int i = 0; i < 4; i++) {
            mAnimators[i] = new ObjectAnimator();
            mAnimators[i].setDuration(600);
            mAnimators[i].setTarget(this);
            mAnimators[i].setPropertyName(PROPERTY[i]);
        }
    }
    /**
     * @param position 被赋值的位置
     * @param value    -100 到 100
     */
    @Override
    public void setValue(int position, int value) {
        position &= 3;
        int toHeight = value * getHeight() / 100 / 2;
        ObjectAnimator animator = mAnimators[position];
        animator.setIntValues(toHeight);
        animator.start();
    }
}
```

### 绘图

1. 计算四个点（A, B, C, D）的横坐标。 x0,x1,x2,x3
2. 计算所有角的坐标 a,b,c,...h
3. 计算 0,1,2,3...15 点的坐标。
4. 使用二阶贝塞尔曲线和直线连接各点。
5. 线条：使用向量计算计算出点的位置。然后跟上面的操作一样。

画笔设置

```java
// 白色区域画笔
mPaint = new Paint();
mPaint.setColor(Color.WHITE);
mPaint.setStyle(Paint.Style.FILL);

// 线的画笔
mLinePaint = new Paint();
mLinePaint.setColor(getResources().getColor(R.color.mainYellow));
mLinePaint.setStyle(Paint.Style.STROKE);
mLinePaint.setStrokeWidth(LINE_WIDTH);
```



参考：[贝塞尔曲线][]

[贝塞尔曲线]: https://github.com/GcsSloop/AndroidNote/blob/master/CustomView/Advance/%5B06%5DPath_Bezier.md


