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
