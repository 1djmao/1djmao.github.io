# 问题

使用VideoView播放网络视频，需要使用cookie进行验证，但是setVideoURI (Uri uri, Map<String, String> headers)需要api21以上才行。

![image](http://ww2.sinaimg.cn/large/64ddfd2bgy1fck7lfvgt5j20qb088glm.jpg)
# 解决
在下面找到了解决办法，使用自定义Videoview来实现在低版本中加入cookie。
http://stackoverflow.com/questions/17730034/custom-videoview-to-send-custom-headers

---

[代码下载](http://download.csdn.net/detail/iidjmao/9750856)

里面包括两个类VideoView和Metadata两个类，可以正常使用setVideoURI (Uri uri, Map<String, String> headers)。如果缺少字符串资源，可以自己添加

```
<string name="VideoView_error_title">Cannot play video</string>
    <string name="VideoView_error_text_invalid_progressive_playback">Sorry, this video is not valid for streaming to
        this device.
    </string>
    <string name="VideoView_error_text_unknown">Sorry, this video cannot be played.</string>
    <string name="VideoView_error_button">OK</string>
    <string name="mediacontroller_play_pause">Play/Pause</string>
```

代码中添加cookie

```
Map<String, String> headers = new HashMap<String, String>();
        headers.put("Cookie", URLs.getCookei(this));
        aVideoView= (com.example.shuili.VideoView) findViewById(R.id.video);
        aVideoView.requestFocus();
        aVideoView.setVideoURI(Uri.parse(path),headers);

        MediaController mc = new MediaController(this);
        aVideoView.setMediaController(mc);
        aVideoView.setOnPreparedListener(new MediaPlayer.OnPreparedListener() {
            @Override
            public void onPrepared(MediaPlayer mp) {
                mp.start();
            }
        });
```
