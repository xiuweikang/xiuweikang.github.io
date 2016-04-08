title: Device Compatibility
date: 2015-12-27 17:20:49
tags: Android
---

## Device feature
Android系统对每个硬件或者软件 feature定义了相应feature id，如果你不希望没有某个feature的设备下载你的app时，你可以在manifest里写：

``` xml
<manifest ... >
    <uses-feature android:name="android.hardware.sensor.compass"
                  android:required="true" />
    ...
</manifest>
```

也可以设置成false，表明不需要，可以在运行时动态检查：

``` java

PackageManager pm = getPackageManager();
if (!pm.hasSystemFeature(PackageManager.FEATURE_SENSOR_COMPASS)) {
    // This device does not have a compass, turn off the compass feature
    disableCompassFeature();//自定义方法处理相应逻辑
}

```
## Platform version

Android是向前兼容的，4.0是兼容2.3的但是却无法跑4.0之后的，因此如果自己的app用到了比较新的api，这时是无法在旧版本上跑的，因此你需要声明你app所支持的最低sdk版本，target版本则是你app充分利用的最高版本，例如当你的app的tagrgetVersion是19，但是用到的某个api在21时更新了，这时你的app会跑19，而不会跑21，这时就需要注意targetSdkVersion这个属性了

``` xml
<uses-sdk android:minSdkVersion="14" android:targetSdkVersion="21"/>
```

``` java
if (Build.VERSION.SDK_INT < Build.VERSION_CODES.HONEYCOMB) {
    // Running on something older than API level 11, so disable
    // the drag/drop features that use ClipboardManager APIs
    disableDragAndDrop();
}
```

