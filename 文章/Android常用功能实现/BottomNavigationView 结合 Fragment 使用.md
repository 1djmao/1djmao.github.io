基于 androidstudio

1. 使用 AndroidStudio 模板
在 AndroidStudio 中使用 “ Bottom Navigation Activity ” 模板新建 Activity
![](https://ws1.sinaimg.cn/large/64ddfd2bgy1fljwlcm6gmj20mo0j4q4g.jpg)
2. 修改 strings.xml 中“title”系列字段为自己需要的页面名称

```
<resources>
    <string name="app_name">SandWaybill</string>
    <string name="title_todaybill">今日订单</string>
    <string name="title_histrybill">历史订单</string>
    <string name="title_mine">个人中心</string>
</resources>

```
3. 根据自己的需要选择 svg 图标，可以直接选择或者使用本地 svg 图片转换。 ![](https://ws1.sinaimg.cn/large/64ddfd2bgy1fljwj04i9oj20jw0eywfx.jpg)

4. 修改 menu/navigation.xml，主要修改 title 和图标
    
```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:id="@+id/navigation_todaybill"
        android:icon="@drawable/ic_waybill_black_24dp"
        android:title="@string/title_todaybill"/>

    <item
        android:id="@+id/navigation_historybill"
        android:icon="@drawable/ic_history_black_24dp"
        android:title="@string/title_histrybill"/>

    <item
        android:id="@+id/navigation_user"
        android:icon="@drawable/ic_person_black_24dp"
        android:title="@string/title_mine"/>

</menu>

```
5. 检查 layout 确保 FrameLayout id 为content

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.idjmao.sandwaybill.MainActivity">
    <FrameLayout
        android:id="@+id/content"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"></FrameLayout>

    <android.support.design.widget.BottomNavigationView
        android:id="@+id/navigation"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?android:attr/windowBackground"
        app:menu="@menu/navigation"/>

</LinearLayout>
```
6. 创建各个页面的 Fragment
7. Activity 中进行管理

```
public class MainActivity extends AppCompatActivity {

    //管理 fragment
    private Map<String,Fragment> mFragmentMap;

    private BottomNavigationView.OnNavigationItemSelectedListener mOnNavigationItemSelectedListener
            = new BottomNavigationView.OnNavigationItemSelectedListener() {

        @Override
        public boolean onNavigationItemSelected(@NonNull MenuItem item) {
            if (getSupportFragmentManager().findFragmentByTag((String) item.getTitle()) == null) {
                getSupportFragmentManager().beginTransaction()
                        .add(R.id.content, mFragmentMap.get(item.getTitle()), (String) item.getTitle())
                        .commit();
            } else {
                List<Fragment> fragmentList = getSupportFragmentManager().getFragments();
                FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();
                for (Fragment f : fragmentList) {
                    transaction.hide(f);
                }
                transaction.show(getSupportFragmentManager().findFragmentByTag((String) item.getTitle()));
                transaction.commit();
            }

            return true;
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        BottomNavigationView navigation = (BottomNavigationView) findViewById(R.id.navigation);

        mFragmentMap = new HashMap<>();
        mFragmentMap.put(getString(R.string.title_todaybill), new TodayWaybillFragment());
        mFragmentMap.put(getString(R.string.title_histrybill), new historybillFragment());
        mFragmentMap.put(getString(R.string.title_mine), new UserFragment());

        //设置默认选项
        navigation.setSelectedItemId(R.id.navigation_todaybill);
        getSupportFragmentManager().beginTransaction()
                .add(R.id.content, mFragmentMap.get(getString(R.string.title_todaybill)), getString(R.string.title_todaybill))
                .commit();

        navigation.setOnNavigationItemSelectedListener(mOnNavigationItemSelectedListener);
        
    }

}

```
8. 最终效果
![](https://ws1.sinaimg.cn/large/64ddfd2bgy1fljwtujz0yj20u01hcmzx.jpg)

9. 当项目数有四个或以上时，选中的项目会放大并把其他的项目挤到一边，效果很不好。解决办法：

原文 [BottomNavigationView 关闭 shiftingMode](http://www.bijishequ.com/detail/255060?p=18)


```
// 利用发射机制，改变 item 的 mShiftingMode 变量
public class BottomNavigationViewHelper {
  public static void disableShiftMode(BottomNavigationView navigationView) {
    BottomNavigationMenuView menuView = 
        (BottomNavigationMenuView) navigationView.getChildAt(0);
    try {
      Field shiftingMode = menuView.getClass().getDeclaredField("mShiftingMode");
      shiftingMode.setAccessible(true);
      shiftingMode.setBoolean(menuView, false);
      shiftingMode.setAccessible(false);

      for (int i = 0; i < menuView.getChildCount(); i++) {
        BottomNavigationItemView itemView = (BottomNavigationItemView) menuView.getChildAt(i);
        itemView.setShiftingMode(false);
        itemView.setChecked(itemView.getItemData().isChecked());
      }
    } catch (NoSuchFieldException e) {
      // Log
    } catch (IllegalAccessException e) {
      // Log
    }
  }
}
```
使用


```
BottomNavigationView bottomNavigationView = 
    (BottomNavigationView) findViewById(R.id.bnv_bottom_menu);
BottomNavigationViewHelper.disableShiftMode(bottomNavigationView);
```
