---
title: Kotlin初探
date: 2017-06-21 00:33:03
tags: Android
---

## Kotlin语言介绍

+ 来源：Kotlin是JetBrains在2010年推出的基于JVM的新编程语言
+ 用途：服务器端程序、Android 应用程序或者在浏览器中运行的前端程序
+ 优势：
    1. 简洁、易表现: 你可以编写少得多的代码,并且代码的可读性可能更好.
    2. 安全: 在编译初期就处理了各种null的情况，避免了执行时异常。
    3. 互操作性:你可以继续使用所有的你用Java写的代码和库，因为两个语言之间的互操作性是完美的。
    4. 甚至可以在一个项目中使用Kotlin和Java两种语言混合编程
    5. 函数式支持(Lambdas)
    6. 代理
    7. ...
 


## Kotlin概览(vs Java)

--- | --- | ---
定义 | Java  |  Kotlin
变量|String string = “Hello”;|var string: String = “Hello”
final变量 |final String string = “Hello”;|const val string: String = “Hello”
函数 |public boolean testString(String name){…} |open fun testString(val name:String): boolean {…}
数组|String[] names = new String[]{“Kyo”, “Ryu”, “Iory”}; String[] emptyStrings = new String[10];
|val ints = intArrayOf(1, 3, 5)
变长参数 |void hello(String… names){…}|fun hello(vararg names: String){
}
三元运算符   |int code = isSuccessfully? 200: 400;|int code = if(isSuccessfully) 200 else 400 
main 函数 |class Main{
public static void main(String… args){
… 
} 
} |fun main(args: Array){ 
… 
}
实例化类    |Date date = new Date(); | val date = Date() 
延迟初始化成员变量   |public class Hello{ 
    private final String name = “Peter”; 
 }|class Hello{ 
    private val name by lazy{ 
        NameProvider.getName()  
    } 
 } 
获得 class 的实例    |public class Hello{ 
    … 
 } 
 … 
 Class<?> clazz = Hello.class; 
 Hello hello = new Hello(); 
 Class<?> clazz2 = hello.getClass();
|class Hello
  val clazz = Hello::class.java 
 val hello = Hello() 
 val clazz2 = hello.javaClass



## Kotlin特性概览

### 简洁，使用更少的代码做更多的事

这是我认为Kotlin最大的优点。更像一种描述性编程语言。

#### 例子一：数据类

数据类大量重复的getter和setter相信会是很多人在开发过程中吐槽的一个点。举一个很经典的例子，我们需要一个Person的数据类。

在Java中，需要这么写：
```java
public class Person {
    private String name;
    private int age;
    private int sex;
    private float height;
    public Person(String name, int age, int sex, float height) {
        this.name = name;
        this.sex = sex;
        this.age = age;
        this.height = height;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public int getSex() {
         return sex;
    }
    public void setSex(int sex) {
        this.sex = sex;
    }
    public float getHeight() {
        return height;
    }
    public void setHeight(float height) {
        this.height = height;
    }
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", sex=" + (sex == 0 ? "男" : "女") +
                ", height=" + height +
                '}';
 }
```

在Kotlin里，我们只需要一行代码就能完成以上的功能：

```java
data class Person(var name: String,
                     var age: Int,
                     var sex: Int,
                     var height: Float)
```

#### 例子二：区间表达式

在Java中我们经常要写这样的代码：

```java
for(int i = 0; i <= 10; i++){
    System.out.println(i)
}
```

但是在Kotlin中，支持 .. 操作符形式的区间表达式，我们转换成Kotlin就变成了这样：

```java
for(i in 0..10){
    println(i)
}
```
不仅如此，还有更多相关的功能。

```java
//倒序迭代
for(i in 10 downTo 0){
    ...
}
//步长为2的迭代
for(i in 0..10 setp 2){
    ...
}
//i在[0,10)区间，排除了10
for(i in 0 until 10){
    ...
}
```

#### 例子三：Lamda表达式

Java中：
```java
btn.setOnClickListener(new View.OnClickListener() {
     @Override
     public void onClick(View v) {
         //dosomething
}});
```

Kotlin中：
```java
btn.setOnClickListener { 
    //dosomething 
}
```

#### 例子四：类扩展

看一个例子：

```java
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' 对应该列表
    this[index1] = this[index2]
    this[index2] = tmp
    }
}
//使用
val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // 'swap()' 内部的 'this' 得到 'l' 的值
fun Fragment.toast(message: CharSequence, duration: Int = Toast.LENGTH_SHORT) { 
    Toast.makeText(getActivity(), message, duration).show()
}
//使用
fragment.toast("Hello world!")
```

## 安全

### 例子一：空安全

一直以来，NullPointException空指针异常在开发中是最低级也最致命的问题。我们往往需要进行各种null的判断以试图去避免NPE的发生。Kotlin基于这个问题，提出了一个空安全的概念，即每个属性默认不可为null。

如果要允许为空，我们需要手动声明一个变量为可空字符串类型，写为String?

```java
var a: String = "abcd"
a = null //编译错误
var a: String? = "abcd"
a = null //编译成功
var person: Person? = null
person.name = "shinelw"  //编译失败
person?.name = "shinelw" //编译成功
// 这里不能通过编译. Artist 不能是null
var notNullArtist: Artist = null
// Artist 可以是 null
var artist: Artist? = null
// 无法编译, artist可能是null，我们需要进行处理
artist.print()
// 只要在artist != null时才会打印
artist?.print()
// 智能转换. 如果我们在之前进行了空检查，则不需要使用安全调用操作符调用
if (artist != null) {
  artist.print()
}
// 只有在确保artist不是null的情况下才能这么调用，否则它会抛出异常
artist!!.print()
// 使用Elvis操作符来给定一个在是null的情况下的替代值
val name = artist?.name ?: "empty"
```

## 与Java互操作(混编)

这是Kotlin与Scala相比，优势突出的一点。我们可以在Kotlin中调用现存的Java代码，并且也能在Java代码中顺利的调用Kotlin代码。这意味着我们可以马上在现有的Java项目中使用上Kotlin，同时所有之前旧的Java也一样有效。

#### Kotlin 调用 Java

大多数Java代码，可以直接调用

```java
import java.util.*
fun demo() {
    val list = ArrayList<string>()
    list.add("hello world")
}
```

#### Java 调用 Kotlin

Kotlin 属性会编译成以下 Java 元素：
+ 一个 getter 方法，名称通过加前缀 get 算出；
+ 一个 setter 方法，名称通过加前缀 set 算出（只适用于 var 属性）；
+ 一个私有字段，与属性名称相同（仅适用于具有幕后字段的属性）。

例如，var firstName: String 编译成以下 Java 声明：

```java
private String firstName;
public String getFirstName() {
    return firstName;
}
public void setFirstName(String firstName) {
    this.firstName = firstName;
}
```

在 org.foo.bar 包内的 example.kt 文件中声明的所有的函数和属性，包括扩展函数， 都编译成一个名为 org.foo.bar.ExampleKt 的 Java 类的静态方法。

```java
// example.kt
package demo
class Foo
fun bar() {
}
// Java
new demo.Foo();
demo.ExampleKt.bar();

```

## 函数

在Kotlin中，函数的声明使用fun关键字：


```java
fun double(x: Int): Int {
}
```

使用：


```java
val result = double(2)
```

使用.操作符调用成员函数：


```java
Simple().foo() //创建累的实例并调用foo（）方法
```

#### 中缀符号

当满足下列条件的时候，函数还可以使用中缀符号调用：

+ 当函数是成员函数或者扩展函数的时候
+ 当他们仅有一个参数的时候
+ 当他们通过infix关键字标记的时候

#### 默认参数

```java
fun read(b:Array<Byte>,off:Int = 0,len:Int = b.size()){
    ...
}
```

#### 返回Unit类型的函数

相当于java的void类型：

```java
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello ${name}")
    else
        println("Hi there!")
    // `return Unit` or `return` is optional
}
```

#### 单表达式函数

当一个函数仅返回一个表达式，则可以省略花括号，函数体可以直接跟在=号后面：

```java
fun double(x: Int): Int = x * 2
```

## 高阶函数和lambda表达式

高阶函数就是可以接受函数作为参数并返回一个函数的函数:


```java
fun <T> lock(lock: Lock, body: () -> T ) : T {
    lock.lock()
    try {
        return body()
    }
    finally {
        lock.unlock()
    }
}
```

一般是传递一个字面函数(通常是 lambda 表达式)：

```java
val result = lock(lock, {
sharedResource.operation() })
```

#### 字面函数和函数表达式

字面函数或函数表达式就是一个 “匿名函数”，也就是没有声明的函数，但立即作为表达式传递下去:

```java
max(strings, {a, b -> a.length < b.length })
```

## 代理

```java
**
 * 被代理接口
 */
interface Base {
    fun display()
}
/**
 * 被代理类
 */
open class BaseImpl : Base {
    override fun display() = print("just display me.")
}
/**
 * 代理类，使用:以及关键词by进行声明
 * 许代理属性和其他继承属性共用
 * class Drived(base: Base) : Base by base,BaseImpl()
 */
class Drived(base: Base) : Base by base
//使用
fun show() {
    var b = BaseImpl()
    var drived = Drived(b)
    drived.display()
}
**
 * 代理类型：
 * 懒加载：Lazy
 * 观察者：Delegates.observable()
 * 非空属性：Delegates.notNull<>()
 */
class Water {
    public var weight:Int by Delegates.notNull()
    /**
     * 代理属性
     */
    public val name: String by lazy {
        println("Lazy.......")
        "Code4Android"
    }
    public var value: String by Delegates.observable("init value") {
        d, old, new ->
        println("$d-->$old->$new")
    }
}
fun main(arg: Array<String>) {
    show()
    var water = Water()
    println(water.name)
    println(water.name)
    water.value = "11111"
    water.value = "22222"
    water.value = "33333"
    println(water.value)
    println(water.value)
    //必须先赋值否则IllegalStateException: Property weight should be initialized before get.
    water.weight=2
    print(water.weight)
}
```

## Use Android APIs with Kotlin

#### 声明Activity

```java
//in Kotlin
class MyActivity : AppCompatActivity() {
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity)
  }
}
//in Java
public class MyActivity extends AppCompatActivity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity);
  }
}
```

#### On-click listener

```java
//in Kotlin
val fab = findViewById(R.id.fab) as FloatingActionButton
fab.setOnClickListener {
  ...
}
//in Java
FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
fab.setOnClickListener(new View.OnClickListener() {
  @Override
  public void onClick(View view) {
    ...
  }
});
```

#### Item click listener

```java
//in Kotlin
private val mOnNavigationItemSelectedListener
    = BottomNavigationView.OnNavigationItemSelectedListener { item ->
  when (item.itemId) {
    R.id.navigation_home -> {
      mTextMessage.setText(R.string.title_home)
      return@OnNavigationItemSelectedListener true
    }
    R.id.navigation_dashboard -> {
      mTextMessage.setText(R.string.title_dashboard)
      return@OnNavigationItemSelectedListener true
    }
 }
 false
}
//in Java
private BottomNavigationView.OnNavigationItemSelectedListener mOnNavigationItemSelectedListener
    = new BottomNavigationView.OnNavigationItemSelectedListener() {
  @Override
  public boolean onNavigationItemSelected(@NonNull MenuItem item) {
    switch (item.getItemId()) {
      case R.id.navigation_home:
        mTextMessage.setText(R.string.title_home);
        return true;
      case R.id.navigation_dashboard:
        mTextMessage.setText(R.string.title_dashboard);
        return true;
    }
    return false;
  }
};
```

## 学习资料

+ 官方文档http://kotlinlang.org/docs/reference/
+ 中文文档http://www.kotlincn.net/docs/reference/
+ Kotlin语言中文教程-GitBookhttps://huanglizhuo.gitbooks.io/kotlin-in-chinese/content/
+ Kotlin for android中文教程https://wangjiegulu.gitbooks.io/kotlin-for-android-developers-zh/zhe_ben_shu_shi_he_ni_ma_ff1f.html
+ java与Kotlin混编http://kotlinlang.org/docs/reference/java-interop.html





