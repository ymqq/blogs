# Android APK 安全之旅

## 密钥存储

### 1. Keystore
[Android里存储加密密钥最好的解决方案应该是使用keystore](http://developer.android.com/reference/java/security/KeyStore.html)

条件：需要target SDK >= 18

特点：
    1.只有应用自身UID可以访问这个KeyStore。
    2.只能存储非对称密钥，也就是说你需要使用这个非对称密钥加密存储在设备上某处的对称密钥。
    3.密钥材料可以绑定安全硬件；
    4.密钥材料无法导出设备；
    5.密钥材料永远不进入应用进程，加解密和签名时仅仅利用系统进程完成。最极端情况下即使应用进程被破解，攻击者也最多能用密钥来加解密，但不能导出密钥材料


### 2. AccountManager
如果target SDK < 18,有个相对来说比较安全的方案，因为Android4.3以前没有一个合适的存储对称密钥的系统方案。

[这时可以使用AccountManager。](http://developer.android.com/reference/android/accounts/AccountManager.html)

特点：
    只允许添加这个密钥的应用访问它，当使用getPassword()方法时会校验调用者的uid以及其是否拥有 AUTHENTICATE_ACCOUNTS 这个权限。

Note:
    虽然拥有root权限的应用依然可以访问到这个密钥，然而却比和被加密的数据一起存储在应用私有目录安全。


### 3. 白盒加密技术
> 白盒加密技术.白盒加密技术的核心思想是把秘钥隐藏起来, 加密执行过程中, 内存中不会出现秘钥的值. 现在通用的技术是查找表技术, 即把秘钥隐藏在查找表中,可以看看Chow的关于白盒AES的奠基性文章white-box cryptography and an aes implementation, 国内也有人做, 看看肖雅莹的毕业论文"白盒密码及AES与SMS4算法的实现"可以对白盒加密有一个感性的认识. 所以, 通过查找表技术隐藏秘钥, 使得攻击者逆向也好, dump内存也罢, 就不会直接看到秘钥的值, 这跟把秘钥放到so中增加逆向分析难度有质的区别. 白盒加密的攻击需要从数学理论上去分析, 这无疑会极大增加破解难度. 白盒加密的研究现状是公布出来的论文并不多, 给你一个参考网址: http://www.whiteboxcrypto.com/. 我相信国内外都有安全公司和实验室在研究白盒加密, 只不过都没公布出来. 从移动互联网到IoT领域, 终端安全问题会越来越突出, 预测白盒加密这种软件解决方案会越来越重要, 因为成本更低. 白盒加密的安全度目前来看是可以值得信任的, 当然这个世界上没有绝对的安全, 白盒加密也会被破, 就是攻击成本的问题啦.


### 4. 密钥直接明文存在sharedprefs文件中，这是最不安全的。


### 5. 密钥直接硬编码在Java代码中，这很不安全，dex文件很容易被逆向成java代码。


### 6. 将密钥分成不同的几段，有的存储在文件中、有的存储在代码中，最后将他们拼接起来，可以将整个操作写的很复杂，这因为还是在java层，逆向者只要花点时间，也很容易被逆向。


### 7. 用ndk开发，将密钥放在so文件，加密解密操作都在so文件里，这从一定程度上提高了的安全性，挡住了一些逆向者，但是有经验的逆向者还是会使用IDA破解的。

[Android使用C/C++来保存密钥](http://blog.csdn.net/dingding_android/article/details/51775876)


### 8. 在so文件中不存储密钥，so文件中对密钥进行加解密操作，将密钥加密后的密钥命名为其他普通文件，存放在assets目录下或者其他目录下，接着在so文件里面添加无关代码（花指令），虽然可以增加静态分析难度，但是可以使用动态调式的方法，追踪加密解密函数，也可以查找到密钥内容。


### 9. [阿里聚安全(一套完整的产品安全服务)](http://jaq.alibaba.com/)


### 10. 通过配置gradle的方式保存秘钥信息


### 11. 通过配置string.xml的方式保存秘钥信息


### 12. 通过gradle + string.xml + java算法实现等多种方式组合使用

使用gradle配置 + 静态代码 + 字符串运算 + string.xml值的方式实现静态秘钥的存储。

首先将静态秘钥分为四部分：

第一部分通过gradle配置的方式存储；

第二部分通过java硬编码的方式存储；

第三部分通过java字符串拼接运算的方式存储；

第四部分通过string.xml保存；



### 参考资料
1. [关于Android应用密钥存储方案](http://www.hackdig.com/04/hack-20771.htm)
2. [android开发如何保障本地加密密钥的安全？](https://www.zhihu.com/question/35136485)
3. [Android中保存静态秘钥（加密秘钥，特殊id字段等）](http://blog.csdn.net/lplj717/article/details/52994331)
4. [Android安全开发之浅谈密钥硬编码](http://www.cnblogs.com/alisecurity/archive/2016/05/16/5498539.html)
5. [Android KeyStore + FingerprintManager 存储密码](http://blog.csdn.net/lintcgirl/article/details/51355203)
6. [android安全存储，使用AndroidKeyStore的问题](http://ask.csdn.net/questions/344410)



## Apk 反编译大作战



### 参考资料
1. [android apk 防止反编译技术第一篇-加壳技术](https://my.oschina.net/u/2323218/blog/393372)
2. [android apk 防止反编译技术第二篇-运行时修改Dalvik指令](https://my.oschina.net/u/2323218/blog/396203)
3. [android apk 防止反编译技术第三篇-加密](https://my.oschina.net/u/2323218/blog/399326)
4. [android apk 防止反编译技术第四篇-对抗JD-GUI](https://my.oschina.net/u/2323218/blog/403621)
5. [android apk 防止反编译技术第五篇-完整性校验](https://my.oschina.net/u/2323218/blog/406860)
6. [[原创]APK自我保护方法](http://bbs.pediy.com/thread-183116.htm)
7. []()





## 攻防结合--Apk逆向作战

### Android逆向之旅



#### 参考资料
1. [关于Android的.so文件你所需要知道的](http://www.open-open.com/lib/view/open1440421271716.html)
2. [Android中怎么破解游戏之修改金币数](http://blog.csdn.net/jiangwei0910410003/article/details/25805453)(还未成功)
3. [Android逆向之旅 SO(ELF)文件格式详解](http://blog.csdn.net/jiangwei0910410003/article/details/49336613/)
3. [Android逆向之旅 Android应用的汉化功能(修改SO中的字符串内容)](http://blog.csdn.net/jiangwei0910410003/article/details/49361281)


### Android jni开发



#### 参考资料
1. [Android Studio ndk-Jni开发详细](http://www.open-open.com/lib/view/open1451917048573.html)
2. [Android使用C/C++来保存密钥](http://blog.csdn.net/dingding_android/article/details/51775876)
3. [Android中的第一个NDK的例子](http://blog.csdn.net/jiangwei0910410003/article/details/17710243)
4. []()



## 混淆攻城战

### 一份实战后的混淆配置

```gradle
#########################################
#                                       #
#             开启混淆攻城战              #
#                                       #
#########################################


##################################
## 通用混淆配置
##################################

# 代码混淆压缩比，0~7之间，默认5
-optimizationpasses 5

# 不采用大小写命名混淆
-dontusemixedcaseclassnames

# 不跳过类库中的非公共类
-dontskipnonpubliclibraryclasses

# 不跳过类库中的非公共方法
-dontskipnonpubliclibraryclassmembers

# 混淆时是否记录日志（混淆后生产映射文件 map 类名 -> 转化后类名的映射
-verbose

# 不优化输入的类文件
-dontoptimize

# 不做预校验
-dontpreverify

# 不混淆注解与内部类
-keepattributes *Annotation*, InnerClass

# 避免混淆泛型
-keepattributes Signature

# 抛出异常时，保持源文件以及行号，友盟统计需要加入此行
-keepattributes SourceFile, LineNumberTable

# Java反射用到的类不能混淆
-keep class com.entity.**

# 混淆算法
-optimizations !code/simplification/case, !field/*, !class/merging/*

# 忽略警告
-ignorewarnings

# 记录生成的日志数据，gradle build时在本项目根目录输出
# apk 包内所有 class 的内部结构
-dump proguard/class_files.txt
# 未混淆的类和成员
-printseeds proguard/seeds.txt
# 列出从 apk 中删除的代码
-printusage proguard/unused.txt
# 混淆前后的映射
-printmapping proguard/mapping.txt

-printconfiguration proguard/config.txt


############################################################
# APP工程具体配置
# 该部分内容需要根据具体项目配置，如何知道需要这些配置，
# 请配置完成上述通用配置后，就可直接运行gradle -i build，
# 然后根据具体的报错信息，添加相应的配置
############################################################

# 根据控制台上显示的提示 Note 添加 -dontnote 配置
-dontnote android.net.http.**
-keep class android.net.http.** {*;}

-dontnote org.apache.http.**
-keep class org.apache.http.** {*;}


# 由于使用到了Gson类库，因此按照官方说明添加
# Gson配置
-keepattributes Signature
-keepattributes *Annotation*
-dontwarn com.google.gson.**
-dontnote com.google.gson.**
-keep class com.google.gson.** {*;}
-keep class sun.misc.Unsafe { *; }
-keep class com.google.gson.stream.** { *; }

# 由于Gson进行转换时，需要保证实体类不能改变，因此加入下方配置
# 实体类不能混淆
-keep class com.ffcs.registersys.bean.** {*;}
-keep class cn.ffcs.cmp.bean.** {*;}


# 根据控制台上显示的提示 Warn 添加 -dontwarn 配置
-dontwarn com.google.zxing.**
-keep class com.google.zxing.** {*;}

# 根据控制台上显示的提示 Warn/Note 添加 -dontwarn / -dontnote 配置
-dontwarn org.dom4j.**
-dontnote org.dom4j.**
-keep class org.dom4j.** {*;}

# 根据控制台上显示的提示 Warn/Note 添加 -dontwarn / -dontnote 配置
-dontwarn com.sunrise.**
-dontnote com.sunrise.**
-keep class com.sunrise.** {*;}

# 根据控制台上显示的提示 Warn/Note 添加 -dontwarn / -dontnote 配置
-dontwarn org.codehaus.**
-dontnote org.codehaus.**
-keep class org.codehaus.** {*;}


-keep class com.example.tyras.customcamera.** {*;}
-keep class com.ymqq.commonresources.** {*;}


```


### 参考资料
1. [Android Proguard工具使用和配置详解](http://blog.csdn.net/ccpat/article/details/52059344)
