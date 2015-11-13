# ViewpagerFragmentStateDemo
keep fragment has listview in viewpager 

ViewPager中切换界面Fragment被销毁

ViewPager的默认加载方式是缓存当前界面前后相邻的两个界面，即最多共缓存包括当前界面在内的三个界面信息。当滑动切换界面的时候，非相邻界面信息将被释放。界面2是当前界面，界面1和3是缓存界面，当切换到1时，界面2仍缓存，界面3被销毁释放，于是便有了onDestroyView的调用。由1切换到2或3时，界面3又被重新创建，于是走了onCreateView流程。

解决方案一：vpMain.setOffscreenPageLimit(2);//一般界面少于或等于3个的时候使用，避免占用过多内存。

以下代码在 HomeActivity extends FragmentActivity中

vpMain = (ViewPager) findViewById(R.id.vpMain);

FragmentPagerAdapter pagerAdapter = new MyFragmentPagerAdapter(this.getSupportFragmentManager());
// 如果当前页面是1，以下语句将缓存2与3两个界面。默认值为1，将缓存与之相邻的两个界面。

// vpMain.setOffscreenPageLimit(2);//一般界面少于或等于3个的时候使用，避免占用过多内容。


解决方案二：在Child1Fragment extends Fragment中，

private View mFragmentView;
@Override
public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
if (mFragmentView == null) {
mFragmentView = inflater.inflate(R.layout.frag_goodslist, null, false);
myHandler = new MyHandler();
getGoodsData();
// PromptManager.showLoadingImg(getActivity());
listGoods = (ListView) mFragmentView.findViewById(R.id.listGoods);
progressBar = (ProgressBar) mFragmentView.findViewById(R.id.progressBar);
progressBar.setVisibility(ProgressBar.VISIBLE);
}
return mFragmentView;
}

重用mFragmentView的话，一定要记得在onDestroyView里面把mFragmentView先给移除掉，因为已经有过父布局的View是不能再次添加到另一个新的父布局上面的。代码如下：

@Override
public void onDestroyView() {
if (null != mFragmentView) {
((ViewGroup) mFragmentView.getParent()).removeView(mFragmentView);
}
}


解决方案三：保存状态并恢复
此方案适用于可用界面信息可由状态保存和恢复实现的情况。在onDestroyView方法内保存相关信息，在onCreateView方法内恢复信息设置。 


解决方案四：FragmentPagerAdapter 继承了 PagerAdapter ，这里我们自定义 FragmentPagerAdapter ()

详情请看：

http://www.cnblogs.com/tiantianbyconan/p/3364728.html
