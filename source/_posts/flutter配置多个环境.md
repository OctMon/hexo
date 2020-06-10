---
title: flutter配置多个环境
date: 2020-06-10 21:37:50
tags: flutter
---

复制一份默认的flutter环境，并修改文件夹为：flutter_dev

打开终端输入：

```bash
open .zshrc 
```

在文本编辑里加入flutter_dev环境：

```bash
export PATH=$PATH:~/Library/Android/sdk/platform-tools;
export PATH=~/Library/Android/flutter/bin:$PATH
export PUB_HOSTED_URL=https://pub.flutter-io.cn 
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

alias flutter_dev=~/Library/Android/flutter_dev/bin/flutter
```

保存之后，新建Shell窗口验证默认flutter环境，输入:

```bash
flutter doctor
```

结果：

```bash
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.12.13+hotfix.10-pre.1, on Mac OS X 10.15.5
    19F101, locale zh-Hans-CN)
[✓] Android toolchain - develop for Android devices (Android SDK version 29.0.3)
[✓] Xcode - develop for iOS and macOS (Xcode 11.4.1)
[✓] Android Studio (version 4.0)
[✓] VS Code (version 1.45.1)
[✓] Connected device (1 available)

• No issues found!
```

验证flutter_dev环境，输入：

```bash
flutter_dev doctor
```

结果:

```bash
[✓] Flutter (Channel dev, 1.19.0-3.0.pre, on Mac OS X 10.15.5 19F101, locale
    zh-Hans-CN)
 
[✓] Android toolchain - develop for Android devices (Android SDK version 29.0.3)
[✓] Xcode - develop for iOS and macOS (Xcode 11.4.1)
[✓] Chrome - develop for the web
[✓] Android Studio (version 4.0)
[✓] VS Code (version 1.45.1)
[✓] Connected device (4 available)

• No issues found!
```

