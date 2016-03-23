# 百战经典第十六战-图片或头像设置功能

开发中，会遇到图片的操作，选取本地图片或拍照并剪切后设置头像等功能，本节将实现这一功能。

1.先看一下布局文件，很是丑陋：

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:tools="http://schemas.android.com/tools"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent" >  
    <ImageView  
        android:id="@+id/image_header"  
        android:layout_width="150dp"  
        android:layout_height="150dp" />  
    <Button  
        android:id="@+id/btn_selectimage"  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:layout_below="@+id/image_header"  
        android:text="相册" />  
    <Button  
        android:id="@+id/btn_takephoto"  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:layout_below="@+id/btn_selectimage"  
        android:text="拍照" />  
</RelativeLayout> 
```

2.MainActivity.java:

```
package com.example.userphoto;  
  //省略导入包
public class MainActivity extends Activity implements OnClickListener {  
    private static final int IMAGE_REQUEST_CODE = 0;  
    private static final int CAMERA_REQUEST_CODE = 1;  
    private static final int RESIZE_REQUEST_CODE = 2;  
    private static final String IMAGE_FILE_NAME = "header.jpg";  
    private ImageView mImageHeader;  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
        setupViews();  //实例化控件
    }  
    private void setupViews() {  
        mImageHeader = (ImageView) findViewById(R.id.image_header);  
        final Button selectBtn1 = (Button) findViewById(R.id.btn_selectimage);  
        final Button selectBtn2 = (Button) findViewById(R.id.btn_takephoto);  
        selectBtn1.setOnClickListener(this);  
        selectBtn2.setOnClickListener(this);  
    }  
    @Override  
    public void onClick(View v) {  
        switch (v.getId()) {  
        case R.id.btn_selectimage:  
            Intent galleryIntent = new Intent(Intent.ACTION_GET_CONTENT);  
            galleryIntent.addCategory(Intent.CATEGORY_OPENABLE);  
            galleryIntent.setType("image/*");//图片  
            startActivityForResult(galleryIntent, IMAGE_REQUEST_CODE);  
            break;  
        case R.id.btn_takephoto:  
            if (isSdcardExisting()) {  
                Intent cameraIntent = new Intent(  
                        "android.media.action.IMAGE_CAPTURE");//拍照  
                cameraIntent.putExtra(MediaStore.EXTRA_OUTPUT, getImageUri());  
                cameraIntent.putExtra(MediaStore.EXTRA_VIDEO_QUALITY, 0);  
                startActivityForResult(cameraIntent, CAMERA_REQUEST_CODE);  
            } else {  
                Toast.makeText(v.getContext(), "请插入sd卡", Toast.LENGTH_LONG)  
                        .show();  
            }  
            break;  
        }  
    }  
  
    @Override  
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {  
        if (resultCode != RESULT_OK) {  
            return;  
        } else {  
            switch (requestCode) {  
            case IMAGE_REQUEST_CODE:  
                resizeImage(data.getData());  
                break;  
            case CAMERA_REQUEST_CODE:  
                if (isSdcardExisting()) {  
                    resizeImage(getImageUri());  
                } else {  
                    Toast.makeText(MainActivity.this, "未找到存储卡，无法存储照片！",  
                            Toast.LENGTH_LONG).show();  
                }  
                break;  
            case RESIZE_REQUEST_CODE:  
                if (data != null) {  
                    showResizeImage(data);  
                }  
                break;  
            }  
        }  
        super.onActivityResult(requestCode, resultCode, data);  
    }  
    private boolean isSdcardExisting() {//判断SD卡是否存在  
        final String state = Environment.getExternalStorageState();  
        if (state.equals(Environment.MEDIA_MOUNTED)) {  
            return true;  
        } else {  
            return false;  
        }  
    }  
    public void resizeImage(Uri uri) {//重塑图片大小  
        Intent intent = new Intent("com.android.camera.action.CROP");  
        intent.setDataAndType(uri, "image/*");  
        intent.putExtra("crop", "true");//可以裁剪  
        intent.putExtra("aspectX", 1);  
        intent.putExtra("aspectY", 1);  
        intent.putExtra("outputX", 150);  
        intent.putExtra("outputY", 150);  
        intent.putExtra("return-data", true);  
        startActivityForResult(intent, RESIZE_REQUEST_CODE);  
    }  
    private void showResizeImage(Intent data) {//显示图片  
        Bundle extras = data.getExtras();  
        if (extras != null) {  
            Bitmap photo = extras.getParcelable("data");  
            Drawable drawable = new BitmapDrawable(photo);  
            mImageHeader.setImageDrawable(drawable);  
        }  
    }  
    private Uri getImageUri() {//获取路径  
        return Uri.fromFile(new File(Environment.getExternalStorageDirectory(),  
                IMAGE_FILE_NAME));  
    }  
}  
```

代码中有几个方法，解释一下：

- （1）setupViews方法，实现控件初始化和控件的监听事件。
- （2）onActivityResult方法，用于Intent跳转时获取返回值，里面有三个参数，requestCode是请求码，用于信息区分；resultCode是结果返回码，当resultCode为RESULT_OK时，才进行返回数据的获取；data是返回数据的载体，可以通过它获取想要的返回数据。
- （3）isSdcardExisting方法，判断是否存在SD卡，方法是固定的。
- （4）resizeImage方法，用于剪切图片，调用此代码会跳转到Android系统自带的一个图片剪裁页面，选取截取位置点击确定之后就会得到一张图片。其中`intent.putExtra("outputX",150);` `intent.putExtra("outputY",150);`是设置裁剪图片的宽和高。
- （5）showResizeImage是展示剪切后的图片，其中用到了getParcelable方法进行反序列化。最后调用setImageDrawable进行图片的显示。
- （6）getImageUri方法返回资源文件的Uri路径。

运行实例，拍一张照片作为头像：

![这里写图片描述](http://img.blog.csdn.net/20160314184442145)