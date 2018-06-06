### 1. 中引用so文件
把so文件放入libs下各自的文件夹中
在android标签中添加
```
 repositories {
        flatDir {
            dirs 'libs'
        }
    }
```
### 2. java.lang.UnsatisfiedLinkError 错误

> Java.lang.UnsatisfiedLinkError: dalvik.system.PathClassLoader
[DexPathList[[zip file "/data/app/com.pckgname.live-2/base.apk"],
nativeLibraryDirectories=[/data/app/com.pckgname.live-2/lib/arm64, /vendor/lib64, /system/lib64]]] couldn't find "libvinit.so"

在app的build.gradle中添加如下代码,然后rebuild。

```
android {
    defaultConfig {
        multiDexEnabled true
        ndk {
            abiFilters "armeabi", "armeabi-v7a", "x86", "mips"
        }
    }
    sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
}
```
### 3. Duplicate files copied in APK META-INF/NOTICE.txt 错误

在app 下的 build.gradle 中的 android 部分增加一段配置：
```
android {  
      
    packagingOptions {  
        exclude 'META-INF/DEPENDENCIES.txt'  
        exclude 'META-INF/LICENSE.txt'  
        exclude 'META-INF/NOTICE.txt'  
        exclude 'META-INF/NOTICE'  
        exclude 'META-INF/LICENSE'  
        exclude 'META-INF/DEPENDENCIES'  
        exclude 'META-INF/notice.txt'  
        exclude 'META-INF/license.txt'  
        exclude 'META-INF/dependencies.txt'  
        exclude 'META-INF/LGPL2.1'  
    }  
}  
```
### 4. 编译错误“编码GBK的不可映射字符”
project 的 build.gradle 中加入
```
tasks.withType(JavaCompile) {  
    options.encoding = "UTF-8"  
}  
```
