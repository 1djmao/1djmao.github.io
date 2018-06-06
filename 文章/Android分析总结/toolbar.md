## 使用
xml中直接添加，可以在其中添加子控件，实现自己的布局。

```
<android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/color_0176da">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="HelloWorld" />
    </android.support.v7.widget.Toolbar>
```
java代码中，activity要继承AppCompatActivity类，直接使用NoActionBar主题来替换actionbar。

```
Toolbar toolbar= (Toolbar) findViewById(R.id.toobar);
setSupportActionBar(toolbar);
```
## 控件
![image](http://www.jcodecraeer.com/uploads/20141118/1416285884351.png)
    
1. setNavigationIcon
    即设定 up button 的图标，因为 Material 的介面，在 Toolbar这里的 up button样式也就有別于过去的 ActionBar 哦。

2. setLogo
    APP 的图标。

1. setTitle
    主标题。

1. setSubtitle
    副标题。

1. setOnMenuItemClickListener
    设定菜单各按鈕的动作。

## 菜单
#### menu文件：
```
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
    >
    <item
        android:id="@+id/action_search"
        android:title="搜索点号"
        app:showAsAction="always"
        android:icon="@drawable/serch"
        />
    
    <item
        android:id="@+id/action_openpro"
        android:orderInCategory="100"
        app:showAsAction="never"
        android:title="打开工程"/>
    <item
        android:id="@+id/action_menagepro"
        android:orderInCategory="100"
        app:showAsAction="never"
        android:title="工程管理"/>
    <item
        android:id="@+id/action_showtype"
        android:orderInCategory="100"
        app:showAsAction="never"
        android:title="查看类型"/>
    <item
        android:id="@+id/action_showall"
        android:orderInCategory="100"
        app:showAsAction="never"
        android:title="全图显示"/>
   
    <item
        android:id="@+id/action_settings"
        android:orderInCategory="100"
        app:showAsAction="never"
        android:title="设置"/>

</menu>
```
效果预览

![image](http://ww3.sinaimg.cn/large/64ddfd2bgy1fet77ep7x5j20bd0ivaa8.jpg)

#### activity中使用

```
@Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main, menu);
        return super.onCreateOptionsMenu(menu);
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        Log.i("hhhhh", "onOptionsItemSelected: ");
        switch (item.getItemId()) {
            case R.id.action_search:
                
                break;
            case R.id.action_settings:
                
                break;
            case R.id.action_openpro:
                
                break;
            case R.id.action_menagepro:
                
                break;
            case R.id.action_showall:
                
                break;
            case R.id.action_showtype:
                
                break;
            default:
                break;
        }
        return super.onOptionsItemSelected(item);
    }
```
menu按钮的颜色改为白色：

```
app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
```
#### menu动态控制
通过toolbar获取到menu之后可以动态控制MenuItem的显示方式，以实现在不同情况下显示不同的操作。

```
Menu menu=mToolbar.getMenu();
MenuItem menuItemEdit=menu.findItem(R.id.action_edit);
MenuItem menuItemDelect=menu.findItem(R.id.action_delect);
menuItemDelect.setVisible(false);
menuItemEdit.setVisible(false);
```





## 相关文章：

[Android Toolbar，你想知道的都在这里了](http://yifeng.studio/2016/10/12/android-toolbar/)

[android：ToolBar详解（手把手教程）](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1118/2006.html)

[Android ToolBar 使用完全解析](http://www.jianshu.com/p/ae0013a4f71a#)