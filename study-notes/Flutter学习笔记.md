# Flutter学习笔记

## Flutter介绍和主流框架对比

跨平台：Linux、Android、IOS、Fuchsia

- Cordova: 混合式开发框架(Hybrid App)
- RN(React Native)：生成原生APP，但以View为基础嵌入
- Flutter：在渲染技术上，选择了自己实现(GDI)

## 环境配置

### java
### Xcode
### android studio

- Unable to access Android SDK add-on list
- java.io.IOException: Cannot download 'https://dl.google.com/android/repository/sdk-tools-darwin-4333796.zip': Connection reset
, response: 200 OK
Warning: An error occurred while preparing SDK package Android SDK Tools: Cannot download 'https://dl.google.com/android/repository/sdk-tools-darwin-4333796.zip': Connection reset
, response: 200 OK.
### flutter sdk

		git clone -b master https://github.com/flutter/flutter.git
		./flutter/bin/flutter --version
		export PUB_HOSTED_URL=https://pub.flutter-io.cn
		export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
		export PATH=`pwd`/flutter/bin:$PATH

- Android licenses not accepted.  To resolve this, run: flutter doctor --android-licenses
- sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
- CocoaPods not installed

To install:

	brew install cocoapods
	pod setup

- libimobiledevice and ideviceinstaller are not installed
- To install with

		Brew, run:
		brew update
		brew install --HEAD usbmuxd
		brew link usbmuxd
		brew install --HEAD libimobiledevice
		brew install ideviceinstaller

- ios-deploy not installed. To install:

		brew install ios-deploy
/users/chenguoqin/library/flutter
