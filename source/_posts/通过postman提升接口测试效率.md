---
title: 通过postman提升接口测试效率
date: 2017-05-10 00:16:54
tags: Android
---

### 介绍

Postman是google开发的一款功能强大的网页调试与发送网页HTTP请求，并能运行测试用例的的Chrome插件。其主要功能包括：
模拟各种HTTP requests
从常用的 GET、POST 到 RESTful 的 PUT 、 DELETE …等等。 甚至还可以发送文件、送出额外的 header。

### Collection 功能（测试集合）

Collection 是 requests的集合，在做完一個测试的時候， 你可以把這次的 request 存到特定的 Collection 里面，如此一來，下次要做同样的测试时，就不需要重新输入。而且一个collection可以包含多条request，如果我们把一个request当成一个test case，那collection就可以看成是一个test suite。通过collection的归类，我们可以良好的分类测试软件所提供的API.而且 Collection 还可以 Import 或是 Share 出來，让团队里面的所有人共享你建立起來的 Collection。

### 人性化的Response整理

一般在用其他工具來测试的時候，response的内容通常都是纯文字的 raw， 但如果是 JSON ，就是塞成一整行的 JSON。这会造成阅读的障碍 ，而 Postman 可以针对response内容的格式自动美化。 JSON、 XML 或是 HTML 都會整理成我们可以阅读的格式
内置测试脚本语言
Postman支持编写测试脚本，可以快速的检查request的结果，并返回测试结果

### 设定变量与环境

Postman 可以自由 设定变量与Environment，一般我们在编辑request，校验response的时候，总会需要重复输入某些字符，比如url，postman允许我们设定变量来保存这些值。并且把变量保存在不同的环境中。比如，我們可能会有多种环境， development 、 staging 或 local， 而这几种环境中的 request URL 也各不相同，但我们可以在不同的环境中设定同样的变量，只是变量的值不一样，这样我们就不用修改我们的测试脚本，而测试不同的环境。

### 安装

从chrome的扩展程序中安装


### 入门

打开postman之后如下图所示,左边是项目具体的接口,右上上面是请求链接,请求体,右边下面是具体的响应体


### URL

要组装一条Request, URL永远是你首先要填的内容，在Postman里面你曾输入过的URL是可以通过下拉自动补全的哦。如果你点击Params按钮，Postman会弹出一个键值编辑器，你可以在哪里输入URL的Parameter，Postman会帮你自动加入到URL当中，反之，如果你的URL当中已经有了参数，那Postman会在你打开键值编辑器的时候把参数自动载入
Headers
点击’Headers’按钮，Postman同样会弹出一个键值编辑器。在这里，你可以随意添加你想要的Header attribute，同样Postman为我们通过了很贴心的auto-complete功能，敲入一个字母，你可以从下拉菜单里选择你想要的标准atrribute
Method
要选择Request的Method是很简单的，Postman支持所有的Method，而一旦你选择了Method，Postman的request body编辑器会根据的你选择，自动的发生改变
Request Body
如果我们要创建的request是类似于POST，那我们就需要编辑Request Body，Postman根据body type的不同，提供了4中编辑方式：

### 进阶

cookies
分开打包的应用程序运行在沙箱浏览器,它不能访问cookie设置浏览器内。这种限制也可以使用拦截器扩展。

身份验证Authentication
postman有一个helpers可以帮助我们简化一些重复和复杂的任务。当前的一套helpers可以帮助你解决一些authentication protocols的问题。

Basic Auth
填写用户名和密码，点击Refresh headers

Digest Auth
要比Basic Auth复杂的多。使用当前填写的值生成authorization header。所以在生成header之前要确保设置的正确性。如果当前的header已经存在，postman会移除之前的header。

OAuth 1.0a
postman的OAuth helper让你签署支持OAuth 1.0基于身份验证的请求。OAuth不用获取access token,你需要去API提供者获取的。OAuth 1.0可以在header或者查询参数中设置value。

OAuth 2.0
postman支持获得OAuth 2.0 token并添加到requests中。

Writting Test
Postman的Tests标签可以用来写测试：

本质上是javascript code，可以为tests object设置values。这里使用描述性文字作为key，检验body中的各种情况，当然你可以创建任意多的key，这取决于你需要测试多少点。 tests也会随着request保存到collection中。api测试保证前端后台都能正常的于api协作工作，而不用在出错时猜测是哪里的问题。 需要在request的test中创建了test后，再进行request，test的结果在body的test中查看。 注意： 1.这里的key描述必须是唯一的，否则相同描述只会执行第一个。 2.这里的key可以使用中文。 例子： tests[“Body contains user_id”] = responseBody.has(“user_id”) 这里描述性的key为：Body contains user_id。检测点为：responseBody.has(“user_id”)，意思是检测返回的body中是否包含”user_id”这个字段。
查看responses中的Tests结果：记过显示每个key,也就是我们测试点的具体结果，是否通过。

Testing Sandbox
postman的测试是运行在沙箱环境，是与app独立的。查看什么在沙箱中是可用的，参见Sandbox documentation.

Snippets
用于快速添加常用的测试代码。可以自定义snippets。

Viewing results
postman每次执行request的时候，会执行tests。测试结果会在tests的tab上面显示一个通过的数量。

Testing Sandbox
Testing examples
测试代码会在发送request并且接收到responses后执行。

1.设置环境变量 postman.setEnvironmentVariable(“key”, “value”);
2.设置全局变量 postman.setGlobalVariable(“key”, “value”);
3.检查response body中是否包含某个string tests[“Body matches string”] = responseBody.has(“string_you_want_to_search”);
4.检测JSON中的某个值是否等于预期的值
var data = JSON.parse(responseBody);
tests[“Your test name”] = data.value === 100;
JSON.parse()方法，把json字符串转化为对象。parse()会进行json格式的检查是一个安全的函数。 如：检查json中某个数组元素的个数(这里检测programs的长度)
var data = JSON.parse(responseBody);
tests[“program’s lenght”] = data.programs.length === 5;
5.转换XML body为JSON对象 var jsonObject = xml2Json(responseBody);
6.检查response body是否与某个string相等 tests[“Body is correct”] = responseBody === “response_body_string”;
7.测试response Headers中的某个元素是否存在(如:Content-Type)
tests[“Content-Type is present”] = postman.getResponseHeader(“Content-Type”);
//getResponseHeader()方法会返回header的值，如果该值存在
或者：
tests[“Content-Type is present”] = responseHeaders.hasOwnProperty(“Content-Type”);
上面的方法，不区分大小写。下面的方法，要区分大小写。

8.验证Status code的值 tests[“Status code is 200”] = responseCode.code === 200;

9.验证Response time是否小于某个值 tests[“Response time is less than 200ms”] = responseTime < 200;
10.name是否包含某个值 tests[“Status code name has string”] = responseCode.name.has(“Created”);
11.POST 请求的状态响应码是否是某个值 tests[“Successful POST request”] = responseCode.code === 201 || responseCode.code === 202;
12.很小的JSON数据验证器
var schema = {
“items”: {
“type”: “boolean”
}
};
var data1 = [true, false];
var data2 = [true, 123];
console.log(tv4.error);
tests[“Valid Data1”] = tv4.validate(data1, schema);
tests[“Valid Data2”] = tv4.validate(data2, schema);
结果：
运行Collections
postman允许你运行collection，你可以运行任意的次数。 最后会给出一个整体运行的结果。会保存每一次运行的结果，提供给你比较每一次运行解雇的不同。
选择collection，选择环境。点击运行按钮。

在需要csv和json文件的地方记得添加。
运行collection测试会在另一个窗口运行。如果需要在main窗口修改东西，在新窗口能正常读取。

参考文档：

https://juejin.im/entry/57597a62a341310061337885

http://www.jianshu.com/p/9d36e77fab9e