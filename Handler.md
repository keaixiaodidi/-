# Handler 引起的内存泄露原因以及最佳解决方案
> Handler允许发送延时消息，在延时期间用户关闭了activity，该activity会泄露。因为Message持有Handler，又因为java的特性，内部类会持有外部类，使得
Activity会被handler持有，最终导致activity泄露。

解决办法：**将Handler定义成静态成员内部类，在内部持有activity的弱引用，并及时移除所有消息**。
