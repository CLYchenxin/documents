# Fragment

###01 Fragment的生命周期

<img src="http://img.blog.csdn.net/20140719225005356?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG1qNjIzNTY1Nzkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast">

可以看到Fragment比Activity多了几个额外的生命周期回调方法：

	onAttach(Activity)
	
当Fragment与Activity发生关联时调用。

	onCreateView(LayoutInflater, ViewGroup,Bundle)
	
创建该Fragment的视图

	onActivityCreated(Bundle)
	
当Activity的onCreate方法返回时调用
	
	onDestoryView()
	
与onCreateView想对应，当该Fragment的视图被移除时调用

	onDetach()
	
与onAttach相对应，当Fragment与Activity关联被取消时调用

> 注意：除了onCreateView，其他的所有方法如果你重写了，必须调用父类对于该方法的实现，

###02 使用Fragment

####2.1 静态的使用Fragment

``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:tools="http://schemas.android.com/tools"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent" >  
  
    <fragment  
        android:id="@+id/id_fragment_title"  
        android:name="com.zhy.zhy_fragments.TitleFragment"  
        android:layout_width="fill_parent"  
        android:layout_height="45dp" />  
  
    <fragment  
        android:layout_below="@id/id_fragment_title"  
        android:id="@+id/id_fragment_content"  
        android:name="com.zhy.zhy_fragments.ContentFragment"  
        android:layout_width="fill_parent"  
        android:layout_height="fill_parent" />  
  
</RelativeLayout>  
```

注意，使用fragment标签，在name中设置fragment的所属类。

####2.2 动态的使用Fragment

``` xml
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:tools="http://schemas.android.com/tools"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent" >  
  
    <fragment  
        android:id="@+id/id_fragment_title"  
        android:name="com.zhy.zhy_fragments.TitleFragment"  
        android:layout_width="fill_parent"  
        android:layout_height="45dp" />  
  
    <include  
        android:id="@+id/id_ly_bottombar"  
        android:layout_width="fill_parent"  
        android:layout_height="55dp"  
        android:layout_alignParentBottom="true"  
        layout="@layout/bottombar" />  
  
    <FrameLayout  
        android:id="@+id/id_content"  
        android:layout_width="fill_parent"  
        android:layout_height="fill_parent"  
        android:layout_above="@id/id_ly_bottombar"  
        android:layout_below="@id/id_fragment_title" />  
  
</RelativeLayout> 
```

在Activity中可以使用FragmentManager动态的设置FrameLayout的所属类。

``` java
@Override  
    public void onClick(View v)  {  
        FragmentManager fm = getFragmentManager();  
        // 开启Fragment事务  
        FragmentTransaction transaction = fm.beginTransaction();  
  
        switch (v.getId())  
        {  
        case R.id.tab_bottom_weixin:  
            if (mWeixin == null)  
            {  
                mWeixin = new ContentFragment();  
            }  
            // 使用当前Fragment的布局替代id_content的控件  
            transaction.replace(R.id.id_content, mWeixin);  
            break;  
        case R.id.tab_bottom_friend:  
            if (mFriend == null)  
            {  
                mFriend = new FriendFragment();  
            }  
            transaction.replace(R.id.id_content, mFriend);  
            break;  
        }  
        // transaction.addToBackStack();  
        // 事务提交  
        transaction.commit();  
    }  
```

####2.3 FragmentManage

获取FragmentManage的方式：

	getFragmentManager() // v4中，getSupportFragmentManager

主要的操作都是FragmentTransaction的方法

	FragmentTransaction transaction = fm.benginTransatcion();//开启一个事务
	transaction.add() 
	
往Activity中添加一个Fragment

	transaction.remove() 
	
从Activity中移除一个Fragment，如果被移除的Fragment没有添加到回退栈（回退栈后面会详细说），这个Fragment实例将会被销毁。

	transaction.replace()
	
使用另一个Fragment替换当前的，实际上就是remove()然后add()的合体~
	
	transaction.hide()
	
隐藏当前的Fragment，仅仅是设为不可见，并不会销毁

	transaction.show()
	
显示之前隐藏的Fragment

	detach()

会将view从UI中移除,和remove()不同,此时fragment的状态依然由FragmentManager维护。

	attach()
	
重建view视图，附加到UI上并显示。

	transatcion.commit()//提交一个事务

> 注意：常用Fragment的哥们，可能会经常遇到这样Activity状态不一致：State loss这样的错误。主要是因为：commit方法一定要在Activity.onSaveInstance()之前调用。

###03 管理Fragment会退栈

``` java
@Override  
public void onClick(View v)  
{  
   FragmentTwo fTwo = new FragmentTwo();  
   FragmentManager fm = getFragmentManager();  
   FragmentTransaction tx = fm.beginTransaction();  
   tx.replace(R.id.id_content, fTwo, "TWO");  
   tx.addToBackStack(null);  
   tx.commit();  
  
}  
```

在管理Fragment时，添加`tx.addToBackStack`，这行代码添加会退栈，点击返回时，回返回到以前的界面。

使用了replace方法，一定记得replace是remove和add的合体，并且如果不添加事务到回退栈，前一个Fragment实例会被销毁。这里很明显，我们调用tx.addToBackStack(null);将当前的事务添加到了回退栈，所以FragmentOne实例不会被销毁，但是视图层次依然会被销毁，即会调用onDestoryView和onCreateView，证据就是：仔细看上面的效果图，我们在跳转前在文本框输入的内容，在用户Back得到第一个界面的时候不见了。

``` java
@Override  
public void onClick(View v)  
{  
   FragmentThree fThree = new FragmentThree();  
   FragmentManager fm = getFragmentManager();  
   FragmentTransaction tx = fm.beginTransaction();  
   tx.hide(this);  
   tx.add(R.id.id_content , fThree, "THREE");  
//      tx.replace(R.id.id_content, fThree, "THREE");  
   tx.addToBackStack(null);  
   tx.commit();  
}  
```

这里点击时，我们没有使用replace，而是先隐藏了当前的Fragment，然后添加了FragmentThree的实例，最后将事务添加到回退栈。这样做的目的是为了给大家提供一种方案：如果不希望视图重绘该怎么做，请再次仔细看效果图，我们在FragmentTwo的EditText填写的内容，用户Back回来时，数据还在~~~

###04 一些技巧

一般情况下，这么动态添加Fragment

``` java
public class MainActivity extends FragmentActivity  {  
      
    private ContentFragment mContentFragment  ;   
  
    @Override  
    protected void onCreate(Bundle savedInstanceState)  
    {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
      
        FragmentManager fm = getSupportFragmentManager();  
        mContentFragment = (ContentFragment) fm.findFragmentById(R.id.id_fragment_container);  
          
        if(mContentFragment == null )  
        {  
            mContentFragment = new ContentFragment();  
            fm.beginTransaction().add(R.id.id_fragment_container,mContentFragment).commit();  
        }  
  
    }  
  
}  
```

***

参考资料：

1. [Android Fragment 你应该知道的一切](http://blog.csdn.net/lmj623565791/article/details/42628537)

2. [Android Fragment 真正的完全解析（上）](http://blog.csdn.net/lmj623565791/article/details/37970961)

3. [Android Fragment 真正的完全解析（下）](http://blog.csdn.net/lmj623565791/article/details/37992017)

4. [Android 官方推荐 : DialogFragment 创建对话框](http://blog.csdn.net/lmj623565791/article/details/37815413)

5. [Android 屏幕旋转 处理 AsyncTask 和 ProgressDialog 的最佳方案](http://blog.csdn.net/lmj623565791/article/details/37936275)


