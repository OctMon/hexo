---
title: flutter中使用 Future.wait 一次执行多个异步操作
date: 2020-06-09 11:09:28
tags: fluttre
categories: app
---

用法：

```dart
Future.wait([
  PackageInfoUtil.init(),
  SharedPreferencesUtil.init(),
]).then((e) {
  log("init:", e);
  callback();
});
```

执行结果：
```dart
flutter: init: => [Instance of 'PackageInfo', Instance of 'SharedPreferences']
```