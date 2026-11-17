# react-issues-textInlineImage

[![Pre Merge Checks](https://github.com/Hao-yiwen/react-issues-textInlineImage/actions/workflows/pre-merge.yml/badge.svg)](https://github.com/Hao-yiwen/react-issues-textInlineImage/actions/workflows/pre-merge.yml)
[![React Native](https://img.shields.io/badge/React%20Native-0.74.3-61DAFB)](https://reactnative.dev)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

基于 [react-native-community/reproducer-react-native](https://github.com/react-native-community/reproducer-react-native) 模板的 **React Native 最小复现工程**。界面把远程图片放进 `Text`（以及一个横向对照行），用来查看阿拉伯语从右到左（RTL）和拉丁字母从左到右（LTR）排版下的内联图片。

仓库里没有产品功能、路由或后端。`ReproducerApp/App.tsx` 是唯一的界面代码；仓库没有写明期望的正确布局，也没有记录具体的崩溃或错位现象。

## 界面内容

`App.tsx` 在居中容器里渲染三块内容，图片地址均为 `https://via.placeholder.com/20`（20×20）：

| 区块 | 结构 |
| --- | --- |
| 阿拉伯语内联图片 | 一个 `Text`，文案为「هنا نص باللغة العربية」和「مزود بصورة」，中间嵌套 `Image`（`resizeMode="contain"`）。`writingDirection` 随 `I18nManager.isRTL` 在 `rtl` / `ltr` 之间切换。 |
| 横向对照 | `flexDirection: 'row-reverse'` 的 `View`。图片与文字是兄弟节点：图片 20×20，文字「هذا نص مع صورة」的 `writingDirection` 固定为 `rtl`。 |
| 拉丁字母内联图片 | 一个 `Text`，两侧文案均为 `ehhehehehahehah`，中间同样嵌套 20×20 的 `Image`。文字方向同样跟随 `I18nManager.isRTL`。 |

根容器为 `flex: 1`、水平垂直居中、`padding: 16`，并设置了 `collapsable={true}`。工程没有截图；Android 启动图标只在 `mipmap-*` 里，不代表上述界面。

## 技术栈

应用依赖只有 `react@18.2.0` 与 `react-native@0.74.3`。包管理器是随仓库附带的 Yarn `3.6.4`（`package.json` 的 `packageManager`，以及 `ReproducerApp/.yarn/releases/yarn-3.6.4.cjs`）。

| 项目 | 版本 / 配置 |
| --- | --- |
| Node.js | `>=18`（CI 使用 18） |
| TypeScript | 5.0.4 |
| Jest / Babel | Jest 29.6.3，`@react-native/babel-preset` 0.74.85 |
| ESLint / Prettier | ESLint 8、`@react-native/eslint-config` 0.74.85，Prettier 2.8.8 |
| Android | `minSdk` 23，`compileSdk` / `targetSdk` 34，Build Tools 34.0.0，NDK 26.1.10909125，Kotlin 1.9.22，Gradle 8.6，应用 ID `com.reproducerapp` |
| JS 引擎 / 架构 | Hermes 开启（`hermesEnabled=true`），新架构关闭（`newArchEnabled=false`） |
| iOS | 部署目标 13.4，Bundle ID `org.reactjs.native.example.ReproducerApp`，版本 1.0；CI 使用 Xcode 15.3 |
| CocoaPods | `>= 1.13` 且 `< 1.15`（见 `ReproducerApp/Gemfile`，Ruby `>= 2.6.10`） |

## 目录结构

```text
.
├── LICENSE                          # MIT
├── README.md                        # 本说明
├── .github/workflows/
│   ├── pre-merge.yml                # 向 main 推送或任意 PR：Android / iOS 原生构建
│   └── auto-updater.yml             # 每日检查 RN 版本；条件限定为官方模板仓库名
└── ReproducerApp/                   # 应用根目录（命令都在这里执行）
    ├── App.tsx                      # 复现界面
    ├── index.js                     # AppRegistry 入口
    ├── app.json                     # 名称 ReproducerApp
    ├── package.json
    ├── __tests__/App.test.tsx       # 仅断言组件能被 test renderer 创建
    ├── android/                     # Kotlin 工程
    ├── ios/                         # Objective-C 工程与 XCTest
    └── README.md                    # CLI 生成的英文模板，以本文件为准
```

## 环境要求

- Node.js 18 或更高。
- 在 `ReproducerApp` 目录执行 `yarn`，以使用仓库自带的 Yarn 3.6.4。
- Android：JDK 17（与 CI 的 Zulu 17 一致）、Android SDK（`compileSdk` 34）以及一台模拟器或真机。CI 的构建命令是 `yarn react-native build-android`（Debug 与 `--mode=Release`）。
- iOS：macOS、Xcode 15.3 一带、CocoaPods。CI 在 `ReproducerApp/ios` 执行 `pod install`，再执行 `yarn react-native build-ios`。

## 安装与运行

```bash
cd ReproducerApp
yarn install
```

| 命令 | 作用 |
| --- | --- |
| `yarn start` | 启动 Metro |
| `yarn android` | `react-native run-android` |
| `yarn ios` | `react-native run-ios`（仅 macOS） |
| `yarn test` | Jest。当前只有「组件能够创建」这一条用例 |
| `yarn lint` | ESLint |

Android 需要先让模拟器或设备处于可用状态，Metro 可另开一个终端保持运行。iOS 首次编译前在 `ReproducerApp/ios` 执行 `bundle exec pod install` 或 `pod install`（版本约束见 `Gemfile`）。

### 本环境已核对的命令

在 Linux 云端环境（Node v22.23.4）中核对过：

- `yarn install` 成功。
- `yarn test`：1 个测试通过。
- `yarn lint`：退出码 0，11 条既有警告（JSX 作用域与内联样式），0 个 error。

同一环境没有 `ANDROID_HOME`，也不是 macOS，因此没有运行 `yarn android`、`yarn ios`、`pod install` 或 `react-native build-android` / `build-ios`。

## 持续集成

`.github/workflows/pre-merge.yml`（Pre Merge Checks）在推送到 `main`，以及对任意分支的 pull request 上运行：

- `test-android`（`ubuntu-latest`）：Java 17、Node 18、Gradle、`yarn install`，然后 Debug 与 Release 的 `yarn react-native build-android`。
- `test-ios`（`macos-latest`）：Node 18、Xcode 15.3、`yarn install`、`pod install`、`yarn react-native build-ios`。

`auto-updater.yml` 每天检查 `react-native` 是否过期，并在过期时用 `npx react-native@latest init` 重建 `ReproducerApp`。该 job 的条件是 `github.repository == 'react-native-community/reproducer-react-native'`，因此只在官方模板仓库执行，不会改写本仓库。

## 备注

- iOS 测试目标 `ReproducerAppTests` 仍查找无障碍标签 `Welcome to React`（模板欢迎页文案）。当前界面没有这段文字。CI 的 iOS job 只做原生构建，不跑这个 XCTest。
- Android 清单声明了 `INTERNET` 权限，用于加载上述 HTTPS 图片。iOS `Info.plist` 允许本地网络，`NSAllowsArbitraryLoads` 为 false。
- 许可证见 [LICENSE](LICENSE)（MIT）。版权声明仍是模板中的占位作者名。

## 参考

- [React Native 0.74 文档](https://reactnative.dev/docs/0.74/getting-started)
- [环境搭建](https://reactnative.dev/docs/set-up-your-environment)
- [官方复现模板](https://github.com/react-native-community/reproducer-react-native)
