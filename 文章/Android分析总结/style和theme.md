### 1. style
先看代码，
布局文件：

```
<TextView
        android:text="text1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:textSize="18sp"
        android:background="#a0a0a0"
        android:padding="20dp"
        android:layout_margin="20dp"
        android:textColor="#66ccff"/>
    <TextView
        android:text="text2"
        style="@style/MyStyle"/>
    <ImageView
        android:src="@mipmap/ic_launcher"
        style="@style/MyStyle"/>
```
style文件：

```
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>
    <style name="MyStyle">
        <item name="android:layout_width">match_parent</item>
        <item name="android:layout_height">wrap_content</item>
        <item name="android:textSize">18sp</item>
        <item name="android:padding">20dp</item>
        <item name="android:layout_margin">20dp</item>
        <item name="android:gravity">center</item>
        <item name="android:textColor">#66ccff</item>
        <item name="android:background">#a0a0a0</item>

    </style>

</resources>
```
显示效果：

![image](http://ww4.sinaimg.cn/large/64ddfd2bgw1fajfpceqs5j20bc0iv3yv.jpg)

可以发现，style就是把控件中相同的属性抽出来，统一引用，在同一个app中便于统一显示效果和修改。
### 2. 继承
style有两种继承方式：
1. 通过“.”继承，在符style后加“.”进行继承，这种方式适用于自定义的style，不能用于继承系统style。

```
<style name="CodeFont.Red.Big">
        <item name="android:textSize">30sp</item>
    </style>
```

2. 通过“parent”继承，这种方式用于所有已有style，如果使用这种继承的方式的话，命名时的“.”继承方式就会失效，只作为一种命名的方式。

```
<style name="GreenText" parent="@android:style/TextAppearance">
        <item name="android:textColor">#00FF00</item>
    </style>
```
### 3. theme
我们注意到theme都是在style中定义的，这说明，他只是一种特殊的style，其用法和style是相同的。
theme和其他style不同的地方主要在于其中的属性不同，以及应用的地方不同。

* theme是应用在application和activity中的，所以其中的属性主要都是定义窗体的，如修改状态栏，应用标题等，通常在androidmanifeat.xml中使用；
* 普通style是用于定义view或viewgroup的所以其主要定义的是控件的属性。

theme中的常见属性：
![image](http://ww3.sinaimg.cn/large/64ddfd2bgw1faka206wkkj20j60b50t8.jpg)

所有属性见[R.attr](https://developer.android.google.cn/reference/android/R.attr.html)。

所有可用的style和theme见[R.style](https://developer.android.google.cn/reference/android/R.style.html)。



[google api指南](https://developer.android.google.cn/guide/topics/ui/themes.html#ApplyingStyles)