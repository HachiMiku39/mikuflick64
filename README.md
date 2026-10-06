# Miku Flick 02 · 64 位重写

在 64 位 iPhone 和 iPad 上重现 Miku Flick 02 的假名滑动音游与 MV 播放。沿用原版谱面、影片、图片和提示音，提供原版风格的 Legacy 界面及两套现代界面，并加入多语言字幕、进度拖动和直接导入资源包。

当前发布为 **v1.1.6（build 8）测试版**。界面支持英语、日语、简体中文、法语、西班牙语和韩语；字幕覆盖 17 首歌曲的英语、简体中文、法语、西班牙语和韩语五种译文。工程内置 11 首基础歌曲，另外六首的影片需要导入 Mov_11／Mov_99 资源包。

## 下载与运行

[打开 1.1.6 测试版发布页](https://github.com/HachiMiku39/mikuflick64/releases/tag/mikuflick64-v1.1.6-r3)。请选择适合你的文件：

| 下载 | 用途 |
|---|---|
| [未签名真机 IPA](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r3/MikuFlick64-1.1.6-build8-unsigned.ipa) | 64 位 iPhone／iPad，需自行签名后安装。 |
| [可编辑源码工程](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r3/MikuFlick64-Source.zip) | 在 Xcode 中编译、修改或使用自己的开发团队运行到真机。 |
| [IPA 校验结果](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r3/IPA-VALIDATION.json) | 平台、版本、资源和压缩完整性检查。 |
| [SHA-256 校验清单](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r3/SHA256SUMS.txt) | 核对下载文件是否完整。 |

**系统要求：**iOS／iPadOS 17.0 或更高版本、arm64 设备。iPhone Duo 的专用折叠形态适配需要 iOS 27.1 或更高版本；真机折叠切换尚未测试。

**真机安装：**下载 IPA，导入你使用的签名工具，用自己的开发者账号或证书签名，再安装到设备。发布包没有开发团队签名；本轮尚未验证用户签名后的真机安装和运行。

**从源码运行：**解压工程，用 Xcode 打开 `MikuFlick64.xcodeproj`，选择 `MikuFlick64` Scheme 和目标设备后按 ⌘R。本轮使用 Xcode 27.2 beta 2（27B5028f）构建；真机运行需在 Signing 中选择自己的开发团队，模拟器无需开发者账号。

已完成本轮模拟器布局冒烟检查；全曲手动通关、真实设备触摸与音频表现仍需人工验证。已执行项目与待测范围见 [验证记录](VALIDATION.md)，人工测试事项见 [交接说明](HANDOFF.md)。

## build 8：ProMotion 与 Developer 性能工具（2026-10-06）

支持 ProMotion 最高 120 Hz：iPhone 开启 `CADisableMinimumFrameDurationOnPhone`；游戏与 MV 的画面更新改用 `CADisplayLink`，根据当前屏幕能力请求最高不超过 120 Hz。60 Hz 设备保留 60 Hz 上限。系统仍可因低电量、温度及用户设置降低刷新率；暂停、拖动进度及后台停止播放更新，恢复时重新申请。

轨道、键盘、特效按媒体时钟更新；原版 30 Hz 判定规则及原影片帧率保持不变。性能悬浮窗允许系统自适应刷新，不会在菜单强制持续 120 Hz。已在三台模拟器编译和冒烟检查；模拟器读数不能证明真机稳定 120 FPS，真机 ProMotion 及低电量切换仍需实测。Apple 文档：[ProMotion 适配](https://developer.apple.com/documentation/quartzcore/optimizing-iphone-and-ipad-apps-to-support-promotion-displays)。

`OPTIONS → DISPLAY → Developer` 开启后，在 `DEVELOPER` 子页打开 **Performance overlay**。悬浮窗显示本应用 CPU、RAM 与 UI FPS，每秒刷新；可拖动、点击标题收起，或用 × 关闭。页面切换和全屏游戏保留悬浮窗，窗口外的触摸继续传递给游戏。背景停止采样，回到前台重新建立基线；关闭 Developer 会同时关闭 Autoplay 和性能工具。

- CPU 为本应用进程的用户与系统 CPU 时间增量：100% 表示一个核心，多核心工作可能超过 100%。RAM 为应用的 physical footprint，单位 MiB（界面显示 MB）。模拟器数据属于模拟器进程，不能作为真机耗电或性能结论。
- **UI FPS** 是 `CADisplayLink` 回调频率，不是影片帧率，也不能代替 GPU 实际完成渲染的 FPS。
- **Apple Metal HUD** 使用 Apple 公开的 `MetalHUDForceEnabled` 与 `CAMetalLayer.developerHUDProperties`。改变后重启应用；支持的 Metal 表面可显示 GPU 耗时、图形内存和渲染 FPS。当前 SwiftUI／AVPlayer 页面可能不暴露 Metal 表面，因此不保证官方 HUD 出现。本应用没有可公开读取的完整 GPU 占用百分比，悬浮窗明确显示 `GPU —`。详细 GPU 分析请用 Xcode Instruments 的 Metal System Trace。
- 官方说明：[Metal Performance HUD](https://developer.apple.com/documentation/xcode/monitoring-your-metal-apps-graphics-performance)。这两个工具默认关闭，采样仅留在本机，不上传性能数据。

## build 6：Developer、原版特效与布局（2026-10-06）

- 轨道假名恢复原版专用字图：平假名、片假名、浊音、半浊音、小假名和长音符统一原版描边；保留三种键盘显示模式。

- `OPTIONS → DISPLAY → Developer` 开启后出现 `DEVELOPER` 子页，其中可开启 `Autoplay`。默认关闭；自动打歌将全部可判定音符判为 COOL，忽略手动输入，暂停时停止推进。屏幕标记 AUTO PLAY，测试成绩不保存、不覆盖最高分。
- DISPLAY 可选择原版判定图形：原版多边形底图位于 Cool!!／Fine! 等字体下方，启用时隐藏 FAST／LATE。
- 彩虹轨道按原版当前连击达到 100 触发，与难度或 AP 状态没有直接绑定。风火轮按连击 5／25／45 增加层数；烟花按 15／35／85 增加层数。触发条件来自原版反编译，宣传片已完整观看并作视觉对照，详见 [特效研究](EFFECTS-RESEARCH.md)。
- 麦克风 gauge 的底图、描边、填充采用同一个等比缩放；保持 146:388 原素材比例和填充偏移。iPhone 竖屏将 gauge 与键盘并排，轨道在上方。
- 主页 Logo 和菜单共用中心线；Duo 保持系统安全区，其他设备按可用空间居中。音量条、百分比和加减按钮对称对齐，原音量分段素材保持比例。音量设置在横屏采用左右分区，较短竖屏收紧装饰与间距，失败音效开关完整显示。
- Legacy 的间奏阶段隐藏 gauge，TAP 在输入区域居中；结束后恢复键盘和 gauge，仅作用于 Legacy，不改变 SEKAI-A／B。
- 普通 iPhone 固定竖屏，只保留一套手机 UI；iPhone Duo（包括折叠外屏）和 iPad 保留横竖屏。方向限制由系统应用代理处理，布局仍按可用空间与安全区适配。
- 本轮已在三个独立模拟器执行检查；Air 完整自动打歌获得 171 COOL／171 MAX COMBO，返回选歌最高分仍为 0。具体形态、测试结果及限制见 [验证记录](VALIDATION.md)。

## 1.1.6 · build 6 更新（2026-10-06）

- Bundle ID 按用户指定改为 `jp.sbga.mikuflick`，版本号为 `1.1.6`。
- 六种语言均保留 EASY／NORMAL／HARD／EXTREME／BREAK THE LIMIT（BTL）、NORMAL MODE、COOL／FINE／SAFE／SAD／WORST 和 FAST／LATE 的英文名称。周围说明仍按界面语言显示。
- IPA 完整包含 11 首内置免费歌曲的影片、谱面、预听、封面和歌曲 Logo；打包时逐文件核对 SHA-256，并验证 iPhoneOS arm64、包名、版本及压缩完整性。
- iPhone Air、iPad Pro 和 Duo 的本轮模拟器检查记录见验证文档；全部六语言文字策略和 11 首资源校验通过。

包名变化后，旧 `local.rewrite.MikuFlick64` 版本的数据不会自动迁入新应用；旧成绩和导入曲包仍留在旧应用沙盒。签名工具如果改写包名，安装后的标识会以签名工具设置为准。

## r3 · build 4 更新（2026-10-06）

- iPhone 竖屏麦克风计量条加宽；Legacy 歌曲 Logo 放大，选歌标题统一与系统返回键平齐。
- 结算与失败页显示 OCR-A `RESULT`，移除背景旧字母；先进行 1 秒黑屏淡入淡出，再逐项从个位向高位滚动数字。点击屏幕跳过，重新开始后动画重新播放；开启系统“减弱动态效果”时直接显示成绩。
- 菜单与歌曲加载文字统一为 OCR-A `Loading...`，菜单背景移除重复的 Loading 字样。
- 修复 Duo 折叠横屏难度第二行裁切、折叠竖屏返回键错位，以及 Book 模式结算按钮溢出；较矮结算页使用紧凑布局并支持滚动。
- 已在 iPhone Air、iPad Pro 11-inch (M5) 和 iPhone Duo 模拟器中进行本轮布局与失败结算检查；版本为 0.2.0（build 4），IPA 未签名。

## 界面与语言

- UI 支持英语、日语、简体中文、法语、西班牙语、韩语，跟随 iOS 应用语言设置。繁体中文回落简体中文，不支持的语言按系统语言优先序选择可用语言，最终回落英语。
- 英语、数字、法语和西班牙语使用 Futura-Medium；中文使用 PingFang SC；韩文使用 iOS 的 AppleSDGothicNeo 系统字体。所有语言界面的难度、COOL／FINE／SAFE／SAD／WORST 和 FAST／LATE 名称保留英语。
- 首次启动显示三页教程，也可从设置 → 帮助重新查看。游戏帮助共九章，每章提供六种语言。旧商店章节已改为本地资源包导入教程，界面统一使用 MV。
- 场景转场至少显示 1 秒，歌曲在转场结束且媒体准备完成后开始；如果准备仍未完成，继续显示加载画面。设置进入子选项、帮助章节，以及从这些子页返回，均不播放转场。
- 选歌提供 Legacy Cover Flow、SEKAI-A 封面网格和 SEKAI-B 列表。Legacy 显示歌曲标题、难度星数、最高分、BPM，以及每首歌每个难度的最高评级角标；难度文字保留倒影和贯穿横线。选歌底部仅保留 Play，资源导入入口位于主菜单。菜单使用系统导航返回按钮。
- 选歌与设置顶部的 `MUSIC SELECT`、`OPTIONS` 固定为英文大写，使用 OCR A 字体，任何语言都不翻译这两个标题。背景已移除原来的烘焙标题，保持连续的原版构图。
- 设置中的音量、握持和键盘页面复用原素材，音量页在一个面板内包含音乐、音效和失误提示音开关；握持说明位于左右手按钮下方。布局根据可用区域调整，交互控件遵循安全区。
- 大屏横向游戏中，键盘在其输入区域内居中。Duo 折叠横向采用紧凑的判定效果；类 3DS 分区布局为 MV 留出更高空间，将竖向计量条和得分、连击、暂停安排到下方区域。

## 游戏与 MV

- EASY、NORMAL、HARD、EXTREME、BREAK THE LIMIT 读取原谱面对应的难度槽，不使用另行生成的难度。灰色半透明假名无需输入，也不计为漏失；原间奏事件切换为大点击按钮。
- 支持 ORIGINAL、ROMAN SUPPORT、FULL ROMAN 三套原按键图集及预览卡片。PANEL GUIDE 随音符接近放大；FLOWER 控制按住时展开的字符花瓣。浊音、小假名按原操作码对应的行与方向输入。
- 评分按静态分析确认的 30 Hz tick 边界执行，按下与释放取较差判定。COOL／FINE／SAFE／SAD 为 300／150／50／30 基础分，WORST 为 0。普通模式中 FINE／COOL 增加连击，SAFE／SAD／WORST 清零；5 连击起产生额外奖励。BREAK THE LIMIT 保留非成功判定前的连击，抑制 SAD／WORST 的负判定且不加分。
- 暂停提供继续、重开和返回选歌。INPUT TIMING 保留 −10…+10 共 21 档，显示按原 BPM／30 Hz 换算的毫秒值，并按歌曲保存。最高分和最高评级按歌曲、难度保存；进入后台时暂停。
- 主菜单 MV 进入独立选歌和播放流程，不计游戏分数。Playlist 支持多选并按顺序连播，Loop 在播放列表启用后按顺序循环；Lyrics 控制字幕，Karaoke 使用伴奏音轨。随机选歌和 Shuffle 功能已移除。现代 MV 支持拖动播放进度。
- 日语模式只显示原文；英语、简体中文、法语、西班牙语、韩语模式显示原日语和对应译文。没有对应译文时省略第二行。

## 下载与安装资源包

在主菜单 RESOURCE PACK INPUT 打开 [资源包下载页](https://github.com/HachiMiku39/mikuflick02_soundpack/releases/tag/pack)，将 ZIP／RAR 保存到系统“文件”应用，再回到游戏选择“导入 ZIP / RAR”。也可从“文件”应用将压缩包分享给 Miku Flick 64。

应用内置 ZIP、RAR4、RAR5 解压，读取 USM 的 CuePoint 数据，在设备内将 MPEG-1 视频和 ADX 音频转换为 H.264 与双 AAC 音轨，并自动注册歌曲。安装页显示解压、校验、逐曲处理和注册阶段。

压缩包应包含一个或多个 `Mov_<编号>` 文件夹；允许外层包含 `InstallData`，`Thum_<编号>` 缩略图目录为可选项。例如：

```text
InstallData/
  Mov_99/
    romio_to_cinderella.usm
    electric_angew.usm
    finder.usm
    pv043.adx
    pv030.adx
    pv039.adx
    music_99_01.png
    verificationFile.dat
  Thum_99/                 # 可选
```

USM 包含视频、音轨、谱面和歌词；PNG 包含封面与标题图集；ADX 用于选曲试听。保留原文件名。目录遵循[谱包仓库](https://github.com/HachiMiku39/mikuflick02_soundpack)的布局，应用会核对存在的 SHA-1 清单。全部歌曲转换成功后才提交安装；已有同名目录保存到 `Library/ImportBackups`，失败时回滚。歌曲使用相对路径保存，应用容器地址变化后仍能读取。

安装到应用沙盒的 `Library/InstallData/Mov_<编号>`，其中 `Playback` 目录保存自动生成的 MP4、谱面 JSON、封面及标题，`miku64-songs.json` 保存歌曲索引。无需修改旧版存档或手动注册 DLC。

安装需要同时容纳压缩包、解压原件和转换缓存。当前支持本游戏的 MPEG-1／ADX USM，以及完整、无密码、非分卷的 ZIP／RAR；不支持其他游戏的任意 USM 编码、加密包或分卷压缩。

## 歌词翻译

已提供 11 首基础歌曲和 Mov_11／Mov_99 六首歌曲，共 17 首的五种语言译文。每种语言覆盖 479 条不同的游戏歌词 cue，总计 85 份 JSON、2395 条译文。译文与完整歌曲音源可能存在段落差异，以游戏实际 cue 为准。

英语和简体中文为项目自译；法语、西班牙语、韩语为本项目 AI 翻译，参考用户提供的游戏日语原文及既有项目译文。没有标注为萌娘百科或 Vocaloid Wiki 的转载译文。各文件保留译者说明与权利信息。

在主菜单资源包导入页可导入 `formatVersion: 1` 的字幕 JSON，也支持曲包中的 `<song.id>.lyrics.<language>.json`。语言代码为 `en`、`zh`／`zh-Hans`、`fr`、`es`、`ko`；导入文件优先于曲包和内置译文。格式、来源与覆盖清单见 [歌词翻译说明](Docs/LyricTranslations.md)。

## 音频与素材

主页 BGM01、设置等菜单 BGM03、结算 BGM05 自动循环并服从 MUSIC 音量。通用按钮音 SE02_01、选歌切换音 SE03.caf、暂停继续音 SE09.caf 等服从 SFX；SAD 提示音另受 FAIL SOUND 控制。初次进入或重复选中同一歌曲不播放切歌提示音。11 首基础歌曲使用原版短试听；导入歌曲按元数据定位 ADX 试听，并在本机解码缓存。进入游戏后停止试听。

原素材定位资料见 [IPA 拆包与素材定位说明](Docs/LegacyAssetNotes.zh-CN.md)。RESULT 背景的恢复图、原图以及生成记录见 [背景提示词](Tools/result-background-prompt.txt)。原 MV 分辨率仍为 320×480。

## 还原限制与工具

本项目以 Swift／SwiftUI／Objective-C++ 重新实现，使用用户提供 IPA 中的原始资源，不包含 SEGA 源码，也不是将原 armv7 可执行文件直接转换为 arm64。

原难度索引和事件分派表参考研究资料；`hello_planet` 五档有效假名数为 65／107／161／225／376，间奏点击数为 0／26／42／46／46。研究链接：[谱面研究](https://github.com/HachiMiku39/mikuflick_self-made_pattern)、[INPUT TIMING 换算证据](Docs/OriginalInputTiming.md)。

评分、连击、间奏、计量条、BTL、评级和延时换算有独立逻辑测试。原版触摸与音频延迟仍需实机对照，不能据此宣称原版手感完全一致。完整 Rainbow／Crimax 视觉、原解锁逻辑、重播、Twitter 和 Game Center 服务尚未完整复刻；所有歌曲的真人通关、真机性能和音频中断测试仍待完成。

主要源码与工具：

- `ChartLogic.swift`：难度操作码、灰色音符、间奏、评分和时间轴。
- `InterfacePreferences.swift`：语言、UI 文案、字体策略、播放顺序、评级及字幕校验。
- `UTFTable.swift`：读取 USM 的 CRI UTF 表。
- `NativeMedia.mm`：ZIP／RAR 解压和设备内媒体转换。
- `PackStore.swift`：SHA-1 校验、事务安装和歌曲注册。
- `Tools/import_ipa.py`：离线导出 IPA 的图集、CAF、谱面和双音轨 MP4，需要 Python 3、Pillow、ffmpeg。
- `Tools/verify_assets.py`：11 首基础歌曲的资源验证，需要 ffprobe。
- `Tests`：实际 Swift 解析、规则、界面基础逻辑和五语言字幕覆盖；命令见 [测试说明](Tests/README.md)。
- `Vendor/Sources`、许可证和 `Tools/build-codecs.sh`：最小 FFmpeg 库源码及重建配置，不启用 GPL／nonfree 编解码器。
