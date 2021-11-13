# reflection 反射机制

在不修改源码的情况下控制程序

> 通过读取的字符串找到对应的类，执行对应的方法
>
> 反射获取注解使得各种开发框架得以实现

## 1. 反射机制原理

* 类加载器

## 2. 主要的类

* Field 类的成员变量
* Method 类的方法
* Constructor 类的构造器
* Class 类在堆中的对象

### 2.1 Class 类

* Class 类也是 Object 类的子类

* Class 类不是 new 出来的，是利用系统创建的

* 一个类的 Class 对象只能被类加载器加载一次，只会在内存中存在一份

  > 一个类的所有实例调用`getClass()`方法返回的对象 hashCode 都相等

#### 2.1.1 Class 类的方法

* `getClass()`返回对应的 Class 类

* `getPackage()` 返回包

  `getPackage().getName`返回包名

* `newInstance()`创建实例

  ~~~java
  Test test = cls.newInstance();
  ~~~

  

* `getField(String fieldName)`返回字段名为`fieldName`的变量类

  ~~~java
  //利用反射赋值
  Field name = cls.getField("name");
  name.set(test, "a");
  ~~~

  

* `getFields()`返回一个 Field[] 数组，返回所有变量类

* ...不难发现，Class 类方法可以 get 这个对象内所有的信息

### 2. 2 Method 类

#### 2.2.1 Method 类方法

* `getName()`返回字段名
* `getModifiers()`返回修饰符 int 值，默认修饰符对应0，public对应1，private对应2， protected对应4，static对应8，final对应16，多个修饰符返回符值之和
* `getType()`返回字段对应的类的 Class 对象
* ...还可以获取返回值类型，形参数组等等

### 2.3 Constructor 类

#### 2.3.1 Construtor 类方法

### 2.4 Field 类

## 3. 反射的调用和优化

* 优点：动态创建和使用对象

* 缺点：反射基本是解释执行，执行速度又有影响

* 正射写法

  ~~~java
  Test test = new Test();
  for (int i = 0; i < MAX_VALUE; i++) {
      test.hi();
  }
  ~~~

* 反射写法

  ~~~java
  Class cls = Class.forName("Test");//需要完整包名
  Object o = cls.newInstance();
  Method hi = cls.getMethod("hi");
  for (int i = 0; i < Integer.MAX_VALUE; i++) {
      hi.invoke(o);
  }
  ~~~

  > 经过对比，执行相同逻辑正射写法瞬间就可以输出结果，反射写法需要等待，效率差别非常大

### 3.1 优化：关闭访问检查

* Method、Field、Constructor 对象都有`setAccessible(Boolean b)`方法，true 表示取消访问检查，可以提高反射效率

  ~~~java
  Class cls = Class.forName("Test");
  Object o = cls.newInstance();
  Method hi = cls.getMethod("hi");
  hi.setAccessible(true);
  for (int i = 0; i < Integer.MAX_VALUE; i++) {
      hi.invoke(o);
  }
  ~~~

  > 运行时间由2500ms+ 减少为1500ms+



## 4. 通过反射创建对象

### 4.1 通过反射调用无参构造器

* 调用对应 Class 对象的 newInstance() 方法

### 4.2 调用指定构造器创建对象

* 调用对应 Constructor 对象的 newInstance(Class...clazz) 方法 
* 希望使用 private 修饰的的构造器需要调用 Constructor 的 `setAccessible(true)`方法爆破对象

## 5. 通过反射访问类中的成员



