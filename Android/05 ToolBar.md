# ToolBar

###1 实现步骤

####1.1 在布局文件中添加：

``` xml
<android.support.design.widget.AppBarLayout
	android:id="@+id/appbar"
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:paddingTop="@dimen/appbar_padding_top"
	android:theme="@style/AppTheme.AppBarOverlay">

	<android.support.v7.widget.Toolbar
		android:id="@+id/main_toolBar"
		android:layout_width="match_parent"
		android:layout_height="?android:actionBarSize"
		android:background="?colorPrimary"
		android:elevation="                                               1dp"
		android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
		android:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

</android.support.design.widget.AppBarLayout>
```

####1.2 在activity中设置：

``` java
mToolBar = (Toolbar) findViewById(R.id.main_toolBar);
setSupportActionBar(mToolBar);
```

####1.3 添加item的xml文件：menu_main.xml

``` xml
<menu 

    <item
        android:id="@+id/action_favorite"
        android:icon="@android:drawable/ic_popup_reminder"
        android:title="@string/main_action_favorite"
        android:showAsAction="ifRoom" />

    <item
        android:id="@+id/action_setting"
        android:icon="@android:drawable/ic_menu_set_as"
        android:title="@string/main_action_setting"
        android:showAsAction="ifRoom" />

</menu>
```

####1.4 在Activity中重写父类方法`onCreateOptionsMenu`创建标题栏上的item

``` java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
   getMenuInflater().inflate(R.menu.menu_main, menu);
   return true;
}
```

####1.5 在Activity中重写父类方法`onOptionsItemSelected`创建标题栏item的点击事件

``` java
@Override
public boolean onOptionsItemSelected(MenuItem item) {

   switch (item.getItemId()) {
       case R.id.action_favorite:
           break;
       case R.id.action_setting:
           break;
   }
   return super.onOptionsItemSelected(item);
}
```

####1.6 使用代码设置选中的页面

``` java
mViewPage.setCurrentItem(2, true);
```

###2 TabLayout

可以在ToolBar中添加TabLayout，相当于选项卡的一个东西。主要用于和ViewPage一同协作。

```
<android.support.design.widget.AppBarLayout
	android:id="@+id/appbar"
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:paddingTop="@dimen/appbar_padding_top"
	android:theme="@style/AppTheme.AppBarOverlay">

	<android.support.v7.widget.Toolbar
		android:id="@+id/toolbar"
		android:layout_width="match_parent"
		android:layout_height="?attr/actionBarSize"
		android:background="?attr/colorPrimary"
		app:layout_scrollFlags="scroll|enterAlways"
		app:popupTheme="@style/AppTheme.PopupOverlay" />

	<android.support.design.widget.TabLayout
		android:id="@+id/tabs"
		android:layout_width="match_parent"
		android:layout_height="wrap_content" />

</android.support.design.widget.AppBarLayout>
```

1. app:tabIndicatorColor tab的指示符颜色
2. app:tabSelectedTextColor 选择tab的文本颜色
3. app:tabTextColor 普通tab字体颜色
4. app:tabMode 模式，可选fixed和scrollable fixed是指固定个数，scrollable是可以横行滚动的（逼格高）
5. app:tabGravity 对齐方式，可选fill和center

在代码中设置：

与ViewPage一起使用时：

``` java
TabLayout tabLayout = (TabLayout) findViewById(R.id.tabs);
tabLayout.setupWithViewPager(mViewPager);
```

单独使用时：

``` java
TabLayout tabLayout = (TabLayout) findViewById(R.id.tl);
for(int i=0;i<20;i++) {
	tabLayout.addTab(tabLayout.newTab().setText("TAB" + i));
}
tabLayout.setOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
	@Override
	public void onTabSelected(TabLayout.Tab tab) {
	Toast.makeText(MainActivity.this, tab.getText(), Toast.LENGTH_SHORT).show();
	}
	@Override
	public void onTabUnselected(TabLayout.Tab tab) {
	}
	@Override
	public void onTabReselected(TabLayout.Tab tab) {
	}
});
```

设置TabLayout的选中相：

``` java
tabLayout.setScrollPosition(position, 0, true);
```

***

参考资料：

1. [Android ActionBar完全解析，使用官方推荐的最佳导航栏(上)](http://blog.csdn.net/guolin_blog/article/details/18234477);
2. [Android ActionBar完全解析，使用官方推荐的最佳导航栏(下)](http://blog.csdn.net/guolin_blog/article/details/25466665)


