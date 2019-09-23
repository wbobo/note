# MVVMHabit
---

### 基础
> 基于谷歌DataBinding+LiveData+VIewModel框架为基础，整合Okhttp+RxJava+Retrofit+Glide等流行模块，加上各种原生控件自定义的BindingAdapter,让事件与数据源完美绑定的一款实用性MVVM快速开发框架。  

+ 网络请求:retrofit+okhttp+rxJava
+ 解析json数据:gson
+ 加载图片:glide
+ 管理View的生命周期:rxlifecycle
+ 结合databinding扩展UI事件:rxbinding
+ Android6.0权限申请:rxpermissions
+ 可定制的material design风格的对话框:material-dialogs

### 快速开始
1. Login为例
	* 1.1 关联ViewModel
	* 1.2 继承BaseActivity
	> 两个范型参数:ViewDataBinding，BaseViewModel。
	* 1.3 继承BaseViewModel
2. 数据绑定
	* 2.1 传统绑定
	* 2.2 自定义绑定
	* 2.3 自定义ImageView图片加载
	* 2.4 RecyclerView绑定
3. 网络请求
	* 3.1 Retrofit + Okhttp + RxJava
	* 3.2 网络拦截器
	* 3.3 Cookie管理
	* 3.4 绑定生命周期
	* 3.5 网络异常处理


### 辅助功能

1. 事件总线
	* 1.1 RxBus
	* 1.2 Messenger
2. 文件下载
3. ContainerActivity
4. 6.0权限申请
5. 图片压缩
6. 其他辅助类