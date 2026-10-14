# MikuFlick64

**Miku Flick 02 的 64 位重写版 · A 64-bit rewrite of Miku Flick 02**

**1.1.6（build 13）现已正式发布 / 1.1.6 (build 13) is now released — 2026-10-15.**

在现代 iPhone、iPad 和 Apple Silicon Mac 上重现假名滑动音游与 MV 播放。项目以 Swift、SwiftUI 和 Objective-C++ 重新实现，沿用原版谱面、影片、图像与音效，并加入自适应布局、多语言字幕和设备内曲包导入。这是独立的非官方重写项目。

Play the kana-flick rhythm game and watch music videos on modern iPhone, iPad and Apple silicon Macs. Reimplemented in Swift, SwiftUI and Objective-C++, MikuFlick64 uses the original charts, movies, artwork and sound effects, with adaptive layouts, multilingual lyrics and on-device song-pack imports. This is an independent, unofficial rewrite.

[正式版下载 / Download release](https://github.com/HachiMiku39/mikuflick64/releases/tag/mikuflick64-v1.1.6-r7) · [问题反馈 / Report an issue](https://github.com/HachiMiku39/mikuflick64/issues) · [验证记录 / Validation](VALIDATION.md)

## 下载与安装 / Download and installation

| 项目 / Item | 内容 / Details |
| --- | --- |
| 版本 / Version | **1.1.6 · build 13** |
| iPhone / iPad | **iOS / iPadOS 17.0+ · arm64** |
| Mac | **macOS 14.0+ · Apple Silicon (M1+) · Mac Catalyst** |
| 包名 / Bundle ID | `com.sbga.MikuFlick02` |
| 安装包 / App package | [MikuFlick64-1.1.6-build13-unsigned.ipa](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r7/MikuFlick64-1.1.6-build13-unsigned.ipa) |
| Mac 安装包 / Mac package | [MikuFlick64-1.1.6-build13-macos-arm64.dmg](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r7/MikuFlick64-1.1.6-build13-macos-arm64.dmg) |
| 完整源码 / Full source | [MikuFlick64-Source.zip](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r7/MikuFlick64-Source.zip) |
| 下载校验 / Download checksums | [SHA256SUMS.txt](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r7/SHA256SUMS.txt) · [MAC-VALIDATION.json](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r7/MAC-VALIDATION.json) · [IPA-VALIDATION.json](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r7/IPA-VALIDATION.json) |

**安装说明：**IPA 未签名，需要使用自己的有效 iOS 签名证书和描述文件签名后安装。直接打开 IPA 不会安装应用。源码工程可在 Xcode 中打开 `MikuFlick64.xcodeproj`，选择 `MikuFlick64` Scheme；真机运行需选择自己的开发团队。build 13 使用 Xcode 27.2 beta 2 构建。

**Installation:** The IPA is unsigned. Sign it with your own valid iOS signing identity and provisioning profile before installing; opening the IPA alone will not install it. To build from source, open `MikuFlick64.xcodeproj` in Xcode, select the `MikuFlick64` scheme and your signing team for a physical device. Build 13 was built with Xcode 27.2 beta 2.

安装包包含 **11 首基础歌曲**的影片、谱面、预听、封面和歌曲 Logo。额外 DLC 音视频不随包发布，需自行导入。build 13 新增 Mac Catalyst 安装包，iOS 的判定和曲包逻辑保持原样。

The app includes movies, charts, previews, covers and title artwork for **11 built-in songs**. Additional DLC audio and video are imported separately. Build 13 adds a Mac Catalyst package while retaining the iOS judgement and pack-import logic.

**Mac 安装：**打开 DMG，将应用拖入 Applications。顶部按钮切换 **iPad 横屏（4:3）/ iPad 竖屏（3:4）**并记住选择；切换时保留播放与暂停状态，窗口缩放保持比例。Mac 包为 ad hoc 签名，**尚无 Developer ID 签名及公证**；首次打开可能需要你通过系统“隐私与安全性”批准。完整说明及构建命令见 [Mac 使用说明](Docs/MacSupport.md)。不提供 Intel Mac 包。

**Mac installation:** Open the DMG and drag the app to Applications. The top buttons select and remember **iPad Landscape (4:3) / iPad Portrait (3:4)**, preserving playback and pause state. Window resizing preserves the canvas ratio. The Mac package is ad hoc signed, **without Developer ID signing or notarization**; macOS may require your approval under Privacy & Security on first launch. See [Mac support](Docs/MacSupport.md) for installation and build instructions. There is no Intel Mac package.

## 主要功能 / Features

| 功能 | Features |
| --- | --- |
| 沿用原版五档谱面：EASY、NORMAL、HARD、EXTREME、BREAK THE LIMIT（BTL），保留原版 30 Hz 判定规则。 | Original five-difficulty charts: EASY, NORMAL, HARD, EXTREME and BREAK THE LIMIT (BTL), retaining the researched 30 Hz judgement rules. |
| Legacy Cover Flow、SEKAI-A 网格、SEKAI-B 列表三套选曲界面，适配 iPhone、iPad 与 Duo 安全区。 | Legacy Cover Flow, SEKAI-A grid and SEKAI-B list selection layouts, with iPhone, iPad and Duo safe-area handling. |
| Mac 顶部按钮切换 iPad 横屏与竖屏，保持固定画布比例和当前游戏状态。 | Mac buttons select iPad landscape or portrait while preserving canvas proportions and session state. |
| 英、日、简中、法、西、韩六种 UI；难度、判定与 FAST / LATE 在所有语言下保持英语。 | Six interface languages: English, Japanese, Simplified Chinese, French, Spanish and Korean. Difficulty, judgement and FAST / LATE labels remain English. |
| 71 首歌曲的五语言译文，共 355 份字幕文件、10,040 条译文；已覆盖 DLC 导入后自动匹配。 | Five translated lyric languages for 71 songs: 355 subtitle files and 10,040 translated cues, automatically matched after importing supported DLC. |
| 独立 MV 模式、播放列表、循环、歌词、伴奏音轨与现代界面进度拖动。 | Separate MV playback, playlists, looping, lyrics, instrumental tracks and seeking in the modern MV interface. |
| ZIP / RAR / 文件夹导入，设备内转换 USM；并发下载列表显示速度、进度和状态，完成后排队安装。 | ZIP, RAR and folder imports with on-device USM conversion; concurrent downloads show speed, progress and status, then queue installation. |
| 原版假名字图、原版判定样式、麦克风计量条、连击特效及歌曲独立 INPUT TIMING 调整。 | Original kana glyphs, optional original judgement graphics, microphone gauge, combo effects and per-song INPUT TIMING calibration. |
| 支持设备最高请求 120 Hz；Developer 提供自动演奏和 CPU / RAM / UI FPS 悬浮窗。自动演奏成绩不保存。 | Requests up to 120 Hz on supported displays; Developer options offer autoplay and a CPU / RAM / UI FPS overlay. Autoplay scores are not saved. |

实际刷新率受硬件、系统设置、低电量和温度影响。UI FPS 不等同于影片帧率或 GPU 渲染帧率。普通 iPhone 固定竖屏；iPad 和 Duo 保留横竖屏。Duo 专用折叠适配需要 iOS 27.1+，真机折叠切换尚未验证。

Actual refresh rate depends on hardware, system settings, power and temperature. UI FPS is not movie FPS or GPU rendering FPS. Ordinary iPhones use portrait orientation; iPad and Duo retain rotation. Duo-specific folding support requires iOS 27.1+; physical folding transitions have not been validated.

## 曲包与歌词 / Song packs and lyrics

在主菜单 **RESOURCE PACK INPUT** 中选择 ZIP / RAR 或文件夹，或打开下载列表粘贴 HTTPS 压缩包直链或 GitHub Release 页面。兼容原版 `Mov_<编号>` 目录，允许外层包含 `InstallData`，`Thum_<编号>` 为可选缩略图目录。保留原文件名；不支持加密或分卷压缩包。

Open **RESOURCE PACK INPUT** from the main menu to select a ZIP, RAR or folder, or paste an HTTPS archive link or GitHub Release page into the download list. Original `Mov_<number>` folders are supported, optionally wrapped in `InstallData`, with optional `Thum_<number>` artwork folders. Keep original filenames. Encrypted and split archives are not supported.

下载在应用前台时进行，暂停续传取决于服务器支持。原压缩包保存在应用 `Documents/Downloads`，可从系统“文件”应用管理；多个下载可并行，转换和注册按顺序完成。build 12 修复了真机 `/var` 与 `/private/var` 路径别名导致的解压校验误报；失败任务可点击 **Resume / retry** 复用已下载文件。

Downloads run while the app is in the foreground; resume support depends on the server. Archives remain in the app's `Documents/Downloads` folder and are accessible through Files. Downloads can run concurrently, while conversion and registration run sequentially. Build 12 fixes archive-path validation for iOS `/var` and `/private/var` aliases; use **Resume / retry** to reuse an already-downloaded file.

日语显示原文，其他支持语言显示原文与对应译文。DLC 译文已随应用提供，影音需另行导入；用户字幕可通过导入 JSON 覆盖。译文含项目自译及 AI 翻译，来源、格式与覆盖范围见[歌词翻译说明](Docs/LyricTranslations.md)。

Japanese displays the original lyrics; other supported languages display the original and its translation. DLC translations are bundled, while DLC media is imported separately. Imported subtitle JSON can override bundled translations. Translations include project-authored and AI-generated work; see the [lyric translation guide](Docs/LyricTranslations.md) for attribution, format and coverage.

## 验证与已知限制 / Validation and known limits

已完成 iPhone Air、iPad Pro、iPhone Duo 模拟器的阶段性布局与运行检查。Duo 模拟器曾实际导入 20 个曲包、60 首 DLC，并验证 ZIP / RAR 并行下载。build 12 在 iPad Pro 11-inch (M4) / iPadOS 27.2 真机验证了从 GitHub 下载的 `Mov_1.zip` 三首歌曲完整安装及重复导入。

Staged layout and runtime checks were performed on iPhone Air, iPad Pro and iPhone Duo simulators. The Duo simulator imported 20 packs containing 60 DLC songs and exercised concurrent ZIP / RAR downloads. Build 12 was verified on a physical iPad Pro 11-inch (M4) running iPadOS 27.2, including installation and reimport of all three songs from the GitHub-downloaded `Mov_1.zip`.

IPA 的 arm64 iPhoneOS 平台、版本、11 首内置资源 SHA-256 和压缩完整性已校验。正式发布不代表全部设备、全部曲包或全曲人工通关验收：真机触摸与音频延迟、稳定 120 Hz 和部分原版视觉细节仍需继续验证；原版重播、解锁、Twitter 和 Game Center 服务未完整复刻。Mac 版在 Apple Silicon / macOS 27.2 上验证横竖屏主页、选曲、设置、游戏、暂停及模式切换；NORMAL 全曲自动演奏为 83 COOL、42554 分，测试成绩未保存。DMG 内应用启动及签名校验通过。Mac 曲包导入、下载、真机 120 Hz、Intel Mac 和 macOS 14 的实际运行尚未验收。

The IPA's arm64 iPhoneOS platform, version, built-in song resource hashes and archive integrity have been checked. Release status does not imply complete testing of every device, pack or manually played chart. Physical touch/audio latency, sustained 120 Hz and some original visual details need further validation; original replay, unlock, Twitter and Game Center services are not fully reproduced. The Mac app was checked on Apple silicon / macOS 27.2 across both layouts, navigation, gameplay, pause and live layout switching. A full NORMAL autoplay produced 83 COOL and 42,554 points without saving the score. The app inside the DMG launched and passed signature checks. Mac pack importing/downloading, hardware 120 Hz, Intel Macs and actual operation on macOS 14 have not been validated.

旧包名 `local.rewrite.MikuFlick64` / `jp.sbga.mikuflick` 的成绩与导入曲包不会自动迁移到 `com.sbga.MikuFlick02`。签名工具若改写包名，安装标识以工具设置为准。

Scores and imported packs from the old `local.rewrite.MikuFlick64` / `jp.sbga.mikuflick` identifiers do not automatically migrate to `com.sbga.MikuFlick02`. Signing tools may change the installed bundle identifier.

## 文档与反馈 / Documentation and feedback

- [验证记录 / Validation history](VALIDATION.md)
- [交接与人工检查事项 / Handoff and manual checks](HANDOFF.md)
- [原版特效研究 / Original effect research](EFFECTS-RESEARCH.md)
- [INPUT TIMING 研究 / Input timing research](Docs/OriginalInputTiming.md)
- [原版假名字图 / Original kana glyphs](Docs/OriginalNoteGlyphs.md)
- [测试说明 / Test instructions](Tests/README.md)
- [问题反馈 / GitHub Issues](https://github.com/HachiMiku39/mikuflick64/issues)

反馈时请附版本、设备、系统、页面 / 方向 / 折叠形态、复现步骤与截图；导入问题请注明曲包格式及错误阶段。

Please include the version, device, OS, page/orientation/folding state, reproduction steps and screenshots. For import problems, include the pack format and the failing stage.

本仓库提供介绍、研究与验证文档；完整可编辑工程、工具和第三方库源码 / 重建说明位于 Release 的 `MikuFlick64-Source.zip`。项目不是原 armv7 可执行文件的直接转换，不包含 SEGA 源码；原始游戏内容的权利归各自权利方所有。

This repository hosts the introduction, research and validation documents. The complete editable project, tools and third-party library source/rebuild notices are in the release's `MikuFlick64-Source.zip`. This is a reimplementation rather than a direct conversion of the armv7 executable and does not contain SEGA source code. Original game content remains the property of its respective rights holders.
