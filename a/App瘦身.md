## 基础

#### apk组成
- asserts
> 存放配置文件或资源文件
- lib
> 各种so文件，微信只支持了armeabi-v7a
- res
> r: 资源文件，包括图片，字符串等。  
resources.arsc: 编译后的二进制资源文件，id-name-value的map

	id|name|default
	---|:--:|---:
	0x7f070000|a|r/ad/a.xml

- dex
> 一个dex文件最多支持65536个方法
- META-INF
> 可以参考[《Android APK 签名文件MANIFEST.MF、CERT.SF、CERT.RSA分析》](https://link.jianshu.com/?t=http://www.blogfshare.com/android-apk-sign.html)
- androidManifest
> ...

## 优化assets

- 删除无用字体
> 考虑中文字体的利用率，删除不使用的文字，利用工具[FontZip](https://github.com/forJrking/FontZip).
- 减少icon-font的使用
> 与svg重叠
- 动态下载资源
> 字体,js代码，一并下载解压使用。
- 压缩资源文件
> 必须携带的资源可以压缩，使用的时候解压使用

## 优化lib

- 配置abiFilters
> 硬件设备对应一个架构，只需要适配其中一个即可，现在主流的是 armeabi-v7a  
ndk {
	abiFilters "armeabi", "armeabi-v7a" ,"x86"
}
- 分析用户手机的cpu
> 根据实际情况排除so文件
- 避免复制so
> ...

## 优化resources.arsc

- 删除无用的资源映射
> 会频繁使用id，不建议压缩该文件。  
可以删除不必要的 <font color=red>string entry</font>, 工具：[android-arscblamer](https://link.jianshu.com/?t=https://github.com/google/android-arscblamer)
- 资源名混淆
> 编码方式，id数量，减少命名长度， 工具： 微信的 [AndResGuard](https://link.jianshu.com/?t=https://github.com/shwenzhang/AndResGuard)

## 优化META-INF
> 混淆资源名，可以减少 \*.sf 和 \*.MF 的大小，\*.RSA不能压缩

## 优化res

- 通过AS删除无用资源
- 打包时剔除无用资源
> shrinkResource true
- 删除无用的语言
> 国内APP推荐只支持中文
```
android {

    //...
    
    defaultConfig {
        resConfigs "zh"
    }
}
```
- 控制raw中资源的大小
> ogg适合做音效的格式

- 统一应用风格,减少shape文件
> 统一颜色，按钮形状，圆角，阴影等。。。  
style继承，规范等。。。

- 使用toolbar，减少menu文件
- 限制灵活性，减少layout文件
	+ 复用layout
	> 建立一个list_layout.xml, include它...
	+ 融合layout  
		* [UiBlock](https://link.jianshu.com/?t=https://github.com/tianzhijiexian/UiBlock/)
- 动态下载图片
> 贴纸，表情这类图片资源，强烈建议在线获取

- 放置不同分辨率的图片
	* 聊天表情出一套放hdpi中
	* 纯色小icon用svg做
	* 背景等大图，出一套房xhdpi中
	* logo等权重较大的图片可以出两套，hdpi和xhdpi
	* 如果某些图片在真机中异常，就出多套
	* 奇葩机型，针对出图

## 优化图片

- 使用VectorDrawable
> VD->WebP->Png->JPG
    + 纯色icon，使用svg
    + 两种颜色以上的icon,使用WebP
    + WebP无法达到效果，使用png
    + 没有alpha通道，可以使用jpg
- svg使用技巧
    + 设置恰当的宽度
    > 如果复用设置成TextView中drawable的宽度
    + 利用padding和scaleType属性
    > 铺满，压缩，原样，比例压缩
- webP使用技巧
> png转webp : [智图](https://link.jianshu.com/?t=http://zhitu.isux.us/)或[isparta](https://link.jianshu.com/?t=http://isparta.github.io/)
- 复用图片
> 通过svg可以让一张图片适应不同大小的容器，比如关闭的"x"图标。
- 使用Tint着色器
> TintMode: add, multiply, screen, srcatop, srcin, src_over
- 复用按压效果
    + 列表由LinearLayout、RecyclerView组成
    + 分割线用统一的shape进行绘制，不用切图
    + 整个列表背景设置为白色
    + item的背景是一个selector文件，正常颜色透明，按下颜色灰色
- 通过旋转来复用
- 压缩图片
    1. 优先压缩大图，后小图
    2. 不压缩点9图
    3. 开屏大图压缩需要设计确认
    4. 特别大图50k+考虑有损压缩
    > 量化两张图片在视觉上的差别，可以尝试[butteraugli](https://link.jianshu.com/?t=https://github.com/google/butteraugli)

    + [ImageOptim](https://link.jianshu.com/?t=https://imageoptim.com/mac)
    + [pngquant](https://link.jianshu.com/?t=https://pngquant.org/)
    + [tinypng](https://link.jianshu.com/?t=https://tinypng.com/)
    > [gradle插件](https://link.jianshu.com/?t=https://github.com/mogujie/TinyPIC_Gradle_Plugin)
    + 关闭aapt的图片压缩  

```
    android {  
    defaultConfig {  
        //...
    }  

    aaptOptions {  
        cruncherEnabled = false 
    }  
} 
```

## 优化dex

- 记录方法数和代码行数
    + [dexcount](https://link.jianshu.com/?t=https://github.com/KeepSafe/dexcount-gradle-plugin)
    + statistic插件
    + [apk method](https://link.jianshu.com/?t=http://inloop.github.io/apk-method-count/)
- 利用Lint分析无用代码
> 借助as中的**Inspect Code**对工程做静态代码检查
- 通过proguard来删除无用代码
```
android {
    buildTypes {
        release {
            minifyEnabled true // 是否混淆
        }
    }
}
```
>>配合proguard配置才能起作用
- 剔除测试代码
- 区分debug/rtm/release模式
- 使用拆分后的support库
- 减少方法数,不用mulitdex
- 使用更小库或合并现有库





  







  


#### 参考
> [App瘦身最佳实践](https://www.jianshu.com/p/8f14679809b3 "App瘦身最佳实践")