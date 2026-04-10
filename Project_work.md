---

# Part 1: English Prompt for Cursor / Claude

下面这份是可以直接复制给 AI 的版本。  
我尽量按**软件工程式任务说明**写，方便 Cursor / Claude 理解。

---

## Prompt Title
**Generate a complete Android Studio project for Pixel-style and Nothing-style home screen widgets**

---

## Full English Prompt

```text
You are a senior Android native engineer. Generate a complete, compilable Android Studio project named `PixelNothingWidgets`.

The project must be a real Android native app written in Kotlin. It must provide multiple home screen widgets (App Widgets) that mimic Pixel-style and Nothing-style widgets, while staying within public Android APIs only.

========================
1. PROJECT GOAL
========================

Build an Android app that provides these widgets:

1. Pixel-style Clock Widget
2. Pixel-style Weather Widget
3. Pixel-style Clock + Weather Combined Widget
4. Nothing-style Clock Widget
5. Nothing-style Weather Widget

The Pixel-style widgets should feel close to Material Design 3 / Material You.
The Nothing-style widgets should feel close to Nothing OS digital / monochrome / industrial minimal style.

The app must include:
- a main screen
- a settings screen
- a weather detail screen
- widget update logic
- weather fetching logic
- local settings persistence
- system app launch intents
- periodic weather refresh

========================
2. TECHNICAL REQUIREMENTS
========================

Use the following stack and versions:

- Kotlin
- Android native app
- Android Studio project
- Gradle 8.7
- Android Gradle Plugin 8.5.2
- Kotlin 1.9.24
- JDK 17
- compileSdk = 34
- targetSdk = 34
- minSdk = 26

Use:
- AppWidgetProvider + RemoteViews for widgets
- Jetpack Compose ONLY for in-app screens (main/settings/weather detail)
- DataStore Preferences for local config
- WorkManager for periodic weather refresh
- Retrofit + Kotlinx Serialization for HTTP and JSON
- public Android APIs only

Do NOT use:
- Flutter
- React Native
- private Android APIs
- Pixel private services
- Nothing private services
- unnecessary heavy architecture
- DI frameworks unless absolutely needed

========================
3. WEATHER REQUIREMENTS
========================

Weather data source: Caiyun Weather API.

User must manually configure:
- Caiyun API token
- latitude
- longitude
- city display name

Store these values in DataStore Preferences.

Weather refresh requirements:
- periodic refresh every 30 minutes using WorkManager
- trigger immediate refresh after user saves settings
- trigger refresh when weather widgets are first added

At minimum parse:
- temperature
- weather condition code
- humidity (optional but preferred)

Map weather codes to readable text, e.g.
- CLEAR_DAY -> Sunny
- CLOUDY -> Cloudy
- LIGHT_RAIN -> Light Rain
etc.

Error states must be handled and shown on widgets:
- token missing
- request failed
- network error
- empty data

For Pixel-style widgets, use readable human-friendly text.
For Nothing-style widgets, prefer uppercase concise error labels like:
- TOKEN REQUIRED
- SYNC FAILED
- NETWORK ERROR

========================
4. PIXEL-STYLE REQUIREMENTS
========================

Pixel-style widgets must:
- visually resemble Material Design 3 / Material You
- use rounded card-like layouts
- have good spacing and hierarchy
- feel close to Pixel home widget aesthetics, but do not copy private Google UI assets

Dynamic color:
- on Android 12+ use WallpaperManager / WallpaperColors to derive system wallpaper colors
- use these colors to tint widget background and/or accent elements
- ensure text contrast remains readable
- on Android 8 to 11, fall back to a default Material-like palette

Pixel Clock Widget:
- show current time
- show current date
- clicking time opens system clock / alarms
- clicking date opens calendar

Pixel Weather Widget:
- show temperature
- show condition
- show city
- optionally show icon placeholder
- clicking widget opens in-app weather detail screen

Pixel Clock + Weather Widget:
- show time
- show date
- show temperature
- show condition
- show city
- clicking time opens clock
- clicking date opens calendar
- clicking weather area opens in-app weather detail screen

========================
5. NOTHING-STYLE REQUIREMENTS
========================

Nothing-style widgets must:
- use monochrome black/white design
- feel close to Nothing OS digital / dot-matrix / industrial minimal aesthetic
- not use system dynamic color
- prefer black or dark gray background with white text
- use uppercase text where suitable
- large bold geometric time display
- minimal industrial modular layout

Nothing Clock Widget:
- show current time
- show current date
- clicking opens system clock

Nothing Weather Widget:
- show temperature
- show condition
- show city
- clicking opens in-app weather detail screen

If a true dot-matrix font is not available, simulate the style using:
- bold text
- uppercase text
- modular spacing
- segmented layout
- geometric typography

========================
6. SYSTEM INTENTS REQUIREMENTS
========================

Use public Android intents only.

Clock:
- use AlarmClock.ACTION_SHOW_ALARMS

Calendar:
- use Intent.ACTION_VIEW with CalendarContract.CONTENT_URI

Weather:
- open in-app weather detail activity

All system launches must be safe:
- if unavailable on some ROMs, avoid crash
- provide fallback behavior if needed

========================
7. IN-APP SCREENS
========================

MainActivity:
- app title
- brief description
- button to open settings
- brief note telling the user how to add widgets on the home screen

Settings screen (Compose):
- input for Caiyun token
- input for latitude
- input for longitude
- input for city display name
- save button

On save:
- persist values to DataStore
- update existing widgets
- trigger immediate weather sync

Weather detail screen (Compose):
- show city name
- show temperature
- show weather condition
- show humidity if available
- show last updated time
- keep UI simple but real and compilable

========================
8. PROJECT STRUCTURE
========================

Use a clear maintainable structure similar to:

app/src/main/java/com/example/pixelnothingwidgets/
├─ MainActivity.kt
├─ data/
│  └─ SettingsDataStore.kt
├─ weather/
│  ├─ CaiyunApi.kt
│  ├─ WeatherModels.kt
│  ├─ WeatherRepository.kt
│  └─ WeatherMapper.kt
├─ system/
│  ├─ DynamicColorHelper.kt
│  └─ SystemAppLauncher.kt
├─ widget/
│  ├─ WidgetRenderer.kt
│  ├─ WidgetTimeUtils.kt
│  ├─ WidgetUpdateReceiver.kt
│  ├─ pixel/
│  │  ├─ PixelClockWidgetProvider.kt
│  │  ├─ PixelWeatherWidgetProvider.kt
│  │  └─ PixelClockWeatherWidgetProvider.kt
│  └─ nothing/
│     ├─ NothingClockWidgetProvider.kt
│     └─ NothingWeatherWidgetProvider.kt
├─ work/
│  └─ WeatherSyncWorker.kt
└─ ui/
   ├─ settings/
   │  ├─ SettingsActivity.kt
   │  └─ SettingsScreen.kt
   └─ weather/
      ├─ WeatherDetailActivity.kt
      └─ WeatherDetailScreen.kt

========================
9. OUTPUT REQUIREMENTS
========================

You must output a COMPLETE project, not fragments.

Output in this order:
1. full project tree
2. settings.gradle.kts
3. root build.gradle.kts
4. gradle.properties
5. app/build.gradle.kts
6. AndroidManifest.xml
7. all Kotlin source files
8. all XML layout files
9. all widget provider XML files
10. all values XML files needed
11. run instructions
12. deployment instructions

For every file, output using this exact format:

FILE: relative/path/to/file
```kotlin
...full code...
```

or

FILE: relative/path/to/file
```xml
...full code...
```

Rules:
- do not omit imports
- do not omit manifest declarations
- do not omit resources
- do not leave pseudo-code
- do not leave large TODO placeholders
- code should be as compilable as possible on first paste

If response length is too long, split into multiple consecutive parts:
- Part 1
- Part 2
- Part 3
but keep the output complete and continuous.

========================
10. CHINA MAINLAND DEVELOPMENT CONSTRAINTS
========================

The project is intended to be developed on Windows inside mainland China.
Therefore:
- prefer stable and minimal dependencies
- avoid obscure repositories
- use google() and mavenCentral() only in project files
- keep repository declarations clean and standard
- do not add experimental plugins
- do not require private package registries

After generating the project, also provide:
- a short Windows + Android Studio setup guide
- recommended SDK components to install
- notes for users in mainland China about Gradle dependency download issues
- recommended directory layout without Chinese characters in paths
- recommendation to use Android Studio Embedded JDK 17

========================
11. FINAL QUALITY CHECK
========================

Before finalizing, self-check:
- package names are consistent
- manifest matches file structure
- widget XML names match receiver metadata
- resource IDs exist
- all referenced classes are included
- build files are complete
- Compose imports are correct
- WorkManager code is valid
- DataStore code is valid
- Retrofit serialization setup is valid

Now generate the complete project.
```

---

# Part 2: 更适合 Cursor 的补充用法

如果你用的是 Cursor，建议你不是一次全贴进去“让它在对话里吐全部文件”，而是这样做：

## 用法 A：先让它规划
先给它一句：

```text
Read the following project specification carefully. First, output only:
1. the final project tree
2. a file generation plan
3. a list of dependencies
Do not generate code yet.
```

然后再把上面的完整 Prompt 粘进去。

等它规划完，再继续：

```text
Now generate the project files in batches. Each batch must contain complete files only. Start with Gradle files, Manifest, and core data/network/system files.
```

再继续：

```text
Now continue with widget providers, widget renderers, layouts, and widget XML files.
```

最后：

```text
Now continue with Compose screens, run instructions, deployment instructions, and a final consistency check.
```

这样 Cursor 更稳，不容易丢文件。

---

# Part 3: 适合 Claude 的补强 Prompt

如果你是用 Claude，建议你在上面 Prompt 之后再补一句：

```text
Important:
Do not optimize for brevity.
Optimize for correctness, completeness, and first-pass compilability.
If you need to make reasonable implementation choices, prefer stable Android-native solutions over fancy abstractions.
```

如果你担心它偷懒，再加一句：

```text
Do not summarize. Output actual files.
```

---

# Part 4: 中国境内 Windows 开发环境搭建细节
下面这部分不是给 AI 的，是给你自己人工搭环境的。  
我按**中国大陆 + Windows + Android 开发**最稳路线写。

---

# 一、最推荐的开发环境组合

## 推荐组合
- Windows 10/11 64位
- Android Studio 最新稳定版
- Android Studio Embedded JDK 17
- Android SDK 放到英文路径
- 项目放到英文路径
- 优先真机调试
- Gradle 使用官方仓库声明，但在本地做网络优化

---

# 二、目录规划
建议固定目录，不要用中文路径。

```txt
D:\Android\
  ├─ AndroidStudio\
  ├─ Sdk\
  ├─ Projects\
  └─ Temp\
```

例如：
- SDK：`D:\Android\Sdk`
- 项目：`D:\Android\Projects\PixelNothingWidgets`

不要放这里：
- 桌面
- 下载
- 中文目录
- OneDrive 同步目录

---

# 三、安装 Android Studio

## 1）下载建议
中国大陆常见问题是官网下载慢。建议：

### 方案A：官网能下就官网
最优先。

### 方案B：用稳定网络环境下载
如果你有自己的合规网络环境，这最省时间。

### 方案C：可信镜像源
如果必须镜像，注意来源可信。

---

## 2）安装时建议
- 安装路径：`D:\Android\AndroidStudio`
- 首次启动：**不要导入旧配置**
- 选择 Custom 安装
- SDK 路径指定到：`D:\Android\Sdk`

---

# 四、必须安装的 SDK 组件

打开：
`File > Settings > Android SDK`

## SDK Platforms
安装：
- Android 14 (API 34)

## SDK Tools
安装：
- Android SDK Build-Tools 34.x
- Android SDK Platform-Tools
- Android SDK Command-line Tools (latest)
- Android Emulator（可选）
- Google USB Driver（可选，真机有时用得上）

---

# 五、JDK 设置
不要手动装很多 JDK。  
**直接用 Android Studio 自带 JDK。**

检查位置：
`File > Settings > Build, Execution, Deployment > Build Tools > Gradle`

设置：
- **Gradle JDK = Embedded JDK 17**

不要乱配：
- 多个 `JAVA_HOME`
- 多个系统 JDK
- 老版 JDK 8/11 混用

---

# 六、环境变量建议

## 对新手最稳方案
其实可以不配太多环境变量。

只在需要命令行 adb 时再配：

### 推荐配
`ANDROID_SDK_ROOT`
```txt
D:\Android\Sdk
```

Path 增加：
```txt
D:\Android\Sdk\platform-tools
D:\Android\Sdk\cmdline-tools\latest\bin
```

## 不建议一开始配
- `GRADLE_HOME`
- 各种第三方 JDK 到 Path 前面

---

# 七、中国大陆网络问题：Gradle 依赖下载
你这个项目的依赖主要来自：
- `google()`
- `mavenCentral()`

这是标准写法，**项目文件里不要乱改成奇怪仓库**。  
但你可以做本地层面的优化。

---

## 1）全局 `gradle.properties`
位置：
```txt
C:\Users\你的用户名\.gradle\gradle.properties
```

建议内容：

```properties
org.gradle.jvmargs=-Xmx4096m -Dfile.encoding=UTF-8
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.configureondemand=true

systemProp.http.connectionTimeout=60000
systemProp.http.socketTimeout=60000
systemProp.https.connectionTimeout=60000
systemProp.https.socketTimeout=60000
```

如果内存小，就把 `-Xmx4096m` 改成 `2048m`。

---

## 2）首次同步建议
第一次 `Sync` 时：

- 不要同时开太多程序
- 先耐心等
- 不要一报错就乱删文件
- 如果是超时，先重试一次

---

## 3）如果 Gradle 分发包下载失败
典型文件：
```txt
gradle/wrapper/gradle-wrapper.properties
```

里面类似：
```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-8.7-bin.zip
```

如果一直下载失败，你可以：

### 方案A：浏览器手动下载对应 gradle 包
下载：
- `gradle-8.7-bin.zip`

然后放进 Gradle 缓存对应位置。  
这个方式偏手工，但在国内有时很好用。

### 方案B：使用稳定网络环境先让它下载成功一次
只要成功一次，后续就轻松很多。

---

# 八、Android Studio 常见报错与中国环境下处理

---

## 1）报错：Gradle Sync 卡住 / timeout
### 排查：
- 网络问题
- Gradle 分发包没下完
- Maven Central/Google Maven 拉不到

### 处理：
- 等 3~5 分钟
- Retry
- 检查 `Embedded JDK 17`
- 检查 `.gradle\gradle.properties` 超时设置
- 必要时清除损坏缓存后重试

---

## 2）报错：SDK location not found
### 处理：
确认 SDK 路径正确：
- Android Studio 设置中的 SDK 路径
- `local.properties` 中的 `sdk.dir`

例如：
```properties
sdk.dir=D\:\\Android\\Sdk
```

---

## 3）报错：Unsupported class file major version
### 原因
JDK 版本错了。

### 处理
统一使用：
- Android Studio Embedded JDK 17

---

## 4）报错：Could not resolve dependencies
### 原因
多半是网络。

### 处理
- 检查网络
- Retry
- 不要混入奇怪仓库
- 使用标准 `google()` + `mavenCentral()`
- 必要时先在更稳定网络环境下载首轮依赖

---

## 5）报错：模拟器打不开
### 建议
你这种项目优先：
- **真机调试**

模拟器在 Windows 中国环境下，经常折腾：
- 虚拟化
- Hyper-V
- 显卡
- 内存

而桌面小组件这种功能，真机调试体验反而更真实。

---

# 九、真机调试详细步骤

## 1）手机打开开发者选项
- 设置
- 关于手机
- 连续点击版本号 7 次

## 2）打开 USB 调试
- 开发者选项
- USB 调试

## 3）数据线必须是可传数据的
很多线只能充电。

## 4）电脑识别设备
终端输入：
```bash
adb devices
```

看到：
```txt
xxxx device
```
即可。

如果是：
```txt
unauthorized
```
看手机弹窗，点允许。

---

# 十、项目导入与运行顺序

最稳顺序如下：

## 第1步
先把 Android Studio 和 SDK 装好。

## 第2步
打开 AI 生成的项目。

## 第3步
等 Android Studio 建索引。

## 第4步
确认：
- SDK 路径正确
- Gradle JDK = Embedded JDK 17

## 第5步
点击 `Sync Project with Gradle Files`

## 第6步
如果同步成功，先 `Build > Make Project`

## 第7步
连接真机，Run

## 第8步
打开 App 设置页，填写：
- 彩云 Token
- 经度
- 纬度
- 城市名

## 第9步
回桌面添加 Widget

---

# 十一、我建议你让 AI 额外输出的内容
你喂给 Cursor / Claude 时，可以再加一小段要求，让它更贴合你中国环境：

```text
Also include a short practical setup note for Windows users in mainland China:
- recommend Android Studio stable version
- recommend using Embedded JDK 17
- list required SDK components
- recommend avoiding Chinese characters in paths
- recommend real device debugging first instead of emulator
- mention common Gradle sync/network issues and basic remedies
```

---

# 十二、给你的“最终实战建议”

你现在最适合的流程是：

## 流程A：先生成
把我上面的英文 Prompt 喂给 Cursor / Claude。

## 流程B：生成策略
不要让它一次吐几万行，改成：
1. 先目录树和计划
2. 再 Gradle + Manifest + data/network
3. 再 widget + xml
4. 再 Compose 页面
5. 再运行说明

## 流程C：本地编译
在 Android Studio 里导入，开始同步。

## 流程D：出错就修
把**完整编译报错**发给我，我可以继续帮你做这三种事：

1. **帮你写“二次修复 Prompt”给 Cursor/Claude**
2. **直接人工帮你定位哪几个文件错了**
3. **给你一套中国环境下专门的 Gradle/SDK 排障步骤**

---

如果你愿意，我下一条可以直接继续给你两份非常实用的东西：

### 1）**“适合 Cursor 的分步生成 Prompt”**
我帮你拆成：
- 第一步 Prompt
- 第二步 Prompt
- 第三步 Prompt
让 AI 分阶段生成，成功率更高

### 2）**“中国大陆 Windows 安卓开发排障手册”**
专门写：
- Gradle 下载失败
- JDK 混乱
- SDK 路径错误
- adb 设备识别失败
- 真机调试失败
- 模拟器虚拟化失败

如果你要，我建议下一条我直接给你：

**“Cursor 分步生成 Prompt + 中国大陆 Android 开发排障手册”**
