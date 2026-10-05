# 人工适配测试交接

2026-10-05，v0.2.0 r2 测试版交付。此次更新包含新一轮布局调整和法语、西班牙语、韩语本地化，并提供供用户自行签名的真机 IPA。原生构建、布局冒烟检查及其限制详见 [验证记录](VALIDATION.md)；八种形态的完整游戏与适配验收仍交由用户人工执行。按用户要求，完成打包发布后停止自动适配操作。

## 下载与运行

[r2 发布页](https://github.com/HachiMiku39/mikuflick64/releases/tag/mikuflick64-v0.2.0-r2)提供以下文件：

- [MikuFlick64-0.2.0-build3-unsigned.ipa](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v0.2.0-r2/MikuFlick64-0.2.0-build3-unsigned.ipa)：用于真实 64 位 iPhone／iPad，需用自己的账号或证书自行签名再安装。
- [MikuFlick64-Source.zip](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v0.2.0-r2/MikuFlick64-Source.zip)：可编辑的 Xcode 源码工程。
- [MikuFlick64-iOS-Simulator-arm64.zip](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v0.2.0-r2/MikuFlick64-iOS-Simulator-arm64.zip)：用于 Apple Silicon Mac 的 iOS 模拟器，不能作为真机 IPA 安装。
- [SHA256SUMS.txt](https://github.com/HachiMiku39/mikuflick64/releases/download/mikuflick64-v0.2.0-r2/SHA256SUMS.txt)：下载文件校验清单。

真机 IPA 的系统要求为 iOS／iPadOS 17.0 或更高版本、arm64。iPhone Duo 的专用折叠形态 API 要求 iOS 27.1 或更高版本，真机折叠切换尚未测试。

解压源码包，用 Xcode 27.2 beta 2 打开 MikuFlick64.xcodeproj，Scheme 选择 MikuFlick64，选择目标设备后按 ⌘R。已有模拟器安装数据不会捆绑到源码包；当前 iPad 已安装的 Mov_99 保存在该模拟器应用沙盒。其他设备从“导入资源包”选择用户原有 Mov_99.zip 即可安装。

IPA 是未签名的设备构建，当前没有可用的项目开发团队签名。本轮尚未验证用户签名后的真机安装、触摸或音频表现；从源码运行到真机也需在 Xcode Signing 选择自己的开发团队。此前的交付包保留供对照。

## 本轮变更

- 歌曲加载和普通场景转场均为 1 秒；设置进入子选项或帮助章节、再从子页返回，双向都不转场。媒体尚未准备完成时，仍需等待实际加载结束。
- Legacy 选曲使用连续的新背景，将标题、歌曲 Logo、封面、歌曲信息、难度和底部操作分区摆放。Cover Flow 保持居中，底部仅保留 Play，返回操作使用系统导航位置。移除所有随机选曲、Shuffle 按钮及其播放功能，保留播放列表和循环。
- 各难度显示已保存的最高评级角标；无记录时不显示。难度文字恢复原版倒影和贯穿横线。文字采用固定可读字号，窄区域调整排列并遵守安全区，避免靠整体缩小文字解决空间不足。
- 设置页面采用连续的新背景，使用原音量、握持和键盘素材重新安排面板；标题下移、字号放大，说明放在控件下方。横屏歌曲 loading 文字移到白色区域。
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

`MUSIC SELECT` 和 `OPTIONS` 保持固定大写英语，使用 OCR-A 字体；其余动态英文、法语、西班牙语及数字使用 Futura-Medium，动态中文使用苹方，韩文使用 iOS 系统韩文字体。中文界面中的判定、难度分类和 FAST／LATE 保留英语；素材内的文字保持原图。

## 导入与历史验证范围

用户提供的 Mov_99.zip 此前已在 iPad 完整安装，三首新歌元数据、谱面与双音轨文件通过检查。这是此前的安装结果，不等于本轮在全部设备重新安装通过。可继续检查三首 MV、伴奏、试听、Playlist／Loop 及重启后曲库。

RAR4／RAR5 原生提取此前通过本机样本测试；样本测试不能替代实际 RAR 曲包在 iOS 上的完整安装验证，仍需人工补测。

帮助有九章、六种语言；旧商店购买内容改为自助导入教程，并删除随机播放说明，所有界面统一用 MV。实际验证项目、布局冒烟检查结果和未覆盖范围见 `VALIDATION.md`。
