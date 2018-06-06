# WebView 的使用

```
mWebView.getSettings().setJavaScriptEnabled(true);
mWebView.loadUrl("file:///android_asset/demo.html");

```
设置WevView要显示的网页
```
//打开本包内asset目录下的demo.html文件
myWebView.loadUrl(" file:///android_asset/demo.html ");  
//打开本地sd卡内的demo.html文件
myWebView.loadUrl("content://com.android.htmlfileprovider/sdcard/demo.html");
//打开指定互联网URL的html文件
myWebView.loadUrl(" http://www.example.com/demo.html");
```

# javascrip 调用 java 代码
1. 要在 js 中执行的 java 方法要加上 @JavascriptInterface，如：
    
	```
	@JavascriptInterface
	    public  void toMain2(){
	        Intent intent=new Intent(MainActivity.this,Main2Activity.class);
	        startActivity(intent);
	    }
	```

2. webview 使用 addJavascriptInterface 方法把 java 类传进 js 中，其第一个参数是 java 实例，第二个参数是在 js 中的变量名，==调用此方法也需要使用标签 @SuppressLint("JavascriptInterface")==：
    
	```
	@SuppressLint("JavascriptInterface")
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	        WebView webView= (WebView) findViewById(R.id.web);
	        webView.getSettings().setJavaScriptEnabled(true);
	        webView.loadUrl("file:///android_asset/water/index.html");
	        webView.addJavascriptInterface(this, "ToMain2");
	
	    }
	```
3. html 中使用，也可以携带参数

```
<button type="button" onclick="ToMain2.toMain2()">打开Main2Activity</button>
```



# java 中调用 javascript 代码
1. 先定义一个 js 方法：
    
	```
	function androidGetJs(msg){
	    document.getElementById("androidgetjs").innerHTML="来自Android的消息："+msg;
	  }
	```
2. 在 java 中通过 webview.loadUrl("javascript:") 调用：
    
	```
	webview.loadUrl("javascript:androidGetJs('" + msg + "')");
	```


**参考文章**：
[Android与Js调用](http://www.jianshu.com/p/cb928de89d49)
