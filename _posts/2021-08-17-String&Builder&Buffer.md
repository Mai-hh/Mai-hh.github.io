# String & Builder & Buffer



## 一. 三者区别

* String

  对象内容一但创建就不可以改变

* StringBuffer

  对象内容可以改变，线程安全，可以同步访问

* StringBuilder

  对象内容可以改变，线程不安全，但是速度较Buffer更快，大多数情况使用Builder

## 二. 二者相同点

* 都是`CharSequence`接口的实现类



## 三. 使用方法

先行概念：字符串池

* 静态字符串会存在池中

  ~~~java
  String str = "xxx";
  //线程池中存在了"xxx"静态字符串，所以之后所有调用此静态字符串的变量的地址都是一样的
  //所以
  String str2 = "xxx";
  
  str == str2 -> TRUE
  ~~~

  

* String构造对象的字符不会存在池中

  ~~~java
  String str = new String("xxx");
  String str2 = "xxx";
  
  str == str2 -> FALSE
  ~~~

  

* 将对象的构造字符串存入池，使用`intern()`方法

  ~~~java
  String str = new String("xxx");
  String str2 = "xxx";
  String str3 = str.intern();
  str3 == str2 -> TRUE
  ~~~

  

