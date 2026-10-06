# 测试与交付说明

2026-10-06，v1.1.6（build 10）测试版。当前新增三张开屏和标题语音优先播放；历史版本新增 Developer 自动打歌、原版判定多边形与连击特效，修正 gauge 比例、主页及音量对齐；Legacy 间奏隐藏 gauge 并居中 TAP。普通 iPhone 固定竖屏，Duo 外屏及 iPad 保留横屏。三台模拟器测试和限制详见 [验证记录](VALIDATION.md)。

## 下载与运行

[1.1.6 发布页](https://github.com/HachiMiku39/mikuflick64/releases/tag/mikuflick64-v1.1.6-r4)提供以下文件：

- [MikuFlick64-1.1.6-build10-unsigned.ipa](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r4/MikuFlick64-1.1.6-build10-unsigned.ipa)：用于真实 64 位 iPhone／iPad，需用自己的账号或证书自行签名再安装。
- [MikuFlick64-Source.zip](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r4/MikuFlick64-Source.zip)：可编辑的 Xcode 源码工程。
- [SHA256SUMS.txt](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v1.1.6-r4/SHA256SUMS.txt)：下载文件校验清单。

真机 IPA 的系统要求为 iOS／iPadOS 17.0 或更高版本、arm64。iPhone Duo 的专用折叠形态 API 要求 iOS 27.1 或更高版本，真机折叠切换尚未测试。

解压源码包，用 Xcode 27.2 beta 2 打开 MikuFlick64.xcodeproj，Scheme 选择 MikuFlick64，选择目标设备后按 ⌘R。已有模拟器安装数据不会捆绑到源码包；用户已有的额外资源包保存在原应用沙盒。其他设备从“导入资源包”选择用户原有 Mov_99.zip 即可安装。

IPA 是未签名的设备构建，当前没有可用的项目开发团队签名。本轮尚未验证用户签名后的真机安装、触摸或音频表现；从源码运行到真机也需在 Xcode Signing 选择自己的开发团队。此前的交付包保留供对照。

## build 10 本轮变更

包名 `com.sbga.MikuFlick02`，1.1.6 build 10。新增 SBGA → Crypton → CRIWARE 开屏，每张 1 秒、点击跳下一张；SBGA 播放 SEGA 音效。主界面先完整播放标题语音，再启动 BGM；所有提前音乐请求统一排队。Duo 模拟器已复现并修复首次教程提前播放 BGM03，最终正常启动 BGM 比标题语音完成回调晚 25 ms。日志见 VALIDATION.md，本轮未重新进行三机型布局测试。

`jp.sbga.mikuflick` 的成绩与曲包不会自动迁移到新包名，请保留旧应用并自行重新导入需要的曲包。

## 历史变更

1.1.6 新增：Bundle ID `jp.sbga.mikuflick`；全部六语言难度、判定和 FAST／LATE 保持英语；打包校验明确验证完整的 11 首内置免费歌曲。新包名的首次启动、选歌、媒体播放、失败结算已在 iPhone Air 模拟器检查，全部六语言的英文术语通过逻辑测试。旧包名的数据不会自动迁入新沙盒。


r3 新增：iPhone 竖屏 Gauge 加宽、Legacy Logo 放大、选歌标题与返回按钮同高；RESULT OCR-A 标题与去字背景、1 秒黑屏渐变和逐位滚动成绩、点按跳过；唯一 OCR-A Loading...；修复 Duo 难度裁切、返回位置及 Book 结算按钮溢出。

- 歌曲加载和普通场景转场均为 1 秒；设置进入子选项或帮助章节、再从子页返回，双向都不转场。媒体尚未准备完成时，仍需等待实际加载结束。
- Legacy 选曲使用连续的新背景，将标题、歌曲 Logo、封面、歌曲信息、难度和底部操作分区摆放。Cover Flow 保持居中，底部仅保留 Play，返回操作使用系统导航位置。移除所有随机选曲、Shuffle 按钮及其播放功能，保留播放列表和循环。
- 各难度显示已保存的最高评级角标；无记录时不显示。难度文字恢复原版倒影和贯穿横线。文字采用固定可读字号，窄区域调整排列并遵守安全区，避免靠整体缩小文字解决空间不足。
- 设置页面采用连续的新背景，使用原音量、握持和键盘素材重新安排面板；字号放大，说明放在控件下方。横屏歌曲 loading 文字移到白色区域。
- 首页版权信息上移至安全位置，避免与底部区域互相遮挡。Duo 紧凑横屏收敛判定效果并调整暂停页；类 3DS 模式增加 MV 可用空间，麦克风计量条放在左下方竖向显示，分数、连击和暂停放到下方控制区域。

以上描述为交付实现范围，不代表八种设备形态都已完成全曲游戏测试。

## 八种形态

| 设备 | 需要人工检查的形态 |
|---|---|
| iPhone | 竖屏 |
| iPad | 横屏、竖屏 |
| iPhone Duo | 外屏横、外屏竖、内屏横、内屏竖、类 3DS |

各形态检查主页、Legacy Cover Flow 的封面中心和歌曲信息布局、唯一底部 Play 按钮、系统返回位置、现代 MV、游戏键盘、暂停和结算。切换歌曲和难度后，控件位置应保持稳定，较长的文字与最高分应可读。横屏现代键盘应在输入侧区域居中，Duo 横屏可使用现代布局。背景应覆盖屏幕底部；MV 原片保留比例后的两侧黑色留白属于影片区域。

切换场景检查 loading 最少展示 1 秒，设置与子页之间进入、返回都不展示；歌曲准备完成且最短展示时间结束后才开始媒体／谱面时钟。暂停页延时显示 ms，并按原游戏的 30 Hz／BPM 规则保留 21 档。Duo 外屏横向的输入时机面板须完整落在安全区内。

分别切换英语、日语、简体中文、法语、西班牙语和韩语（`en`／`ja`／`zh`／`fr`／`es`／`ko`）。日语仅显示原文，其他五种语言检查 MV 日语原文与译文第二行、横屏歌词侧栏以及拖动进度后的同步；缺少译文的句子只显示原文。

字幕覆盖当前 17 首曲目：每种翻译语言 479 句，五种译文共 85 个字幕轨道、2395 条译文。新增法语、西班牙语和韩语字幕标明为本项目 AI 翻译。额外曲包的影片仍需自行导入，模拟器沙盒中已安装的影片不随源码交付。

`MUSIC SELECT` 和 `OPTIONS` 保持固定大写英语，使用 OCR-A 字体；其余动态英文、法语、西班牙语及数字使用 Futura-Medium，动态中文使用苹方，韩文使用 iOS 系统韩文字体。全部六种语言中的判定、难度分类和 FAST／LATE 保留英语；素材内的文字保持原图。

## 导入与历史验证范围

用户提供的 Mov_99.zip 此前已在 iPad 完整安装，三首新歌元数据、谱面与双音轨文件通过检查。这是此前的安装结果，不等于本轮在全部设备重新安装通过。可继续检查三首 MV、伴奏、试听、Playlist／Loop 及重启后曲库。

RAR4／RAR5 原生提取此前通过本机样本测试；样本测试不能替代实际 RAR 曲包在 iOS 上的完整安装验证，仍需人工补测。

帮助有九章、六种语言；旧商店购买内容改为自助导入教程，并删除随机播放说明，所有界面统一用 MV。实际验证项目、布局冒烟检查结果和未覆盖范围见 `VALIDATION.md`。

Developer 默认关闭。开启路径：OPTIONS → DISPLAY → Developer，再返回 OPTIONS → DEVELOPER → Autoplay。测试成绩不保存。原版判定图形开关位于 DISPLAY，会禁用 FAST／LATE 的显示。

本地 IPA：`../MikuFlick64-1.1.6-r4/MikuFlick64-1.1.6-build10-unsigned.ipa`。


## build 8 性能工具

Developer 子页新增 Performance overlay 和 Apple Metal HUD，默认均关闭。悬浮窗显示本应用 CPU／RAM／UI FPS；100% CPU 表示一个核心。点击标题收起，× 关闭，窗外仍可点按游戏；进入后台停止采样，前台重建基线。关闭 Developer 会同步关闭 Autoplay 与两种性能工具。

Apple Metal HUD 开关改变后需重启。当前 SwiftUI／AVPlayer 不保证提供支持的 Metal 表面；本轮没有观测到官方 GPU 面板，GPU 百分比显示 —，不能作为 GPU 实测结果。UI FPS 是显示回调频率，不能代替 GPU 渲染 FPS 或影片帧率。模拟器指标不能代表真机性能。GPU 深入分析用 Xcode Instruments → Metal System Trace。

已检查 Air 游戏、暂停、后台恢复，以及 iPad 横竖屏、Duo 外屏／展开横竖和 Book 页面安全区；自动拖动观测到零位移，未计入通过。请在签名安装后的设备上复核真实触摸拖动、窗口移动后的形态切换、长期采样与 GPU 工具表现。

## ProMotion（build 8）

最高请求 120 Hz，实际随屏幕能力及系统调度降低。iPhone 启用高刷新 Info.plist 开关；游戏画面时钟由固定 60 Hz 的播放器观察器改为显示同步回调，每次仍读 AVPlayer 媒体时钟，30 Hz 谱面判定不变，影片不补帧。暂停／拖动／后台停止更新；恢复重新配置。HUD 的 UI FPS 是显示回调频率。

真机验收：在支持 ProMotion 的 iPhone／iPad 开启 Performance overlay，进入游戏查看 UI FPS（最高 120），切换低电量、暂停／恢复、后台／前台；分别检查普通手动输入及自动打歌的判定、轨道、音视频同步。模拟器检查不能代替真机 120 Hz 验证。
