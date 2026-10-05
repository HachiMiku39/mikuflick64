# Miku Flick 64 · 64 位重构测试版

在现代 iPhone 和 iPad 上重现 Miku Flick /02 的假名滑动音游与 MV 播放。提供原版风格的 Legacy 界面、Modern 界面、多语言字幕、MV 进度拖动，以及游戏内 ZIP / RAR 资源包安装。

当前发布为 **v0.2.0 r2 测试版**，供玩家签名安装、测试设备适配并反馈问题。**本轮尚未进行真机安装及运行验证。**

- [下载 r2 测试版](https://github.com/HachiMiku39/mikuflick64/releases/tag/mikuflick64-v0.2.0-r2)
- [资源包下载](https://github.com/HachiMiku39/mikuflick02_soundpack/releases/tag/pack)
- [阅读保留的原始开发与研究文档](#original-research)

## 下载与运行

| 文件 | 用途 |
| --- | --- |
| [MikuFlick64-0.2.0-build3-unsigned.ipa](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v0.2.0-r2/MikuFlick64-0.2.0-build3-unsigned.ipa) | arm64 真机应用；未签名，需自行签名后安装。 |
| [MikuFlick64-Source.zip](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v0.2.0-r2/MikuFlick64-Source.zip) | 完整、可编辑的 Xcode 源码工程，可修改或用自己的开发团队运行到真机。 |
| [MikuFlick64-iOS-Simulator-arm64.zip](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v0.2.0-r2/MikuFlick64-iOS-Simulator-arm64.zip) | Apple Silicon Mac 的 iOS 模拟器应用；请在模拟器中运行。 |
| [SHA256SUMS.txt](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v0.2.0-r2/SHA256SUMS.txt) | 下载文件的 SHA-256 校验清单。 |

**系统要求：**iOS / iPadOS **17.0 或更高版本**、**arm64** 设备。iPhone Duo 的专用折叠形态 API 适配需要 **iOS 27.1 或更高版本**；折叠切换尚待真机测试。

**真机安装：**下载 IPA，导入你使用的签名工具，用自己的开发者账号或证书签名，再安装到设备。发布包未包含开发团队签名，用户签名后的安装与运行仍待验证。

**从源码运行：**解压 `MikuFlick64-Source.zip`，用 Xcode 打开 `MikuFlick64.xcodeproj`，选择 `MikuFlick64` Scheme 和目标设备。本轮使用 Xcode 27.2 beta 2（27B5028f）构建；真机运行时在 Signing 中选择自己的开发团队，模拟器无需开发者账号。

## 已提供的功能

- **界面：**Legacy Cover Flow、SEKAI-A 封面网格、SEKAI-B 列表；现代横向游戏将 MV 和输入区分开，键盘在输入区域内居中。选歌显示最高分及各难度最高评级，设置提供音量、握持和原版键盘预览。
- **六种界面语言：**日语、英语、简体中文、法语、西班牙语、韩语，跟随系统应用语言设置。中文采用 PingFang SC；英语、数字、法语和西班牙语采用 Futura，韩文采用系统字体。顶部 `MUSIC SELECT` / `OPTIONS` 在各语言中保持英文大写 OCR A 字体。
- **音游：**EASY、NORMAL、HARD、EXTREME、BREAK THE LIMIT；三套原版按键图集、引导与字符花瓣，暂停、重开、按歌曲保存的 INPUT TIMING。判定采用研究确认的原版 30 Hz 音频时钟模型。
- **MV：**独立播放、进度拖动、播放列表顺序连播、循环、歌词开关与卡拉 OK 伴奏。日语显示原文，其它支持语言显示日语原文与对应译文；缺少译文时省略第二行。
- **歌词：**17 首歌曲配套英语、简体中文、法语、西班牙语、韩语五种译文，各语言 479 条歌词 cue。英语和中文为项目自译，法语、西班牙语、韩语为项目 AI 翻译；以游戏实际歌词段落为准。
- **资源包：**主菜单可直接选择 ZIP / RAR 安装，应用内解压、校验、转换影片和音轨并注册歌曲。界面帮助以本地导入教程替代旧商店内容，统一使用 MV 名称。

工程内置 **11 首基础歌曲**；另外六首歌曲的字幕已准备好，影片需导入 **Mov_11 / Mov_99** 资源包后播放。

## 导入资源包

1. 从[资源包发布页](https://github.com/HachiMiku39/mikuflick02_soundpack/releases/tag/pack)下载 ZIP / RAR，保存到系统“文件”应用。
2. 回到游戏主菜单，打开“资源包导入（RESOURCE PACK INPUT）”，选择“导入 ZIP / RAR”；也可从“文件”应用分享给 Miku Flick 64。
3. 等待解压、校验、媒体转换和注册完成，再进入音游或 MV 选歌。

压缩包保留原文件名，并包含 `Mov_<编号>` 文件夹；允许外层 `InstallData`，`Thum_<编号>` 缩略图目录可选。当前支持本游戏的 MPEG-1 / ADX USM，以及完整、无密码、非分卷 ZIP / RAR；安装时需为解压原件和转换缓存预留空间。曲包与谱面研究另见[资源包仓库](https://github.com/HachiMiku39/mikuflick02_soundpack)和[自制谱面研究](https://github.com/HachiMiku39/mikuflick_self-made_pattern)。

## 测试范围与反馈

r2 是用于设备适配和玩法反馈的测试交付。iPhone 竖屏、iPad 横竖屏、iPhone Duo 外屏与内屏横竖屏及类 3DS 形态仍由用户人工验证；真机性能、音画同步、触控时延、输入校准、音频中断以及全部歌曲的实际通关测试均待完成。原版完整 Rainbow / Crimax 视觉、解锁逻辑、重播、Twitter 和 Game Center 服务尚未完整复刻。

反馈时请注明设备、系统版本、Legacy / Modern 界面、语言、横竖屏或折叠形态、歌曲与难度，并附截图或复现步骤。源码包包含验证记录、测试说明和人工测试交接资料，可用于继续修正。

原游戏歌曲、MV、图像与音效的权利归原权利人；本项目重新实现运行时，未包含 SEGA 源码。汉化与逆向研究保留在下方及[相关档案仓库](https://github.com/HachiMiku39/mikuflick_chinese_localization)。

---

<a id="original-research"></a>

## 原始开发与研究文档（保留全文）

以下原文记录项目此前的设计建议、汉化与机制研究背景、架构规划和平台目标，完整保留。原文中的 iOS 26+ 开发基线、规划状态和后续功能表述作为历史参考；**当前 r2 的安装要求、已有功能和测试范围以本页顶部发布介绍为准**。

# MikuFlick64

> **MikuFlick /02 的现代 64 位重构项目改进建议与架构说明**  
> 本仓库已经有正在 Xcode 中实现的项目。本文不是“从零开始重做”的施工清单，而是为现有 Codex / Xcode 工程提供持续改进建议、兼容性约束和架构参考。基线面向 iOS / iPadOS 26+，并将 **iPhone Duo** 作为正式支持目标。

![Status](https://img.shields.io/badge/status-design%20%2F%20research-4c8bf5)
![Platform](https://img.shields.io/badge/platform-iOS%20%7C%20iPadOS-black?logo=apple)
![Target](https://img.shields.io/badge/deployment-26%2B-0a84ff)
![Architecture](https://img.shields.io/badge/architecture-64--bit-34c759)

## 项目定位

`MikuFlick64` 不是把旧 IPA 机械地“升位数”，也不是把 320×480 时代的界面整张放大。

它的目标是：

- 用现代 64 位工程重新实现 **MikuFlick /02 的核心 Gameplay**
- 原版判定、Combo、Score、Microphone Gauge、Crimax、Interlude 尽量保持兼容（详见Ghidra逆向结果）
- 使用现代 iPhone / iPad 的布局体系、Safe Area 和可变窗口能力
- 在支持 ProMotion 的设备上获得流畅的 60/80/120 Hz 视觉表现
- **判定逻辑仍保持原版约 30 Hz 的音频时钟模型**，渲染刷新率不得改变手感
- 原版低分辨率 Texture Atlas 可作为兼容资源和视觉参考，但不再绑定旧坐标系
- 原版ipa内歌曲、MV、音效与图像资源默认随开源代码分发，保留原游戏感
- 包名暂定为 com.sbga.mikuflick64

一句话概括：

```text
Original Gameplay Contract
+
Modern Apple Runtime
+
Adaptive UI
=
MikuFlick64
```

---

## 当前状态

目前仓库已经进入 Xcode / Codex 实际开发阶段；README 主要用于记录 **改进建议、逆向结果整合、兼容性约束和后续架构方向**。

> **对 Codex 的使用原则**
>
> - 优先阅读并理解现有 Xcode 工程，而不是按 README 重新生成一套平行工程。
> - 任何建议都应先映射到当前代码结构，再决定是重构、替换还是保留。
> - 已经工作的模块不应仅为了“更现代”而无意义重写。
> - 原版机制兼容性高于代码风格统一。
> - UI、媒体、资源和设备适配可以渐进式改进，不要求一次性推倒重来。
> - 原版内容在原有的ipa里，可以解压获得。

| 模块 | 状态 |
|---|---|
| MikuFlick2 1.1.5 核心机制逆向 | 基本完成 |
| 原版 Score Engine | 基本完成，可作为兼容基线 |
| 30 Hz Audio Clock 判定模型 | 已确认 |
| Microphone Gauge | 已确认主要规则 |
| Crimax / Rainbow | 已确认主要规则 |
| Interlude | 已确认主要规则 |
| Rank / Result | 已确认 |
| Legacy Texture Atlas / plist 结构 | 已确认主要加载方式 |
| 原版 BGM / SE 用途 | 部分确认 |
| 现代 64 位运行时 | 规划中 |
| 现代 UI / iPad 适配 | 规划中 |
| iPhone Duo Outer / Inner Display 适配 | 规划中 |
| iPhone Duo Pose / Resize 连续性 | 规划中 |
| ProMotion Presentation | 规划中 |
| 原版资源本地导入器 | 规划中 |

详细逆向与谱面研究不再全部复制到本 README，避免开发文档变成考古卷轴。

相关仓库：

- [`mikuflick_chinese_localization`](https://github.com/HachiMiku39/mikuflick_chinese_localization) - 汉化、旧设备安装、机制逆向总档案
- [`mikuflick_self-made_pattern`](https://github.com/HachiMiku39/mikuflick_self-made_pattern) - USM / CUE / 自制谱面与曲包研究

---

# 1. 开发原则

## 1.1 原版机制是兼容契约

现代化首先改变的是：

```text
Runtime
Rendering
Layout
Asset Pipeline
Media Pipeline
```

而不是：

```text
Timing
Judgement
Score
Combo
Gauge
Crimax
Interlude
```

只要实机分数对照没有发现偏差，`OriginalGameplay` / `OriginalScoreEngine` 应逐步冻结。

未来如果需要实验新的计分、辅助判定或 Modern Mode，应作为独立规则集存在，不能悄悄污染原版模式。

## 1.2 游戏状态与界面彻底解耦

推荐架构：

```text
                    ┌──────────────────┐
                    │   Media / Audio  │
                    │   Master Clock   │
                    └────────┬─────────┘
                             │
                             ▼
┌──────────────┐    ┌──────────────────┐
│ Chart Engine │ -> │ Judgment Engine  │
└──────────────┘    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   Game State     │
                    │ Score/Combo/etc. │
                    └────────┬─────────┘
                             │
              ┌──────────────┴──────────────┐
              ▼                             ▼
     ┌─────────────────┐          ┌─────────────────┐
     │ Classic Phone UI│          │ Wide / iPad UI  │
     └─────────────────┘          └─────────────────┘
```

`GameplayCore` 不应该知道 MV 在屏幕左边还是上面，也不应该知道设备是不是 iPad。

## 1.3 不用设备型号决定布局

不要写：

```swift
if modelIdentifier == "iPhoneXX,Y" {
    ...
}
```

当前 Apple 平台应优先使用：

- 当前 `UIWindowScene` 的几何尺寸
- `traitCollection`
- Safe Area
- 当前可用窗口空间
- App 自己定义的 capability gate

普通 iPhone 即使很宽，也不因为单个宽度阈值自动进入 iPad 风格 UI。

---

# 2. Apple 平台适配策略

Apple 当前设计指导强调：界面应适应不同显示尺寸、方向和多任务窗口，而不是只针对一个固定分辨率。

## 2.1 Standard iPhone

默认使用：

```text
Classic Phone Layout
```

目标：

- 延续原版纵向信息结构
- 适配现代长屏比例
- HUD / 输入区避开 Dynamic Island 与底部系统手势区域
- 背景可以 full-bleed，但关键操作必须服从 Safe Area
- Pro Max 不因为尺寸更大而自动切成双栏布局

## 2.2 iPad Portrait

iPad 竖屏建议继续使用 Classic Gameplay Composition，但由 App 原生布局实现：

```text
┌────────────────────────────┐
│                            │
│       Classic Canvas       │
│        centered            │
│                            │
└────────────────────────────┘
```

注意：

- 不是旧时代的 iPhone Compatibility Mode
- App 本身是原生 iPad App
- 背景可延伸至整个窗口
- 主 Gameplay Canvas 保持合理的物理尺寸
- 不要把九宫格输入区按 iPad 宽度无限放大

## 2.3 iPad Wide / Resizable Window

现代 iPadOS 窗口可以自由改变尺寸，因此开发时不能只假设“全屏横屏 iPad”。

推荐根据实际窗口空间切换：

```text
Compact
Classic
Wide
```

而不是只依赖 portrait / landscape。

Wide 模式示意：

```text
┌──────────────────────┬──────────────────────┐
│                      │ SCORE / COMBO        │
│         MV           │                      │
│                      │ Lyrics / Note info   │
│                      │                      │
│                      │ Flick Keyboard       │
└──────────────────────┴──────────────────────┘
```

Apple 的 iPadOS 指导特别强调对可变窗口尺寸进行平滑适配，所以 resize 时应只重排 Presentation，不重建歌曲状态。

MV Wide模式示意：
```text
┌──────────────────────┬──────────────────────┐
│                      │                      │
│         MV           │                      │
│                      │ Lyrics      Rolling  │
│                      │                      │
│                      │ Like Apple Music     │
└──────────────────────┴──────────────────────┘
```
## 2.4 iPhone Duo

**iPhone Duo 已经是 Apple 正式公布并提供开发文档的产品目标，不再作为未来硬件占位符处理。**

Apple 官方资料确认 iPhone Duo 具有：

- Outer Display
- Inner Display
- 中央 hinge
- partially folded / book / tent 等多种姿态
- Inner Display 上更宽的 regular-width 空间
- Split View multitasking
- 多显示区域和 scene 相关能力

MikuFlick64 应把 iPhone Duo 当作与 iPhone / iPad 并列的正式测试目标。

Apple 现在对 iPhone Duo 的说法比较明确，核心词是 pose（姿态），不是给每个铰链角度硬塞一个“模式”。

官网产品页直接列出的官方名称有这 5 个：

Apple官方英文 ｜	可以怎么理解｜	典型状态
Closed	｜闭合态	｜完全折叠，只用 5.4 英寸外屏
Landscape	｜横向展开态	｜内屏完全展开，宽屏使用
Portrait	｜纵向展开态｜	内屏完全展开后旋转 90°
Seated	｜坐姿 / 桌面态	｜半折，像小笔记本放桌上，内屏朝向用户
Standing	｜站立态	｜半折后靠两侧边缘自行站立，常用外屏看内容


其中开发文档还用了两个很重要、但更偏“状态描述”的术语：

- Fully open：完全展开。
- Partially folded：部分折叠。Apple 经常进一步描述成 “partially folded, like a book”，也就是像书一样半开。HIG 甚至专门比较了 Fully open 和 Partially folded 的布局变化。Apple Developer

### Outer Display

Apple 指导中，Outer Display 的 size class 行为接近传统 iPhone：

```text
Portrait:
horizontal = compact
vertical   = regular

Landscape:
horizontal = compact
vertical   = compact
```

MikuFlick64 默认使用：

```text
Classic Phone Layout
```

但需要额外注意：

- 侧边系统 controls
- 非对称 Safe Area
- 外屏前置相机区域
- Split View / PiP 导致的实时 resize

### Inner Display

Inner Display 提供：

```text
horizontal = regular
vertical   = regular
```

因此是 MikuFlick64 的主要大屏布局目标：

```text
MV | Lyrics / Gameplay / HUD
```

Apple 明确建议不要只把外屏 UI 横向吹大，而是利用 regular-width 空间显示更多内容，例如 split view / two-column layout。


### 不按“折叠状态枚举”硬切 UI

虽然 iPhone Duo 有多种物理姿态，Apple 当前官方指导的核心不是：

```swift
if foldState == .book { ... }
if foldState == .tent { ... }
```

而是优先根据：

```text
Size Classes
+ Scene Geometry
+ Safe Area
+ Reserved Regions
+ Available Window Size
```

让同一界面连续适配。

因此 MikuFlick64 的布局入口建议保持：

```swift
struct DisplayEnvironment {
    let horizontalSizeClass: UIUserInterfaceSizeClass
    let verticalSizeClass: UIUserInterfaceSizeClass
    let sceneBounds: CGRect
    let safeAreaInsets: UIEdgeInsets
}
```

并由 `LayoutResolver` 决定 Classic / Wide Presentation。

### Hinge / Reserved Regions

iPhone Duo 的 hinge 和摄像头会形成需要避让的区域。

Gameplay 原则：

- 不让关键 Flick 控件跨 hinge
- 不让暂停键、判定文字等交互元素压在 reserved region 上
- 连续滚动背景 / MV 可以跨区域显示，但关键交互应服从 Safe Area
- partially folded 时只改变 Presentation，不重建 Gameplay State

### Seated / Desk Mode

iPhone Duo 在坐姿 / 桌面态（Seated）下可以采用更明确的上下屏分工。交互思路可以参考 Nintendo 3DS 这类上下屏设备，但不复制其具体 UI。

核心原则：

- Upper Display 主要承载观看信息
- Lower Display 主要承载触控交互
- hinge 视为明确的布局分界
- 关键触控目标不跨 hinge
- 状态变化只重排 Presentation，不改变 Gameplay State

#### Gameplay

推荐布局：

```text
┌──────────────────────┐  upper
│ MV                   │
│ SCORE / COMBO        │
│ Lyrics / Note info   │
├──────────────────────┤  hinge
│                      │
│    Flick Keyboard    │
└──────────────────────┘  lower
```

职责划分：

**Upper Display**

- MV
- Score
- Combo
- Lyrics / Note information
- 非必要触控 HUD

**Lower Display**

- 9-key Flick Keyboard
- 与 Flick 直接相关的触控反馈
- 必要时的 Pause / gameplay controls

这样可以把观看和操作自然分离，减少手指遮挡 MV / Lyrics，也避免为了适配大屏而把 Flick Keyboard 无限放大。

#### MV Mode

推荐布局：

```text
┌──────────────────────┐  upper
│ MV                   │
│                      │
│                      │
├──────────────────────┤  hinge
│                      │
│       Lyrics         │
└──────────────────────┘  lower
```

在纯 MV 播放时：

- Upper Display 专注视频
- Lower Display 专注同步歌词
- 当前歌词可以高亮，上下句弱化
- 不需要把歌词继续叠在 MV 上
- 如果用户将设备恢复到 flat / fully-open 状态，应平滑返回 Wide MV + Lyrics 布局

#### Interaction Model

Seated 模式应被视为同一个 Gameplay / MV Scene 的另一种 Presentation，而不是启动另一套页面。

```text
Same GameState / MediaState
        +
Seated Geometry
        ↓
Upper / Lower Presentation
```

切入或退出 Seated 时必须保持：

- audio / video current time
- chart position
- current Note
- Combo
- Gauge
- Score
- Crimax / Interlude state
- lyric timeline
- pause state

不允许：

- 重新开歌
- 重新加载谱面
- 重置 Flick 状态
- 因为上下屏切换重新计算歌曲时间

#### Implementation Note

不要把 Seated 写成“设备型号 + 固定角度”的硬编码模式。

建议由平台层提供：

```swift
enum DuoPresentationMode {
    case outerClassic
    case innerWide
    case seated
}
```

具体进入 `.seated` 的依据应来自 Apple 提供的 Duo pose / scene geometry / reserved-region 信息，再由 `LayoutResolver` 选择上下屏布局。

### Inner Display 的 Orientation 注意事项

Apple 明确指出：**Inner Display 不应依赖 supported interface orientations 来决定布局。**

所以不要写：

```swift
if orientation == .landscape {
    useWideLayout()
}
```

应优先写成：

```text
regular width + available geometry
→ Wide Layout
```

### SDK

Apple 已提供 iPhone Duo 专用开发资源，并要求使用最新 SDK 进行构建和测试。

当前开发基线：

```text
Xcode 27.1 beta or newer
iOS 27 SDK for iPhone Duo testing
```

项目仍可保留 iOS / iPadOS 26+ deployment target，但 iPhone Duo 特定适配代码应使用可用性检查与当前 SDK 构建。

---

# 3. Safe Area 与现代屏幕

Apple 的 `safeAreaLayoutGuide` 表示未被系统栏、硬件区域和其他覆盖内容遮挡的可用区域。

开发规则：

- 背景、MV 或装饰层可以延伸到窗口边缘
- 关键 Gameplay HUD、暂停按钮、输入区不能盲目贴边
- 不要为 Dynamic Island 写死像素常量
- 窗口变化后重新读取 Safe Area
- 横屏、iPad 小窗口、iPhone Duo Outer / Inner Display 都必须经过同一套布局解析器

推荐：

```text
Window Bounds
+
Safe Area Insets
+
Trait Environment
↓
Layout Resolver
↓
Presentation
```

---

# 4. ProMotion 与帧率

这是现代化版本最容易“看起来升级了，却把手感改坏”的地方。

## 4.1 原版 Gameplay Clock

Ghidra 已确认原版核心计时近似：

```text
playTick = ceil(audioPlayTimeSeconds × 30)
noteTick = playTick - note.baseTime
```

因此：

```text
1 gameplay tick ≈ 33.33 ms
```

原版判定不是：

```text
renderFrame++
```

现代版必须继续保持这一点。

## 4.2 Presentation 可以 60 / 80 / 120 Hz

推荐分离：

```text
Audio Clock     -> authoritative gameplay time
30 Hz tick      -> original judgement model
Display Link    -> visual interpolation only
```

支持 ProMotion 时，渲染层可以请求更高刷新率，让：

- Note 移动更顺滑
- UI 动画更顺滑
- 歌词滚动更顺滑
- MV 周边 HUD 更自然

但绝不能让 120 Hz 设备产生 4 倍 Gameplay Tick。

## 4.3 Apple ProMotion API 注意事项

如果使用自定义 `CADisplayLink` 更新渲染：

- 使用 `preferredFrameRateRange` 表达允许和期望的刷新率
- 实际刷新率由系统决定，不能假设永远固定 120 Hz
- Low Power Mode、热状态和辅助功能设置都可能改变刷新率
- 应只请求 App 能稳定维持的范围

如果需要在 iPhone 上使用高于 60 Hz 的 Core Animation / display-link 更新，还应按 Apple 文档配置：

```xml
<key>CADisableMinimumFrameDurationOnPhone</key>
<true/>
```

UIKit、SwiftUI 和 Core Animation 管理的标准动画很多情况下已经会自动适应 ProMotion，不需要为每个动画自己造一套帧循环。

---

# 5. Audio / MV / Lyrics

## 5.1 Audio Clock 是唯一真时钟

所有这些系统都应该读取同一个播放时间：

```text
Chart
Judgement
Lyrics
MV synchronization
Interlude
Crimax cue
```

禁止分别维护：

```text
audioTimer
videoTimer
noteTimer
lyricsTimer
```

然后靠每隔几秒硬校准。

## 5.2 AVFoundation 的定位

现代 Apple 平台可使用 AVFoundation 负责现代媒体播放与时间表达。

`AVPlayer` 提供 `currentTime()`、periodic time observer 和 boundary time observer，适合 UI / 歌词事件跟随。

但 Gameplay 判定层不应依赖低频 UI callback 数量计时，而应在需要判定时读取 master playback time 并转换成原版 30 Hz tick。

## 5.3 原版 USM

原版歌曲大量使用 CRIWARE USM / ADX。

公开项目推荐的现代流程是：

```text
User-owned Original IPA
↓
OriginalAssetImporter
↓
extract / decode supported resources locally
↓
Modern Asset Pack
↓
AVFoundation / modern renderer
```

这样可以把：

- 原版商业资源
- 开源重构代码

分离开。

## 5.4 Lyrics Presentation

Classic：

```text
MV + lyric overlay
```

Wide：

```text
MV | independent lyrics panel
```

Wide 歌词面板建议显示：

- 上一句
- 当前句高亮
- 下一句
- 自动跟随播放

歌词跳转、翻译、罗马音属于后续扩展，不进入首版 P0。

---

# 6. Flick 输入系统

九宫格 Flick 区应该被视为：

> **software keyboard, not scalable content**

允许：

- 整体移动
- 等比例缩放
- 左 / 中 / 右预设
- 左右手布局偏置
- iPad / Wide 模式下保存独立布局

禁止：

- 横向拉伸九宫格
- 单独缩放某个键
- 因为窗口更宽就无限放大按键
- 改变原版方向判定定义

推荐内部使用稳定的逻辑坐标：

```text
Logical Keyboard Space
↓ scale + translation
Screen Space
```

这样 Layout 变化不会改变 Flick 几何规则。

---

# 7. 原版 Gameplay Compatibility Contract

下面只保留现代实现必须遵守的核心规则。完整逆向过程请看相关研究仓库。

## 7.1 Judgement

```text
5 COOL
4 FINE
3 SAFE
2 SAD
1 WORST
0 NONE / INVALID
```

Early：

```text
COOL 0-2 tick
FINE 3-6
SAFE 7-8
SAD  9-10
```

Late：

```text
COOL 0-3 tick
FINE 4-7
SAFE 8-9
SAD  10
```

普通 Flick Note 同时考虑 TouchDown / TouchUp，并包含原版的 early-hold 宽容逻辑。

## 7.2 Base Stage Score

| Result | Score |
|---|---:|
| COOL | 300 |
| FINE | 150 |
| SAFE | 50 |
| SAD | 30 |
| WORST | 0 |

## 7.3 Combo

普通模式：

```text
COOL / FINE -> Combo +1
SAFE / SAD / WORST -> Reset Combo
```

Combo Bonus：

```text
min(500, floor((Combo + 5) / 10) × 50)
```

## 7.4 Crimax

```text
Crimax Note
+ COOL
+ Combo >= 100
= +200 Stage Score
```

同时进入原版 28 色 Rainbow Note 表现。

## 7.5 Interlude

```text
FINE / COOL -> success
each success -> +1 Stage Score
all success -> additional +39
```

## 7.6 Microphone Gauge

```text
Start = 128
Max   = 256
```

权重：

```text
COOL  +2
FINE  +2
SAFE   0
SAD   -5
WORST -10
```

实际变化按 Total Notes 归一化。BTL 不走普通 Gauge 更新路径。

## 7.7 Total Score

```text
TotalScore = StageScore + ComboScore
```

## 7.8 Rank

```text
Perfect! -> all COOL
S        -> COOL + FINE = 100%, but not all COOL
A        -> COOL + FINE >= 95%
B        -> COOL + FINE >= 80%
C        -> COOL + FINE + SAFE >= 70%
D        -> below 70%, no Game Over
E        -> Game Over
```

## 7.9 Break The Limit

BTL 有独立规则，不允许简单套用普通难度：

- 不走普通 Gauge
- SAFE 不增加 Combo，但也不清 Combo
- SAD 在已确认流程中会转为内部 `0`
- timeout / miss 不执行普通模式的 Combo reset

首版实现应先复刻当前已确认逻辑，再通过原版实机逐曲回归。

---

# 8. UI 架构

推荐模块：

```text
MikuFlickCore
├── AudioClock
├── ChartEngine
├── JudgmentEngine
├── OriginalScoreEngine
├── ComboEngine
├── GaugeEngine
├── CrimaxEngine
├── InterludeEngine
├── LyricsTimeline
└── GameState

MikuFlickMedia
├── SongAssetProvider
├── AudioPlayer
├── VideoPlayer
├── PreviewPlayer
└── AssetImporter

MikuFlickUI
├── ClassicPhoneLayout
├── TabletPortraitLayout
├── WideGameplayLayout
├── MVContainer
├── LyricsPanel
├── GameplayHUD
├── FlickKeyboard
└── InputLayoutEditor

Platform
├── SceneMetricsProvider
├── SafeAreaProvider
├── DisplayRefreshProvider
├── LayoutResolver
├── DuoDisplayEnvironment
└── ReservedRegionAdapter
```

---

# 9. Resize / Rotate / Layout Transition

Apple 平台上的窗口变化只能改变 Presentation。

必须保持：

```text
master playback time
chart position
current note index
Combo
Max Combo
Gauge
Stage Score
Combo Score
Crimax state
Interlude state
lyrics index
pause state
```

禁止：

- resize 时重新开歌
- 旋转时重新加载 USM / media
- 重置 Combo / Gauge / Score
- 根据刷新率重建谱面时间

推荐：

```text
Persistent GameState
       +
New Window Metrics
       ↓
Re-resolve Layout
       ↓
Animate Presentation Change
```

---

# 10. Legacy Assets

原版大量 UI 使用：

```text
PNG + plist Texture Atlas
```

Ghidra 已确认旧 `TPManager` 会读取 plist 的 `frames`，排序后建立全局 Texture ID。

现代版本不要继续把业务逻辑绑死在全局数字 ID 上。

推荐：

```text
Legacy Texture ID
↓
Frame Name
↓
Atlas + Rect
↓
Modern Asset ID
```

资源分层建议：

```text
assets/
├── original/      # immutable source
├── extracted/
├── restored/      # upscale / cleanup
├── modern/
└── manifests/
```

## 10.1 AI Upscale

适合：

- 背景
- 插画
- 角色素材
- 部分特效

更适合重绘 / 矢量化：

- 字体
- 数字
- 几何按钮
- 线框
- Rank 字母

原则：

> AI upscale 是 Asset Restoration，不是重新设计。

不能让模型改变 Note 形状、UI 比例或关键判读信息。

---

# 11. App Icon 与 Launch Screen

## App Icon

原版存在高分辨率 Miku 图标，可作为现代图标的原始视觉来源。

面向 iOS / iPadOS 26+，可以使用 Apple 的 **Icon Composer** 建立一个多层图标源，并让 Xcode 为不同外观和平台生成需要的表示。

不要继续维护一长串旧时代 `Icon-57.png` / `Icon@2x.png` 作为主方案。

## Launch Screen

旧：

```text
Default.png
LaunchImage-*
```

现代：

```text
UILaunchScreen
or
LaunchScreen.storyboard
```

Launch Screen 只负责静态启动外观，不应包含运行时逻辑。

---

# 12. 推荐技术方向

这是方向，不是强制锁死的依赖选择。

| 层 | 推荐 |
|---|---|
| Gameplay Core | Swift 或 C++ / Swift 混合，纯状态逻辑 |
| UI Shell | SwiftUI / UIKit 均可，优先易于自适应布局 |
| Gameplay Presentation | Core Animation / Metal / 自定义 View，按实际性能决定 |
| Media | AVFoundation 为现代播放与时间基础 |
| High-refresh presentation | CADisplayLink / framework-managed animation |
| Window environment | UIWindowScene + trait environment |
| Assets | Asset Catalog + 自定义 legacy manifest |
| Persistence | Codable / 明确版本化的数据模型 |

如果 Gameplay 采用 Metal，仍应保证：

```text
Metal frame loop != gameplay clock
```

---

# 13. 改进建议与后续优先级

## P0 - 先保证当前工程可玩与原版兼容

- [ ] 建立 iOS / iPadOS 26+ 64 位工程
- [ ] Scene-based 生命周期
- [ ] 现代 Launch Screen
- [ ] OriginalAssetImporter 最小版本
- [ ] 能导入并播放一首测试歌曲的音频 / MV
- [ ] Master Audio Clock
- [ ] 原版 30 Hz Gameplay Tick
- [ ] Chart Engine
- [ ] Flick 输入
- [ ] Judgement Engine
- [ ] Original Score Engine
- [ ] Combo / Gauge / Crimax / Interlude
- [ ] Classic iPhone Gameplay UI
- [ ] 一首歌从进入 Gameplay 到 Result 完整跑通

## P1 - 在现有工程上补强现代 Apple 设备支持

- [ ] Safe Area 全面接入
- [ ] iPad native target
- [ ] iPad portrait Classic Canvas
- [ ] iPad resizable window
- [ ] Wide Gameplay Layout
- [ ] iPhone Duo Outer Display Classic Layout
- [ ] iPhone Duo Inner Display Wide Layout
- [ ] iPhone Duo partially folded / resize 状态连续性
- [ ] hinge / reserved region 避让
- [ ] resize / rotate / open-close 不重置 GameState
- [ ] ProMotion presentation
- [ ] 60 Hz 与 120 Hz 真机手感一致性测试

## P2 - UI 与资源现代化

- [ ] Legacy Song Select
- [ ] Modern Song List
- [ ] Preview playback
- [ ] 独立 Lyrics Panel
- [ ] Input Layout Editor
- [ ] 左手 / 居中 / 右手预设
- [ ] Asset restoration pipeline
- [ ] Result UI 现代化

## P3 - 可选扩展

- [ ] iPhone Duo advanced multi-display / scene experiences
- [ ] Practice Mode
- [ ] lyric seek
- [ ] romanization / translation layers
- [ ] additional accessibility options
- [ ] custom chart import integration

---

# 14. 测试矩阵

## Timing

至少验证：

- 60 Hz 非 ProMotion iPhone
- 120 Hz ProMotion iPhone
- iPad Pro ProMotion
- Low Power Mode
- 暂停 / 恢复
- 前后台切换
- 热状态导致刷新率变化时，判定不漂移

核心断言：

> 同一段输入时间戳，在不同显示刷新率上必须得到相同判定。

## Layout

- 小尺寸 iPhone
- 大尺寸 iPhone
- iPad portrait
- iPad wide
- iPad 半屏 / 三分屏 / 小窗口
- 旋转
- resize 过程中暂停和继续

## Regression

对每个机制建立原版实机对照：

```text
same chart
+ same input timing
+ same difficulty
↓
same judgement
same combo
same stage score
same combo score
same gauge behavior
same rank
```

---

# 15. 非目标

当前改进阶段不优先追求：

- 逐像素复制所有 2012 UI
- 使用旧 iPhone Compatibility Mode
- 用屏幕刷新率替代原版 Gameplay Tick
- 重新发明一套默认计分系统
- 把普通 Pro Max 自动当作“大屏双栏设备”
- 把所有原版商业歌曲、MV、图像直接打包进公开仓库
- 为 iPhone Duo 写死设备型号、固定尺寸或猜测性的私有 API

---

# 16. Apple 官方开发文档基线

实现时优先以 Apple 当前官方文档为准，而不是照搬旧 iOS 时代样例代码。

- [Get ready for iPhone Duo](https://developer.apple.com/iphone-duo/)
- [Human Interface Guidelines: Designing for iPhone Duo](https://developer.apple.com/design/human-interface-guidelines/designing-for-iphone-duo)
- [Tech Talk: Prepare your app for iPhone Duo](https://developer.apple.com/videos/play/tech-talks/111461/)
- [Tech Talk: Design for iPhone Duo](https://developer.apple.com/videos/play/tech-talks/111466/)
- [Tech Talk: Strike a pose with adaptive layouts on iPhone Duo](https://developer.apple.com/videos/play/tech-talks/111463/)
- [Tech Talk: Leverage multiple displays and scenes on iPhone Duo](https://developer.apple.com/videos/play/tech-talks/111464/)
- [Human Interface Guidelines: Layout](https://developer.apple.com/design/human-interface-guidelines/layout)
- [Positioning content relative to the safe area](https://developer.apple.com/documentation/uikit/positioning-content-relative-to-the-safe-area)
- [UIView.safeAreaLayoutGuide](https://developer.apple.com/documentation/uikit/uiview/safearealayoutguide)
- [UITraitCollection](https://developer.apple.com/documentation/uikit/uitraitcollection)
- [UIWindowScene.sizeRestrictions](https://developer.apple.com/documentation/uikit/uiwindowscene/sizerestrictions)
- [Supporting multiple windows on iPad](https://developer.apple.com/documentation/uikit/supporting-multiple-windows-on-ipad)
- [Optimizing iPhone and iPad apps to support ProMotion displays](https://developer.apple.com/documentation/quartzcore/optimizing-iphone-and-ipad-apps-to-support-promotion-displays)
- [CADisplayLink.preferredFrameRateRange](https://developer.apple.com/documentation/quartzcore/cadisplaylink/preferredframeraterange)
- [AVPlayer](https://developer.apple.com/documentation/avfoundation/avplayer)
- [Specifying your app's launch screen](https://developer.apple.com/documentation/xcode/specifying-your-apps-launch-screen/)
- [Creating your app icon using Icon Composer](https://developer.apple.com/documentation/xcode/creating-your-app-icon-using-icon-composer)
- [Xcode Asset Management](https://developer.apple.com/documentation/xcode/asset-management)

### 从 Apple 文档得到的几条硬规则

1. **Safe Area 是运行时布局输入，不是机型常量。**
2. **iPad UI 必须按窗口尺寸适配，不能只按设备屏幕尺寸适配。**
3. **ProMotion 刷新率是动态的，App 只能表达偏好，不能假设恒定 120 Hz。**
4. **显示刷新率和 Gameplay Clock 必须解耦。**
5. **Launch Screen 使用现代系统配置，不继续依赖旧 LaunchImage 体系。**
6. **iPhone Duo 是正式支持目标。布局应以 size classes、scene geometry、safe areas 和 reserved regions 为基础，不要用 model identifier 或固定 Duo 尺寸驱动 Gameplay。**
7. **Outer Display 与 Inner Display 属于同一连续体验，打开、关闭、部分折叠和 resize 时不得重置歌曲或 Gameplay State。**

---

# 17. 发布与资源边界

MikuFlick /02 的原版资源来自商业游戏。

公开仓库建议包含：

- 64 位重构代码
- reverse-engineered compatibility rules
- asset importer
- mapping / manifest
- tests
- documentation

不默认包含：

- 原版 DLC 资源包，但开启自制谱/曲包导入功能

推荐最终用户流程：

```text
Select a legally obtained original IPA / installed data
↓
Local verification
↓
Local extraction
↓
Build MikuFlick64 Asset Pack
↓
Play
```

---

# 18. 最终目标

项目最终希望做到：

```text
2012 gameplay identity
        +
modern 64-bit Apple runtime
        +
adaptive iPhone / iPad UI
        +
high-refresh presentation
        +
maintainable community codebase
```

最重要的约束只有一句：

> **屏幕可以变快、变大、变宽，但原版的节奏判定不能跟着漂。**

---

# 19. 本项目使用的软件工具

下面列出 MikuFlick64 当前开发、逆向分析、资源处理和协作流程中已经使用或明确纳入工作流的软件。未实际确定采用的候选工具，不放进这张表。

## 19.1 开发与调试

| 工具 | 用途 | 链接 |
|---|---|---|
| **Xcode** | MikuFlick64 主工程开发、编译、签名、真机调试、Simulator、性能分析 | [Apple Developer - Xcode](https://developer.apple.com/xcode/) |
| **Xcode Simulator / Device Support** | iPhone、iPad、iPhone Duo 等不同窗口与设备环境测试 | [Xcode Documentation](https://developer.apple.com/documentation/xcode) |
| **Instruments** | CPU、内存、卡顿、GPU / rendering、Energy 等性能分析；随 Xcode 提供 | [Apple Developer - Instruments](https://developer.apple.com/documentation/xcode/instruments) |
| **Icon Composer** | iOS / iPadOS 26+ 多层 Liquid Glass App Icon 制作 | [Apple Developer - Icon Composer](https://developer.apple.com/icon-composer/) |
| **OpenAI Codex** | 阅读和修改现有 Xcode 工程、实现功能、重构、测试与持续改进 | [OpenAI Codex](https://openai.com/codex/) |
| **Git** | 本地版本控制 | [git-scm.com](https://git-scm.com/) |
| **GitHub** | 代码托管、提交历史、研究文档与项目协作 | [github.com](https://github.com/) |

### Xcode Command Line Tools

逆向和工程检查中还会直接使用 Apple / macOS 自带的命令行工具，例如：

```text
file
otool
lipo
shasum
xcrun
codesign
```

这些工具主要用于：

- 检查 Mach-O 架构
- 查看 Load Commands / `LC_ENCRYPTION_INFO`
- 检查 armv7 / arm64 架构
- 计算资源哈希
- 查询 SDK / toolchain
- 检查签名状态

入口：[Xcode Resources / Additional Tools](https://developer.apple.com/xcode/resources/)

## 19.2 逆向工程与二进制分析

| 工具 | 用途 | 链接 |
|---|---|---|
| **Ghidra 12.1.4** | MikuFlick2 ARMv7 主程序静态分析、Decompiler、XREF、数据表与 Objective-C 符号研究 | [NSA Ghidra](https://github.com/NationalSecurityAgency/ghidra) · [Releases](https://github.com/NationalSecurityAgency/ghidra/releases) |
| **JDK 25 (64-bit)** | Ghidra 12.1.x 运行环境 | [OpenJDK 25](https://jdk.java.net/25/) |
| **Hex Fiend** | macOS 下查看、比较和原位修改 USM / 二进制资源 | [hexfiend.com](https://hexfiend.com/) |

## 19.3 CRIWARE / 媒体资源研究

| 工具 | 用途 | 链接 |
|---|---|---|
| **CriStudio / CriCodecs** | 查看和解析 CRIWARE 的 USM、ADX、UTF、CPK 等资源；辅助提取与资源结构研究 | [Youjose/CriCodecs](https://github.com/Youjose/CriCodecs) |
| **FFmpeg / FFprobe** | 检查 USM 中的媒体流、音视频属性、解码与格式验证 | [ffmpeg.org](https://ffmpeg.org/) · [Download](https://ffmpeg.org/download.html) |
| **Python 3** | CUE / UTF / plist / NSKeyedArchiver 数据解析、批量资源处理、哈希与验证脚本 | [python.org](https://www.python.org/) |

## 19.4 Apple SDK / Frameworks

这些不是单独安装的第三方软件，但属于 MikuFlick64 当前建议使用的 Apple 开发栈：

| Framework / API | 主要用途 | 链接 |
|---|---|---|
| **Swift / SwiftUI / UIKit** | App 主体、现代 UI、窗口与输入交互 | [Swift](https://developer.apple.com/swift/) · [SwiftUI](https://developer.apple.com/xcode/swiftui/) · [UIKit](https://developer.apple.com/documentation/uikit) |
| **AVFoundation** | 音频、MV 播放和现代媒体时间基础 | [AVFoundation](https://developer.apple.com/av-foundation/) |
| **QuartzCore / CADisplayLink** | ProMotion、高刷新率 Presentation 与显示同步 | [QuartzCore](https://developer.apple.com/documentation/quartzcore) |
| **Metal** | 如果自定义 Gameplay Renderer 需要更底层的 GPU 渲染时使用 | [Metal](https://developer.apple.com/metal/) |
| **Core Animation** | HUD、歌词、过渡和其他 UI 动画 | [Core Animation](https://developer.apple.com/documentation/quartzcore) |

## 19.5 工具链原则

```text
Xcode / Codex
    ↓
Modern 64-bit implementation

Ghidra
    ↓
Original behavior reference

Python + CriCodecs + FFmpeg + Hex Fiend
    ↓
Legacy asset / USM / metadata research

Git + GitHub
    ↓
Version control + documentation
```

对于 Codex：

> README 是对现有 Xcode 工程的改进建议和兼容性约束。Codex 应优先读取当前工程，再决定如何修改，不应根据本文重新生成一个平行项目。

对于逆向工具：

> Ghidra、CriCodecs、FFmpeg、Python 与 Hex Fiend 的产出用于确认原版行为和建立资源兼容层；现代版代码应逐步减少对旧格式内部细节的直接耦合。

---

*Development specification refreshed: 2026-10-05*  
*Target: iOS / iPadOS 26+ baseline; iPhone Duo support built and tested with iOS 27 / Xcode 27.1 SDK*  
*Original reference: MikuFlick2 1.1.5 / ARMv7 / cryptid 0*
