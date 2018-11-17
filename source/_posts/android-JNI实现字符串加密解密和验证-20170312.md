---
title: JNI实现字符串加密解密和验证
date: 2017-03-12 00:08:53
tags: Android
---

### 需求

我们公认的，在java层的加解密运算都是不安全的，因为apk可以被反编译，你的加解密算法会很容易的暴露出去。一些现有的对称加密算法，密钥必须要在客户端保留，所以也是不安全的；这里不包括和后台通讯时使用的RSA加密，客户端保留公钥加密，后台使用私钥解密，这也是现有的比较大众的做法，但是如果只在客户端做加解密，我想很对人都会想到使用JNI技术，因为对于反编译而言，c／c++的代码反编译难度更大，增加了反编译的成本。下面是我的实战经历分享给大家。

### 步骤

开发工具：Android studio

+ 搭建NDK开发环境
+ 使用CMake(AS2.2以后才有的)创建JNI环境
+ c／c++编写字符串加解密方法
+ 测试验证

### 搭建NDK开发环境

打开Preferences到SDK选项，安装CMake、LLDB、NDK如下图

[]()

### 使用CMake创建JNI环境

创建jni folder

[]()

配置CMake

[]()

+ 配置build.gradle文件
+ 导入CMakeLists.txt,放入app目录下，内容如下


CMake相关内容请自行Google.native.c、native_lib.h、native_lib.c是创建在jni目录下的c文件。

### c／c++编写字符串加解密方法

#### java层

创建NativeHelper类
```java
System.loadLibrary(“native_lib”);该库名与CMakeLists.txt中配置的对应。
option+enter，在native.c文件下创建与java层的对应方法
```

#### c/c++层

native.c中写了关联方法

```java 
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
#include <jni.h>
#include <string.h>
#include "native_lib.h"
JNIEXPORT jstring JNICALL
Java_com_example_machan_jnistrencode_NativeHelper_encrypt(JNIEnv *env, jobject instance,
                                                          jstring str_) {
    const char *buf_deviceid = (*env)->GetStringUTFChars(env, str_, 0);
    if (buf_deviceid == NULL) {
        return NULL;
    }
    int len = strlen(buf_deviceid);
    char cpyme[len];
    strcpy(cpyme, buf_deviceid);
    //2. 释放内存
    (*env)->ReleaseStringUTFChars(env, str_, buf_deviceid);
    char *result;
    encrypt(cpyme, result);
    return (*env)->NewStringUTF(env, result);
}
JNIEXPORT jstring JNICALL
Java_com_example_machan_jnistrencode_NativeHelper_decryot(JNIEnv *env, jobject instance,
                                                          jstring enc_) {
    const char *buf_deviceid = (*env)->GetStringUTFChars(env, enc_, 0);
    if (buf_deviceid == NULL) {
        return NULL;
    }
    int len = strlen(buf_deviceid);
    char cpyme[len];
    strcpy(cpyme, buf_deviceid);
    //2. 释放内存
    (*env)->ReleaseStringUTFChars(env, enc_, buf_deviceid);
    char *result;
    decrypt(cpyme, result);
    return (*env)->NewStringUTF(env, result);
}
JNIEXPORT jstring JNICALL
Java_com_example_machan_jnistrencode_NativeHelper_checkEnc(JNIEnv *env, jobject instance,
                                                           jstring enc_) {
    const char *buf_deviceid = (*env)->GetStringUTFChars(env, enc_, 0);
    if (buf_deviceid == NULL) {
        return NULL;
    }
    int len = strlen(buf_deviceid);
    char cpyme[len];
    strcpy(cpyme, buf_deviceid);
    //2. 释放内存
    (*env)->ReleaseStringUTFChars(env, enc_, buf_deviceid);
    char* tag;
    decryotCheck(cpyme, tag);
    int result = strcmp(tag, TAG);
    return result == 0;
}
```

其中的encrypt、decrypt、decryotCheck方法规则如下：

#### 加密、解密

明文：11111

tag：tag

密钥：随机生成6位字符，例如：123456

加密字符：11111tag

```java
1 1 1 1 1 t a g
+ + + + + + + +
1 2 3 4 5 6 1 2
= = = = = = = =
```

Ascii码相加获得的值转换成字符串就是我们要的密文（超过127可以减去一个定值保证在0-127之间）
实现的话由于我本身c语言功底很渣，就不贴代码了，只是给大家提供一种思路，变换规则按自己的来就好。

#### 验证

由于我们在加密的串后加了tag，所以验证tag值是否正确就可以，这只是一种简单的验证方式，不完美，也是提供一种思路 - -。

### 结束

+ 以前有用Android_mk来做JNI，但有了CMake后感觉更简单了。
+ 上面提到的加密算法只能算是小儿科，对于一些不要求很严格的项目来说可以用用，毕竟成本不高。
+ 后续可能将代码整理到github,因为可能涉及一些保密信息不便透露，望谅解。
+ 本人也不是大牛，还在摸爬滚打阶段，有什么不对的地方望指正。

希望大家都能在IT这条道路上走的更远～
共勉～


