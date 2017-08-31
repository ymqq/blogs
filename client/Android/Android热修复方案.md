# Android热修复方案
 

## 技术方案

1. [QQ空间补丁方案DroidFix](https://github.com/bunnyblue/DroidFix)
    
    https://mp.weixin.qq.com/s?__biz=MzI1MTA1MzM2Nw==&mid=400118620&idx=1&sn=b4fdd5055731290eef12ad0d17f39d4a&scene=1&srcid=1106Imu9ZgwybID13e7y2nEi#wechat_redirect

2. [阿里AndFix](https://github.com/alibaba/AndFix)

3. [微信Tinker](https://github.com/Tencent/tinker)

4. 百度金融 HotFix(Deprecated) -> RocooFix

    [HotFix](https://github.com/dodola/HotFix)

    [RocooFix](https://github.com/dodola/RocooFix)

5. [大众点评Nuwa](https://github.com/jasonross/Nuwa)

6. [美团Robust](https://github.com/Meituan-Dianping/Robust)

7. [饿了么Amigo](https://github.com/eleme/Amigo)


> 1. [Android热修复技术选型——三大流派解析](http://www.chinaz.com/news/2016/0912/579753.shtml)

> 2. [Android 热修复其实很简单](http://blog.csdn.net/qq_31530015/article/details/51785228?locationNum=11)

> 3. [Android 热补丁动态修复框架小结](http://blog.csdn.net/lmj623565791/article/details/49883661)



## 最终选择

### 最终确定方案
1. Robust

2. Tinker


### 原因
1. DroidFix：两年前方案，从未更新 Star：5245 Fork：1339

2. Nuwa：两年前方案，从未更新 Star：2646 Fork：557

3. AndFix：最近更新5个月前，但是有220多个issues未关闭 Star：5245 Fork：1339
    制作补丁需要使用命令行工具对两个版本apk进行手动生成补丁包

4. HotFix：已废弃
   RocooFix：最近更新8个月前，私人维护，没有大型app应用 Star：1355 Fork：286

    目前存在的问题
    兼容性测试（无法预知兼容情况，不能兼容7.0系统）
    目录规则调整（项目变动大）
    对Win系统的支持（目前开发都用Windows）

5. Robust：最近更新本周内 Star：1909 Fork：294

6. Tinker：最近更新15天前 Star：8652 Fork：1796


#### Tinker给出的对比图

| 对比项     | Tinker | QZone | AndFix | Robust |
 --
| 类替换     | yes    | yes   | no     | no     |
| So替换     | yes    | no    | no     | no     |
| 资源替换   | yes    | yes   | no     | no     |
| 全平台支持 | yes    | yes   | yes    | yes    |
| 即时生效   | no     | no    | yes    | yes    |
| 性能损耗   | 较小   | 较大  | 较小   | 较小   |
| 补丁包大小 | 较小   | 较大  | 一般   | 一般   |
| 开发透明   | yes    | yes   | no     | no     |
| 复杂度     | 较低   | 较低  | 复杂   | 复杂   |
| gradle支持 | yes    | no    | no     | no     |
| Rom体积    | 较大   | 较小  | 较小   | 较小   |
| 成功率     | 较高   | 较高  | 一般   | 最高   |


## 最终方案的使用指导
### [Robust](https://github.com/Meituan-Dianping/Robust)


[Android 美团Robust热更新 使用入门](http://blog.csdn.net/snailbaby_soko/article/details/69524380)





### [Tinker](https://github.com/Tencent/tinker)

#### 由于原理与系统限制，Tinker有以下已知问题
1. Tinker不支持修改AndroidManifest.xml，Tinker不支持新增四大组件；

2. 由于Google Play的开发者条款限制，不建议在GP渠道动态更新代码；

3. 在Android N上，补丁对应用启动时间有轻微的影响；

4. 不支持部分三星android-21机型，加载补丁时会主动抛出"TinkerRuntimeException:checkDexInstall failed"；

5. 对于资源替换，不支持修改remoteView。例如transition动画，notification icon以及桌面图标。

#### 项目依赖设置
##### 项目build.gradle中配置

###### 在项目的build.gradle中，添加tinker-patch-gradle-plugin的依赖

```gradle
buildscript {
    dependencies {
        classpath ('com.tencent.tinker:tinker-patch-gradle-plugin:1.7.9')
    }
}
```

###### 然后在app的gradle文件app/build.gradle

1. 添加apply tinker的gradle插件，在`apply plugin: 'com.android.application'`之下添加

> 这里应该按照tinker-sample-android的方案使用`if`进行做分支

```gradle
//apply tinker插件
apply plugin: 'com.tencent.tinker.patch'
```

2. 添加tinker的库依赖

```gradle
dependencies {
    // 可选，用于生成application类 
    // 当需要自己实现application时，就不需要该依赖，但是要遵守Tinker给出的规则
    provided('com.tencent.tinker:tinker-android-anno:1.7.9')
    // tinker的核心库
    compile('com.tencent.tinker:tinker-android-lib:1.7.9') 
}
```

3. 

[微信热修复 tinker 及 tinker server 快速接入](https://juejin.im/post/5838298d61ff4b006b442b53)
[Tinker热修复初探](http://www.knowsky.com/986205.html)




只用70行javascript代码做俄罗斯方块游戏：https://buluo.qq.com/p/detail.html?bid=318128&pid=787862-1494405467&from=share_qq