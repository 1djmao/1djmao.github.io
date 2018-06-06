
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
