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
