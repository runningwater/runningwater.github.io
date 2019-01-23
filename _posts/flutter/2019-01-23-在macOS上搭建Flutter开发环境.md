---
layout: post
title: 在 macOS 上搭建 Flutter 开发环境
categories: flutter
description: Flutter 安装
keywords:  flutter, mac OS X,install
---

由于在国内访问Flutter有时可能会受到限制，Flutter官方为中国开发者搭建了临时镜像

**目录**

* TOC
{:toc}

## 系统要求

要安装并运行Flutter，您的开发环境必须满足以下最低要求:

操作系统: macOS (64-bit)
磁盘空间: 700 MB (不包括 Xcode 或 Android Studio 的磁盘空间）.
工具: Flutter 依赖下面这些命令行工具.
`bash`, `mkdir`, `rm`, `git`, `curl`, `unzip`, `which`

## 使用镜像

由于在国内访问Flutter有时可能会受到限制，Flutter官方为中国开发者搭建了临时镜像，大家可以将如下环境变量加入到用户环境变量中：

```
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

> 注意： 此镜像为临时镜像，并不能保证一直可用，读者可以参考详情请参考 Using Flutter in China 以获得有关镜像服务器的最新动态。

## 获取Flutter SDK

去 flutter 官网下载其最新可用的安装包，转到下载页 。

> 注意，Flutter的渠道版本会不停变动，请以Flutter官网为准。另外，在中国大陆地区，要想正常获取安装包列表或下载安装包，可能需要翻墙，读者也可以去Flutter github项目下去下载安装包，转到下载页 。

解压安装包到你想安装的目录，如：

	cd ~/development
	unzip ~/Downloads/flutter_macos_v0.5.1-beta.zip

添加flutter相关工具到path中：

	export PATH=pwd/flutter/bin:$PATH

此代码只能暂时针对当前命令行窗口设置PATH环境变量，要想永久将 Flutter 添加到PATH中请参考下面更新环境变量部分。

> 注意： 由于一些flutter命令需要联网获取数据，如果您是在国内访问，由于众所周知的原因，直接访问很可能不会成功。 上面的 `PUB_HOSTED_URL` 和 `FLUTTER_STORAGE_BASE_URL` 是google为国内开发者搭建的临时镜像。详情请参考 [Using Flutter in China][1]

要更新现有版本的 Flutter，请参阅[升级 Flutter][2]。

## 运行 `flutter doctor`

运行以下命令查看是否需要安装其它依赖项来完成安装：

	flutter doctor

该命令检查您的环境并在终端窗口中显示报告。Dart SDK 已经在捆绑在 Flutter 里了，没有必要单独安装 Dart。 仔细检查命令行输出以获取可能需要安装的其他软件或进一步需要执行的任务（以粗体显示）

例如:

	[-] Android toolchain - develop for Android devices
	• Android SDK at /Users/obiwan/Library/Android/sdk
	✗ Android SDK is missing command line tools; download from https://goo.gl/XxQghQ
	• Try re-installing or updating your Android SDK,
	visit https://flutter.io/setup/#android-setup for detailed instructions.

一般的错误会是 Xcode 或 Android Studio 版本太低、或者没有 `ANDROID_HOME` 环境变量等，请按照提示解决。下面贴一个笔者本机(mac)的环境变量配置，您可以对比修正：

	export PATH=/Users/用户名/Documents/flutter/flutter/bin:$PATH
	export ANDROID_HOME=”/Users/用户名/Documents/android_sdk” //android sdk目录，替换为你自己的即可
	export PATH=${PATH}:${ANDROID_HOME}/tools
	export PATH=${PATH}:${ANDROID_HOME}/platform-tools
	export PUB_HOSTED_URL=https://pub.flutter-io.cn
	export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

第一次运行一个flutter命令（如 `flutter doctor`）时，它会下载它自己的依赖项并自行编译。以后再运行就会快得多。

以下各部分介绍如何执行这些任务并完成设置过程。你会看到在flutter doctor输出中， 如果你选择使用IDE，我们提供了，IntelliJ IDEA，Android Studio和VS Code的插件， 请参阅编辑器设置 以了解安装Flutter和Dart插件的步骤。

一旦你安装了任何缺失的依赖，再次运行flutter doctor命令来验证你是否已经正确地设置了。

该flutter工具使用 Google Analytics 匿名报告功能使用情况统计信息和基本崩溃报告。 这些数据用于帮助改进Flutter工具。Analytics不是一运行或在运行涉及flutter config的任何命令时就发送， 因此您可以在发送任何数据之前退出分析。要禁用报告，请执行 `flutter config –no-analytics` 并显示当前设置，然后执行 `flutter config`。 请参阅 [Google的隐私政策][3]。

## 更新环境变量
您在命令行只能更新当前会话的PATH变量，如Clone Flutter repo所示。 但是，您可能需要的是永久更新此变量，以便您可以运行flutter命令在任何终端会话中。

对于所有终端会话永久修改此变量的步骤是和特定计算机系统相关的。通常，您会在打开新窗口时将设置环境变量的命令添加到执行的文件中。例如

1. 确定您Flutter SDK的目录，您将在步骤3中用到。

2. 打开(或创建) $HOME/.bash_profile. 文件路径和文件名可能在您的机器上不同.

3. 添加以下行并更改`[PATH_TO_FLUTTER_GIT_DIRECTORY]`为克隆Flutter的 git repo 的路径:

```
export PUB_HOSTED_URL=https://pub.flutter-io.cn //国内用户需要设置
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn //国内用户需要设置
export PATH=PATH_TO_FLUTTER_GIT_DIRECTORY/flutter/bin:$PATH
export PATH=~/document/code/flutter/bin:$PATH
```

4. 运行 source $HOME/.bash_profile 刷新当前终端窗口.
 > 注意: 如果你使用的是 **zsh** ，终端启动时 `~/.bash_profile` 将不会被加载，解决办法就是修改 `~/.zshrc` ，在其中添加：`source ~/.bash_profile`

5.通过运行flutter/bin命令验证目录是否在已经在PATH中: 

```echo $PATH```



更多详细信息，请参阅 [this StackExchange question][4].

## 平台设置

macOS 支持为 `iOS` 和 `Android` 开发 Flutter 应用程序。现在完成两个平台设置步骤中的至少一个，以便能够构建并运行您的第一个Flutter应用程序
### iOS 设置
#### 安装 Xcode

要为iOS开发Flutter应用程序，您需要 Xcode 7.2 或更高版本:

安装 Xcode 7.2 或更新版本(通过链接下载或苹果应用商店).

配置Xcode命令行工具以使用新安装的Xcode版本 `sudo xcode-select –switch /Applications/Xcode.app/Contents/Developer` 对于大多数情况，当您想要使用最新版本的Xcode时，这是正确的路径。如果您需要使用不同的版本，请指定相应路径。

确保Xcode许可协议是通过打开一次Xcode或通过命令 `sudo xcodebuild -license`同意过了.

使用 Xcode，您可以在iOS设备或模拟器上运行Flutter应用程序。

#### 设置iOS模拟器

要准备在iOS模拟器上运行并测试您的Flutter应用，请按以下步骤操作：

在Mac上，通过Spotlight或使用以下命令找到模拟器:
	open -a Simulator
通过检查模拟器 硬件>设备 菜单中的设置，确保您的模拟器正在使用64位设备（iPhone 5s或更高版本）.
根据您的开发机器的屏幕大小，模拟的高清屏iOS设备可能会使您的屏幕溢出。在模拟器的 Window> Scale 菜单下设置设备比例
运行 `flutter run` 启动您的应用

#### 安装到iOS设备

要将您的Flutter应用安装到iOS真机设备，您需要一些额外的工具和一个Apple帐户，您还需要在Xcode中进行设置。

安装 homebrew （如果已经安装了brew,跳过此步骤）.
打开终端并运行这些命令来安装用于将Flutter应用安装到iOS设备的工具

	brew update
	brew install –HEAD libimobiledevice
	brew install ideviceinstaller ios-deploy cocoapods
	pod setup

如果这些命令中的任何一个失败并出现错误，请运行brew doctor并按照说明解决问题.

遵循Xcode签名流程来配置您的项目:
在你Flutter项目目录中通过 `open ios/Runner.xcworkspace` 打开默认的 Xcode workspace.

在Xcode中，选择导航面板左侧中的 `Runner` 项目

在 Runner target 设置页面中，确保在 `常规>签名>团队` 下选择了您的开发团队。当您选择一个团队时，Xcode 会创建并下载开发证书，向您的设备注册您的帐户，并创建和下载配置文件（如果需要）

要开始您的第一个iOS开发项目，您可能需要使用您的 Apple ID 登录Xcode.

任何Apple ID都支持开发和测试。需要注册Apple开发者计划才能将您的应用分发到App Store. 查看differences between Apple membership types.
当您第一次attach真机设备进行iOS开发时，您需要同时信任你的Mac和该设备上的开发证书。首次将iOS设备连接到Mac时,请在对话框中选择 Trust。

	Trust Mac

然后，转到iOS设备上的设置应用程序，选择 常规>设备管理 并信任您的证书。
如果Xcode中的自动签名失败，请验证项目的 `General > Identity > Bundle Identifier` 值是否唯一.
	Check the app’s Bundle ID

运行启动您的应用程序 `flutter run`.

### Android 设置
#### 安装Android Studio

要为Android开发Flutter应用，您可以使用 Mac，Windows 或 Linux（64位）机器.

Flutter需要安装和配置 Android Studio:

下载并安装 Android Studio.

启动 Android Studio，然后执行 `“Android Studio安装向导”`。这将安装最新的 Android SDK，Android SDK  平台工具和 Android SDK构建工具，这是Flutter为Android开发时所必需的

#### 设置您的Android设备

要准备在Android设备上运行并测试您的Flutter应用，您需要安装 Android 4.1（API level 16）或更高版本的Android设备.

在您的设备上启用 开发人员选项 和 USB 调试 。详细说明可在Android文档中找到。
使用USB将手机插入电脑。如果您的设备出现提示，请授权您的计算机访问您的设备。
在终端中，运行 `flutter devices` 命令以验证Flutter识别您连接的Android设备。
运行启动您的应用程序 `flutter run`。
默认情况下，Flutter 使用的 Android SDK版本是基于你的 adb 工具版本。 如果您想让Flutter使用不同版本的Android SDK，则必须将该 `ANDROID_HOME` 环境变量设置为 SDK 安装目录。

#### 设置Android模拟器
要准备在 Android 模拟器上运行并测试您的Flutter应用，请按照以下步骤操作：

1. 在您的机器上启用 [**VM acceleration**][5] .
2. 启动 `Android Studio>Tools>Android>AVD Manager` 并选择 `Create Virtual Device`.
3. 选择一个设备并选择 `Next`。
4. 为要模拟的Android版本选择一个或多个系统映像，然后选择 `Next`. 建议使用 `x86` 或 `x86_64` image .
5. 在 Emulated Performance下, 选择 `Hardware - GLES 2.0` 以启用 硬件加速.
6. 验证AVD配置是否正确，然后选择 `Finish`。

> 有关上述步骤的详细信息，请参阅 [Managing AVDs][6].

7. 在 Android Virtual Device Manager 中, 点击工具栏的 `Run`。模拟器启动并显示所选操作系统版本或设备的启动画面.
运行 flutter run 启动您的设备. 连接的设备名是 `Android SDK built for `,其中 platform 是芯片系列, 如 x86.



[1]: https://flutter.io/community/china
[2]: https://flutter.io/docs/development/tools/sdk/upgrading
[3]: https://www.google.com/intl/en/policies/privacy
[4]: https://unix.stackexchange.com/questions/26047/how-to-correctly-add-a-path-to-path
[5]: https://developer.android.com/studio/run/emulator-acceleration
[6]: https://developer.android.com/studio/run/managing-avds