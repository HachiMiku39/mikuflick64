# mikuflick64
MikuFlick02 renew version for iOS26+ devices.

# MikuFlick 64-bit Modernization / iPad & iPhone Duo Adaptation Spec

> 用途：交给 Codex 继续实现与拆任务。  
> 目标：在尽量保留原版 MikuFlick 手感与视觉身份的前提下，完成现代 iOS 64 位化，并针对 iPad 与未来可折叠 iPhone Duo 设计真正的大屏 UI。  
> 本文汇总 2026-10-04 讨论内容，包含设备逻辑、UI 架构、MV/歌词布局、输入区、大屏 HUD、Gauge/Combo 重定义、BTL 规则、折叠状态连续性、测试策略与历史机制考据。

---

## 1. 项目目标

### 1.1 核心目标

1. 保留普通不可折叠 iPhone 上的经典 MikuFlick 竖屏体验。
2. 针对 iPad 与未来 iPhone Duo 展开态提供新的大屏 UI。
3. 不让普通 iPhone 因为横屏或屏幕变宽而误触发新 UI。
4. iPad 竖屏继续使用经典 iPhone 风格布局，整体居中。
5. iPad 横屏与 iPhone Duo 展开态使用专门的大屏布局。
6. 将 MV、歌词、Gameplay、Flick 输入区、Score、Gauge 等模块彻底解耦，便于后续适配折叠状态与不同窗口尺寸。
7. 折叠/展开、旋转、iPad 窗口大小改变时，保持播放、谱面、Combo、Gauge、歌词位置等状态连续，不重启歌曲。
8. 项目作为开源社区中较早一批针对未来 iPhone Duo 做原生适配的示范性项目。

---

## 2. 设计原则

### 2.1 普通 iPhone 保留经典 UI

普通不可折叠 iPhone：

- 不支持新的 Duo / iPad 大屏 UI。
- 即使是 Pro Max，也继续使用经典 iPhone UI。
- 即使横屏，也不能因为宽度达到阈值而自动进入 Duo UI。
- 经典 UI 保留原版纵向信息流：
  - MV
  - 歌词 / Note
  - Next input
  - Combo / Score
  - Flick 输入区

### 2.2 Duo / iPad 才有资格进入新 UI

大屏新 UI 的“资格”和“当前布局”要分两层处理：

```text
设备类别决定是否有资格使用新 UI
+
当前姿态 / 窗口尺寸决定使用哪一种大屏布局
```

普通 iPhone 永远不允许进入 `expandedDualPane`。

### 2.3 大屏不是把 iPhone UI 吹大

禁止：

```text
iPhone UI × 1.8
```

正确思路：

```text
大屏 = 重新组织空间
```

例如：

- MV 和歌词分栏
- Score / Gauge 放入视频黑边
- Flick 区保持合理物理尺寸
- 输入区允许用户移动 / 缩放
- iPad / Duo 额外空间用于内容，而不是让按键变成“餐盘”

### 2.4 Flick 输入区是“软件键盘”，不是可无限伸缩的内容

内部设计原则：

> Flick controls behave like a software keyboard, not scalable content.

即：

- 可以整体移动
- 可以整体等比例缩放
- 不允许横向拉伸
- 不允许单独改变某个键大小
- 不允许破坏键之间相对位置
- 不随着大屏宽度无限放大

---

## 3. 设备与布局模式

建议定义：

```swift
enum DeviceFamily {
    case iPhone
    case iPhoneDuo
    case iPad
}
```

布局模式：

```swift
enum MikuLayoutMode {
    case phoneClassic
    case duoExpanded
    case tabletPortraitClassic
    case tabletWide
}
```

### 3.1 设备状态映射

| 设备 / 状态 | UI |
|---|---|
| 普通 iPhone | Classic iPhone UI |
| 普通 iPhone Pro Max | Classic iPhone UI |
| 普通 iPhone 横屏 | Classic iPhone UI |
| iPhone Duo 折叠 | Classic iPhone UI |
| iPhone Duo 展开 | Duo Expanded UI |
| iPad 竖屏 | Classic iPhone UI，居中 |
| iPad 横屏 | Large / Expanded UI |
| iPad 窄窗口 | 可退回较紧凑布局，但仍属于 iPad 分支 |

### 3.2 推荐 Resolver

```swift
func resolveLayout(
    family: DeviceFamily,
    isExpanded: Bool,
    windowSize: CGSize
) -> MikuLayoutMode {
    switch family {
    case .iPhone:
        return .phoneClassic

    case .iPhoneDuo:
        return isExpanded ? .duoExpanded : .phoneClassic

    case .iPad:
        if windowSize.width > windowSize.height {
            return .tabletWide
        } else {
            return .tabletPortraitClassic
        }
    }
}
```

### 3.3 未来 Duo API 接入原则

不要写死：

```swift
if deviceModel == "iPhone XX,YY" { ... }
```

应该把官方折叠状态 API 隔离在一层：

```text
DeviceContext
    ↓
Posture / Fold State
    ↓
LayoutResolver
    ↓
Presentation
```

未来 Apple SDK 出来以后，只替换 `DeviceContext` / `Posture` 检测，不重构 Gameplay。

---

## 4. iPad 竖屏

iPad 竖屏不重新设计大屏 UI。

使用：

> Classic iPhone UI，原生实现，居中显示

注意：

- 不是系统 iPhone Compatibility Mode。
- App 本身仍然是原生 iPad App。
- Classic Canvas 自己控制逻辑尺寸。
- 推荐接近现代 Pro Max 逻辑宽度，而不是 320×480 / 320×568 时代的兼容尺寸。
- 背景可以延展到整个 iPad。
- 游戏主体保持中央竖屏构图。

示意：

```text
┌────────────────────────────┐
│                            │
│      ┌──────────────┐      │
│      │      MV      │      │
│      │   视频字幕   │      │
│      │──────────────│      │
│      │ Note / Kana  │      │
│      │              │      │
│      │ Flick Area   │      │
│      └──────────────┘      │
│                            │
└────────────────────────────┘
```

---

## 5. iPhone Duo 展开 / iPad 横屏

### 5.1 总原则

进入新的大屏布局：

```text
MV / Gameplay 内容
+
独立歌词
+
独立 HUD
+
可调整 Flick 输入区
```

### 5.2 MV Playback

普通 iPhone / Duo 折叠：

- 歌词可以继续叠在 MV 上。
- 保留经典手机版显示方式。

Duo 展开 / iPad 横屏：

> 不在视频上显示歌词。

采用类似 Apple Music 的双栏思路：

```text
┌────────────────────┬────────────────────┐
│                    │      上一句        │
│                    │                    │
│        MV          │      当前歌词      │
│                    │                    │
│                    │      下一句        │
│                    │                    │
└────────────────────┴────────────────────┘
```

要求：

- 左侧 MV 保持原始比例。
- 右侧歌词独立滚动。
- 当前行明显高亮。
- 上下句弱化。
- 自动跟随播放。
- 未来可选支持点击歌词跳转。
- 保留较大的左右 padding。
- 不做“文档式密集歌词列表”。

可选扩展：

- 沉浸播放模式
- 跟唱模式
- 罗马音 / 翻译
- 歌词跳转

---

## 6. Gameplay 大屏布局

建议：

```text
┌────────────────────┬────────────────────┐
│                    │ SCORE        COMBO │
│                    │                    │
│        MV          │ Note / Lyrics      │
│                    │                    │
│                    │ Next input         │
│                    │                    │
│                    │ ┌──────────────┐   │
│                    │ │ Flick Area   │   │
│                    │ └──────────────┘   │
└────────────────────┴────────────────────┘
```

或者中央保留更强的游戏画布，再将 MV / HUD / 输入区分配到左右空间。

原则：

- Gameplay engine 不知道当前是 iPhone、iPad 还是 Duo。
- 谱面只输出 timing / kana / direction。
- UI 决定这些元素如何排布。

---

## 7. 输入区设置模式

仅在：

- iPad
- iPhone Duo 展开态

提供。

普通 iPhone与 Duo 折叠态不开放。

### 7.1 入口

推荐：

```text
暂停
↓
输入区域设置
```

正式游戏中不允许误拖。

Practice 模式可考虑允许实时微调。

### 7.2 可调整项目

允许：

- 整体移动
- 整体等比例缩放
- 左 / 中 / 右预设
- 上下位置调整
- 重置
- 测试模式

禁止：

- 单独调整某个键
- 横向拉伸
- 改变九宫格比例
- 改变键间距逻辑

### 7.3 建议预设

- Left-handed
- Centered
- Right-handed
- Compact
- Default

### 7.4 测试模式

在设置模式中允许玩家直接 Flick，显示：

```text
Detected:
あ → ↑ → う

Swipe distance: 42 pt
```

用于即时确认手感。

### 7.5 设置按设备状态分别保存

例如：

```text
iPad Portrait
iPad Landscape
Duo Expanded
```

分别存储。

iPad 竖屏默认 Classic UI 居中，可以不强制用户设置输入区。

---

## 8. 华为折叠屏参考结论

研究华为阔折叠设备和记事本交互后，采纳以下原则：

### 8.1 展开不是重新启动另一套 App

展开 / 折叠是同一页面发生布局变化。

必须保持：

- MV 当前时间
- 音频状态
- 歌词位置
- 当前谱面时间
- Combo
- Gauge
- 当前输入状态
- 暂停状态

### 8.2 内容变大，输入器保持人体尺度

华为大屏键盘会变成相对固定尺寸的输入岛，而不是铺满整个屏幕。

MikuFlick 输入区同理：

- 不随屏幕宽度无限增长
- 大屏增加内容空间
- 输入区保持合理大小
- 允许左右偏置 / 居中

### 8.3 可借鉴，但不照抄

只借鉴：

- Fold / Unfold 状态连续性
- 大屏信息架构
- 输入组件的尺度策略

不要提前照抄：

- 华为具体尺寸
- 动画曲线
- 分屏比例
- 悬停角度逻辑

这些要等未来 Apple Duo 真机 / SDK 再定。

---

## 9. MV 黑边 HUD 设计

如果 MV 原始比例在现代设备上自然产生左右黑边，则把黑边作为 HUD 空间。

核心原则：

> 黑边是功能区，不是浪费空间。

### 9.1 默认布局

```text
┌────────────────────────────────┐
│ Gauge │          MV        │ SCORE │
│       │                    │       │
│       │                    │       │
│       │                    │       │
└────────────────────────────────┘
```

建议：

- 左黑边：Microphone Gauge
- 右黑边：Score
- MV 不被 HUD 遮挡
- Combo / Kana / Flick 区放在下方

### 9.2 BTL

BTL 无 Gauge：

- 左黑边不显示 Gauge
- 可以留白
- 不建议强塞其他信息
- 右侧 Score 仍保留

---

## 10. 原版机制考据结论

### 10.1 名称

原版更准确的叫法：

> マイクゲージ / Microphone Gauge

不建议再称为 Fever Gauge。

### 10.2 SEGA 官方视频观察

见后文逆向结果处理，和原版游戏保持一致

#### MikuFlick 无印

- Gauge 初始约 50%
- Gauge 满格没有额外特效
- AP 演示中约 67 次 Cool 到满
- 官方演示只展示了高水平连续 Cool 情况

#### MikuFlick /02

- Gauge 初始约 50%
- Gauge 满格没有额外特效
- AP 演示中约 52 次 Cool 到满
- 连续 20 次 Cool 时，轨道出现彩虹闪光
- Gauge 满格与彩虹轨道不是同一机制

### 10.3 BTL

- BTL 没有 Microphone Gauge
- 不使用普通生命值归零失败逻辑
- UI 层与逻辑层都要真正关闭 Gauge 系统

---

## 11. 新版 Gauge 机制

取消，按照原版逆向结果做

### 11.1 数值范围

```text
初始值：128
最小值：0
最大值：256
```

### 11.2 判定增减

```text
见后文逆向结果处理，和原版游戏保持一致
```


### 11.3 失败条件

```text
Gauge == 0
另见后文逆向结果处理，和原版游戏保持一致
```

### 11.4 满格

```text
Gauge == 256
见后文逆向结果处理，和原版游戏保持一致

---

## 12. Gauge 视觉设计

见后文逆向结果处理，和原版游戏保持一致
采用连续渐变色。

建议表达危险程度：

```text
0              64            128            192            256
│──────────────|──────────────│──────────────│──────────────│
危险            警告            正常            明亮 / Miku 色
```

原则：

- 不做硬切分段。
- Gauge 变化时平滑插值。
- 低值区域明显危险。
- 高值区域更偏 Miku 青绿 / 亮青。
- 满格保持稳定，不闪烁。

---

## 13. Combo 与彩虹视觉系统

与 Gauge 完全独立。

### 13.1 Combo 规则

采用 Project DIVA 风格：

```text
只有 Cool 和 Fine 续 Combo
其他判定断 Combo
```

### 13.2 Rainbow Level

见后文逆向结果处理，和原版游戏保持一致

### 13.3 Fine 的处理

见后文逆向结果处理，和原版游戏保持一致

### 13.4 断 Combo

见后文逆向结果处理，和原版游戏保持一致

### 13.5 建议实现

见后文逆向结果处理，和原版游戏保持一致

### 13.6 彩虹效果风格

见后文逆向结果处理，和原版游戏保持一致

---

## 14. Gameplay Engine 与 UI 解耦

建议分层：

```text
Chart Engine
    ↓
Timing / Kana / Flick Direction
    ↓
Judgment Engine
    ↓
Combo / Gauge / Score State
    ↓
Presentation Layer
    ↓
Classic / Duo / iPad UI
```

Gameplay Engine 不应该知道：

- 当前是不是 iPad
- 当前是不是 Duo
- 当前是不是横屏
- MV 在左还是上
- 歌词是 overlay 还是 side panel

---

## 15. Fold / Rotate / Resize 连续性

切换布局时，必须保持：

```text
AVPlayer currentTime
Audio clock
Chart position
Current note index
Combo
Cool streak
Rainbow level
Gauge
Score
Lyrics timeline index
Pause state
```

禁止：

- 重新加载歌曲
- 重新创建谱面
- 重置 Combo
- 重置 Gauge
- 重置 Score
- 重新从 0 开始播放 MV

推荐：

```text
State Object 保持
View 只换 Presentation
```

---

## 16. Classic / Expanded 歌词策略

### Classic Phone

```text
MV
+
视频内歌词 Overlay
```

### Duo Expanded / iPad Wide

```text
MV
|
Lyrics Side Panel
```

建议属性：

```swift
enum LyricsPresentation {
    case videoOverlay
    case sidePanel
}
```

映射：

```swift
switch layoutMode {
case .phoneClassic, .tabletPortraitClassic:
    lyricsPresentation = .videoOverlay

case .duoExpanded, .tabletWide:
    lyricsPresentation = .sidePanel
}
```

---

## 17. iPhone-only 兼容模式问题

旧 iPhone-only App 在 iPad 上可能进入古老的兼容画布。

现代化目标：

- 不依赖系统旧式 Compatibility Mode。
- 使用原生 iPad target / Universal 结构。
- Classic iPhone UI 由 App 自己实现。
- 不让系统把 App 固定在 320×480 / 320×568 的老画布再整体放大。

需要检查旧工程 / IPA：

- `Info.plist`
- `UIDeviceFamily`
- `UILaunchImages`
- 旧 `Default.png`
- `UILaunchStoryboardName`
- Launch Screen

目标：

- 使用现代 Launch Screen
- 不让系统误判为老 iPhone 尺寸
- iPad 竖屏由 App 自己居中 Classic Canvas

---

## 18. 输入判定注意事项

见后文逆向结果处理，和原版游戏保持一致

---

## 19. 推荐模块划分

```text
MikuFlickCore
├─ AudioClock
├─ ChartEngine
├─ JudgmentEngine
├─ ScoreEngine
├─ ComboEngine
├─ GaugeEngine
├─ LyricsTimeline
└─ GameState

MikuFlickUI
├─ ClassicPhoneLayout
├─ DuoExpandedLayout
├─ TabletPortraitClassicLayout
├─ TabletWideLayout
├─ DualPaneMVPlayer
├─ LyricsPanel
├─ BoundedFlickKeyboard
├─ GameplayHUD
└─ InputLayoutEditor

DeviceLayer
├─ DeviceFamilyResolver
├─ FoldStateProvider
├─ WindowMetricsProvider
└─ LayoutResolver
```

---

## 20. 测试矩阵

### 20.1 设备 / 布局

- 普通 iPhone 竖屏
- 普通 iPhone Pro Max
- 普通 iPhone 横屏
- Duo 折叠
- Duo 展开
- iPad 竖屏
- iPad 横屏
- iPad 小窗口
- iPad 大窗口

### 20.2 状态切换

在以下时机执行折叠 / 展开 / 旋转 / resize：

- MV 刚开始
- 歌词滚动中
- BTL 模式
- 暂停状态
- 恢复状态

检查：

- 音画同步
- 歌词位置
- Score
- Combo
- Cool streak
- Rainbow Level
- Gauge
- 当前 Note
- 输入区位置
- UI 无跳动 / 黑屏 / 重载

### 20.3 Gameplay

重点验证：
见后文逆向结果处理，和原版游戏保持一致

---

## 21. 开发优先级

### P0

- 64 位运行
- 现代 Launch Screen
- Classic iPhone UI
- iPad 原生 target
- 基础 Flick 输入
- Audio / Chart / Judgment 解耦
- Gauge / Combo / Score State

### P1

- iPad 竖屏 Classic 居中
- iPad 横屏 Dual-pane
- Duo Expanded 抽象布局
- MV 左 / 歌词右
- 输入区最大尺寸限制
- 输入区拖动 / 缩放
  

### P2

- 真机折叠状态 API
- Fold / Unfold 无缝 morph
- 歌词点击跳转
- Practice 输入测试模式
- 左右手预设
- 高级视觉效果

---

## 22. 非目标 / 暂不做

- 普通不可折叠 iPhone 使用新 UI
- 因为 Pro Max 更宽就进入 Duo UI
- 把 iPad 横屏做成简单放大版 iPhone
- 将 Flick 区无限拉伸
- Gauge 满格触发 Fever
- Gauge 与 Rainbow Combo FX 混为一个系统
- BTL 伪装隐藏 Gauge 但后台仍跑生命值
- 折叠 / 展开时重启歌曲
- 为了“现代化”改变谱面本身的 timing 语义

---

## 23. 最终产品哲学

### 普通 iPhone

> 保留 MikuFlick 原版竖屏身份。

### iPad 竖屏

> Classic iPhone UI，原生实现，居中显示。

### iPad 横屏 / iPhone Duo 展开

> 真正的大屏 UI，而不是放大版手机 UI。

### MV

> 小屏歌词可以叠在视频上，大屏改为左 MV / 右歌词。

### Gameplay

> 内容可以扩展，输入区保持人体尺度。

### 状态

> Fold / Unfold 只改变 Presentation，不改变 Game State。

### Gauge

> 见后文逆向结果处理，和原版游戏保持一致

### Combo

> 见后文逆向结果处理，和原版游戏保持一致

---

## 24. Codex 开发提示

建议 Codex 开始前先做以下步骤：

1. 读取现有工程结构。
2. 找到当前：
   - Audio playback
   - Chart parsing
   - Flick handling
   - Score / Combo
   - MV view
   - Lyrics rendering
3. 不要直接在现有 View Controller 里堆设备判断。
4. 先建立：
   - `GameState`
   - `DeviceContext`
   - `LayoutResolver`
   - `LyricsPresentation`
   - `GaugeEngine`
   - `ComboEngine`
5. 再逐步迁移旧 UI。
6. 所有 Fold / Rotate / Resize 测试都要求：
   - 不重建 GameState
   - 不重启 AVPlayer
7. 每完成一个模块就加入自动测试或最小可复现测试场景。

---

## 25. 建议的第一批任务拆分

### Task A: Device / Layout abstraction

实现：

- `DeviceFamily`
- `MikuLayoutMode`
- `LayoutResolver`
- iPhone / iPad 分支
- Duo 占位接口

### Task B: Classic Canvas

实现：

- 普通 iPhone
- iPad 竖屏居中
- 同一套 Classic UI 组件

### Task C: Dual-pane MV Player

实现：

- 左 MV
- 右歌词
- 当前句高亮
- 自动滚动
- iPad 横屏启用
- Duo Expanded 占位启用

### Task D: Input Layout Editor

实现：

- 拖动
- 等比缩放
- Left / Center / Right
- Reset
- Preview / Test

### Task E: Gauge Engine

实现：

```text
见后文逆向结果处理，和原版游戏保持一致
```

### Task F: Combo / Rainbow Engine

实现：

```text
见后文逆向结果处理，和原版游戏保持一致
```

### Task G: State continuity

验证：

- rotate
- iPad resize
- simulated fold/unfold

过程中：

- AVPlayer 不重启
- GameState 不重置
- HUD 状态不丢失

---

## 26. 备注


### 关于 Duo

在 Apple 正式公开 iPhone Duo 硬件 / SDK 前：

- 不猜具体机型 identifier
- 不硬编码实际折叠尺寸
- 用抽象 `FoldStateProvider`
- 用 iPad / 自定义宽窗口 / 其他折叠设备作为 UX 参考
- 真机发布后只补最后一层官方设备状态检测

# MikuFlick2 1.1.5 游戏机制逆向笔记

> 基于 `MikuFlick2` iOS 版 1.1.5（ARMv7）在 Ghidra 中的静态分析结果整理。  
> 本文只记录本轮已经从二进制中确认或能够高置信推导出的机制。尚未完全追踪的部分会明确标注。

---

## 0. 研究对象与可信度标记

### 二进制基本信息

- App：`MikuFlick2`
- 版本：1.1.5
- 主程序：`Payload/MikuFlick2.app/MikuFlick2`
- Mach-O：32 位 ARMv7
- Universal/Fat：否，仅 `armv7`
- FairPlay：
  - `LC_ENCRYPTION_INFO`
  - `cryptid 0`
  - 当前样本代码段已解密，可直接静态分析
- 主要框架风格：
  - Objective-C
  - C / C++
  - CRI Middleware（CRI Mana / CRI Atom）
- 反编译工具：Ghidra 12.1.4

### 本文标记

- **[确认]**：可直接从函数、数据表或控制流中确认。
- **[高置信推断]**：代码关系已经非常明确，但尚未追到所有外围逻辑。
- **[未完成]**：当前尚未继续深挖。

---

# 1. 游戏主循环

核心入口：

```text
-[SceneGame_Exec]
```

目前确认的每帧执行顺序：

```text
SceneGame_Exec
│
├─ 更新时间 / 游玩时间
├─ MikuFlickCriManager_Exec
├─ NoteManager_exec
├─ ReplayManager_Replay       （仅 Replay Mode）
├─ TouchManager_exec
├─ StageManager_exec
├─ WindowManager_exec
└─ EffectManager_exec
```

## 1.1 Note 更新顺序

`+[NoteManager_exec]` 会遍历当前 Note 集合，对每个 Note：

```text
note.exec()
setNextTarget(0)
根据 active 状态调整 Z 位置
```

随后再次遍历 Note，找到第一个 active Note：

```text
setNextTarget(1)
```

因此 `NextTarget` 本质上是当前需要优先提示/显示的目标 Note。

---

# 2. 时间系统：判定不是跟着渲染帧跑

这是整个判定系统里非常关键的一点。

## 2.1 播放计数器

`+[MikuFlickCriManager_Exec]`：

```c
playTime = CriManager::GetPlayTime();
PlayCnt = ceil(playTime * 30.0f);
```

全局播放计数器：

```text
DAT_00164340
```

`+[MikuFlickCriManager_GetPlayCnt]` 只是：

```c
return DAT_00164340;
```

## 2.2 Note 自己的时间

`-[NoteNormal_exec]` 中：

```c
m_Cnt = MikuFlickCriManager_GetPlayCnt() - m_BaseTime;
```

所以 Note 不是简单地：

```text
每画一帧 → m_Cnt++
```

而是：

```text
CRI 音频播放时间
        ↓
转换成 30 Hz PlayCnt
        ↓
减去 Note 的 BaseTime
        ↓
得到 Note 当前 m_Cnt
```

### 结论

**[确认]**

```text
1 tick ≈ 1 / 30 秒 ≈ 33.33 ms
```

这意味着游戏逻辑时间与音频时钟绑定，而不是与屏幕刷新帧率绑定。

这对于 2012 年移动设备非常重要：即使画面掉帧，判定时钟也不应该跟着漂移。

## 2.3 CRI 时间来源

调用链：

```text
MikuFlickCriManager_Exec
↓
CriManager::GetPlayTime
↓
_criManaPlayer_GetTime
↓
CriMvEasyPlayer::GetTime
```

`CriManager::GetPlayTime()` 最终返回：

```text
timeValue / timeBase
```

并且 `CriMvEasyPlayer::GetTime()` 中可看到与：

```text
0.0333667
```

相关的时间修正逻辑，约等于 29.97 fps 的帧周期。

---

# 3. 判定枚举

从 `NoteNormal_checkResult::::`、结果画面以及 `StatusData` 统计逻辑可以完全确定判定索引：

| 内部值 | 判定 |
|---:|---|
| 0 | 内部无有效判定 / 空结果 |
| 1 | WORST |
| 2 | SAD |
| 3 | SAFE |
| 4 | FINE |
| 5 | COOL |

结果画面直接按以下顺序读取：

```text
GetTmpNoteResult(5) → Cool
GetTmpNoteResult(4) → Fine
GetTmpNoteResult(3) → Safe
GetTmpNoteResult(2) → Sad
GetTmpNoteResult(1) → Worst
```

`0` 不作为五种玩家可见判定显示，但内部数组和最高成绩记录会保留 `0~5` 六个槽位。

---

# 4. 普通 Note 的判定窗

相关数据表：

```text
s_tblNoteNormal_Front @ 0x00139488
s_tblNoteNormal_Back  @ 0x001394B4
```

定义：

```text
Front = 玩家输入早于 JustFrame
Back  = 玩家输入晚于 JustFrame
```

因为代码计算：

```c
delta = JustFrame - TouchFrame;
```

所以：

```text
delta > 0 → 提前
delta < 0 → 延后
```

## 4.1 提前判定表

`s_tblNoteNormal_Front`：

| 提前 tick | 判定 | 约时间 |
|---:|---|---:|
| 0 | COOL | 0 ms |
| 1 | COOL | 33 ms |
| 2 | COOL | 67 ms |
| 3 | FINE | 100 ms |
| 4 | FINE | 133 ms |
| 5 | FINE | 167 ms |
| 6 | FINE | 200 ms |
| 7 | SAFE | 233 ms |
| 8 | SAFE | 267 ms |
| 9 | SAD | 300 ms |
| 10 | SAD | 333 ms |

## 4.2 延后判定表

`s_tblNoteNormal_Back`：

| 延后 tick | 判定 | 约时间 |
|---:|---|---:|
| 0 | COOL | 0 ms |
| 1 | COOL | 33 ms |
| 2 | COOL | 67 ms |
| 3 | COOL | 100 ms |
| 4 | FINE | 133 ms |
| 5 | FINE | 167 ms |
| 6 | FINE | 200 ms |
| 7 | FINE | 233 ms |
| 8 | SAFE | 267 ms |
| 9 | SAFE | 300 ms |
| 10 | SAD | 333 ms |

### 非对称性

**[确认]**

晚按比早按多给约 1 tick 的宽容：

```text
提前 COOL：0~2
延后 COOL：0~3

提前 FINE：3~6
延后 FINE：4~7

提前 SAFE：7~8
延后 SAFE：8~9
```

这是非常明显的触屏输入补偿设计。

---

# 5. TouchDown / TouchUp 合并规则

普通 Flick Note 并不是只在一个触摸时刻判断。

`-[NoteNormal_checkResult::::]` 同时计算：

```text
JustFrame - TouchDownFrame
JustFrame - TouchUpFrame
```

两者都会查普通 Note 的 Front / Back 判定表。

## 5.1 基本组合逻辑

**[确认]**

当 TouchDown 和 TouchUp 都处于有效判定范围时，系统通常会取两者中较差的判定：

```text
TouchDown 判定
TouchUp 判定
       ↓
取较差者
```

但存在针对“提前按住再 Flick”的特殊修正。

## 5.2 提前按住的容错

如果 TouchDown 提前，并且组合出来的结果低于 SAFE：

```c
if (result < SAFE && TouchDown 在 JustFrame 之前)
    result = SAFE;
```

另外，如果 TouchDown 已经远早于正常窗口，但 TouchUp 时机仍然有效：

```text
最终结果最高被限制到 SAFE
```

### 设计意义

**[高置信推断]**

这允许玩家：

```text
提前把手指放到屏幕上
↓
等待节奏点
↓
在正确时机完成 Flick / 抬手
```

但这种输入方式不能轻易拿到 FINE / COOL。

这是一套明显为触摸 Flick 操作设计的“预触摸容错”。

---

# 6. Flick 方向与 BoardType

## 6.1 Flick 方向错误

代码比较：

```text
m_FlickResult
与
本次输入期望 Flick 方向
```

若方向不一致：

```text
最高判定被限制为 SAFE（3）
```

因此方向错不会得到 FINE / COOL。

## 6.2 BoardType 不匹配

如果 Note 的 `m_BoardType` 与输入 Board 不一致：

```text
result = SAD（2）
```

这是一个直接降级路径。

## 6.3 BTL 特殊行为

在 `Difficulty == 4`（Break The Limit）时，普通判定函数存在特殊分支：

```c
if (difficulty == 4) {
    if (result == SAD)
        result = 0;
}
```

并且这一分支不会调用普通的：

```text
NoteManager_AddTensionGauge
```

同时，普通难度下失误会执行的 `ResetCombo()`，在 BTL 分支中也被跳过。

### 当前结论

**[确认代码行为 / 未完成玩法解释]**

BTL 明显不是普通五难度机制的简单数值增强，而是对：

```text
Gauge
SAD
Combo Reset
```

有独立处理。

具体 BTL 完整玩法尚未继续追踪。

---

# 7. WORST 与 Note 生命周期

`NoteNormal_exec` 中，Note 会根据 `m_JustFrame` 判断是否处于判定区域。

普通难度：

```text
InsideJudge：
JustFrame - 11
到
JustFrame + 11 附近
```

但判定表实际只给出了 `0~10 tick` 的正常判定结果。

因此边缘存在 1 tick 的“缓冲/无有效判定”区域。

## 7.1 自动 WORST

普通难度在：

```text
m_Cnt >= JustFrame + 12
```

之后，Note 自动失活，并记录：

```text
WORST
Gauge 惩罚
ResetCombo
失败特效
```

约等于：

```text
晚 12 tick ≈ 400 ms
```

## 7.2 Break The Limit

数据表：

```text
s_tblDifficultEndTimeOfs
```

内容：

| 难度 | Offset |
|---|---:|
| Easy | 0 |
| Normal | 0 |
| Hard | 0 |
| Extreme | 0 |
| Break The Limit | -2 |

因此 BTL 的 Note 生命周期尾端缩短约：

```text
2 tick ≈ 66.7 ms
```

---

# 8. 基础得分

数据表：

```text
s_tblAddScore @ 0x001394E4
```

主要判定组：

| 判定 | 基础 Stage Score |
|---|---:|
| 无有效判定 | 0 |
| WORST | 0 |
| SAD | 30 |
| SAFE | 50 |
| FINE | 150 |
| COOL | 300 |

因此：

```text
COOL = 300
FINE = 150
SAFE = 50
SAD  = 30
WORST = 0
```

## 8.1 第二组分数

表中还存在第二组：

```text
0, 0, 30, 50, 150, 250
```

该组通过 `local_28` 分支选择。

当前已追踪到的普通 Flick 方向错误路径同时会把结果最高限制到 SAFE，因此第二组中的高档 `150/250` 在当前已观察路径中没有正常发挥。

**[未完成]**

这部分可能存在其他调用情形或历史遗留逻辑，暂不强行解释。

---

# 9. Combo

## 9.1 续 Combo 条件

**[确认]**

```text
COOL / FINE → AddCombo
SAFE / SAD / WORST → ResetCombo
```

即：

```text
result >= 4 → 续 Combo
result < 4  → 断 Combo
```

普通难度中成立。

BTL 在该函数中跳过了正常的 ResetCombo 分支。

## 9.2 Max Combo

每次 Combo 增长后：

```text
如果当前 Combo > MaxCombo
→ 更新 MaxCombo
```

结果画面也会读取 `GetMaxCombo()`。

---

# 10. Combo Bonus 公式

在成功续 Combo 后，游戏会计算额外 Combo Score。

反编译中的 magic-number 除法等价于：

```text
floor((Combo + 5) / 10) × 50
```

并且：

```text
最大 500 分
```

因此大致表现为：

| 当前 Combo | 单次 Combo Bonus |
|---:|---:|
| 1~4 | 0 |
| 5~14 | 50 |
| 15~24 | 100 |
| 25~34 | 150 |
| 35~44 | 200 |
| 45~54 | 250 |
| 55~64 | 300 |
| 65~74 | 350 |
| 75~84 | 400 |
| 85~94 | 450 |
| ≥95 | 500 |

该 Bonus 被累计进：

```text
TmpComboScore
```

结果画面总分使用：

```text
TmpStageScore + TmpComboScore
```

---

# 11. Tension Gauge

相关数据：

```text
s_tblTensionGauge @ 0x00138F4C
DAT_00163088       当前 Gauge
```

## 11.1 初始值与上限

初始化函数：

```text
+[NoteManager_Initialize]
```

中：

```c
DAT_00163088 = 0x43000000;
```

`0x43000000` 作为 float：

```text
128.0
```

而 `AddTensionGauge` 中最大值：

```text
0x43800000 = 256.0
```

所以：

```text
初始 Gauge = 128
最大 Gauge = 256
开局 = 50%
```

这与实际游戏 UI 开局显示半管一致。

---

# 12. Gauge 判定权重

`s_tblTensionGauge`：

| 判定 | 权重 |
|---|---:|
| 无有效判定 | 0 |
| WORST | -10 |
| SAD | -5 |
| SAFE | 0 |
| FINE | +2 |
| COOL | +2 |

但实际 Gauge 变化不是直接加减这些整数。

## 12.1 谱面长度归一化

`+[NoteManager_AddTensionGauge:]` 会根据总 Note 数计算系数：

```text
GaugeCoefficient ≈ 64 / TotalNotes + 0.01
```

实际变化：

```text
Gauge += JudgeWeight × GaugeCoefficient
```

然后上限 clamp 到：

```text
256
```

因此：

```text
COOL  +2 × coefficient
FINE  +2 × coefficient
SAFE   0
SAD   -5 × coefficient
WORST -10 × coefficient
```

### 设计意义

Note 越少的谱面：

```text
单个判定对 Gauge 的影响越大
```

Note 越多的谱面：

```text
单个判定的影响会自动缩小
```

这是一个按谱面 Note 数量做生存难度归一化的设计。

---

# 13. Game Over 条件

`AddTensionGauge` 中可以确认存在两套失败条件。

## 13.1 Gauge 归零

如果：

```text
Gauge <= 0
```

则：

```c
DAT_00163088 = 0.0;
StatusData_SetGameOver(1);
SceneManager_NextScene(8);
```

也就是说：

```text
Gauge <= 0
→ Game Over
→ Gauge 强制清零
→ 切换到 Scene 8
```

## 13.2 “理论上已经不可能达到 50%”提前失败

游戏计算：

```text
成功判定数 = SAFE + FINE + COOL
剩余 Note 数 = TotalNotes - 已判定 Note 数
```

然后估算：

```text
(成功判定数 + 剩余 Note 数) / TotalNotes
```

这代表：

> 假设从现在开始剩下的 Note 全部至少打到 SAFE，理论上最高还能达到多少成功率。

如果该比例：

```text
< 50%
```

则立即 Game Over。

因此即使 Gauge 还有剩余，只要：

```text
数学上已经不可能达到 50% 的 SAFE-or-better
```

也会提前结束游戏。

---

# 14. Gauge 与 BGM 音量联动

Gauge 不只影响生存，还直接控制主音频音量。

代码等价于：

```text
volumeFactor = min(1.0, Gauge / 128 + 0.35)
```

然后：

```text
MainAudioVolume = 用户 BGM 音量 × volumeFactor
```

因此：

- Gauge 高时：音量保持 100%
- Gauge 越低：BGM 越弱
- Gauge 接近 0 时：约剩 35% 基础倍率
- 进入 Game Over 后 Gauge 被强制清零

这是一个非常典型的“危险状态音频反馈”。

---

# 15. Crimax 系统

Crimax 不是单纯全局开关，而是通过谱面 CuePoint 给特定 Note 打标记。

入口：

```text
CuePointFunc_Crimax
```

## 15.1 CuePoint 按难度读取开关

逻辑：

```text
difficulty = GetDifficulty()

取 CuePoint 字符串中：
[difficulty + 1] 的一个字符
↓
intValue
```

若该字符非 0：

```text
GetLastCrimaxEnableNote()
↓
setCrimaxMode(1)
```

因此同一个 Crimax CuePoint 可以针对：

```text
Easy
Normal
Hard
Extreme
BTL
```

分别指定是否生效。

---

# 16. Crimax 目标 Note 选择

`+[NoteManager_GetLastCrimaxEnableNote]`：

```text
从当前 Note 列表末尾开始
↓
最多向前检查 4 个 Note
↓
第一个 isCrimaxEnable == 1 的 Note
↓
返回
```

因此 CuePoint 不要求与目标 Note 精确一一对齐，而是允许在最近 4 个 Note 内寻找目标。

## 16.1 isCrimaxEnable

`-[NoteNormal_isCrimaxEnable]` 实际上只看：

```text
m_OptCharIdx < 0
```

即：

```text
m_OptCharIdx < 0  → 可 Crimax
m_OptCharIdx >= 0 → 不可 Crimax
```

---

# 17. Crimax 的 Rainbow 效果

数据表：

```text
s_tblRainbow @ 0x00139310
```

长度：

```text
84 个 float
= 28 组 RGB
= 每组 3 × float32
```

颜色范围使用：

```text
0~255
```

渲染时再除以：

```text
255.0
```

转换成 OpenGL/渲染使用的 `0.0~1.0`。

## 17.1 Rainbow 触发条件

`-[NoteNormal_render]` 中明确要求同时满足：

```text
m_OptCharIdx < 0
m_Active != 0
m_CrimaxMode != 0
Combo >= 100
```

才进入彩虹渲染。

否则走普通 Note 渲染。

## 17.2 Rainbow 索引

索引等价于：

```text
(m_CrimaxCnt + 10) % 28
```

每组 RGB 占：

```text
12 bytes
```

`m_CrimaxCnt` 在 Note 更新中循环：

```text
0~27
```

因此 Note 会在 28 色表中持续轮转。

### 结论

Crimax Rainbow 不是普通装饰，而是：

```text
Crimax 标记
+
Note Active
+
Combo ≥ 100
↓
彩虹 Note
```

它是 100 Combo 以上 Crimax 奖励状态的直接视觉提示。

---

# 18. Crimax 额外得分

在普通 Note 判定成功后：

```text
m_CrimaxMode != 0
且
result > 4
```

由于判定最大为 5，所以这里实际上要求：

```text
COOL
```

然后再检查：

```text
Combo >= 100
```

满足后：

```text
TmpStageScore +200
```

同时触发：

```text
StartSpScoreEffect
```

因此：

```text
Crimax Note
+ COOL
+ Combo ≥ 100
= 额外 200 Stage Score
```

FINE 不触发这个额外 200 分。

---

# 19. ClearCrimax

`+[NoteManager_ClearCrimax]` 会：

```text
遍历当前所有 Note
↓
setCrimaxMode(0)
```

也就是说它不是只清一个 Note，而是全局清除当前 Note 集合中的 Crimax 标记。

**[未完成]**

目前尚未可靠定位真正调用 `ClearCrimax` 的游戏逻辑位置。Ghidra 给出的一个直接 XREF 已确认属于 CRI Atom 区域的假引用。

因此：

```text
Crimax 如何在完整流程中结束
```

尚未继续深挖。

---

# 20. Interlude：间奏小游戏

Interlude 已确认是游戏中的“间奏单键小游戏”。

CuePoint 入口：

```text
CuePointFunc_Interlude
```

它与 Crimax 类似，也会：

```text
读取当前 Difficulty
↓
从 CuePoint 字符串中取 difficulty + 1 位置的字符
↓
转成 int
↓
通过函数表分发
```

## 20.1 Interlude 类型表

```text
s_tblInterludeType
```

内容：

| Type | 函数 |
|---:|---|
| 0 | `InterludeType_None` |
| 1 | `InterludeType_Normal` |
| 2 | `InterludeType_FadeOut` |

---

# 21. Interlude 开始与结束

## 21.1 Normal

`InterludeType_Normal()`：

```c
WindowManager_StartInterludeMode();
NoteManager_AddNote::::(1, 0, 1, 0);
```

也就是：

```text
进入 Interlude Mode
↓
生成一个 NoteInterlude
```

## 21.2 FadeOut

`InterludeType_FadeOut()`：

```c
WindowManager_EndInterludeMode();
```

所以该类型实际上用于结束 Interlude 段。

---

# 22. NoteInterlude 判定

`NoteInterlude` 拥有完整的：

```text
exec
render
setTouchDown
setTouchUp
checkResult::::
isJustFrame
```

但它不是 Flick 方向玩法，而是单键时机输入。

## 22.1 时间窗

`NoteInterlude_checkResult::::` 直接复用：

```text
s_tblNoteNormal_Front
s_tblNoteNormal_Back
```

因此时间精度与普通 Note 相同：

```text
30 Hz tick
COOL / FINE / SAFE / SAD
```

## 22.2 成功条件

真正计为 Interlude 成功：

```text
result > 3
```

即：

```text
FINE
或
COOL
```

SAFE / SAD 不计成功。

这符合实际玩法：

> 间奏里只出现一个键，玩家按节奏点按音符即可。

---

# 23. Interlude 得分

每次 Interlude 成功：

```text
TmpInterludeCnt +1
TotalInterludeSuccess +1
TmpStageScore +1
```

如果当前歌曲 / 难度下：

```text
TmpInterludeCnt >= TotalInterlude
```

也就是所有 Interlude 全成功，则追加：

```text
TmpStageScore +39
```

并：

```text
播放特殊音效
StartSpScoreEffect(39)
EffectManager_AddEffect2D(..., 7, ...)
```

因此最后一个 Interlude 成功时：

```text
该次基础 +1
全成功奖励 +39
总计 +40
```

---

# 24. 难度枚举

结果界面已经确认：

| 内部值 | 难度 |
|---:|---|
| 0 | Easy |
| 1 | Normal |
| 2 | Hard |
| 3 | Extreme |
| 4 | Break The Limit |

BTL 在普通 Note 判定、Gauge、Combo Reset、Note 生命周期尾窗中均存在特殊逻辑。

---

# 25. 结算与 Clear Rank

结果界面会读取：

```text
Total Score
Stage Score
Combo Bonus
Max Combo
Total Notes
Cool
Fine
Safe
Sad
Worst
Interlude
Difficulty
```

总分：

```text
TmpStageScore + TmpComboScore
```

## 25.1 Rank 初步规则

当前已经看到结果界面利用：

```text
COOL + FINE + SAFE
```

与 `TotalNotes` 的比例进行 Rank / Clear 判定。

代码中明确出现：

```text
70%
80%
95%
100%
```

等阈值。

此外：

```text
GetMaxCombo() == TotalNotes
```

时：

```text
SetPerfectClear(...)
```

**[未完成]**

当前尚未完整整理所有 Rank 数值 `0~6` 与游戏界面具体称呼的映射，因此暂不在本文强行命名。

---

# 26. 当前可用于现代化重构的简化模型

如果目标不是 1:1 恢复所有旧代码，而是做 64 位现代化版本，目前已经足够把核心逻辑压缩成下面这个模型。

## 26.1 Timing

```text
playTick = ceil(audioPlayTimeSeconds × 30)
noteTick = playTick - note.baseTime
```

## 26.2 Judge

```text
读取 TouchDown / TouchUp tick
↓
查 Front / Back 判定表
↓
合并两个时间判定
↓
应用提前按住修正
↓
检查 Flick 方向
↓
检查 BoardType
↓
得到 0~5 判定枚举
```

## 26.3 Result

```text
5 COOL
4 FINE
3 SAFE
2 SAD
1 WORST
0 INVALID / NONE
```

## 26.4 Combo

```text
COOL/FINE → combo++
SAFE/SAD/WORST → combo reset
```

BTL 除外。

## 26.5 Score

```text
COOL 300
FINE 150
SAFE 50
SAD 30
WORST 0

+ Combo Bonus
+ Crimax Bonus
+ Interlude Bonus
```

## 26.6 Gauge

```text
Start = 128
Max = 256

weight:
COOL  +2
FINE  +2
SAFE   0
SAD   -5
WORST -10

coefficient = 64 / TotalNotes + 0.01
```

## 26.7 Fail

```text
Gauge <= 0
OR
理论最高 SAFE-or-better 成功率 < 50%
→ Game Over
```

## 26.8 Crimax

```text
CuePoint
↓
最近 4 个可 Crimax Note
↓
m_CrimaxMode = 1
↓
Combo >= 100
↓
Rainbow

如果同时 COOL：
+200 Stage Score
```

## 26.9 Interlude

```text
Interlude Cue
↓
StartInterludeMode
↓
生成 NoteInterlude
↓
单键时机输入
↓
COOL/FINE 成功
↓
每个 +1
↓
全部成功额外 +39
```

---

# 27. 当前尚未继续研究的部分

以下内容目前有入口，但尚未继续完整逆向：

- BTL 完整规则
- Clear Rank 的名称与完整阈值映射
- `ClearCrimax` 的真实调用时机
- 第二套 `s_tblNoteNormal_Front / Back` 的用途
- `s_tblAddScore` 第二组高档分数的实际可达路径
- Replay 对判定结果的具体回放方式
- `NoteThrow`
- `NoteArrow`
- `NoteWait`
- `NoteInterlude.exec`
- StageManager 的完整状态机
- WindowGameWindow HUD 渲染细节
- Telop / SmallTelop
- Lyrics
- FadeIn / FadeOut
- SetBPM / SetDelay
- 谱面文件格式及 Note 生成格式
- TouchManager 的完整 Flick 手势识别算法
- CRI 音视频与谱面同步的完整校正逻辑

---

# 28. 已确认的重要 Ghidra 符号速查

```text
SceneGame_Exec
NoteManager_exec
NoteNormal_exec
NoteNormal_checkResult::::
NoteNormal_render
NoteNormal_isCrimaxEnable
NoteObjBase_isInsideJudgeFrame
NoteObjBase_setCrimaxMode:
MikuFlickCriManager_Exec
MikuFlickCriManager_GetPlayCnt
CriManager::GetPlayTime
CriMvEasyPlayer::GetTime
StatusData_AddFlickTypeNum:
NoteManager_AddTensionGauge:
NoteManager_GetLastCrimaxEnableNote
NoteManager_ClearCrimax
CuePointFunc_Crimax
CuePointFunc_Interlude
InterludeType_Normal
InterludeType_FadeOut
NoteInterlude_checkResult::::
WindowResult_loadTexture
```

关键表：

```text
s_tblTensionGauge          @ 0x00138F4C
s_tblDifficultEndTimeOfs   @ 0x001392FC
s_tblRainbow               @ 0x00139310
s_tblNoteNormal_Front      @ 0x00139488
s_tblNoteNormal_Back       @ 0x001394B4
s_tblAddScore              @ 0x001394E4
```

关键全局：

```text
DAT_00163088 → Tension Gauge
DAT_00164340 → CRI-derived PlayCnt
```

---

# 29. 总结

MikuFlick2 的核心机制可以概括为：

```text
CRI 音频时钟
↓
30 Hz 逻辑 tick
↓
TouchDown / TouchUp 双时间点判定
↓
Flick 方向与 Board 区域修正
↓
COOL / FINE / SAFE / SAD / WORST
↓
Score + Combo + Gauge
↓
Crimax / Interlude 等特殊机制
```

它的判定粒度以今天的音游标准看很粗，但结构并不草率。

相反，当前逆向结果显示它专门处理了：

- 音频时钟同步
- 早按与晚按非对称容错
- 提前按住再 Flick 的触屏行为
- Flick 方向错误降级
- 谱面长度对 Gauge 的归一化
- Gauge 与 BGM 音量联动
- 数学上已无法 Clear 时的提前失败
- Crimax 100 Combo 彩虹反馈
- 间奏单键 Bonus Game

对于 2012 年触屏 Flick 音游而言，这是一套明显经过实际手感调校的系统，而不是简单的“时间差查表”。

---

*整理时间：2026-10-05*  
*样本：MikuFlick2 1.1.5 / ARMv7 / cryptid 0*  
*状态：持续逆向中*
