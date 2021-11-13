# Fragment和ViewPager知识整理

## 1. Fragment

### 1.1 碎片的使用方式

* 新建碎片继承类
* 创建对应布局
* 在MainActivity布局中注册对应的Fragment控件

### 1.2 碎片的动态添加

* 在Activity布局中嵌套一个布局控件,相当于在ViewGroup下再创建一个ViewGroup
* 往这个嵌套布局中添加内容实现动态添加碎片
* 拿到一个碎片管理类**FragmentManager**开启一个**transaction**事件，调用**replace**方法更新并用**commit**方法提交碎片

### 1.3 碎片模拟返回栈

* 在transaction事务提交之前调用 **transaction.addToBackStack** 方法将transaction中的fragment加入返回栈，这样按下Back就会回到上一个fragment而不是直接退出Activity

### 1.4 活动和碎片间相互获取实例

* 活动获取碎片及其中方法：

  >FragmentManager提供的**findFragmentById(R.id.xxx)**方法，可以获取到对应的碎片实例
  >
  >* 
  >
  > ~~~java
  >RightFragment rightFragment = (RightFragment) fragmentManager.findFragmentById(R.id.right_fragment);
  > ~~~
  >
  >* ps：也可以在Fragment调用这个方法实现碎片之间的通信
  >
  >2. 碎片调用活动中的方法：
  >
  >  **getActivity()**
  >
  >* ~~~java
  >  MainActivity mainActivity = (MainActivity) getActivity();
  >   //同时这个Activity也是一个Context，所以getActivity()也可以获取Context

### 1.5 碎片的生命周期

* onAttach - onCreate - onCreateView - OnActivityCreated - onStart - OnResume - onPasue - onStop - onDestroyView - onDestroy - onDetach

## 2. ViewPager

* PagerAdapter：PageAdapter 负责 ViewPager 内容的刷新，ViewPager 将调用它来取得所需显示的页，而 PageAdapter 也会在数据变化时，通知 ViewPager。这个类也是FragmentPagerAdapter 以及 FragmentStatePagerAdapter 的基类。如果继承自该类，至少需要实现 instantiateItem(), destroyItem(), getCount() 以及 isViewFromObject()。
* 为了滑动的流畅性，会缓存前后两个pager的视图

### 2.1 ViewPager1：安卓自带ViewPager类，Adapter继承pagerAdapter

### 2.2 ViewPager2：ViewPager2：对RecyclerView的封装，Adapter继承自RecyclerViewAdapter

### 2.3 ViewPager + Fragment

* 相当于在ViewPager中放置Fragment

* 使用ViewPager1时有有两种Fragment专用的adapter可以继承：

  1. **fragmentPagerAdapter**：缓存Fragment

     FragmentPagerAdapter 重载实现了几个必须的函数，因此来自 PagerAdapter 的函数，我们只需要实现 getCount()，即可。且，由于 FragmentPagerAdapter.instantiateItem() 的实现中，调用了一个新增的虚函数 getItem()，因此，我们还至少需要实现一个 getItem()。

     **缓存原理**：每次在生成的 Fragment 判断是否已经生成过了，如果生成过了，就使用旧的，旧的将被 Fragment.attach()；如果没有，就调用 getItem() 生成一个新的，新的对象将被 FragmentTransation.add()。FragmentPagerAdapter 会将所有生成的 Fragment 对象通过 **FragmentManager** 保存起来备用，以后需要该 Fragment 时，都会从 FragmentManager 读取，而不会再次调用 getItem() 方法。

  2. **FragmentStatePagerAdapter**：不缓存Fragment

     同样继承自 PagerAdapter。只保留当前页面，当页面离开视线后，就会被消除，释放其资源；而在页面需要显示时，生成新的页面(就像 ListView 的实现一样)。这么实现的好处就是当拥有大量的页面时，不必在内存中占用大量的内存。

     **不缓存原理**：除非碰到 FragmentManager 刚好从 SavedState 中恢复了对应的 Fragment 的情况外，该函数将会调用 getItem() 函数，生成新的 Fragment 对象。新的对象将被 FragmentTransaction.add()。否则就destroy调用 FragmentTransaction.remove()来释放资源

* ViewPager2 + Fragment：Fragment相当于一个功能强大的ViewHolder
  
     
