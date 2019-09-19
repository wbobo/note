***标准写法***


```
public class DemoDialog extends DialogFragment {

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //得到各种外部参数
    }

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        return null;
    }

    @NonNull
    @Override
    public Dialog onCreateDialog(@Nullable Bundle savedInstanceState) {
        // 根据参数建立dialog
        return new AlertDialog.Builder(getActivity())
                .setTitle("title")
                .setMessage("message")
                .create();
    }

    @Override
    public void setupDialog(@NonNull Dialog dialog, int style) {
        super.setupDialog(dialog, style);
        // 进一步配置
    }

    @Override
    public void onStart() {
        super.onStart();

        // 这里的view来自于onCreateView，所以是null，不要使用
        View view = getView();

        // 可以在这里进行dialog的findViewById操作

        Window window = getDialog().getWindow();
        view = window.getDecorView();

    }
}
```

#### context
\\|Application|Activity|Service|ContentProvider|BroadcastReceiver
---|:--:|:--:|:--:|:--:|---:
显示Dialog|NO|Y|NO|NO|NO
(1)启动Activity|NO|Y|NO|NO|NO
(2)Layout Inflation|NO|Y|NO|NO|NO
启动Service|Y|Y|Y|Y|Y
绑定Service|Y|Y|Y|Y|NO
发送Broadcast|Y|Y|Y|Y|Y
(3)注册Broadcast|Y|Y|Y|Y|NO
加载资源文件|Y|Y|Y|Y|Y

关于NO上标的解释：

* 数字2：在这些类中去inflate布局文件是合法的，但会使用系统默认的主题样式
* 数字1：这些类中是可以启动activity，但是需要创建一个新的task，一般不推荐
* 数字3：在receiver为null时仍旧允许发送广播，但我们一般不会这么使用



#### 可全局弹出的Dialog
> 思路：让application持有当前activity的引用, 传入activity即可弹出dialog.




#### 参考资源
> * [Android Context 上下文 你必须知道的一切](https://link.juejin.im/?target=https%3A%2F%2Fblog.csdn.net%2Flmj623565791%2Farticle%2Fdetails%2F40481055)
* [让AlertDialog为我所用](https://juejin.im/post/595afcac6fb9a06b9c7411e2)
* [EasyDialog](https://github.com/tianzhijiexian/EasyDialog)