---
title: Android获取设备唯一标识
date: 2017-13-10 00:00:50
tags: Android
---

## Android获取设备唯一标识

### 需求

对于移动端产品来说，能准确的识别、定位、跟踪一台设备是一件很重要的事，基于设备唯一性可以做的事有很多，比如设备锁、防作弊、安全防护等。但是在android和ios两大阵营上，都没有能标识设备唯一性的ID供开发者采集，所以就有了本篇文章，让我们来一起分析下android端可以用来标识唯一设备的属性、优缺点及解决方案。

### 备选设备ID
#### IMEI
国际移动设备识别码（International Mobile Equipment Identity，IMEI），即通常所说的手机序列号、手机“串号”，用于在移动电话网络中识别每一部独立的手机等行动通讯装置，相当于移动电话的身份证。序列号共有15位数字，前6位（TAC）是型号核准号码，代表手机类型。接着2位（FAC）是最后装配号，代表产地。后6位（SNR）是串号，代表生产顺序号。最后1位（SP）一般为0，是检验码，备用。

##### 获取方式
```java
TelephonyManager manager = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
String imei = manager.getDeviceId();
```
##### 问题
+ 可以被作弊软件修改。
+ 厂商定制系统中的Bug：少数手机设备上，由于该实现有漏洞，会返回垃圾。如000000000000000、11111111111111111.
+ 手机管家，360手机助手，和一些厂商ROM自带的权限管家，有阻止应用采集手机IMEI的功能
+ 山寨手机厂商为了节省成本，批量复制重复IMEI串烧进手机的EEPROM里面。

#### Mac Address
wifi无线网卡的MAC地址，与终端硬件关联，可用作设备的唯一标识。

##### 获取方式
```java
WifiManager wifi = (WifiManager) context.getSystemService(Context.WIFI_SERVICE);
WifiInfo info = wifi.getConnectionInfo();
String mac = info.getMacAddress();
```

##### 问题
可以被作弊软件修改。
Android6.0及以上的系统上，通过上面的系统接口采集到的MAC地址返回的是一个固定串：02:00:00:00:00:00
#### IMSI
国际移动用户识别码（IMSI：International Mobile Subscriber Identification Number）是区别移动用户的标志，储存在SIM卡中，可用于区别移动用户的有效信息。其总长度不超过15位，同样使用0~9的数字。其中MCC是移动用户所属国家代号，占3位数字，中国的MCC规定为460；MNC是移动网号码，由两位或者三位数字组成，中国移动的移动网络编码（MNC）为00；用于识别移动用户所归属的移动通信网；MSIN是移动用户识别码，用以识别某一移动通信网中的移动用户。此ID是跟SIM卡绑定的，更换SIM卡就会发生变化，同样在仅支持wifi的pad设备上是没有的。

##### 获取方式
```java
TelephonyManager manager = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
String imsi = manager.getSubscriberId();
```

#####问题
存在无SIM卡的终端设备获取不到的问题
最大的问题是会发生变化，与终端设备非强绑定。

#### Android_ID
Android_ID是设备首次启动时，系统生成的一个唯一串号，长16字节(例：70560687d711af97)，由com.android.providers.settings 这个系统程序所管理，Android6.0 以下储存在/data/data/com.android.providers.settings/databases/settings.db中的secure 表。

##### 获取方式
```java
Settings.Secure.getString(context.getContentResolver(), "android_id")
```

##### 问题
此ID与硬件无关，与Android系统有关，所以当系统还原出厂设置，刷机时这个ID就会重新生成了。
由于国内的手机基本都是厂商定制的ROM，所以此ID的控制权均由厂商ROM层来控制，难免会发生一些bug，导致部分机型上的ID都相同。
另外在OTA ROM升级的时候，有些厂商会保留原有db信息不变，有些厂商会清除重新生成。
与系统强依赖，稳定性不足。
#### CID
Card Identification Register(CID)，顾名思义，做为存储卡片的唯一识别信息，规范上要求每张卡片的ID都唯一。CID 寄存器有 16 字节长，如下表 所示，它包含了本卡的特别识别码（ID 号）。 这些信息是在卡的生产期间被编程（烧录），主控制器不 能修改它们的内容。 注意：SD卡的 CID 寄存器和 MMC 卡的 CID 寄存器在记录结构上是不同的。一般来讲MMC0 保存的是内置存储卡的信息，MMC1 保存的是扩展存储卡也就是SD卡的信息。因为SD卡跟终端设备又不是强关联的，会因为更换升级发生变化，那么我们主要看手机内置存储卡的CID。

##### 获取方式
```shell
adb shell cat /sys/class/mmc_host/mmc*/mmc*:*/cid
```

#####问题
在部分山寨低端机器上，会出现采集不到和采集的是SD卡的CID。

### 方案
通过以上对可能作为设备唯一ID的属性分析后可以看到，没有一个属性可以作为标识设备的唯一ID，我们可能抱着90%多的有效率，选择某一个属性作为设备的唯一标识，又不可避免android端复杂的系统、定制厂商、root等环境。所以这里给出的方案又不能称之为方案，只是尽可能的标识唯一设备。所以方案可能是这样的：

按照以上分析的稳定性和可靠性分下先后顺序：
##CID > IMEI > MAC > ANDROID_ID##
生成deviceId规则为：
按照以上先后顺序依次获取，判断取得的值的有效性，有效的场合返回，无效的场合获取下一个值；直到取到有效值，都无效的场合使用UUID随机生成。
在尽可能获得设备唯一ID的基础上，将获得的ID保存在本地，下次直接从内存或外部存储中读取。这样尽量保证获取的设备id的唯一性。

Tip：保存到SD卡的设备ID可以保存为”.xxxx.txt”的形式，隐藏文件也能尽可能不会被用户误删。

所以，所谓的方案只是提供了一种简单的思路，不是什么完美的解决方案。

### 兼容性问题
大家都知道在Android6.0系统上，新增了不少特性，与终端ID采集密切相关的就是权限控制这个特性，结论如下：

IMEI，IMSI需用户手动授权方可采集。
6.0里面READ_PHONE_STATE权限被归属成敏感权限，在运行时，如果应用程序获取TelephonyManager实例时，系统就会弹出是否允许应用拨打电话和管理通话的选择对话框，用户拒绝之后，就采集不到。
针对6.0及以上的系统，ID的采集需要进行兼容，使用 checkPermission 接口从内存中直接获取权限数据，判断用户是否已经授权。

MAC地址通过非系统接口可采集(6.0以下通过系统接口采集)，采集场景仅限于处于wifi连接状态。
非系统接口，其实指的就是直接读取文件的方式，如下：

```java
cat /sys/class/net/wlan0/address 
cat /sys/devices/virtual/net/wlan0/address
```

AndroidID采集不受影响，但此ID不与硬件绑定，刷机，系统还原，升级等场景会发生变化，不稳定。

> 说明：应用编译时选择的targetSdkVersion>=23，在6.0及以上的系统运行才会触发权限提示框，<23打出的apk包不受此影响。

### 备注
以上大部分内容引自腾讯质量开放平台,在此感谢。