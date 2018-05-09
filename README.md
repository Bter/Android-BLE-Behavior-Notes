# Android7.0-BLE-Behavior-Notes
Android BLE相关的一些要点。

1.Android7.0 以后频繁开关扫描5次会触发保护机制，导致30s后才能再次接收到扫描回调。

Android BLE扫描不到设备https://github.com/Bter/Android7.0-BLE-Behavior-Changes/blob/master/Android%20BLE%E6%89%AB%E6%8F%8F%E4%B8%8D%E5%88%B0%E8%AE%BE%E5%A4%87

参考Alex Suzuki大神的博客https://blog.classycode.com/undocumented-android-7-ble-behavior-changes-d1a9bd87d983

2.A short story about Android BLE connection timeouts and GATT internal errors

https://blog.classycode.com/a-short-story-about-android-ble-connection-timeouts-and-gatt-internal-errors-fa89e3f6a456
