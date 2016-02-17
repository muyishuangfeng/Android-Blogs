# Activity的生命周期和启动模式
这篇文章介绍activity的生命周期和启动模式，尽量使用简洁的语言，让读者在短时间内回顾或者学习生命周期和启动模式的关键性知识点。

----------------------------
## 生命周期
---------------
### 1.三对方法
- onCreate和onDestroy，标识activity的创建和销毁
- onStart和onStop，标识屏幕的点亮和熄灭或用户操作(是否可见)
- onResume和onPause，标识屏幕的点亮和熄灭或用户操作(是否位于前台)

### 2.不能在onPause中执行重量级操作
因为在activity跳转中，只有第一个activity执行onPause后，第二个activity才会执行onCreate-onStart-onResume，然后第一个activity执行onStop.

### 3.重写onSaveInstanceState方法
通过bundle来保存数据,在onRestoreInstanceState中恢复数据。
有以下两种情况导致Activity被销毁：

1. 资源相关的系统配置发生改变导致Activity被杀死并重新创建
2. 资源内存不足导致低优先级的Activity被杀死

    在Activity中添加了 android:configChanges属性，目的是当所指定属性(Configuration Changes)发生改变时，通知程序调用onConfigurationChanged()函数（可防止activity被销毁重建）。 创建一个Layout UI。

## Activity的启动模式（LaunchMode）
---------------------------------
### 四种启动模式
>activity被放入了"先进后出"的任务栈中

1. standard模式

    这种模式的activity只能被Context启动，如ApplicationContext就不行。解决方法是为待启动activity指定FLAG_ACTIVITY_NEW_TASK标记位，这样启动的时候就会为它创建一个新的任务栈，这个时候待启动activity实际上是以singleTask模式启动的。
    
2. singleTop

    栈顶复用模式。系统调用onNewIntent方法。

3. singleTask

    栈内复用模式。系统调用onNewIntent方法。在该启动模式的activity所需要的栈（由TaskAffinity决定）中，只存在一个该activity的实例。
    

4. singleInstance

    单实例模式。

#### TaskAffinity

- 当TaskAffinity和singleTask启动模式配对使用的时候，它是具有该模式的activity的目前任务栈的名字，待启动的activity会运行在名字和TaskAffinity相同的任务栈中。
- 当TaskAffinity和allowTaskReparenting结合的时候，这种情况比较复杂，会产生特殊的效果。当一个应用A启动了应用B的某个activity后，如果这个activity的allowTaskReparenting属性为true的话，那么当应用B被启动后，此activity会直接从应用A的任务栈转移到应用B的任务栈中。