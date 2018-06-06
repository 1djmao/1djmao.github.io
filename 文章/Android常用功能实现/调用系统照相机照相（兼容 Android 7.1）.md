Android 7.1 中不再允许直接在应用间传递 file，而需要使用 FileProvider。

1. xml 下 provider_paths.xml

```
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path name="external_files" path="."/>
</paths>
```
2. AndroidManifest.xml

```

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.CAMERA"/>
    
    <application
        <provider
            android:name="android.support.v4.content.FileProvider"
            android:authorities="com.idjmao.sandwaybill.provider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/provider_paths" />
        </provider>
    </application>
```

3. 检查权限调用系统相机方法，权限检查方法见[权限申请最佳实践](http://note.youdao.com/noteshare?id=33353da31aa5899b284c139885a34bce&sub=A2C46FD4AB6349B48C163A773CCA78CB)。

```
/**
 * 调用照相机获取照片
 */
public static void addPhoto(final Activity activity, final File picFile, final int intentRequestCode ) {


 // 申请摄像头和文件读写权限

 PermissionUtil.checkPermission(activity, Manifest.permission.CAMERA, "照相机", 111, new PermissionUtil.Done() {
     @Override
     public void done() {
         PermissionUtil.checkPermission(activity, Manifest.permission.WRITE_EXTERNAL_STORAGE, "文件读写", 1222, new PermissionUtil.Done() {
             @Override
             public void done() {
                 File f=picFile.getParentFile();
                 f.mkdirs();
                 try {
                     picFile.createNewFile();
                 } catch (IOException e) {
                     e.printStackTrace();
                 }

                 Uri fileUri;
                 if (android.os.Build.VERSION.SDK_INT >= Build.VERSION_CODES.N){
                     fileUri = FileProvider.getUriForFile(
                             activity,
                             activity.getPackageName() + ".provider",
                             picFile);
                 }else {
                     fileUri = Uri.fromFile(picFile);
                 }

                 Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
                 intent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri); // set the image file name
                 activity.startActivityForResult(intent, intentRequestCode);
             }
         });
     }
 });
}
```

4. activity 中调用示例，保存好图片的文件路径和 intentRequestCode ，以在返回后进行操作

```
String path= Environment.getExternalStorageDirectory().getAbsolutePath()+"/Pic/";
picFile=new File(path+System.currentTimeMillis()+".jpg");
PhotoUtils.addPhoto((Activity) getContext(),picFile,2);
```

5. activity 中监听返回

```
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    
    if (requestCode==2&&resultCode==RESULT_OK){
        mPresenter.addPhoto(picFile.getAbsolutePath());
        photosList.getAdapter().notifyDataSetChanged();
    }
}
```

