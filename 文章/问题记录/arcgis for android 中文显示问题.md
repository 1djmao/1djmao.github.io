对于TextSymbol中中文不能显示的问题，网上找到了几种解决方案，对比后采用
[ArcGIS For Android 中文标注不显示解决方案](http://blog.csdn.net/u014014578/article/details/36180485)。

### 解决方案
这个方案是把文字转成bitmap，再以PictureMarkerSymbol的形式加到graphic上，关键代码：


```
public static Drawable createMapBitMap(String text) {  
  
        Paint paint = new Paint();  
        paint.setColor(Color.BLACK);  
        paint.setTextSize(20);  
        paint.setAntiAlias(true);  
        paint.setTextAlign(Align.CENTER);  
  
        float textLength = paint.measureText(text);  
  
        int width = (int) textLength + 10;  
        int height = 40;  
  
        Bitmap newb = Bitmap.createBitmap(width, height, Config.ARGB_8888);  
  
        Canvas cv = new Canvas(newb);  
        cv.drawColor(Color.parseColor("#00000000"));  
  
        cv.setDrawFilter(new PaintFlagsDrawFilter(0, Paint.ANTI_ALIAS_FLAG  
                | Paint.FILTER_BITMAP_FLAG));  
  
        cv.drawText(text, width / 2, 20, paint);  
  
        cv.save(Canvas.ALL_SAVE_FLAG);// 保存  
        cv.restore();// 存储  
  
        return new BitmapDrawable(newb);  
  
    }  
```
这种方式会对性能造成影响，经过我的测试，添加100个文字标注需要3·4秒，而同样的文字，使用TextSymbol只有1秒，所以使用的时候要注意这个问题。

我对这个方法进行了完善，加入了不同dpi屏幕的大小适配和文字显示位置的设置，并打包成一个类，方便以后直接使用。

### 完整类：

```
public class CHTextSymbol{
    int textSize;
    int textColor;
    String text;
    float dpi;

    int horizontalAlignment=2;
    int verticalAlignment=0;
    public static final int LEFT=0;
    public static final int CENTER=1;
    public static final int RIGHT=2;
    public static final int TOP=0;
    public static final int BOTTOM=2;


    public CHTextSymbol(int textSize, int textColor, String text, Context context) {
        this.textSize = textSize;
        this.textColor = textColor;
        this.text = text;
        DisplayMetrics metrics= context.getResources().getDisplayMetrics();
        int ppi=metrics.densityDpi;
        dpi=ppi/160f;
    }
    public PictureMarkerSymbol build(int horizontalAlignment,int verticalAlignment){
        this.horizontalAlignment=horizontalAlignment;
        this.verticalAlignment=verticalAlignment;
        PictureMarkerSymbol symbol=new PictureMarkerSymbol(createMapBitMap(text));
        return symbol;
    }
    public PictureMarkerSymbol build(){
        PictureMarkerSymbol symbol=new PictureMarkerSymbol(createMapBitMap(text));
        return symbol;
    }
    /**
     * 文字转换BitMap
     * @param text
     * @return
     */
    private Drawable createMapBitMap(String text) {

        Paint paint = new Paint();
        paint.setColor(textColor);
        paint.setTextSize(textSize*dpi);
        paint.setAntiAlias(true);
        paint.setTextAlign(Paint.Align.CENTER);

        float textLength = paint.measureText(text);

        int width = (int) textLength*2;
        int height = (int) (textSize*dpi*2);

        Bitmap newb = Bitmap.createBitmap(width, height, Bitmap.Config.ARGB_8888);

        Canvas cv = new Canvas(newb);
        cv.drawColor(Color.parseColor("#00000000"));

        cv.setDrawFilter(new PaintFlagsDrawFilter(0, Paint.ANTI_ALIAS_FLAG
                | Paint.FILTER_BITMAP_FLAG));

        float x=width*3/4;
        float y=height/2-textSize/2;
        switch (horizontalAlignment){
            case LEFT:
                x=width/4;
                break;
            case CENTER:
                x=width/2;
                break;
            case RIGHT:
                x=width*3/4;
                break;
        }
        switch (verticalAlignment){
            case TOP:
                y=height/2-textSize/2;
                break;
            case CENTER:
                y=height*3/4-textSize/2;
                break;
            case BOTTOM:
                y=height-textSize/2;
                break;
        }

        cv.drawText(text, x,y, paint);

        cv.save(Canvas.ALL_SAVE_FLAG);// 保存
        cv.restore();// 存储

        return new BitmapDrawable(newb);

    }
}

```
### 使用

```
graphicsLayer.addGraphic(new Graphic(point,new CHTextSymbol(20,Color.BLUE,"HSJKF哈市将开发哈坎",this).build()));
graphicsLayer.addGraphic(new Graphic(point,new CHTextSymbol(20,Color.BLUE,"HSJKF哈市将开发哈坎",this).build(CHTextSymbol.LEFT,CHTextSymbol.BOTTOM)));
```
build中可以设置文字的位置，默认是右上角。
