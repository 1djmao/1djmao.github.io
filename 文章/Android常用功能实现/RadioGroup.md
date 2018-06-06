1. RadioButton 去掉图标

```
android:button="@null"
```
2. 文字颜色选择器

    路径：res/color/radio_textcolor_select.xml

```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_checked="true" android:drawable="@color/md_white_1000"/>
    <item android:state_checked="false" android:drawable="@color/bg_grey"/>
</selector>
```

3. 背景选择器

    路径：res/drawable/radio_bg_select.xml

```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_checked="true" android:drawable="@color/md_white_1000"/>
    <item android:state_checked="false" android:drawable="@color/bg_grey"/>
</selector>
```
4. style

```
<!--统计界面的月和年的切换 RadioButton-->
    <style name="MouthWeek">
        <item name="android:button">@null</item>
        <item name="android:textColor">@color/radio_textcolor_select</item>
        <item name="android:background">@drawable/radio_bg_select</item>
        <item name="android:textSize">15sp</item>
        <item name="android:gravity">center</item>
        <item name="android:layout_width">45dp</item>
        <item name="android:layout_height">45dp</item>
    </style>
```

5. 布局

```
<RadioGroup
            android:id="@+id/date_modle_select"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:checkedButton="@id/rb_week"
            android:orientation="horizontal">
            <RadioButton
                android:id="@+id/rb_month"
                android:text="月"
                style="@style/MouthWeek"/>
            <RadioButton
                android:id="@+id/rb_week"
                android:text="周"
                style="@style/MouthWeek"/>
        </RadioGroup>
```

6. 监听

```
mRadioGroup=findViewById(R.id.date_modle_select);
        mRadioGroup.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(RadioGroup radioGroup, int i) {
                switch (i){
                    case R.id.rb_week:
                        Log.i("hhhhh", "onCheckedChanged: week"+i);

                        break;
                    case R.id.rb_month:

                        Log.i("hhhhh", "onCheckedChanged: month"+i);
                        break;
                }
            }
        });
```
7. 效果

![](https://ws1.sinaimg.cn/large/64ddfd2bgy1fme2a22q99j203g01saa0.jpg)