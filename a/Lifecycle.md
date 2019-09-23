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
	
}
```










