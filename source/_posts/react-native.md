---
title: react-native踩坑记录
date: 2018-01-05 13:37:46
tags: ['react-native']
categories: 笔记
---
## 打包命令说明
使用 `react-native bundle --help` 来查看打包的具体参数

```bash
react-native bundle [options]
  builds the javascript bundle for offline use

  Options:

    -h, --help                   output usage information
    --entry-file <path>          Path to the root JS file, either absolute or relative to JS root
    --platform [string]          Either "ios" or "android"
    --transformer [string]       Specify a custom transformer to be used
    --dev [boolean]              If false, warnings are disabled and the bundle is minified
    --prepack                    When passed, the output bundle will use the Prepack format.
    --bridge-config [string]     File name of a a JSON export of __fbBatchedBridgeConfig. Used by Prepack. Ex. ./bridgeconfig.json
    --bundle-output <string>     File name where to store the resulting bundle, ex. /tmp/groups.bundle
    --bundle-encoding [string]   Encoding the bundle should be written in (https://nodejs.org/api/buffer.html#buffer_buffer).
    --sourcemap-output [string]  File name where to store the sourcemap file for resulting bundle, ex. /tmp/groups.map
    --assets-dest [string]       Directory name where to store assets referenced in the bundle
    --verbose                    Enables logging
    --reset-cache                Removes cached files
    --config [string]            Path to the CLI configuration file
```

中文版:
```bash
react-native bundle [参数]
  构建 js 离线包 

  Options:

    -h, --help                   输出如何使用的信息
    --entry-file <path>          RN入口文件的路径, 绝对路径或相对路径
    --platform [string]          ios 或 andorid
    --transformer [string]       Specify a custom transformer to be used
    --dev [boolean]              如果为false, 警告会不显示并且打出的包的大小会变小
    --prepack                    当通过时, 打包输出将使用Prepack格式化
    --bridge-config [string]     使用Prepack的一个json格式的文件__fbBatchedBridgeConfig 例如: ./bridgeconfig.json
    --bundle-output <string>     打包后的文件输出目录, 例: /tmp/groups.bundle
    --bundle-encoding [string]   打离线包的格式 可参考链接https://nodejs.org/api/buffer.html#buffer_buffer.
    --sourcemap-output [string]  生成Source Map，但0.14之后不再自动生成source map，需要手动指定这个参数。例: /tmp/groups.map
    --assets-dest [string]       打包时图片资源的存储路径
    --verbose                    显示打包过程
    --reset-cache                移除缓存文件
    --config [string]            命令行的配置文件路径

```
<!-- more -->

## ios 打包流程

### 1.执行打包命令

```bash
react-native bundle --entry-file index.ios.js --bundle-output ./ios/bundle/index.ios.jsbundle --platform ios --assets-dest ./ios/bundle --dev false
```
<p class="tip">必须保证输出文件夹存在</p>

在`ios`文件夹中可以找到如下打包产物
![](http://7xrqm7.com1.z0.glb.clouddn.com/ios-bundle.png)

### 2.设置`jsbundle`

- 用`xcode`打开`ios`文件夹
![](http://7xrqm7.com1.z0.glb.clouddn.com/xcode-ios-project.png)

- 选中与项目同名的文件夹`open`,右键选择`Add Files to "open"`
![](http://7xrqm7.com1.z0.glb.clouddn.com/add-file-ios-project.png)

- 选择打包产物所在文件夹,将文件导入到项目中
![](http://7xrqm7.com1.z0.glb.clouddn.com/add-file-ios-project-2.png)

<p class="tip">assets 目录导入工程中时，要选择 Create folder references。</p>

- 修改`AppDelegate.m`文件证的代码

改写`jsCodeLocation`的值

```
NSURL *jsCodeLocation;

// jsCodeLocation = [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index.ios" fallbackResource:nil];
jsCodeLocation = [NSURL URLWithString:[[NSBundle mainBundle] pathForResource:@"index.ios.jsbundle" ofType:nil]];
RCTRootView *rootView = [[RCTRootView alloc] initWithBundleURL:jsCodeLocation
                                                      moduleName:@"RNBundleDemo"
                                               initialProperties:nil
                                                   launchOptions:launchOptions];

```

### 3.修改打包类型

- 点击如图菜单项
![](http://7xrqm7.com1.z0.glb.clouddn.com/xcode-edit-schema.png)

- 将`Build Configuration`选项修改为`release`
![](http://7xrqm7.com1.z0.glb.clouddn.com/xcode-run-release.png)

### 4.`target` 设置

- 设置左侧四个`target`的`General`标签下的部分选项如下图所示
![](http://7xrqm7.com1.z0.glb.clouddn.com/xcode-target-general.png)

### 5.安装到`iphone`

- 将`iphone`与`mac`用usb线连接
- 将`target`设置为目标`iphone`
  ![](http://7xrqm7.com1.z0.glb.clouddn.com/xcode-target.png)
- 点击run按钮(左侧小三角形),安装到手机。

<p class="tip">打开过程中若提示是不受信任的开发者，则点开设置->通用->描述文件与设备管理,选择对应开发者,点击信任。</p>

## android 打包流程

### 1.执行打包命令

第一步与ios相同，执行打包命令，生成离线包。

```bash
eact-native bundle --entry-file index.android.js --platform android --dev false --bundle-output ./android/app/src/main/assets/index.android.bundle --assets-dest ./android/app/src/main/res/  
```
<p class="tip">必须保证输出文件夹存在</p>

### 2.生成签名文件

```bash
$ keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```
![](http://7xrqm7.com1.z0.glb.clouddn.com/android-keystore.png)
根据提示输入后，输入"是"完成签名配置

> 在运行上面这条语句之后，密钥库里应该已经生成了一个单独的密钥，有效期为10000天。--alias参数后面的别名是你将来为应用签名时所需要用到的，所以记得记录这个别名。

<p class="tip">注意：请记得妥善地保管好你的密钥库文件，不要上传到版本库或者其它的地方。</p>

### 3.设置gradle变量
把`my-release-key.keystore`文件放到你工程中的android/app文件夹下。
编辑`~/.gradle/gradle.properties`（没有这个文件你就创建一个），添加如下的代码（注意把其中的****替换为相应密码）
注意：~表示用户目录，比如windows上可能是C:\Users\用户名，而mac上可能是/Users/用户名。

```
MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
MYAPP_RELEASE_KEY_ALIAS=my-key-alias
MYAPP_RELEASE_STORE_PASSWORD=*****
MYAPP_RELEASE_KEY_PASSWORD=*****
```

上面的这些会作为全局的gradle变量，我们在后面的步骤中可以用来给应用签名。

关于密钥库的注意事项:

一旦你在Play Store发布了你的应用，如果想修改签名，就必须用一个不同的包名来重新发布你的应用（这样也会丢失所有的下载数和评分）。所以请务必备份好你的密钥库和密码。

提示：如果你不想以明文方式保存密码，同时你使用的是macOS系统，那么你也可以把密码保存到钥匙串（Keychain）中。这样一来你就可以省略掉上面配置中的后两行（即MYAPP_RELEASE_STORE_PASSWORD和MYAPP_RELEASE_KEY_PASSWORD）。

添加签名到项目的gradle配置文件
编辑你项目目录下的android/app/build.gradle，添加如下的签名配置：

```
...
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            storeFile file(MYAPP_RELEASE_STORE_FILE)
            storePassword MYAPP_RELEASE_STORE_PASSWORD
            keyAlias MYAPP_RELEASE_KEY_ALIAS
            keyPassword MYAPP_RELEASE_KEY_PASSWORD
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...
```

### 4.生成发行APK包
只需在终端中运行以下命令：

```bash
$ cd android && ./gradlew assembleRelease
```

> cd android表示进入android目录（如果你已经在android目录中了那就不用输入了）。./gradlew assembleRelease在macOS、Linux或是windows的PowerShell环境中表示执行当前目录下的名为gradlew的脚本文件，且其运行参数为assembleRelease，注意这个./不可省略；而在windows的传统CMD命令行下则需要去掉./。
> Gradle的assembleRelease参数会把所有用到的JavaScript代码都打包到一起，然后内置到APK包中。如果你想调整下这个行为（比如js代码以及静态资源打包的默认文件名或是目录结构等），可以看看android/app/build.gradle文件，然后琢磨下应该怎么修改以满足你的需求。

生成的APK文件位于`android/app/build/outputs/apk/app-release.apk`，它已经可以用来发布了。

##  SDK location not found

```bash
* What went wrong:
A problem occurred configuring project ':app'.
> SDK location not found. Define location with sdk.dir in the local.properties file or with an ANDROID_HOME environment variable.
```

若遇到设置了`ANDROID_HOME`依然报这个错误，可以在`android`文件夹下建名为`local.properties`的文件
```bash
sdk.dir=/Users/USERNAME/Library/Android/sdk
ndk.dir=/Users/USERNAME/Library/Android/sdk/ndk-bundle
```
`USERNAME`是用户文件夹

## The SDK Build Tools revision (23.0.1) is too low for project 

若报如下错误,则可将`android/app/build.gradle`中的`buildToolsVersion`设置为提示的版本
```bash
* What went wrong:
A problem occurred configuring project ':app'.
> The SDK Build Tools revision (23.0.1) is too low for project ':app'. Minimum required is 25.0.0
```