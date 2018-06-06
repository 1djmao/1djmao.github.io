使用tablayout+viewpage+fragment实现滑动页面，参考原文：
[http://blog.csdn.net/chenguang79/article/details/48804125](http://blog.csdn.net/chenguang79/article/details/48804125)
### 1. 布局文件中添加控件
开始之前之前要先添加依赖库
```
compile 'com.android.support:design:25.0.0'
```
添加控件
```
<android.support.design.widget.TabLayout
        android:id="@+id/tablayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
    </android.support.design.widget.TabLayout>
    <android.support.v4.view.ViewPager
        android:id="@+id/viewpage"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_below="@+id/tablayout"/>
```
关于tablayout的详细属性可以看这篇文章[http://www.jianshu.com/p/2b2bb6be83a8](http://www.jianshu.com/p/2b2bb6be83a8)
### 2. viewpage适配器
可以直接用
```
public class FragmentViewPageAdapter extends FragmentPagerAdapter {
    private List<Fragment> list_fragment;                         //fragment列表
    private List<String> list_Title;

    public FragmentViewPageAdapter(FragmentManager fm, List<Fragment> list_fragment, List<String> list_Title) {
        super(fm);
        this.list_fragment = list_fragment;
        this.list_Title = list_Title;
    }

    @Override
    public Fragment getItem(int position) {
        return list_fragment.get(position);
    }

    @Override
    public int getCount() {
        return list_Title.size();
    }
    //此方法用来显示tab上的名字
    @Override
    public CharSequence getPageTitle(int position) {

        return list_Title.get(position % list_Title.size());
    }
}
```
### 3. fragment
这里使用newInstance传递参数进行初始化。
```
public static class MyFragment extends Fragment{
        private String text;
        public static MyFragment newInstance(String text) {

            Bundle args = new Bundle();

            MyFragment fragment = new MyFragment();
            fragment.text=text;
            fragment.setArguments(args);
            return fragment;
        }
        @Nullable
        @Override
        public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            View view = inflater.inflate(R.layout.fragment_layout,container,false);
            TextView textView= (TextView) view.findViewById(R.id.fragment_text);
            textView.setText(text);
            return view;

        }
    }
```
### 4. 初始化数据并设置适配器
```
mTabLayout= (TabLayout) findViewById(R.id.tablayout);
        mViewPager= (ViewPager) findViewById(R.id.viewpage);
        List<String> stringList=new ArrayList<>();
        stringList.add("page1");
        stringList.add("page2");
        stringList.add("page3");
        List<Fragment> fragmentList=new ArrayList<>();
        fragmentList.add(MyFragment.newInstance(stringList.get(0)));
        fragmentList.add(MyFragment.newInstance(stringList.get(1)));
        fragmentList.add(MyFragment.newInstance(stringList.get(2)));

        FragmentViewPageAdapter pageAdapter=new FragmentViewPageAdapter(getSupportFragmentManager(),fragmentList,stringList);
        mViewPager.setAdapter(pageAdapter);
        mTabLayout.setupWithViewPager(mViewPager);
```
显示效果：
![image](http://ww1.sinaimg.cn/large/64ddfd2bgw1fadbz3pfarj20hg0swtab.jpg)