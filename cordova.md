# Cordova Guide

1. Install Xcode

2. Install JDK

3. Install Gradle

4. Install Android SDK

5. Install CocoaPods

```bash
sudo gem install cocoapods
pod setup
```

6. Install Cordova

```bash
npm install -g cordova

npm install -g ios-sim
npm install -g ios-deploy
```

7. Configure Cordova Project for Android

Open **~/.bash_profile** and add same as next
```bash
export ANDROID_HOME=/Development/android-sdk/
export PATH=${PATH}:/Development/android-sdk/platform-tools:/Development/android-sdk/tools
```

then for change reflection

```bash
source ~/.bash_profile
```

8. Configure Cordova Project for iOS

```bash
cordova emulate ios --buildFlag="-UseModernBuildSystem=0"
```

Alternatively, you can add a **build.json** file to root of project instead with following code below and cordova will pickup build flag from there

```json
{
	"ios": {
		"debug": { 
			"buildFlag": [
				"-UseModernBuildSystem=0"
			]
		},
		"release": {
			"buildFlag": [
				"-UseModernBuildSystem=0"
			]
		}
	}
}
```

9. Start Project Android

If you wish to run your Cordova app on an Android emulator, you will first need to create an Android Virtual Device (AVD). See the Android documentation for:
- [Managing AVDs](https://developer.android.com/studio/run/managing-avds)
- [Configuring the emulator](https://developer.android.com/studio/run/emulator#about)
- [Setting up hardware acceleration](https://developer.android.com/studio/run/emulator-acceleration)

Simple guide for AVD:
- Start Android Studio
- Run *Tools -> AVD Manager*
- Add device with **API level android-27**
- Run *Tools -> SDK Manager*
- Install SDK Platform Android 8.1 (Oreo) with 27 API Level

```bash
cordova emulate android
```

10. Start Project iOS

```bash
cordova emulate ios --buildFlag="-UseModernBuildSystem=0"
```

or

```bash
cordova run --target='iPad-Air-2' --buildFlag='-UseModernBuildSystem=0'
```

or with using **build.json**

```bash
cordova emulate ios
```

11. YUPIII
