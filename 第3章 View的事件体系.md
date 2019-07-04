## 1. View的位置参数
```
width = right - left
height = bottom - top

x = left + translationX
y = top + translationY
```
## 2. TouchSlop
> TouchSlop是系统所能识别出的被认为是滑动的最小距离。

```
int slop = ViewConfiguration.get(this).getScaledTouchSlop();
```

## 3. View的滑动
* **scrollTo/scrollBy：** 操作简单，适合对View内容的滑动
* **动画：** 操作简单，主要用于没有交互的View和复杂的动画效果
* **改变布局参数：** 操作稍微复杂，适用于有交互的View

## 4. 事件分发
```
public boolean dispatchTouchEvent(MotionEvent event) {
    boolean consume =  false;
    if (onInterceptTouchEvent(event)) {
        consume = onTouchhEvent(event);
    } else {
        consume = child.dispatchTouchEvent(event);
    }
    
    return consume;
}
```

1. 同一个事件序列是指手指接触屏幕那一刻起，到手指离开屏幕那一刻结束。这个事件序列以down开始，up结束，中间有数量不定的move；
2. 一个事件序列只能被一个View拦截且消耗；
