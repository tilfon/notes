*windows 平台下有强大的 visual studio*

# cordova环境部署

## 安装cordova

```
	npm install -g cordova
```

这里只是安装了 `cordova-cli` 和 `plugman`。

## iOS环境

* 安装 XCODE 和 iOS 等工具


* 安装用于启动 iOS 模拟器的 node.js 包
 
	```
npm install -g ios-sim

	```

## Android环境

* 安装 JDK (JAVA官网和 Android 官网都有链接)
* 安装 Android SDK (直接安装官网的 Android studio 省心，安装完后启动配置把 Android SDK 也一起装了就完了)
* 配置 Android SDK 环境路径

	```
export ANDROID_HOME=你的Android SDK路径
export PATH=$PATH:$ANDROID_HOME/bin
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
	``` 
	
# cordova 打包相关

* **添加/删除平台**
	```
cordova platform add ios
cordova platform rm ios
	```

* **编译** (打包后会在对应platforms 文件夹下的对就平台的 build 目录下)
	```
cordova build
cordova build ios
cordova build android
cordova build wp8
	```
	
* **打开模拟器**
	```
cordova emulate ios
cordova emulate android
	```