# 测试说明

以下命令在工程根目录执行，使用 macOS 的 Swift 编译器和 Python 3。模块缓存和测试程序放在临时目录；这些测试直接编译交付的 Swift 逻辑。测试范围与实际运行记录分别见本文件和工程根目录的 `VALIDATION.md`。

## 无需原始 USM 的逻辑与字幕测试

```sh
mkdir -p /tmp/miku-module-cache
swiftc -module-cache-path /tmp/miku-module-cache \
  ChartLogic.swift Tests/RulesTests.swift -o /tmp/miku-rules-tests
/tmp/miku-rules-tests

swiftc -module-cache-path /tmp/miku-module-cache \
  InterfacePreferences.swift Tests/InterfaceTests.swift -o /tmp/miku-interface-tests

swiftc -module-cache-path /tmp/miku-module-cache \
  DisplayRefresh.swift PerformanceMetrics.swift Tests/PerformanceTests.swift -o /tmp/miku-performance-tests
/tmp/miku-performance-tests
/tmp/miku-interface-tests

swiftc -module-cache-path /tmp/miku-module-cache \
  InterfacePreferences.swift Tests/SubtitleAssetsTests.swift -o /tmp/miku-subtitle-tests
/tmp/miku-subtitle-tests GameAssets Tests/Fixtures/lyric-cues.json

swiftc -module-cache-path /tmp/miku-module-cache \
  PackDownloads.swift Tests/DownloadTests.swift -o /tmp/miku-download-tests
/tmp/miku-download-tests
```

`RulesTests.swift` 覆盖：

- 31 个时序边界、按下／释放、正确按键与滑动方向。
- −10…+10 的 BPM／30 Hz 延时换算、重复毫秒档位、BPM 变化。
- 假名操作方向、难度启用掩码、间奏掩码、谱面滚动提前量。
- 同时存在的按键提示、晚判窗口的最近音符顺序、媒体时钟控制的效果生命周期。
- 基础分、连击奖励、Crimax 额外得分、独立间奏、BTL、评级和失败条件。

`InterfaceTests.swift` 覆盖：

- 六语言 `en`／`ja`／`zh`／`fr`／`es`／`ko` 的系统优先序；繁简中文归一、地区代码及不支持语言的回落。
- UI 六语言覆盖、格式化文案、全部六语言的难度、模式、判定及 FAST／LATE 名称保留英语，以及 Futura／PingFang／韩文系统字体策略。
- 三种键盘设置、引导方向和花瓣素材索引。
- MV 去重、顺序连播、停止、重开、循环、单曲及空列表；最高评级选择。没有 Shuffle 功能或对应测试。
- 字幕双行查找、日语只显示原文、缺失译文、Unicode／空白归一和标点区分。
- 字幕 JSON 往返，以及已安装歌曲 ID、路径、语言、来源网址、版本、重复轨道和冲突原文键的校验。

`PerformanceTests.swift` 覆盖真实进程 CPU／RAM 获取、单核心与多核心 CPU 百分比、显示回调频率（120／60／10 Hz 及动态切换）、设备刷新率上限、无效／缺失／回退时钟样本，以及后台恢复后重置基线。它不验证 GPU 占用或 SwiftUI 实际渲染帧率。

`SubtitleAssetsTests.swift` 逐份解码 355 个字幕文件，覆盖 71 首歌 × 英语／简体中文／法语／西班牙语／韩语。每种语言核对 2008 条游戏歌词 cue，合计 10,040 条译文，检查非空、曲目 ID、语言、署名、无多余原文键及日语模式不返回译文。原文清单为 `Tests/Fixtures/lyric-cues.json`。

## 原始谱面与媒体资源测试

这一组需要原始 USM 和 ffprobe。`Tools/import_ipa.py` 可从用户提供的原 IPA 导出基础曲资源和 USM；请为原文件保留备份。将原始 USM 目录和 Mov_11 中的 `hello_planet.usm` 路径替换为本机实际位置：

```sh
swiftc -module-cache-path /tmp/miku-module-cache \
  ChartLogic.swift UTFTable.swift Tests/ChartTests.swift -o /tmp/miku-chart-tests
/tmp/miku-chart-tests "$PWD" "/path/to/original-usm" \
  "/path/to/Mov_11/hello_planet.usm"
python3 Tools/verify_assets.py
```

`ChartTests.swift` 检查 11 首原始 USM 与导出 JSON 的每条事件一致、五档难度分派、有效假名和间奏数量、操作码范围、按键行、灰色音符及时间偏移。`hello_planet` 的有效假名数应为 65／107／161／225／376，间奏数为 0／26／42／46／46。

`verify_assets.py` 检查 11 首基础曲、6818 个 cue、5280 个假名事件，核对事件顺序、时间戳、文件头、封面、H.264 视频及双 AAC 音轨，并验证键盘覆盖的假名。它不检查导入到模拟器沙盒中的额外曲包。

## 原生构建与人工测试

用 Xcode 构建 `MikuFlick64` Scheme。以上命令不编译完整 UIKit／SwiftUI 应用，也不能证明布局、压缩包安装流程、播放拖动或音频在设备上的实际表现。

历史 r2 布局及本轮 build 11 下载／导入验证详见 `VALIDATION.md`。冒烟检查不等于每种形态完成全曲游戏、全部菜单、媒体播放和安装验收；本文件不将八种形态全部标为 PASS。

完整形态验证交由用户人工执行：iPhone 竖屏，iPad 横／竖屏，以及 iPhone Duo 外屏横／竖、内屏横／竖、类 3DS 共五种形态。重点检查：

- 歌曲加载与普通转场为 1 秒，设置子菜单无转场；未准备完成的媒体仍等待实际加载。
- 连续选曲／设置背景与固定可读字号，歌曲或难度变化后不应引起控件跳位；Cover Flow、最高评级角标、底部唯一 Play 按钮及系统返回位置。
- 六语言文字排版与安全区。`MUSIC SELECT`／`OPTIONS` 固定大写 OCR-A、不翻译；其他英文、法语、西班牙语及数字为 Futura，中文为 PingFang，韩文为系统韩文字体。
- 五种译文语言各 71 首、2008 句的 MV 双行字幕、横屏侧栏、进度拖动和字幕同步；日语只显示原文。
- 移除随机选曲及 Shuffle 后的播放列表／循环；版权安全位置、Duo 外屏暂停面板，以及类 3DS 模式更大的 MV 区域、左下竖向计量条和下方分数／连击／暂停。

用户提供的 Mov_99.zip 在此前 iPad 会话中完整安装，曲目元数据、谱面和双音轨通过检查；本轮没有据此宣称全部设备的安装重新验证通过。RAR4／RAR5 提取有本机样本测试记录，build 11 已在 Duo 模拟器实际下载并完整导入 Mov_98.rar，媒体检查通过。build 12 在物理 iPad Pro 11-inch (M4) 上验证 Mov_1 ZIP 下载后的完整安装；其他曲包和设备未因此标为真机通过。

交付物包含 build 12 源码和未签名的 arm64 iPhoneOS IPA；IPA 需要自行签名后安装。额外曲包影片保存在具体模拟器的应用沙盒中，不随源码包复制。完整交接内容见工程根目录 `HANDOFF.md`。


`DownloadTests.swift` 覆盖 HTTPS ZIP/RAR 直链、GitHub blob 直链转换、发布页和包含斜杠的 tag、非法协议／页面路径、未知文件大小、百分比边界与任务记录编解码。实际 Duo 并行下载、暂停续传、404、ZIP/RAR 自动串行导入记录见 `VALIDATION.md`；不使用模拟下载代替运行验证。

## 真机解压路径回归（build 12）

运行 `python3 Tests/ArchiveTests.py`。脚本直接编译交付的 `MFExtractArchive` 函数并使用真实 ZIP 文件，检查目录别名、末尾斜杠、嵌套 Mov/Thum 目录，以及绝对路径、../、反斜杠越界、归档符号链接和已存在的符号链接拒绝。它使用 macOS libarchive，不能替代物理 iOS 导入；物理 Mov_1 安装结果见 VALIDATION.md。
