# FloatingActionButton

一个圆形控件，继承与ImageView。

<img src="http://www.apkbus.com/data/attachment/forum/201507/27/183235na3e1n33mu4s6eul.jpg"/>

使用：在xml文件中添加：

``` xml
<android.support.design.widget.FloatingActionButton
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:onClick="click"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:layout_alignParentBottom="true"
        android:layout_margin="0dp"
        android:src="@drawable/add"
        app:backgroundTint="#FF00FF00"
        app:rippleColor="#FF0000FF"
        app:borderWidth="0dp"
        app:fabSize="normal"
        app:elevation="10dp"
        app:pressedTranslationZ="20dp"/>
```

简单解释一下命名空间为app的配置项。

1. `app:backgroundTint`是指定默认的背景颜色
2. `app:rippleColor`是指定点击时的背景颜色
3. `app:borderWidth` border的宽度
4. `app:fabSize`是指FloatingActionButton的大小，可选normal|mini
5. `app:elevation` 可以看出该控件有一个海拔的高度
6. `app:pressedTranslationZ` 哈，按下去时的z轴的便宜

# CoordinatorLayout

CoordinatorLayout这个控件的作用是让它的子view更好的去协作，在接下来的时间里，我们基本都会用到这个控件，这里我们只是简单的用CoordinatorLayout来包裹一下FloatingActionButton来达到和Snackbar更好协作的效果。修改我们的布局:

``` xml

<android.support.design.widget.CoordinatorLayout
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:layout_alignParentBottom="true"
	android:layout_alignParentRight="true">
	
	<android.support.design.widget.FloatingActionButton
		xmlns:app="http://schemas.android.com/apk/res-auto"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:layout_margin="0dp"
		android:onClick="click"
		android:layout_gravity="right"
		android:src="@drawable/add"
		app:backgroundTint="#FF00FF00"
		app:borderWidth="0dp"
		app:elevation="20dp"
		app:fabSize="normal"
		app:pressedTranslationZ="10dp"
		app:rippleColor="#FF0000FF" />
		
</android.support.design.widget.CoordinatorLayout>
```

此时的效果：

<img src="http://www.apkbus.com/data/attachment/forum/201507/27/183235vb513z17mb350o50.jpg">

# AppBarLayout

AppBarLayout这个控件也是让子view共同协作的，它和CoordinatorLayout的区别在于：
AppBarLayout是在效果上的协作，用AppBarLayout包裹的子view会以一个整体的形式作为AppBar。
CoordinatorLayout是在行为上的一种协作，尤其是在滚动的协作上，可以说非常完美(额， 也不是那么完美)

<img src="http://www.apkbus.com/data/attachment/forum/201507/27/183241bb8gotb0gzvfrf6y.jpg"/>

代码为：

``` xml
<android.support.design.widget.CoordinatorLayout
	xmlns:android="http://schemas.android.com/apk/res/android"
	xmlns:tools="http://schemas.android.com/tools"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:fitsSystemWindows="true"
	tools:context=".MainActivity">
	
	<android.support.design.widget.AppBarLayout
		android:layout_width="match_parent"
		android:layout_height="wrap_content">
		
		<android.support.v7.widget.Toolbar
			android:id="@+id/tb"
			android:layout_width="match_parent"
			android:layout_height="?attr/actionBarSize"/>
			
		<android.support.design.widget.TabLayout
			android:id="@+id/tl"
			android:layout_width="match_parent"
			android:layout_height="wrap_content"/>
			
	</android.support.design.widget.AppBarLayout>
	
</android.support.design.widget.CoordinatorLayout>
```

***

参考文档：

1. [高逼格UI-ASD(Android Support Design)](http://blog.csdn.net/qibin0506/article/details/46850763);
2. [Android Design Support Library 的 代码实验——几行代码，让你的 APP 变得花俏](http://www.open-open.com/lib/view/open1436152483833.html)



