---
layout: post
title: "android studio自动为APK签名"
description: ""
date: 2017-09-16 16:36:48 +0800
category:
tags: []
---
# 使apk获得系统权限

如果apk需要使用system权限，
可以在AndroidManifest.xml中添加一行

    android:sharedUserId="android.uid.system"

添加这一行之后，编译出的apk需要系统签名才可以安装。

为了免除每一次编译后都需要签名的麻烦，可以将签名动作添加到gradle文件中。

## 安卓系统密钥

安卓系统使用以下方法生成签名密钥对：

`device/vendor/common/security/README`

> ### The following commands were used to generate the test key pairs:
>
> ```bash
> development/tools/make_key testkey  '/C=US/ST=California/L=Mountain View/O=Android/OU=Android/CN=Android/emailAddress=android@android.com'
> development/tools/make_key platform '/C=US/ST=California/L=Mountain View/O=Android/OU=Android/CN=Android/emailAddress=android@android.com'
> development/tools/make_key shared   '/C=US/ST=California/L=Mountain View/O=Android/OU=Android/CN=Android/emailAddress=android@android.com'
> development/tools/make_key media    '/C=US/ST=California/L=Mountain View/O=Android/OU=Android/CN=Android/emailAddress=android@android.com'
> ```
>
> The following standard test keys are currently included:
>
> ```text
> testkey -- a generic key for packages that do not otherwise specify a key.
> platform -- a test key for packages that are part of the core platform.
> shared -- a test key for things that are shared in the home/contacts process.
> media -- a test key for packages that are part of the media/download system.
> ```
>
> These test keys are used strictly in development, and should never be assumed
> to convey any sort of validity.  When $BUILD_SECURE=true, the code should not
> honor these keys in any context.
>
> ### Signing using the openssl commandline (for boot/system images)
>
> 1. convert pk8 format key to pem format
>    % openssl pkcs8 -inform DER -nocrypt -in testkey.pk8 -out testkey.pem
>
> 2. create a signature using the pem format key
>    % openssl dgst -binary -sha1 -sign testkey.pem FILE > FILE.sig
>
> ### extracting public keys for embedding
>
> it's a Java tool
> but it generates C code
> take a look at commands/recovery/Android.mk
> you'll see it running $(HOST_OUT_JAVA_LIBRARIES)/dumpkey.jar

从上述描述可知，想要访问系统设备，至少需要platform对应的密钥对 `platform.x509.pem` 和 `platform.pk8`。

## 生成签名证书

获取上述的密钥对之后，需要转换成安卓编译需要的keystore证书。

下载证书生成工具keytool-importkeypair，使用该工具从密钥对生成证书：

    git clone https://github.com/getfatday/keytool-importkeypair
    cd keytool-importkeypair
    ./keytool-importkeypair -k xx.keystore -p android -pk8 platform.pk8 -cert platform.x509.pem -alias platform-xx

这样就使用系统的platform密钥对生成了签名证书，该证书可以为platform访问权限签名。

证书的密码是 `android`,
证书名称是 `platform-xx`。

将签名文件复制到app目录备用。

## 指定签名的证书

在app的编译配置文件build.gradle中，
找到defaultConfig。

在defaultConfig后面同一级别的位置，添加配置：

```gradle
    signingConfigs{
        release {
            storeFile file("xx.keystore")
            storePassword 'android'
            keyAlias 'platform-xx'
            keyPassword 'android'
        }

        debug {
            storeFile file("xx.keystore")
            storePassword 'android'
            keyAlias 'platform-xx'
            keyPassword 'android'
        }
    }
```

## 配置APK自动签名

gradle文件默认编译目标是release版的，在release动作中加入release版的签名配置：

```gradle
     buildTypes {
         release {
             minifyEnabled false
             proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
+            signingConfig signingConfigs.release
         }
     }
```

然后修改android studio中该project编译的目标（Build Variants），
配置为默认生成release版的apk:

    studio 左侧边栏 --> 点击 “Build Variants” --> 修改Modules的“Build Variant”为release

这样当在Android Studio中点击 'Run app' （shift+F10）时，
会自动编译出带签名的release版apk，安装到目标板。

## 跳过签名

如果不想配置签名，可以删除这一行，再重新编译就可以了。

`app/src/main/AndroidManifest.xml`

    android:sharedUserId="android.uid.system"

只是这样apk中需要用到system权限的函数调用会失败。

## 参考

1. [让Android Studio支持系统签名](http://www.jianshu.com/p/47265c8899b5)
