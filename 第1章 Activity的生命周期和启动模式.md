### onCreate和onRestoreInstanceState方法的其区别
> onRestoreInstanceState里的Bundle不会为空，onCreate里的Bundle有可能为空

### 7个生命周期
1. onCreate
2. onStart
3. onResume
4. onPause（onPause必须先执行完，新Activity的onResume才会执行）
5. onStop
6. onDestroy
7. onReStart


### Activity之间的通信
* Intent/Bundle
* 类静态变量
* 全局变量

### Activit将数据传递发给Fragment
* Bundle
* 直接在Activity中定义方法

### Fragment传数据给Activity
1. 在Fragent中定义内部接口
2. 在Fragment的onAttach方法中实现回调
3. 调用onDetach把传进来的对象销毁掉


### Activity与Service的数据通信
* 绑定服务，利用onServiceConnection类
* 简单通信，利用Intent进行传值
* 定义一个callback接口来监听服务中的进程的变化

### Android启动模式
* standard
> 每启动一个Activity都会创建一个新的实例，onCreate、onStart、onResume都会走
* singleTop
> 实例位于栈顶时复用，回调onNewIntent方法。应用：即时通信对话框、新闻推送
* singleInstance
> 回调onNewIntent。应用：主界面
1. 首先会根据taskAffinity去寻找当前是否存在一个对应名字的任务栈
2. 不能存在则创建一个新的task
3. 若存在，得到任务栈，在该任务栈中查找是否存在对应activity实例
* singleTask
> 具有全局唯一性（整个系统中只有一个实例）；具有独占性（任务栈只有一个实例）

## Service
#### Service和Thread的区别和应用场景
* Thread：程序执行的最小单元，分配CPU的基本单位

##### Thread的生命周期
1. 新建
2. 就绪
3. 运行
4. 死亡
5. 阻塞

**Thread的致命缺点**：无法控制

##### Service的生命周期
1. onCreate
2. onStartCommand
3. onBind
4. onUnbind
5. onDestroy

### Serializable
> 1. Java的接口
> 2. 实现简单，内存开销大

### Parcelable
> 1. Android的接口
> 2. 实现复杂，内存开销小

**内存间传递数据用Parcelable，写到磁盘用Serializable**

## 广播的静态注册和动态注册
* 静态注册
> 在AndroidManifest.xml中通过\<receiver\>标签声明<br>常驻进程中，不受组件生命周期影响<br>应用场景：需要时刻监听广播
* 动态注册
> 在代码中调用Context.registereceiver<br>跟随组件的生命周期变化<br>应用场景：需要在特定时刻接收广播



### onSaveInstanceState和onRestoreInstanceState时序
* onSaveInstanceState在onStop之前调用，和onPause没有既定的时序关系，只会出现在Activity被异常终止的情况下。
* onRestoreInstanceState在onStart之后调用

> Activity被意外终止时，Activity会调用onSaveInstanceState去保存数据，然后Activity会委托Window去保存数据，接着Window再委托它上面的顶层容器去保存数据，顶层容器是一个ViewGroup，很可能是DecorView。最后顶层容器再去一一通知它的子元素来保存数据。每个View都有onSaveInstanceState和onRestoreInstanceState这两个方法。

### Activity的优先级
1. 前台Activity--优先级最高
2. 可见但非前台的Activity
3. 后台Activit--优先级最低
> 当系统内存不足时，系统会按照上述优先级去杀死目标Activity所在进程，并在onSaveInstanceState和onRestoreInstanceState来存储和恢复数据。如果一个进程中没有四大组件在执行，那么这个进程将很快被系统杀死。因此，一些后台工作不适合脱离四大组件而独自运行在后台中，比较好的方法是将后台工作放入Service中从而保证进程有一定有优先级，这样就不会轻易地被系统杀死。

### 启动模式
1. standard：标准模式
> 谁启动了这个Activity，这个Activity就运行在启动它的那个Activity所在栈中。若用非Activity类型的Context（如ApplicationContext）启动Activity，会crash，因为非Activit的Context没有所谓的任务栈。解决问题的方法是为待启动的Activity指定FLAG_ACTIVITY_NEW_TASK标记位，这样启动时会为它创建一个新的任务栈，此时待启动Activity实际上是以singleTask模式启动的。
2. SingleTop：栈顶复用模式
> 若位于栈顶，不会被重新创建，onNewIntent会回调。不位于栈顶，则重新创建。
3. SingleTask：栈内复用模式
> 只要Activity在一个栈中存在，多次启动都不会重新创建实例。会回调onNewIntent。当一个具有SingleTask模式的Activity  A请求启动，系统会先寻找是否存在A想要的任务栈，如果不存在，则创建一个任务栈，再创建A的实例并把A方法栈中；如存在任务栈，看栈中是否有实例存在，如果有，则调到栈顶，回调onNewIntent，如果实例不存在，就创建A并压入栈中。
4. SingleInstance：单实例模式

### TaskAffinit：任务相关性
> 标志了一个Activity所需要的任务栈的名字，默认情况下，索欧Activity所需任务栈的名字为应用的包名。TaskAffnity属性主要和singleTask启动模式或allowTaskReparenting属性配对使用，在其他情况下没有意义。

###### 当TaskAffnity和allowTaskReparenting结合的时候：当一个应用A启动了应用B的某个Activity后，若Activity的allowTaskAffnity属性为true，那么当应用B被启动后，此Activity会直接从应用A的任务栈移到应用B的任务栈中。

#### 如何给Activity指定启动模式？
1. AndroidManifest中指定launchMode
2. 在Intent中设置标记位
> 第二种方式的优先级高于第一种，当两种同时存在时，以第二种方式为准。第一种方式无法直接为Activity设定FLAG_ACTIVITY_CLEAR_TOP标识，第二种方式无法为Activity指定singleInstance模式。

### Flags
* **FLAG_ACTIVITY_NEW_TASK**
> 指定为singleTask模式
* **FLAG_ACTIVITY_SINGLE_TOP**
> 指定为singleTop模式
* **FLAG_ACTIVITY_CLEAR_TOP**
> 当Activity启动时，在同一个任务战中所有位于它之上的Activity都要出栈，一般和FLAG_ACTIVITY_NEW_TASK配合使用。在这种情况下，被启动的Activity实例如果已经存在，那么系统会调用它的onNewIntent。如果被启动的Activity采用standard模式启动，那么它连同它之上Activity都要出栈，系统会创建新的Activity实例放入栈顶
* **FLAG_ACTIVITY_EXCLUDE_FROM_RECENT**
> 具有这个标记的Activity不会出现在历史Activity的列表中。等同于在XML中指定Activity的属性android:excludeFromRecent="true"。

### IntentFilter的匹配规则
> 匹配过滤表，需要同时匹配过滤列表中的action、category、data信息，否则匹配失败。一个Activity中可以有多个intent-filter，一个intent只要能匹配任何一组intent-filter即可成功启动对应的Activity。
1. action：action的匹配要求intent中的action存在且必须和过滤规则中的其中一个action相同
2. category：intent可以没有category，如果一旦有category，不管有几个，每个都要能够和过滤规则中的任何一个category相同。为了activity能接收隐式调用，就必须在intent-filter中指定“Android.intent.category.DEFAULT”这个category。
3. data：如果过滤规则中定义了data，那么intent中也要定义可匹配的data。data由两部分组成，mimeType和URI。mimeTyle指媒体类型。
