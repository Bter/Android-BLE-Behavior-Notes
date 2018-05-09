# Android7.0-BLE-Behavior-Changes
Android BLE相关的一些要点。

1.Android7.0 以后频繁开关扫描5次会触发保护机制，导致30s后才能再次接收到扫描回调。
参考Alex Suzuki大神的博客https://blog.classycode.com/undocumented-android-7-ble-behavior-changes-d1a9bd87d983

内容如下：

Undocumented Android 7 BLE Behavior Changes

TL;DR version: Android 7 (Nougat) introduces changes in the BLE scanning behavior, aimed at stopping abusive apps. These changes are not documented so might catch some folks by surprise.

What we usually offer our clients when we develop software for them is monitoring upcoming Android and iOS versions for changes that might affect the software after we’ve delivered it. Google lists the behavior changes for each major Android release in one place, which is very handy. See for example the list of behavior changes for Nougat.

BLE Scanning Abuse Prevention

Sometimes there are changes that slip through the cracks and don’t make the official list. One such change is protection against BLE scanning abuse in Android 7.

The change prevents an app from stopping and starting BLE scans more than 5 times in a window of 30 seconds. While this sounds like a completely innocuous and sensible change, it still caught us by surprise. One reason we missed it is that the app is not informed of this condition. The scan will start without an error, but the Bluetooth stack will simply withhold advertisements for 30 seconds, instead of informing the app through the error callback ScanCallback.onScanFailed(int).

The change also converts scans that are long-running into opportunistic scans. Initially long-running was defined to be 5 minutes, which was later increased to 30 minutes. An opportunistic scan will only get results if another non-opportunistic scan sees results that match the opportunistic scan’s scan filter. We assume this change is to prevent apps from scanning in the background endlessly. It could probably break a lot of iBeacon-based proximity use cases.

The change can be found by digging in the AOSP code review system: https://android-review.googlesource.com/#/c/215844/ But this is not a place your average developer frequents, and he shouldn’t need to.

How to detect the condition

Unfortunately there is now way to detect this inside your app.

The only way to know that you’ve been denied advertisements for 30 seconds because you’ve apparently been a bad boy, is by looking at the Logcat output — the line below will appear after the 5th scan attempt:

App 'com.example' is scanning too frequently

Why should you care

Why would anyone stop and start scanning more than five times in 30 seconds? A fair question. In our case, the intent was to scan when the app is in foreground, and to stop scanning when the app is backgrounded. As there is no easy way of reliably detecting that an Android app is going into background/coming into foreground, we went with stopping scans in Activity.onPause() and starting them again in Activity.onResume(). So on every screen change in the app (assuming it uses Activities instead of Fragments), the scanning would restart, triggering the abuse protection.

You may laugh, but incidentally this “lawn mower” approach also helped with reliability in previous Android versions where the BLE stack was notoriously buggy. The SweetBlue library has pretty comprehensive list of known Android BLE issues. That there’s a market for a commercial wrapper around the Android BLE stack speaks for itself, really.

Should this be documented?

We think it should! After some digging, we found that it actually was mentioned in the DP4 release notes, quote:
We’ve changed the BLE Scanning behavior starting in DP4. We’ll prevent applications from starting and stopping scans more than 5 times in 30 seconds. For long running scans, we’ll convert them into opportunistic scans.

However the change didn’t make it into the final release notes, and the DP4 release notes have long since vanished from Google’s site. An old copy of the DP4 release notes can be found here: http://www.droid-life.com/2016/06/15/android-n-developer-preview-4-now-available/
Please recommend this article if you enjoyed reading it.

Thanks to Andreas Schweizer.

Bluetooth Low EnergyAndroidAndroid Nougat

One clap, two clap, three clap, forty?

By clapping more or less, you can signal to us which stories really stand out.
