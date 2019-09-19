## 入门
> DataBinding是谷歌发布的一个框架，是 **MVVM模式** 在Android上的一种实现，用于降低布局和逻辑的耦合性，是代码逻辑更加清晰。

启用方法:

1. 添加
```
android {
    dataBinding {
        enabled = true
    }
}
```
2. 打开布局文件,选中根布局,Alt+回车,点击 **Convert to data binding layout**
3. data标签里声明使用到的变量名等...
> import, alias.
4. 使用
```

@{userInfo.name}
@{userInfo.name,default=xxx}

//Activity
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    ActivityMain2Binding activityMain2Binding = DataBindingUtil.setContentView(this, R.layout.activity_main2);
    user = new User("leavesC", "123456");
    activityMain2Binding.setUserInfo(user);
}

//Fragment
public View onCreateView(@NonNull LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    FragmentBlankBinding fragmentBlankBinding = DataBindingUtil.inflate(inflater, R.layout.fragment_blank, container, false);
    fragmentBlankBinding.setHint("Hello");
    return fragmentBlankBinding.getRoot();
}

//自定义
<data class="CustomBinding">
</data>

```

## 单向数据绑定
1. *BaseObservable*
> 数据变更后UI刷新  
notifyChange() 刷新所有值域  
notifyPropertyChanged() 只会更新对应BR的flag
```
public class User extends BaseObservable {

    //如果是 public 修饰符，则可以直接在成员变量上方加上 @Bindable 注解
    @Bindable
    public String name;

    //如果是 private 修饰符，则在成员变量的 get 方法上添加 @Bindable 注解
    private String details;

    @Bindable
    public String getDetails() {
        return details;
    }
   
    public void setName(String name) {
        this.name = name;
        //只更新本字段
        notifyPropertyChanged(com.xx.BR.name);
    }

    public void setDetails(String details) {
        this.details = details;
        //更新所有字段
        notifyChange();
    }

    ...

}

//当可观察对象的属性更改时：
goods.addOnPropertyChangedCallback(new Observable.OnPropertyChangedCallback() {
    @Override
    public void onPropertyChanged(Observable sender, int propertyId) {
        if (propertyId == com.xx.BR.name) {
            Log.e(TAG, "BR.name");
        } else if (propertyId == com.xx.BR.details) {
            Log.e(TAG, "BR.details");
        } else if (propertyId == com.xx.BR._all) {
            Log.e(TAG, "BR._all");
        } else {
            Log.e(TAG, "未知");
        }
    }
});
```
2. *ObservableField*
> 官方对 BaseObservable中字段的注解和刷新等操作的封装，官方原生提供了对基本数据类型的封装  
```
public class ObservableGoods {

    private ObservableField<String> name;

    private ObservableFloat price;

    private ObservableField<String> details;

    public ObservableGoods(String name, float price, String details) {
        this.name = new ObservableField<>(name);
        this.price = new ObservableFloat(price);
        this.details = new ObservableField<>(details);
    }

    ```
}
```
3. *ObservableCollection*
> 包装类，用于替换原生的List和Map，分别是 ***ObservableList*** 和 ***ObservableMap***
```
<data>
    <import type="android.databinding.ObservableList"/>
    <import type="android.databinding.ObservableMap"/>
    <variable
        name="list"
        type="ObservableList&lt;String&gt;"/>
    <variable
        name="map"
        type="ObservableMap&lt;String,String&gt;"/>
    <variable
        name="index"
        type="int"/>
    <variable
        name="key"
        type="String"/>
</data>
android:text="@{list[index],default=xx}"/>
android:text="@{map[key],default=yy}"/>


```

## 双向数据绑定
> 数据改变刷新UI，UI改变更新数据， 例如 EditText输入内容改变。
```
android:text="@={goods.name}"
```

## 事件绑定
> 实质也是一种变量绑定，设置回调接口。  
android:onClick, onLongClick, afterTextChanged, onTextChanged
```
viewDataBinding.setUserPresenter(new UserPresenter());

@{userPresenter.afterTextChanged}
@{()->userPresenter.onUserNameClick(userInfo)}
@{userPresenter::afterTextChanged}
```

## 使用类方法
> 先定义一个静态方法，倒入
```
 <import type="com.leavesc.databinding_demo.StringUtils" />
android:text="@{StringUtils.toUpperCase(userInfo.name)}"
```

## 运算符
> 在布局文件使用如下运算符  


* 算术 + - / * %
* 二元 & | ^
* 字符串合并 +
* 逻辑 && ||
* 一元 + - ! ~
* 移位 >> >>> <<
* 比较 == > < >= <=
* Instanceof
* Grouping ()
* character, String, numeric, null
* Cast
* 方法调用
* Field 访问
* Array 访问 []
* 三元 ?:
* 空合并运算符 ?? 取第一个不为null的值作为返回值
* 属性控制 android:visibility="@{user.male  ? View.VISIBLE : View.GONE}"

## include和viewStub
> 变量共享
```
<include
    layout="@layout/view_include"
    bind:userInfo="@{userInfo}" />
```

## BindingAdapter
> 注解， 支持自定义属性，或者是修改原有属性。

## BindingConversion
> 对数据进行转换，或者进行类型转换

## Array、List、Set、Map ...
> 在布局中使用 尖括号需要转义字符
```
<String>
&lt;String&gt;
```

## 资源引用
> dimens.xml

```
<dimen name="paddingBig">190dp</dimen>
<dimen name="paddingSmall">150dp</dimen>
<string name="format">%s is %s</string>

android:paddingLeft="@{flag ? @dimen/paddingBig:@dimen/paddingSmall}"
android:text='@{@string/format("leavesC", "Ye")}'
```



#### 参考
> - [Android DataBinding 从入门到进阶](https://www.jianshu.com/p/bd9016418af2)