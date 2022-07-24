---
title: React-Native-基本命令汇总
date: 2021-03-10 21:12:40
categories: react-native
---
最近进行React Native的一些优化工作，发现React Native提供了很多简单方便的配置，但是自己一直没有使用过，而且React Native提供了非常好的help功能，会列出对应的说明和example，这边简单整理下一些常用的命令。

## 1. 查看所有命令 ***react-native --help***
```
mac:react-native --help

  Usage: react-native [options] [command]

  Options:

    -V, --version                      output the version number
    -h, --help                         output usage information

  Commands:

    start [options]                    starts the webserver
    run-ios [options]                  builds your app and starts it on iOS simulator
    run-android [options]              builds your app and starts it on a connected Android emulator or device
    new-library [options]              generates a native library bridge
    bundle [options]                   builds the javascript bundle for offline use
    unbundle [options]                 builds javascript as "unbundle" for offline use
    eject [options]                    Re-create the iOS and Android folders and native code
    link [options] [packageName]       links all native dependencies (updates native build files)
    unlink [options] <packageName>     unlink native dependency
    install [options] <packageName>    install and link native dependencies
    uninstall [options] <packageName>  uninstall and unlink native dependencies
    upgrade [options]                  upgrade your app's template files to the latest version; run this after updating the react-native version in your package.json and running npm install
    log-android [options]              starts adb logcat
    log-ios [options]                  starts iOS device syslog tail
    info [options]                     Get relevant version info about OS, toolchain and libraries
```

## 2. 最常用的启动命令***react-native start***
```
mac:react-native start --help
react-native start [options]
  starts the webserver

  Options:

    --port [number]                                                (default: 8081)
    --host [string]                                                (default: )
    --root [list]                                                 add another root(s) to be used by the packager in this project (default: )
    --projectRoots [list]                                         override the root(s) to be used by the packager (default: /Users/mac/mallcategories)
    --assetExts [list]                                            Specify any additional asset extensions to be used by the packager (default: )
    --sourceExts [list]                                           Specify any additional source extensions to be used by the packager (default: )
    --platforms [list]                                            Specify any additional platforms to be used by the packager (default: )
    --providesModuleNodeModules [list]                            Specify any npm packages that import dependencies with providesModule (default: react-native,react-native-windows)
    --max-workers [number]                                        Specifies the maximum number of workers the worker-pool will spawn for transforming files. This defaults to the number of the cores available on your machine.
    --skipflow                                                    Disable flow checks
    --nonPersistent                                               Disable file watcher
    --transformer [string]                                        Specify a custom transformer to be used
    --reset-cache, --resetCache                                   Removes cached files
    --custom-log-reporter-path, --customLogReporterPath [string]  Path to a JavaScript file that exports a log reporter as a replacement for TerminalReporter
    --verbose                                                     Enables logging
    --https                                                       Enables https connections to the server
    --key [path]                                                  Path to custom SSL key
    --cert [path]                                                 Path to custom SSL cert
    --config [string]                                             Path to the CLI configuration file
    -h, --help                                                    output usage information
```

## 3. iOS启动命令 ***react-native --run-ios***
```
mac: react-native run-ios --help

  react-native run-ios [options]
  builds your app and starts it on iOS simulator

  Options:

    --simulator [string]      Explicitly set simulator to use (default: iPhone 6)
    --configuration [string]  Explicitly set the scheme configuration to use
    --scheme [string]         Explicitly set Xcode scheme to use
    --project-path [string]   Path relative to project root where the Xcode project (.xcodeproj) lives. The default is 'ios'. (default: ios)
    --device [string]         Explicitly set device to use by name.  The value is not required if you have a single device connected.
    --udid [string]           Explicitly set device to use by udid
    --no-packager             Do not launch packager while building
    --verbose                 Do not use xcpretty even if installed
    --config [string]         Path to the CLI configuration file
    -h, --help                output usage information

  Example usage:

    Run on a different simulator, e.g. iPhone 5:
    react-native run-ios --simulator "iPhone 5"

    Pass a non-standard location of iOS directory:
    react-native run-ios --project-path "./app/ios"

    Run on a connected device, e.g. Max's iPhone:
    react-native run-ios --device "Max's iPhone"
```
## 4. Android 启动命令 ***react-native run-android***
```
mac: react-native run-android --help

  react-native run-android [options]
  builds your app and starts it on a connected Android emulator or device

  Options:

    --install-debug
    --root [string]           Override the root directory for the android build (which contains the android directory) (default: )
    --flavor [string]         --flavor has been deprecated. Use --variant instead
    --variant [string]
    --appFolder [string]      Specify a different application folder name for the android source. (default: app)
    --appId [string]          Specify an applicationId to launch after build. (default: )
    --appIdSuffix [string]    Specify an applicationIdSuffix to launch after build. (default: )
    --main-activity [string]  Name of the activity to start (default: MainActivity)
    --deviceId [string]       builds your app and starts it on a specific device/simulator with the given device id (listed by running "adb devices" on the command line).
    --no-packager             Do not launch packager while building
    --port [number]            (default: 8081)
    --config [string]         Path to the CLI configuration file
    -h, --help                output usage information
```

## 5. 生成bundle文件 ***react-native bundle***
```
mac: react-native bundle --help

  react-native bundle [options]
  builds the javascript bundle for offline use

  Options:

    --entry-file <path>                Path to the root JS file, either absolute or relative to JS root
    --platform [string]                Either "ios" or "android" (default: ios)
    --transformer [string]             Specify a custom transformer to be used
    --dev [boolean]                    If false, warnings are disabled and the bundle is minified (default: true)
    --bundle-output <string>           File name where to store the resulting bundle, ex. /tmp/groups.bundle
    --bundle-encoding [string]         Encoding the bundle should be written in (https://nodejs.org/api/buffer.html#buffer_buffer). (default: utf8)
    --max-workers [number]             Specifies the maximum number of workers the worker-pool will spawn for transforming files.This defaults to the number of the cores available on your machine.
    --sourcemap-output [string]        File name where to store the sourcemap file for resulting bundle, ex. /tmp/groups.map
    --sourcemap-sources-root [string]  Path to make sourcemap's sources entries relative to, ex. /root/dir
    --sourcemap-use-absolute-path      Report SourceMapURL using its full path
    --assets-dest [string]             Directory name where to store assets referenced in the bundle
    --verbose                          Enables logging
    --reset-cache                      Removes cached files
    --read-global-cache                Try to fetch transformed JS code from the global cache, if configured.
    --config [string]                  Path to the CLI configuration file
    -h, --help                         output usage information
```
## 5. 生成unbundle文件 ***react-native unbundle***
```
mac: react-native unbundle --help

  react-native unbundle [options]
  builds javascript as "unbundle" for offline use

  Options:

    --entry-file <path>                Path to the root JS file, either absolute or relative to JS root
    --platform [string]                Either "ios" or "android" (default: ios)
    --transformer [string]             Specify a custom transformer to be used
    --dev [boolean]                    If false, warnings are disabled and the bundle is minified (default: true)
    --bundle-output <string>           File name where to store the resulting bundle, ex. /tmp/groups.bundle
    --bundle-encoding [string]         Encoding the bundle should be written in (https://nodejs.org/api/buffer.html#buffer_buffer). (default: utf8)
    --max-workers [number]             Specifies the maximum number of workers the worker-pool will spawn for transforming files.This defaults to the number of the cores available on your machine.
    --sourcemap-output [string]        File name where to store the sourcemap file for resulting bundle, ex. /tmp/groups.map
    --sourcemap-sources-root [string]  Path to make sourcemap's sources entries relative to, ex. /root/dir
    --sourcemap-use-absolute-path      Report SourceMapURL using its full path
    --assets-dest [string]             Directory name where to store assets referenced in the bundle
    --verbose                          Enables logging
    --reset-cache                      Removes cached files
    --read-global-cache                Try to fetch transformed JS code from the global cache, if configured.
    --indexed-unbundle                 Force indexed unbundle file format, even when building for android
    --config [string]                  Path to the CLI configuration file
    -h, --help                         output usage information
```

## 6. 查看React Native相关的信息 ***react-native info***
```
mac: react-native info

Environment:
  OS: macOS High Sierra 10.13.6
  Node: 10.0.0
  Yarn: 1.9.4
  npm: 5.6.0
  Watchman: 4.7.0
  Xcode: Xcode 10.1 Build version 10B61
  Android Studio: 1.4 AI-141.2343393

Packages: (wanted => installed)
  react: 16.2.0
  react-native: 0.52.0
```
