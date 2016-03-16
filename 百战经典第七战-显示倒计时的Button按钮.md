最近在做获取验证码的功能，考虑到优良的用户体验，在用户点击获取验证码后，Button变得不可点，同时上面会显示一个倒计时，倒计时结束后Button变得可点击。结合一些材料写了一个小Demo，大家可以应用到自己的项目中。

一、代码
1.activity_main.xml:
```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <Button
        android:id="@+id/btn_time"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="获取验证码" />
</RelativeLayout>
```
布局文件很简单，就一个button按钮。
2.MainActivity.java:

```
package com.example.timebutton;
//省略import
public class MainActivity extends Activity {
	private Button mTimeButton;
	private TimeCount time;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.activity_main);
		mTimeButton = (Button) findViewById(R.id.btn_time);
		time = new TimeCount(60000, 1000);
		mTimeButton.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				time.start();
			}
		});
	}
	/**
	 * 继承倒计时类
	 * @author 
	 */
	class TimeCount extends CountDownTimer {
		/**
		 * 构造方法
		 * @param millisInFuture
		 *            总倒计时长 毫秒
		 * @param countDownInterval
		 *            倒计时间隔
		 */
		public TimeCount(long millisInFuture, long countDownInterval) {
			super(millisInFuture, countDownInterval);
		}
		@Override
		public void onTick(long millisUntilFinished) {
			mTimeButton.setEnabled(false);
			mTimeButton.setText(millisUntilFinished / 1000 + "秒");
		}
		@Override
		public void onFinish() {// 计时结束
			mTimeButton.setEnabled(true);
			mTimeButton.setText("重新获取");
		}
	}
}

```
这里用到了TimeCount类，初始化时传入两个参数millisInFuture和countDownInterval，millisInFuture表示从开始调用start()到倒计时完成并onFinish()方法被调用的毫秒数，即一个周期；countDownInterval表示接收onTick(long)回调的间隔时间。本实例中采用60秒周期，1秒间隔。TimeCount 继承自CountDownTimer 类，覆写了里面的onTick方法，此方法是在计数过程中执行，借用setEnabled方法，将button变成不可点击的状态，同时更新button上显示的时间。覆写的onFinish方法，在计时结束后触发，将button设置成可点击的状态，并将button上的文字变成“重新获取”。
二、运行实例：
![这里写图片描述](http://img.blog.csdn.net/20160315181840573)
![这里写图片描述](http://img.blog.csdn.net/20160315181850169)
![这里写图片描述](http://img.blog.csdn.net/20160315181859464)

