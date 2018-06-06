#### 1.简单提示对话框
    AlertDialog.Builder builder = new Builder(MainActivity.context);
		  builder.setMessage("确认删除吗？");

		  builder.setTitle("提示");

		  builder.setPositiveButton("确认", new DialogInterface.OnClickListener() {

		   public void onClick(DialogInterface dialog, int which) {
		    dialog.dismiss();
		   }
		  });

		  builder.setNegativeButton("取消", null);
		  builder.create().show();
#### 2. 列表，单选和多选对话框
    AlertDialog.Builder builder = new Builder(activity);
		  builder.setTitle("提示");
    //		  builder.setSingleChoiceItems(new String[]{"item1","item2"}, 0, null);//单选
    //		  builder.setMultiChoiceItems(new String[]{"item1","item2"}, new boolean[]{true,false}, null);//多选
    		  builder.setItems(new String[]{"item1","item2"}, null);//列表
    		  builder.setPositiveButton("确认", new DialogInterface.OnClickListener() {
    		   public void onClick(DialogInterface dialog, int which) {
    		    dialog.dismiss();
    		   }
    		  });
    		  builder.setNegativeButton("取消", null);
    		  builder.create().show();
#### 3. 自定义样式
    LayoutInflater inflater = activity.getLayoutInflater();
        View layout = inflater.inflate(R.layout.dialog_newtype,
                (ViewGroup)findViewById(R.id.id_newproject_ll));
        EditText newProjectet=(EditText) layout.findViewById(R.id.id_newproject_et);

        new AlertDialog.Builder(this).setTitle("增加类型").setView(layout)
                .setPositiveButton("确定", new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog, int which) {

                    }
                })
                .setNegativeButton("取消", null).show();
#### 4. 圆形进度条
    ProgressDialog mProgressDialog = new ProgressDialog(activity);
        mProgressDialog.setProgressStyle(ProgressDialog.STYLE_SPINNER); //设置进度条风格，风格为圆形，旋转的 
        mProgressDialog.setTitle("读取ing...");// 设置ProgressDialog标题 
        mProgressDialog.setMessage("正在读取中请稍候...");// 设置ProgressDialog提示信息 
        mProgressDialog.setIndeterminate(true);//设置ProgressDialog 的进度条不明确
        mProgressDialog.setCancelable(true);// 设置ProgressDialog 可以按退回键取消
        mProgressDialog.setButton("确定", new DialogInterface.OnClickListener()
        {
            public void onClick(DialogInterface dialog, int whichButton)
            {
                // 这里添加点击后的逻辑
            }
        });
        mProgressDialog.show();// 让ProgressDialog显示
#### 5. 条形进度条
    mProgressDialog.setTitle("进度条窗口");// 设置ProgressDialog标题 
        mProgressDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);//设置进度条风格，风格为长形
        mProgressDialog.setMax(100);// 设置ProgressDialo进度条进度 
        mProgressDialog.setButton("确定", new DialogInterface.OnClickListener()
        {
            public void onClick(DialogInterface dialog, int whichButton)
            {
                // 这里添加点击后的逻辑
            }
        });
        mProgressDialog.setButton2("取消", new DialogInterface.OnClickListener()
        {
            public void onClick(DialogInterface dialog, int whichButton)
            {
                // 这里添加点击后的逻辑
            }
        });
        mProgressDialog.show();
        new Thread()
        {
            @Override
            public void run() 
            {
                int Progress = 0;
                while (Progress < 100)
                {
                    try
                    {                
                        mProgressDialog.setProgress(Progress++);  
                        //mProgressDialog.incrementProgressBy(1);
                        Thread.sleep(100);
                    } catch (Exception e)
                    {
                        // TODO Auto-generated catch block
                        mProgressDialog.cancel();
                        //e.printStackTrace();
                    }
                }    
            };
        }.start();
#### 6. 保持对话框不被关闭
导入的是java.lang.reflect.Field。

    try { 
    Field field = dialog.getClass().getSuperclass().getDeclaredField("mShowing"); 
    field.setAccessible(true); 
    field.set(dialog, false);
    
    
    } catch (Exception e) { 
    e.printStackTrace(); 
    }
关闭的时候添加

    try {
    Field field = dialog.getClass().getSuperclass().getDeclaredField("mShowing");
    field.setAccessible(true);
    field.set(dialog, true);
    } catch (Exception e) {
    e.printStackTrace();
    }