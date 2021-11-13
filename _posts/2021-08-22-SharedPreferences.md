# SharedPreferences 存储

利用键值对进行存储

## 1. 数据存入

数据存储在文件包下的 shared_prefs 文件下

### 1.1 获取 SharedPreferences 对象

* `Context`类的`getSharedPreferences(String FileName, int mode)`方法
* `Activity`类的`getPreferences(int mode)`方法
* `PreferenceManager`类的`getDefaultSharedPreferences()`静态方法

### 1.2 获取 Editor 对象

* 调用 SharedPreferences 对象的的`edit()`方法获取一个`SharedPreferences.Editor`对象

### 1.3 利用 Editor对象的`putxxx()`方法传入键值对

* 存储的数据以 xml 格式文件存储

* 代码实战

  ~~~java
  SharedPreferences prefs = PreferenceManager.getDefaultSharedPreferences(MainActivity.this);
  //SharedPreferences.Editor editor = getSharedPreferences("data", MODE_PRIVATE).edit();
  SharedPreferences.Editor editor = prefs.edit();
  editor.putString("name", "Tim");
  editor.putInt("age", 28);
  editor.putBoolean("married", false);
  editor.apply();
  ~~~

## 2. 读取数据

* 使用传入数据类型对应的`get()`方法

