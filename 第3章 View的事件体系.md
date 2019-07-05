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

1. 同一个事件序列是指手指接触屏幕那一刻起，到手指离开屏幕那一刻结束。这个事件序列以down开始，up结束，中间有数量不定的move。
2. 一个事件序列只能被一个View拦截且消耗。
3. 某个View一旦决定拦截，那么这一个事件序列都只能由它处理，并且它的onInterceptTouchEvent不会再被调用。
4. 某个View一旦开始处理事件，如果它不消耗ACTION_DOWN事件（onTouchEvent）返回false，那么同一事件序列中的其他事件都不会再交给它来处理，并且事件将重新交给它的父元素去处理，即父元素的onTouchEvent会被调用。
5. 如果View不消耗除ACTION_DOWN以外的其他事件，那么这个点击事件会消失，父元素的onTouchEvent并不会被调用，并且当前View会持续收到后续事件，最终这些消失的点击事件会传递给Activity处理。
6. ViewGroup默认不拦截任何事件。onInterceptTouchEvent默认返回false。
7. View没有onInterceptTouchEvent方法，一旦有点击事件传递给它，onTouchEvent方法就会被调用。
8. View的onTouchEvent默认都会消耗事件（返回true），除非它是不可点击的。
9. View的enable属性不影响onTouchEvent的默认返回值。
10. onClick会发生的前提是当前View是可点击的，并且收到了down和up事件。
