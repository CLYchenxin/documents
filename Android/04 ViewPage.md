# ViewPage

> 注意：使用ViewPage时必须使用FragmentActivity作为Activity，并且所有的Fragment必须是android.support.v4.app.Fragment的子类。因为ViewPage是android.support.v4的类。

### 01 基本设置

xml中：

``` xml
<android.support.v4.view.ViewPager
        android:id="@+id/main_viewPage"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
```

设置Adapter，其中适配器可以是FragmentPagerAdapter、FragmentStatePagerAdapter的子类，这两者的区别是：

1. `FragmentStatePagerAdapter`使用FragmentStatePagerAdapter会销毁掉不用的fragment。事务提交后，可将fragment从Activity的FragmentManager中彻底移除。其中的state表明：在销毁fragment时，会将其onSaveInstanceState(Bundle)方法中Bundle信息保存下来，用户切换回原来的界面后，保存的实例状态可用于恢复生成新的fragment
2. `FragmentPagerAdapter`对于不需要的fragment选择调用detach(Fragment)方法，而非Remove(Fragment)方法来处理它。也就是说，FragmentPagerAdapter只是销毁了fragment的视图，但仍讲fragment实例保存在FragmentManager中。因此FragmentPagerAdapter的fragment永远不会被销毁。

创建Adapter：

``` java
public static class PageAdapter extends FragmentPagerAdapter {

   public PageAdapter(FragmentManager fm) {
       super(fm);
   }

   @Override
   public int getCount() {
       return 3;
   }

   @Override
   public Fragment getItem(int position) {
       Fragment fragment = null;
       switch (position) {
           case 0:
               fragment = OneFragment.newInstance();
               break;
           case 1:
               fragment = TwoFragment.newInstance();
               break;
           case 2:
               fragment = ThreeFragment.newInstance();
               break;
       }

       return fragment;
   }

}
```

设置ViewPage：

``` java
mViewPage = (ViewPager) v.findViewById(R.id.main_viewPage);
mViewPage.setAdapter(new PageAdapter(getFragmentManager()));
mViewPage.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
       @Override
       public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
       }

       @Override
       public void onPageScrollStateChanged(int state) {
       }

       @Override
       public void onPageSelected(int position) {
           Log.i("MainActivityFragment", "onPageSelected" + position);
       }
});
```

