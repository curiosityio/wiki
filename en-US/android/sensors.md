---
name: Sensors
---

# Detect when user lifts up phone with accelerometer

In a Fragment, Activity, Service, register a listener with sensor manager.

Make sure class implements `SensorEventListener`.

```
override fun onResume() {
    super.onResume()

    val sensorManager: SensorManager = activity.getSystemService(Activity.SENSOR_SERVICE) as SensorManager
    sensorManager.registerListener(this, sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER), SensorManager.SENSOR_DELAY_NORMAL)
}

override fun onSensorChanged(event: SensorEvent?) {
    if (event!!.sensor.type == Sensor.TYPE_ACCELEROMETER) {
        val x = event.values[0]
        val y = event.values[1]
        val z = event.values[2]

        LogUtil.d("x: $x y: $y z: $z")
    }
}

override fun onAccuracyChanged(event: Sensor?, accuracy: Int) {
}
```

X is when you lay the phone flat and rotate the right side of the phone up. To rotate on it's side.
Y is when you lift the phone screen towards you. You lay it lat on table Y is 0. When the screen is up in your hand as if you were texting, it's around 7 - 9.
Z is opposite as X.

The accelerometer uses a bit of battery and I believe only runs when the screen is on. Read more about the class [in the docs](https://developer.android.com/reference/android/hardware/SensorManager.html) before you use it.

# Shake detection

* In your activity or Fragment

```
// create a field
private ShakeDetectionManager mShakeManager;

onCreate() {
    super.onCreate();

    mShakeManager = new ShakeDetectionManager(getActivity());
    mShakeManager.setOnShakeListener(this); // make sure to implement ShakeDetectionListener.OnShakeListener

}

onResume() {
    super.onResume();

    mShakeManager.detectShake();
}

onPause() {
    super.onPause();

    mShakeManager.stopShakeDetect();
}

@Override
    public void onShake() {
        Toast.makeText(getActivity(), "shake", Toast.LENGTH_SHORT).show();
    }
```

* Which refers to this file:

```
import android.content.Context;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.util.FloatMath;

public class ShakeDetectionManager implements SensorEventListener {

    private static final float SHAKE_THRESHOLD_GRAVITY = 2.7F;
    private static final int SHAKE_SLOP_TIME_MS = 500;

    private OnShakeListener mListener;
    private long mShakeTimestamp;

    private Sensor mAccelerometer;
    private SensorManager mSensorManager;

    public void setOnShakeListener(OnShakeListener listener) {
        this.mListener = listener;
    }

    public ShakeDetectionManager(Context context) {
        mSensorManager = (SensorManager) context.getSystemService(Context.SENSOR_SERVICE);
        mAccelerometer = mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
    }

    public void detectShake() {
        mSensorManager.registerListener(this, mAccelerometer, SensorManager.SENSOR_DELAY_UI);
    }

    public void stopShakeDetect() {
        mSensorManager.unregisterListener(this);
    }

    public interface OnShakeListener {
        void onShake();
    }

    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
    }

    @Override
    public void onSensorChanged(SensorEvent event) {
        if (mListener != null) {
            float x = event.values[0];
            float y = event.values[1];
            float z = event.values[2];

            float gX = x / SensorManager.GRAVITY_EARTH;
            float gY = y / SensorManager.GRAVITY_EARTH;
            float gZ = z / SensorManager.GRAVITY_EARTH;

            // gForce will be close to 1 when there is no movement.
            float gForce = FloatMath.sqrt(gX * gX + gY * gY + gZ * gZ);

            if (gForce > SHAKE_THRESHOLD_GRAVITY) {
                final long now = System.currentTimeMillis();
                // ignore shake events too close to each other (500ms)
                if (mShakeTimestamp + SHAKE_SLOP_TIME_MS > now) {
                    return;
                }

                mShakeTimestamp = now;

                mListener.onShake();
            }
        }
    }

}
```

Here is a note:

```
/*
* The gForce that is necessary to register as shake.
* Must be greater than 1G (one earth gravity unit).
* You can install "G-Force", by Blake La Pierre
* from the Google Play Store and run it to see how
* many G's it takes to register a shake
*/
```
