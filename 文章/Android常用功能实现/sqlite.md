#### 1.SQLite 表如果不存在就创建 
    "CREATE TABLE IF NOT EXISTS "+table+" (...............)"
#### 2.遍历sqlite查询结果Cursor
    Cursor c=supDb.rawQuery(
				"select fieldval from feature where (subcode=? or subcode=?) and fieldname=?", 
				new String[]{"ALL",subcode,fieldname});
		if(c.moveToFirst()){
			for(c.moveToFirst();!c.isAfterLast();c.moveToNext()){
				String str=c.getString(0);
			}
		}
#### 3.判断表是否存在
    String sql = "select count(*) as c from sqlite_master where type ='table' and name ='" +typeCode+"_point"+
				"';";
        Cursor cursor = db.rawQuery(sql, null);
        if(cursor.moveToFirst()){
                int count = cursor.getInt(0);
                if(count==0){
                        return  true;
                }
        }
#### 4. 列出sqlite所有表

```
Cursor cursor = db.rawQuery("select name from sqlite_master where type='table' order by name", null);
```
#### 5. 删除表
删除表

```
SQLiteDatabase.execSQL("DROP TABLE CUSTOMERS")
```
清空比偶中数据

```
SQLiteDatabase.execSQL("DELETE FROM CUSTOMERS")
```
