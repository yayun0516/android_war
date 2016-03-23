# 百战经典第十七战-基于加速度传感器的摇一摇功能实例

摇一摇在微信中引入之后，各种APP都以各种形式实现摇一摇功能，下面研究一下摇一摇功能是如何触发的，这里主要是用到了手机的加速度传感器，代码如下：

```
package com.location.activity;
//省略引入包
public class MainActivity extends Activity {
    private SensorManager sensorManager;
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        sensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);// 获取服务
        Sensor sensor = sensorManager
                .getDefaultSensor(Sensor.TYPE_ACCELEROMETER);// 传感器种类-加速度传感器
        sensorManager.registerListener(listener, sensor,
                SensorManager.SENSOR_DELAY_NORMAL);
    }
    @Override
    protected void onDestroy() {
        if (sensorManager != null) {
            sensorManager.unregisterListener(listener);
        }
        super.onDestroy();
    }
    /**
     * 传感器触发
     */
    private SensorEventListener listener = new SensorEventListener() {

        public void onSensorChanged(SensorEvent event) {
            float xValue = Math.abs(event.values[0]);// X轴方向加速度
            float yValue = Math.abs(event.values[1]);// Y轴方向加速度
            float zValue = Math.abs(event.values[2]);// Z轴方向加速度
            if (xValue > 20 || yValue > 20 || zValue > 20) {// 有任何一个方向的加速度值大于20,即触发
                /**
                 * 这里我们只是以Toast为例，您可以按照自己的需求在此写入各种方法；
                 */
                Toast.makeText(MainActivity.this, "您启动了摇一摇功能！",
                        Toast.LENGTH_LONG).show();
            }
        }
        public void onAccuracyChanged(Sensor sensor, int accuracy) {

        }
    };
}
```

必须在真机上模拟，运行实例：

![这里写图片描述](http://img.blog.csdn.net/20160315115849373)

### 总结：

1. getSystemService(Context.SENSOR_SERVICE);// 获取SensorManage对象； 
2. sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);// 获取传感器种类 
3. 本文只是将摇一摇触发为Toast,有需要的可以直接修改方法即可。