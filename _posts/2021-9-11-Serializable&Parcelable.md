# Serializable&Parcelable

## 1. Serializable 使用方法

* 实现`Serializable`接口

  > 这是一个标识接口，不需要实现任何方法，继承了这个接口的类才可以序列化

* serializableUID

  > 可以被序列化的类默认有一个 serializableUID 用于标识序列化对象版本
  >
  > 反序列化时如果 serializableUID 不匹配则会抛出异常

  ~~~java
  private static final long serialVersionUID = -2559426732574116369L;
  ~~~

  > serialVersionUID 由系统默认生成，也可以自定义修改

* 配合 Intent 的 getSerializableExtra(String key) 方法反序列化




## 2. Parcelable 使用方法

* 实现`Parcelable`接口

* 实现方法

  1. `public int describeContents()`

     返回一个 int 值描述文件的类型

  2. `public void writeToParcel(Parcel parcel, int i)`

     写入接口方法：将对象属性写入一个 Parcel 对象，并指定一个 int 值来表示写入方式（AIDL相关）

* 静态创建器接口对象

  * `public static final Creator<T> CREATOR`

    读取接口，目的是要从Parcel中构造一个实现了Parcelable的类的实例处理。因为实现类在这里还是不可知的，所以需要用到模板的方式，继承类名通过模板参数传入
    为了能够实现模板参数的传入，这里定义Creator嵌入接口,内含两个接口函数分别返回单个和多个继承类实例

  * 这个接口对象需要实现两个方法

    1. `public T createFromParcel(Parcel in)`

       顾名思义，这个方法是把 in 这个 Parcel 对象中的数据取出并返回对应类型

    2. `public T[] newArray(int size)`

       返回一个对象数组供外部类反序列化时使用

* 配合 Intent 的 getParcelableExtra() 、getParcelableArrayExtra()、getParcelableArrayListExtra()方法反序列化

## 3. TODO

* 方法`public T[] newArray(int size)`返回对象数组的意义是什么

  发现：Intent 对象利用 Serializable 反序列化只提供了 getSerializableExtra() 方法，返回一个 Serializable 对象，可以转换成数组类型

  但是对于 Parcelable 却提供了三个方法：

  getParcelableExtra() 返回一个泛型 T 对象，不能转换成数组类型

  getParcelableArrayExtra() 返回一个 Parcelable[] 类型对象，可以先用 Parcelable[] 对象接收，在转换成对应的类型

  getParcelableArrayListExtra() 返回一个 List 对象

  > 就此可以猜想到 newArray 方法是为下面两个方法服务的

  


## 4. 配合 Bundle 使用

* Bundle 类对 Parcel 的拆装都进行了高度的优化

  ~~~java
  Bundle bundle = intent.getExtras();
  ParcelableClass pc = bundle.getParcelable("aa");
  Parcelable[] ps = bundle.getParcelableArray("bb");
  ParcelableClass pc2 = (ParcelableClass) ps[0];
  ~~~


* Serializable 也可以配合 Bundle 传递数据

## 5. 两种序列化方式的对比

1. Parcelable 的效率比 Serializable 要快
   * Serializable 反序列化时用到反射机制
   * Serializable 会产生大量对象
2. 适用情况不同
   * 在安卓，IPC通信尽量使用 Parcelable 来提升性能，通过实现 Parcelable 的数据序列化在内存中
   * 需要网络传输和保存到磁盘时使用 Serializable，数据序列化在磁盘中
