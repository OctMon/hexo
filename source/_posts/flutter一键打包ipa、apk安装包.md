---
title: flutter一键打包ipa、apk安装包
date: 2020-06-22 20:44:05
tags: flutter
categories: app
---

经验证，flutter build iOS打出的ipa包太大，用xcodebuild打包正常，支持设置代码混淆。iOS如果因为签名原因打包失败，先用Xcode正常打一次就行。

直接上脚本：

```bash
## 计时
SECONDS=0

# 打包类型 1:android 2:ios any:其它全部
platform=0

# 选择打包类型
read -n1 -p "设置打包类型 1:android 2:ios any:其它全部 (5s后自动执全部打包) [1/2/any]? " -t 5 answer
platform=${answer}

echo "清理 build"
find . -d -name "build" | xargs rm -rf
flutter clean

echo "开始获取 packages 插件资源"
flutter packages get

# 不使用代码混淆
#obfuscate=""
# 开启代码混淆
obfuscate="--obfuscate --split-debug-info=app"

# iOS
build_ios() {
cd ios
project=`find . -name *.xcodeproj | awk -F "[/.]" '{print $(NF-1)}'`

## true or false 默认true
workspace_flag="true"

## Release or Debug 默认Release
configuration="Release"

## Bitcode开关 默认打开
compileBitcode=true

## 签名方式 默认自动签名，如果打包失败，先用Xcode打一次就正常了
signingStyle="automatic"

## 签名方式
### development
### ad-hoc
### app-store
### enterprise
methodStyle="ad-hoc"

## 打包导出目录
path_app="../app"

## 工作目录 build目录在.gitignore添加忽略
path_build="build"

path_archive="${path_build}/${project}.xcarchive"

path_export_options="${path_build}/ExportOptions.plist"

if [ -d "${path_build}" ]
then
    echo "目录${path_build}已存在"
else
    echo "创建目录${path_build}"
    mkdir -pv "${path_build}"
fi

funcExpertOptionFile() {
    if [ -f "$path_export_options" ]
    then
        rm -rf "$path_export_options"
    fi

    /usr/libexec/PlistBuddy -c "Add :compileBitcode bool $compileBitcode" "$path_export_options"
    /usr/libexec/PlistBuddy -c "Add :signingStyle string $signingStyle" "$path_export_options"
    /usr/libexec/PlistBuddy -c "Add :method string $methodStyle" "$path_export_options"
}

## 创建ExportOptions.plist文件
funcExpertOptionFile

if $workspace_flag
then
    ## 清理工程
    xcodebuild clean -workspace ${project}.xcworkspace \
        -scheme ${project} \
        -configuration ${configuration}

    ## 归档
    xcodebuild archive -workspace ${project}.xcworkspace \
        -scheme ${project} \
        -configuration ${configuration} \
        -archivePath ${path_archive}
else
    ## 清理工程
    xcodebuild clean -project ${project}.xcworkspace \
        -scheme ${project} \
        -configuration ${configuration}

    ## 归档
    xcodebuild archive -project ${project}.xcworkspace \
        -scheme ${project} \
        -configuration ${configuration} \
        -archivePath ${path_archive}
fi

## 判断归档结果
if [ -d "${path_archive}" ]
then
    echo "** Finished archive. Elapsed time: ${SECONDS}s **"
    echo
else
    exit 1
fi

## 导出ipa
xcodebuild  -exportArchive \
-archivePath ${path_archive} \
-exportPath ${path_app} \
-exportOptionsPlist ${path_export_options}

## ipa路径
file_ipa="${path_app}/${project}-$(date "+%Y%m%d%H%M").ipa"
mv "${path_app}/${project}.ipa" "${file_ipa}"

## 判断导出ipa结果
if [ -f "${file_ipa}" ]
then
    echo "** Finished export. Elapsed time: ${SECONDS}s **"
    rm -rf "$path_build"
    echo $file_ipa
    open $path_app
    say "iOS打包成功"
else
    echo "遇到报错了😭, 打开Xcode查找错误原因"
    say "iOS打包失败"
    exit 1
fi

echo "------------------------------------------------------------------------------"
echo "🎉  Congrats"

echo "🚀  ${project} successfully published"
echo "📅  Finished. Elapsed time: ${SECONDS}s"
echo "🌎  https://octmon.github.io"
echo "👍  Tell your friends!"
echo "------------------------------------------------------------------------------"

echo

cd ..
}

##==================================apk==================================
if [[ ${platform} -ne 2 ]]; then
    echo "开始build apk"
    flutter build apk $obfuscate --release --verbose #--no-codesign
    echo "build apk已完成"

    file_apk=android-$(date "+%Y%m%d%H%M").apk

    cp -r build/app/outputs/apk/release/app-release.apk app/$file_apk

    if [[ -f app/${file_apk} ]]
        then
        open app
        say "android打包成功"
    else
        echo "遇到报错了😭, 打开Android Studio查找错误原因"
        say "android打包失败"
    fi

    echo "📅  Finished. Elapsed time: ${SECONDS}s"
fi
##==================================apk==================================

##==================================ipa==================================
if [[ ${platform} -ne 1 ]]; then
    echo "开始build ios"
    flutter build ios $obfuscate --release --verbose #--no-codesign
    echo "build ios已完成"

    build_ios
fi
##==================================ipa==================================
```

