# 第三章 View 的事件体系

## 3.1 View 的基础知识

### 3.1.1 什么是 View

View 是 Android 中所有控件的基类，包括 ViewGroup。

### 3.1.2 View 的位置参数
 
* top、left、right、bottom 表示相对于父容器的计算坐标
* x，y 是 View 相对于父容器的左上角实际坐标
* translationX、translationY 实际坐标相对于计算坐标的偏移量

几个计算公式
```
width = right - left
height = bottom - top

x = left + translationX
y = bottom + translationY
```

### 3.1.3 MotionEvent 和 TouchSlop

#### 1. MotionEvent
MotionEvent 表示了手指接触屏幕的一系列动作事件。

常见的事件类型有：ACTION_DOWN、ACTION_MOVE、ACTION_UP，分别表示按下，移动，抬起的动作，DOWN 和 UP 之间可以有若干个 MOVE。

获取点击事件的坐标有两个方法：getX、getY 表示相对于当前 View 的坐标；getRawX、getRawY 表示相对于屏幕左上角的坐标。

#### 2. TouchSlop
TouchSlop 表示系统能辨识被认为是滑动操作的最小距离。使用 ViewConfiguration.get(this).getScaledPagingTouchSlop() 可以获取这个常量。

### 3.1.4 VelocityTracker、GestureDetector 和 Scroller

#### 1. VelocityTracker
VelocityTracker 用于追踪手指在滑动过程中的速度，使用例子

```
//初始化
VelocityTracker tracker=VelocityTracker.obtain();
//添加滑动事件
tracker.addMovement(motionEvent);
//计算速度
tracker.computeCurrentVelocity(1000);
float xVelocity=tracker.getXVelocity();
float yVelocity=tracker.getYVelocity();
//回收
tracker.clear();
tracker.recycle();
```
computeCurrentVelocity 设置的是一个时间间隔，getXVelocity 和 getYVelocity 获取的是在这个时间间隔划过的像素数量。

#### 2. GestureDetector
手势检测，用于检测单击，滑动，长按，双击等行为。使用方法：

```
final GestureDetector detector=new GestureDetector(MainActivity.this, new GestureDetector.OnGestureListener() {
        @Override
        ...
    });
    detector.setIsLongpressEnabled(false);
    detector.setOnDoubleTapListener(new GestureDetector.OnDoubleTapListener() {
        @Override
        ...
});

findViewById(R.id.text1).setOnTouchListener(new View.OnTouchListener() {
    @Override
    public boolean onTouch(View view, MotionEvent motionEvent) {
        return detector.onTouchEvent(motionEvent);
    }
});
```
方法介绍：

![](https://ws1.sinaimg.cn/mw690/64ddfd2bgy1fp9uyhda5zj20tf08ytd3.jpg)
![](https://ws1.sinaimg.cn/mw690/64ddfd2bgy1fp9v04qki9j20tn0aiwjf.jpg)

使用建议：如果监听滑动相关自己在 onTouchEvent 中实现，如果要监听双击就使用 GestureDetector。
#### 3. Scroller
见 3.3.1

## 3.2 View 的滑动

### 3.2.1 使用 scrollTo/scrollBy
这两个方法是 View 中的方法，View 中除 background 之外的内容多会发生偏移，包括其中包括的子 View。

### 3.2.2 使用动画
可以通过 translationX 和 translationY 来移动 View ，也可以使用补间动画和属性动画来实现移动 View。这种方式可以把整个 View 进行平移。

具体查看第七章。

### 3.2.3 改变布局参数
通过设置 View 中 LayoutParams 的 marginLeft 参数，来移动 View。这种方式会导致 View 的布局重新计算，有可能会导致 View 的大小也发生改变。

## 3.3 弹性滑动
### 3.3.1 使用 Scroller
Scroller 是一个计算坐标的工具类，要使用他需要在 View 中实现 computeScroll 并且自己实现操作方法。他的原理就是通过计算坐标改变 onDrow 等一系列绘制方法的坐标，移动效果跟 scrollTo/scrollBy 相同。

示例：

```
public class ScrollerView extends View {

    Scroller mScroller=new Scroller(getContext());

    public ScrollerView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    /**
     * 自定义的接口
     * @param destX
     */
    public void smoothScrollXTo(int destX){
        int scrollX=getScrollX();
        int deltaX=destX-scrollX;

        mScroller.startScroll(scrollX,0,deltaX,0,1000);
        invalidate();
    }
    
    @Override
    public void computeScroll() {
        if (mScroller.computeScrollOffset()){
            scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
            postInvalidate();
        }
        
    }
}
```
### 3.3.2 通过动画

在 ValueAnimator 中设置监听控制移动过程中的位置，效果跟 scrollTo 相同。


```
final int startX=0;
final int delatX=100;

final ValueAnimator animator= ValueAnimator.ofInt(0,1).setDuration(1000);
animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
    @Override
    public void onAnimationUpdate(ValueAnimator valueAnimator) {
        float f=animator.getAnimatedFraction();
        button1.scrollTo(startX+(int)(delatX*f),0);
    }
});
animator.start();
```
### 3.3.3 使用延时策略

可以通过使用 Handler、View 的 postdelayed 方法或者线程的 sleep 方法等一切可以实现定时的方式，然后后在特定时间使用 scrollTo 或 scrollBy 方法类实现弹性移动。

##### postdelayed

## 3.4 View 的事件分发机制

### 3.4.1 点击事件的传递规则

##### 三个方法

* dispatchTouchEvent 事件分发，返回结果受当前 View 的 onTouchEvent 和 下级 View 的 dispatchTouchEvent 影响，表示是否消耗当前事件。
* onInterceptTouchEvent 事件拦截，只存在于 ViewGroup 中，返回结果表示是否拦截某事件。如果当前 View 拦截了某个事件，拦截后将调用当前 View 的 onTouchEvent 方法，且同一个事件序列中此方法将不再被调用。
* onTouchEvent 事件处理，返回结果表示是否消耗当前事件。如果事件没有被消耗，则同一时间序列中此方法将不再被调用。

三者的关系用伪代码表示：

```
public boolean dispatchTouchEvent(MotionEvent ev) {
        boolean consume=false;
        if (onInterceptTouchEvent(ev)){
            consume=onTouchEvent(ev);
        }else {
            consume=child.dispatchTouchEvent(ev);
        }
        return consume;
    }
```

##### 默认值

* ViewGroup 默认不拦截任何事件
* 一个 View 中 clickable 和 longClickable 如果有一个为 true，则其 onTouchEvent 默认返回 true。
* View 的 enable 属性不影响 onTouchEvent 默认返回值

##### 事件序列
事件序列是指从手指接触屏幕到离开屏幕这个过程中的一系列事件，由一个 down 事件和一个 up 事件以及中间的若干个 move 事件组成。

* 一个 View 一旦决定拦截，那么整个事件序列都将交给他去处理，并且他的 onInterceptTouchEvent 不会再被调用。

* 一个 View 一旦开始处理某个事件序列，如果不消耗 ACTION_DOWN (onTouchEvent 返回了 false)，那么同一序列的其他事件将交给其父元素处理，即父元素的 onTouchEvent 会被调用。

* 一个 View 消耗了 ACTION_DOWN 时间，但是不消耗其他事件，那么不会调其父元素的 onTouchEvent 事件，而是直接调用 Activity 的 onTouchEvent 事件

##### requestDisallowInterceptTouchEvent
requestDisallowInterceptTouchEvent 可以在子元素中干预父元素的分发过程，要求父元素跳过自身的 onInterceptTouchEvent 事件。只在本事件序列有效。

```
getParent().requestDisallowInterceptTouchEvent(true);
```
### 3.4.2 事件分发的源码解析
略

## 3.5 View 的滑动冲突



