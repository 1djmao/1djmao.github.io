SwipeRefreshLayout是google推荐的支持下拉刷新的布局控件，但是只支持下拉刷新不能上划加载更多。我观察知乎的刷新和加载更多操作，发现他的加载更多是不需要上拉手势的，当recyclerview滑到最后一个item时自动加载更多数据。所以我通过viewtype在recyclerview最后添加一个不同布局的item，当这个item显示到屏幕时自动获取更多数据。
### 1. SwipeRefreshLayout
SwipeRefreshLayout包含在v4支持包中，使用很简单，布局：

```
<android.support.v4.widget.SwipeRefreshLayout
        android:id="@+id/swiperefresh"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <android.support.v7.widget.RecyclerView
            android:id="@+id/recycleview"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
        </android.support.v7.widget.RecyclerView>
    </android.support.v4.widget.SwipeRefreshLayout>
```
java代码中，设置刷新监听setOnRefreshListener，通过setRefreshing(false)关闭刷新动画：

```
mSwipeRefreshLayout= (SwipeRefreshLayout) findViewById(R.id.swiperefresh);
        mSwipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                mList.add(0,"ajkfgamj");
                mSwipeRefreshLayout.setRefreshing(false);
                adapter.notifyDataSetChanged();
            }
        });
```
效果：

![image](http://ww1.sinaimg.cn/large/64ddfd2bgw1fakk3828dvg20ge0qz0vw.gif)
### 2. 通过viewtype使recyclerview显示不同布局

1. 定义两个ViewHolder类来显示不同布局：

```
protected class MyViewHolder extends RecyclerView.ViewHolder {
        TextView mTextView1;

        public MyViewHolder(View itemView) {
            super(itemView);
            mTextView1= (TextView) itemView.findViewById(R.id.text);
        }
    }

    protected class LastViewHolder extends RecyclerView.ViewHolder{
        public LastViewHolder(View itemView) {
        }

    }
```

2. 重写getItemCount和getItemViewType方法，==要注意的是getItemCount返回的数量要比数据量+1==：

```
@Override
    public int getItemCount() {
        return mList.size()+1;
    }
 @Override
    public int getItemViewType(int position) {
        if (position==mList.size()){
            return 2;
        }else {
            return 1;
        }
    }
```

3. 在onCreateViewHolder和onBindViewHolder中分情况处理：

```
@Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if (viewType==1){
            View view = mInflater.inflate(R.layout.item,parent,false);
            MyViewHolder holder= new MyViewHolder(view);
            return holder;
        }else {
            View view = mInflater.inflate(R.layout.last_item,parent,false);
            LastViewHolder holder=new LastViewHolder(view);
            return holder;
        }
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        if (holder instanceof MyViewHolder){
            ((MyViewHolder) holder).mTextView1.setText(mList.get(position)+position);
        }else if (holder instanceof LastViewHolder){
            getMore();
        }

    }
```

显示效果：

![image](http://ww1.sinaimg.cn/large/64ddfd2bgw1fakl4frujtj20ga0qptb8.jpg)

### 3. 实现当显示最后item时加载更多

1. 为了能在activity中实现加载更多的操作，需要把adapter设为抽象类，并定义抽象方法

```
abstract void getMore();
```
2. 在onBindViewHolder中调用getMore();

```
@Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        if (holder instanceof MyViewHolder){
            ((MyViewHolder) holder).mTextView1.setText(mList.get(position)+position);
        }else if (holder instanceof LastViewHolder){
            getMore();
        }

    }
```

3. 在初始化adapter时实现getMore()方法，但是这时候还不能直接调用notifyDataSetChanged()，所以使用handl来进行处理：

```
final Adapter adapter= new Adapter(mList, this) {
            @Override
            void getMore() {
                mHandler.sendMessage(new Message());
            }
        };

        mHandler=new Handler(new Handler.Callback() {
            @Override
            public boolean handleMessage(Message msg) {
                mList.add("adfjyagjkjgjkukhuhk");
                adapter.notifyDataSetChanged();
                return false;
            }
        });
```

效果：

![image](http://ww4.sinaimg.cn/large/64ddfd2bgw1faklp4p1c1g20ge0qzdtd.gif)

完整代码：

adapter：

```
public abstract class Adapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    List<String> mList;
    Context mContext;
    LayoutInflater mInflater;

    public Adapter(List<String> list, Context context) {
        mList = list;
        mContext = context;
        mInflater=LayoutInflater. from(mContext);
    }

    @Override
    public int getItemCount() {
        return mList.size()+1;
    }

    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if (viewType==1){
            View view = mInflater.inflate(R.layout.item,parent,false);
            MyViewHolder holder= new MyViewHolder(view);
            return holder;
        }else {
            View view = mInflater.inflate(R.layout.last_item,parent,false);
            LastViewHolder holder=new LastViewHolder(view);
            return holder;
        }
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        if (holder instanceof MyViewHolder){
            ((MyViewHolder) holder).mTextView1.setText(mList.get(position)+position);
        }else if (holder instanceof LastViewHolder){
            getMore();
        }

    }

    @Override
    public int getItemViewType(int position) {
        if (position==mList.size()){
            return 2;
        }else {
            return 1;
        }
    }

    protected class MyViewHolder extends RecyclerView.ViewHolder {
        TextView mTextView1;

        public MyViewHolder(View itemView) {
            super(itemView);
            mTextView1= (TextView) itemView.findViewById(R.id.text);
        }
    }

    protected class LastViewHolder extends RecyclerView.ViewHolder{
        public LastViewHolder(View itemView) {
            super(itemView);
        }

    }
    abstract void getMore();
}
```

mainactivity：

```
public class MainActivity extends AppCompatActivity {
    private RecyclerView mRecyclerView;
    private SwipeRefreshLayout mSwipeRefreshLayout;
    List<String> mList;
    static private Handler mHandler;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mRecyclerView= (RecyclerView) findViewById(R.id.recycleview);
        mList=new ArrayList<>();

        for (int i = 0; i < 25; i++) {
            mList.add("akudfhakjnk");
        }
        final Adapter adapter= new Adapter(mList, this) {
            @Override
            void getMore() {
                mHandler.sendMessage(new Message());
            }
        };

        mHandler=new Handler(new Handler.Callback() {
            @Override
            public boolean handleMessage(Message msg) {
                mList.add("adfjyagjkjgjkukhuhk");
                adapter.notifyDataSetChanged();
                return false;
            }
        });

        mRecyclerView.setAdapter(adapter);
        mRecyclerView.setLayoutManager(new LinearLayoutManager(this,LinearLayoutManager.VERTICAL,false));
        mSwipeRefreshLayout= (SwipeRefreshLayout) findViewById(R.id.swiperefresh);
        mSwipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                mList.add(0,"ajkfgamj");
                mSwipeRefreshLayout.setRefreshing(false);
                adapter.notifyDataSetChanged();
            }
        });

    }
}

```
