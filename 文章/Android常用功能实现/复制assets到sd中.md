
```
public class CopyFiles {
	public static void copyFolderFromAssets(Context context, String rootDirFullPath, String targetDirFullPath) {
        try {  
            String[] listFiles = context.getAssets().list(rootDirFullPath);// 遍历该目录下的文件和文件夹
            for (String string : listFiles) {// 看起子目录是文件还是文件夹，这里只好用.做区分了
                if (isFileByName(string)) {// 文件  
                    copyFileFromAssets(context, rootDirFullPath + "/" + string, targetDirFullPath + "/" + string);  
                } else {// 文件夹  
                    String childRootDirFullPath = rootDirFullPath + "/" + string;
                    String childTargetDirFullPath = targetDirFullPath + "/" + string;
                    new File(childTargetDirFullPath).mkdirs();
                    copyFolderFromAssets(context, childRootDirFullPath, childTargetDirFullPath);  
                }  
            }  
        } catch (IOException e) {
            e.printStackTrace();  
        }  
    }  
	private static boolean isFileByName(String string) {
        if (string.contains(".")) {  
            return true;  
        }  
        return false;  
    }  
	public static void copyFileFromAssets(Context context, String assetsFilePath, String targetFileFullPath) {
        InputStream assestsFileImputStream;
        try {  
            assestsFileImputStream = context.getAssets().open(assetsFilePath);  
            File file = new File(targetFileFullPath);
			if (!file.exists()) {
				FileOutputStream fos = new FileOutputStream(file);
				byte[] buffer = new byte[8192];
				int count = 0;// 循环写出
				while ((count = assestsFileImputStream.read(buffer)) > 0) {
					fos.write(buffer, 0, count);
				}
				fos.close();// 关闭流
				assestsFileImputStream.close();
				
			}
             
        } catch (IOException e) {
            e.printStackTrace();  
        }  
    }  
}

```
使用
```
CopyFiles.copyFolderFromAssets(mContext, "DataCollection", rootPath);
```