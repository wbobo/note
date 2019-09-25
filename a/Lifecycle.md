## LifeCycle

> Activity和Fragment是有生命周期的，我们的很多操作需要写在生命周期的方法中，比如：下载，文件操作等，这样的方法在生命周期中越学越多，代码会越来越臃肿。LifeCycle就是用来解决这个问题的。  

+ **Lifecycle**
> Lifecycle是一个持有组件生命周期状态的信息的类，并允许其他对象观察此状态。
+ **Event**
> 生命周期事件，这些事件映射到Activity和Fragment的回调事件。
+ **State**
> 由Lifecycle对象跟踪的组件的当前状态
+ **LifecycleOwner**
> Lifecycle持有者。  
实现该接口的类持有生命周期，该接口的生命周期的改变会被注册气注册的观察者LifecycleObserver观察到并触发其对应事件。
+ **LifecycleObserver**
> Lifecycle观察者。  
可以通过实现LifecycleOwner的类addObserver方法注册，被注册后，LifecycleObserver便可以观察到LifecycleOwner的生命周期事件。




## 源码分析

```
public abstract class Lifecycle{

	//当LifecycleOwner状态改变时，LifecycleObserver将收到通知
	//例如，当LifecycleOwner是State#STARTED，observer将收到（Event#ON_CREATE，Event#ON_START）
	public abstract void addObserver(LifecycleObserver observer);

	//从list中移除
	public abstract void removeObserver(LifecycleObserver observer);

	//获得当前状态
	public abstract State getCurrentState();

	//生命周期持有者发生的Event,比如Activity的生命周期。
	public enum Event {
        ON_CREATE,
        ON_START,
        ON_RESUME,
        ON_PAUSE,
        ON_STOP,
        ON_DESTROY,
        ON_ANY
    }

    //生命周期的状态，Evet对应的State,如下图
    public enum State {
        DESTROYED,
        INITIALIZED,
        CREATED,
        STARTED,
        RESUMED;
        public boolean isAtLeast(@NonNull State state) {
            return compareTo(state) >= 0;
        }
    }
}
```
![Event与State的关系](https://upload-images.jianshu.io/upload_images/11267461-d9551bd201b26354.png?imageMogr2/auto-orient/strip|imageView2/2/w/700/format/webp)



```
public class LifecycleRegistry extends Lifecycle{

	//自定义列表，用于保存观察者，并可以在遍历期间处理删除/添加操作。
	private FastSafeIterableMap<LifecycleObserver, ObserverWithState> mObserverMap = new FastSafeIterableMap<>();

	//当前状态
	private State mState;
}
```





## SafeIterableMap
+ 用链表来实现Map接口
> get操作是通过头指针开始，一直往后找到对应key为止  
put操作是直接在尾部插入
+ 遍历时删除元素
> 每当需要遍历的时候，记下这个Iterator  
删除元素时通知所有正在遍历的Iterator  
Iterator收到通知后修正下一个元素  
记下的Iterator是弱引用，防止遍历结束后内存泄漏
> WeakHashMap可以帮助清理掉key，已经被回收的Entry(被GC的Iteratro)


## addObserver方法
1. 添加observer到SafeIterableMap中。
2. 判断是否已经添加过
3. 计算observer的State
4. Observer计数器累加
5. 重新计算observer的生命周期，并更新它。举个例子：ABCD周期，如果在C添加监听，则AB周期不会监听。生命周期持有者生命周期已经渡过，观察者也自动略过。
6. 同步
    1. 判断是否已经sync过。（通过判断SafeIterableMap中的首尾元素的state与Owner的state比较，Owner的state和Map中所有的Observer的state一致）
    2. backwardPass， forwardPass ？？？






##

[SafeIterableMap：一个能在遍历中删除元素的数据结构](https://www.jianshu.com/p/4edb7dd555e4)
