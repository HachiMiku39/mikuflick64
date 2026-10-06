# 1.1.6 build 10 — startup audio order

The first-run tutorial's `onAppear` requested BGM03 before the home screen requested the title announcement. The previous gate only started on the home screen, allowing tutorial music to play early.

MenuMusic now gates every music request from application launch until the title announcement's AVAudioPlayer completion callback. The first visible home screen replaces the queued tutorial music with BGM01. Requests made during the voice remain queued. Background suspension also pauses the title voice.

Actual iPhone Duo simulator runs:

- Before fix, forced first-run tutorial: HOME at uptime 30408.455; BGM03 START at 30408.592, before the title voice started. Reproduced.
- After fix, forced first-run tutorial: BGM03 DEFER at 30553.379; TITLE START at 30557.946 (source duration 2.403 seconds); TITLE END at 30560.383; BGM01 START at 30560.400. No BGM START before TITLE END. Passed.
- Final source with normal startup, compiled and actually launched: HOME at 30645.362; TITLE START at 30646.434; TITLE END at 30648.873; BGM01 START at 30648.898. The voice ran for 2.439 seconds; BGM started 25 milliseconds after the completion callback. Passed.
- The tutorial replay and timed dismissal were temporary test setup; both are removed from the final source. User preferences, scores, and songs were not reset.

Debug builds retain MenuAudio timestamps for diagnosing playback order; release builds do not print them. Device archive and IPA packaging are recorded below.
## build 10 device archive and packaging

Xcode native Archive succeeded on 2026-10-06 at 18:48. Organizer shows iOS App Archive, arm64, com.sbga.MikuFlick02, 1.1.6 (10). IPA packaging independently checked the Mach-O iOS device platform, unsigned status, source-matching SHA-256 for all 11 built-in movies/charts/previews/covers/titles and launch logos/audio, 85 subtitle tracks, and ZIP CRC.

IPA: 421405375 bytes; SHA-256 `9707e22fa59291e8ade2e5c734b8e4adf60c7b23f72805d75ed346c95e96e40f`. Requires user signing for device installation; no signed physical-device test in this round.



---

# Miku Flick 64 验证记录

初次验证：2026-10-04；补充验证：2026-10-05，Asia/Shanghai。

> 最新版本为 1.1.6（build 8），验收范围以文末 build 7 性能工具与 build 8 ProMotion 检查为准。以下按时间保留历史记录：旧版三秒转场、英／中两种译文（34 条字幕轨）、三语言帮助，以及上轮交接后停止适配测试的描述，均已被 r2 的实现或后续检查覆盖。r2 转场为一秒，设置子菜单进入、返回都不转场；六种界面语言、85 条字幕轨已完成静态校验。设备实屏检查仍是布局冒烟，不能等同于八种形态全部完成打歌验收。

## 环境与构建

Xcode 27.2 beta 2（27B5028f），iPhone 18 Pro / iOS 27.2，模拟器 UUID `A552F9AD-365E-4D7E-BD1D-2BBB9FB1A421`。Xcode 原生构建、安装与启动成功。应用和内置原生库均为 arm64 64 位。设备版媒体静态库亦按 arm64 iPhoneOS SDK 构建；未在真实 iPhone 上运行，未导出签名真机 IPA。

## 谱面与资源

`asset-verification.txt`：11 个视频均有 H.264 画面和两条 AAC 音轨；6,818 条事件、5,280 条前缀 0 假名事件，表头、顺序、时间范围和封面通过。

`chart-verification.txt`：直接编译运行交付的 `ChartLogic.swift` 和 `UTFTable.swift`。Swift 解析的全部 6,818 事件与 Python 导出 JSON 逐条一致；五难度操作码范围、假名所属行、SetDelay、灰色音符及间奏单键映射通过。

《＊ハロー、プラネット。》五难度有效假名数 65／107／161／225／376，间奏点击数 0／26／42／46／46，与参考研究的已知结果吻合。

## 实际 Release 谱包安装

直接从用户指定仓库的 Releases 下载 `Mov_11.zip`（298,640,790 bytes；以下载文件实测为准）。ZIP 内三首 USM、三个 ADX、封面和校验清单全部保留，7 条 SHA-1 校验通过。

通过应用自身 `PackStore.importArchive` 入口，完成系统 libarchive 解压、Swift CuePoint 解析、设备内 FFmpeg 解码／AVFoundation 编码、封面裁剪、事务提交与自动歌曲注册。界面显示 “Song pack installed”，曲库从 11 首变为 14 首。

| 新增歌曲 | 谱面事件 | 视频时长 | 音轨时长 |
|---|---:|---:|---:|
| ダブルラリアット | 637 | 211.80 秒 | 214.000181 秒 ×2 |
| from Y to Y | 464 | 195.95 秒 | 196.000363 秒 ×2 |
| ＊ハロー、プラネット。 | 505 | 203.75 秒 | 204.000363 秒 ×2 |

转换后三个文件均经 ffprobe 确认一条 H.264 与两条 AAC。视频帧数与原 MPEG-1 解码帧数核对一致；视频与音轨末尾长度差来自原文件。

《＊ハロー、プラネット。》MV 实际持续播放到 01:16，显示日语歌词；卡拉 OK 实际持续播放到 02:19，显示歌词且未出现播放错误。卡拉 OK 构造的播放素材仅选择第二条伴奏音轨；普通播放使用人声与伴奏两轨。

重建并重新启动后曲库仍有 14 首。模拟器应用数据容器 UUID 发生变化后仍能读取 DLC，验证了相对路径持久化。

## 压缩格式与界面

原生解压器使用 libarchive 官方测试样本分别验证 ZIP、RAR4、RAR5（RAR4 binary_data、RAR5 compressed）。RAR4／RAR5 测试在本机使用同份 NativeMedia.mm 执行；完整 iOS 安装实测使用上面的 Release ZIP。尚未完整下载测试 Release 的 Mov_98.rar。

英文、日语、简体中文选曲界面均已实际切换。三页教程可以依次阅读并进入游戏；设置页仅有 MUSIC、SFX 与 FAIL SOUND；系统 ZIP/RAR 文件选择器能够打开。

A 类假名键盘与 B 类假名＋罗马字键盘已实测。调整 B 模式视频区域后，十个按键和罗马字全部在屏幕内。

《右肩の蝶》开头按原谱面直接进入大点击按钮；通过实际按钮输入获得 FINE 和 700 分，随后能回到假名键盘。已修复开头间奏默认显示假名键盘的问题。

《＊ハロー、プラネット。》NORMAL 完整播放到结算，零输入时显示 MISS 133，恰好等于 107 个有效假名加 26 个点击音符；灰色音符未计入 MISS。

临时自动导入测试入口及启动参数已从最终源码和 Scheme 移除。

## 未验证

原版实机逐帧时序与评分一致性、所有歌曲完整手动通关、所有 DLC 包、真实设备触摸／音频延迟、长期内存和耗电。密集假名在单行音符轨道上可能重叠，旧版视觉和高潮动画尚未完整复原。


## 2026-10-05 UI 与规则更新

之前记录的 ±75 ms／1000 分原型评分及 A／B 两键盘已由当前实现替换。规则测试通过 31 个 30 Hz 判定边界、释放取较差判定、方向／行、评分连击、间奏、BTL、Rank 和失败逻辑。资源校验再次通过 11 首影片、6818 条 Cue 和 133 种假名。

iPhone Duo / iOS 27.1 实际构建运行：合拢横屏主菜单为底部 2×2，Logo 左上，背景等比铺满且顶部对齐，头部完整；SEKAI-A 横屏目录与五档难度同时显示。EASY 零输入在 00:29 进入 E 评级结算，Mic Gauge 为 0；评级后出现 FAILED，全部 11 项统计及重开／返回按钮在安全区内，无纵向滚动。

新增用户实机确认的音效映射：SE03 同时切歌与 SAD；SE09 暂停继续；SE05_01 结算评级；SE11 评级后失败；SE12 A／B／C 评级、SE13 S、SE14 AP。前四份与用户提供 CAF 的 SHA-1 一致。通过选歌 ID 变化触发切歌音，首次进入与同曲重选不触发；结算音只在引擎完成状态转换时触发，延迟评级提示带 generation 校验。尚未通过录音比对验证声音时序与原版一致性。

2026-10-05 补充：原版 UI plist 共 425 帧切分完成；Duo 合拢横屏实际运行确认十键保持 127:108 的比例，Score／Combo 在 MV 左侧。按用户最新要求，音游判定／数字使用现代字体，结算评级使用 UI_tex_02（数字随后按用户指示统一改为系统字体），轨道与竖屏 Mic Gauge 保留原版素材。尚需对最终竖屏与 iPad 布局继续实测。

2026-10-05 音频修复：BGM01／03／05 和 SE02_01 已实际复制到 GameAssets；Apple afinfo 确认 CAF 可读，11 份 AAC 试听经 ffprobe 检查。音频专用 ADX 原生解码测试通过：pv_001_lp 26.388753 秒，生成 CAF 与 ffmpeg 参考解码的 PCM 逐字节一致。iPad Pro 11-inch (M5) iPadOS 27.2 经 Xcode 构建运行成功，主页和选曲可正常进入。未进行扬声器听感或录音时序比对。


## 2026-10-05 最终交付与 Mov_99 安装

当前版本覆盖上面旧记录的字体选择：所有动态英语和数字为 Futura-Medium，简体中文按字重使用 PingFang SC；原图集内文字保持图集。Legacy 按钮的外框、图标和文字按渲染中心对齐，保持统一等比缩放；横屏键盘使用现代分区布局并在输入区域内居中。根背景延伸至安全区外，避免底部露出黑色窗口背景。

英语和简体中文译文各覆盖 17 首歌曲、479 条不同歌词提示（11 首内置、Mov_11 三首及 Mov_99 三首）。实际交付 Swift 代码的字幕资源测试通过 958 次非空翻译检查，歌曲／语言／署名验证通过，日语模式仍只返回原文。译文为项目自译，不冒用萌娘百科或 Vocaloid Wiki 署名。界面逻辑测试已覆盖全部 164 项三语言文本。

帮助包含九章英、日、中内容。旧乐曲包商店／市场／购买流程均已从帮助章节删除，改为自助 ZIP/RAR 导入教程，包含目录格式、原始资源意义、SHA-1 校验、转换、事务注册及字幕 JSON 格式。UI 名称 PV 统一为 MV，原包文件名保持不变。

使用用户指定的 Mov_99.zip，在 iPad Pro 11-inch (M5) / iPadOS 27.2 上经游戏内系统文件选择器导入，全部七条 SHA-1 校验通过。游戏内完成原生解压、逐曲转换、注册与提交，界面显示“谱包已安装”。重建并启动后仍可读取新增歌曲。无临时自动安装入口。

| Mov_99 歌曲 | 原 Cue 事件 | 转换画面 | AAC 两轨各时长 |
|---|---:|---:|---:|
| えれくとりっく・えんじぇぅ | 563 | 197.85 秒 / 3957 帧 | 201.625397 秒 |
| ファインダー(DSLR remix - re:edit) | 440 | 215.85 秒 / 4317 帧 | 214.000181 秒 |
| ロミオとシンデレラ | 726 | 194.85 秒 / 3897 帧 | 192.000726 秒 |

ffprobe 确认三个转换文件均有 H.264 画面和两条 AAC；原视频、音轨时长不完全一致，未人为截成同长。《えれくとりっく・えんじぇぅ》实际启动 NORMAL 谱面并播放到 00:35；零输入触发失败结算，显示总音符数 122、MISS 24、E 评级。此处为安装后功能检查，不是完整手动通关。最新 NativeMedia.mm 同份原生解压器还通过本机 ZIP、RAR4、RAR5 提取测试；iOS 全流程安装实测为 ZIP。

iPad 系统实际切为简体中文。主页、导入页、导入教程和新曲目结算的中文可显示；现代 MV 的画面及横屏歌词侧栏实际出现日语原文与中文第二行。此前英语模式已验证两行字幕与暂停后拖动定位。

场景切换 loading 的最短展示时间统一为三秒；音游／MV 在这段时间结束后才启动媒体，媒体准备期间保留 loading 图。最终 Xcode 27.2 beta 2 构建成功，并在当前 iPad 模拟器安装、运行。

按用户最新要求，在打包后停止自动适配测试。iPhone 竖屏、iPad 横竖、Duo 外屏横竖／内屏横竖／类 3DS 的最终完整回归交给用户人工测试，未把未完成的形态标作通过。历史 Duo 测试不能代替此次最终回归。交付的是可编辑源码工程与 arm64 iOS 模拟器应用，没有签名真机 IPA。


## 2026-10-05 r2 本轮检查

本节记录本轮已执行的静态检查、构建和模拟器实屏检查，覆盖上面的旧版实现描述。加载歌曲及其它场景的转场最短展示时间均改为一秒；设置子菜单进入、返回都不显示转场。选歌底部只保留 Play，返回采用系统样式；随机选歌／Shuffle 的按钮、播放逻辑及帮助说明已移除。

### 静态校验与构建

- `InterfacePreferences.swift` 的 164 项界面文本均有英语、日语、简体中文、法语、西班牙语和韩语六种值，格式占位符与英语一致。九章帮助的六语言标题和正文均非空；商店／市场／随机播放帮助已移除。
- 17 首歌曲每种译文覆盖 479 条歌词提示，英语、简体中文、法语、西班牙语和韩语合计 85 条字幕轨、2,395 条译文。译文非空，原文键逐条匹配，歌曲及语言标识检查通过。日语模式显示原文，其它语言在存在译文时显示第二行。
- 本轮规则测试全部通过。Xcode 的最新 iPhone Duo 和 iPad 构建成功。
- `MUSIC SELECT` 和 `OPTIONS` 在 Modern／Legacy 分支均使用固定英文大写及已注册的 `OCRA` 字体，不随语言翻译。其它英文、数字及法语／西班牙语使用 Futura；简体中文使用 PingFang SC，韩文使用系统韩文字体。字体文件的真实名称、来源和字形宽度见 [OCR A 记录](Docs/OCRA.md)。
- 选歌、设置背景均使用移除烘焙标题后的连续图片，标题由代码绘制。背景和固定可读字号的调整依据见 [选歌背景与布局原则](Docs/SelectionBackground.md)及[设置背景记录](Docs/OptionsBackground.md)。

### 本轮实屏布局冒烟

| 设备形态／界面 | 已观察内容 |
|---|---|
| Duo 外屏竖屏、外屏横屏、内屏竖屏、内屏横屏 | Legacy 选歌页的 `MUSIC SELECT` 标题及 OCR A 渲染已观察。 |
| Duo 外屏竖屏设置 | `OPTIONS` 顶部标题已观察。 |
| iPad 竖屏、横屏 | 已检查最新 Legacy 选歌及 `OPTIONS` 标题。横屏右侧详情与封面垂直居中；难度标签的镜面倒影、贯穿水平线可辨，无黏连。竖屏音量设置已观察原版单面板布局。 |
| 普通 iPhone 18 Pro 竖屏 | 已检查最新首页、Legacy 选歌和 `OPTIONS`。Legacy 难度标签的镜面倒影、贯穿水平线可辨，无黏连。 |
| Duo Book／类 3DS 游戏布局 | 已观察加大的 MV 区域、左下竖排 Gauge、右下 Score／Combo／Pause 分区。 |
| Duo 外屏横屏游戏／暂停菜单 | Input Timing 面板在安全区内；中文模式的判定名称仍为英语。 |

本轮实际进入 `OPTIONS` → 音量，再从音量返回 `OPTIONS`，两个方向均未出现 loading。`OptionsView` 使用 `once=true`，只在首次进入设置场景展示一秒转场，返回已有设置页面不重复加载。

这些观察仅确认所列模拟器界面的显示与分区，不代表每个形态的全部菜单、MV、触摸输入、暂停恢复及完整歌曲均通过回归。此前 Duo 五种形态为布局抽查，未执行五态的全曲游玩。上述八态不得统一标记为完整游戏测试 PASS。

### 待确认与交付边界

Modern 标题已改为浅青色以提高背景对比，最新视觉尚未完成实屏复核，不能据 Legacy 观察声明 Modern 全部通过。普通 iPhone 最新版已完成上表所列竖屏界面检查，其它界面及完整打歌仍未验收。八种形态的完整人工验收仍交给用户，后续实际结果应另行补充，不能用历史测试代替。

Mov_99 在 iPad 上的游戏内 ZIP 安装、持久化与播放检查，以及本机 RAR4／RAR5 官方样本测试，保留以上历史结果及限制：本轮没有将其扩大为全部压缩包或所有设备的完整安装验收。

交付包括 r2 可编辑源码、arm64 模拟器应用及未签名的 arm64 真机 IPA。Xcode 真机 Release 归档成功，版本 0.2.0（3），最低 iOS 17.0；验证了 Mach-O 原生 iOS platform 2、iphoneos 平台标识、六种界面语言、85 条字幕轨／2,395 条译文、OCR-A、帮助与两张背景资源和源码一致。IPA 的 Payload 结构与压缩 CRC 校验通过，无签名或 provisioning profile；临时禁签构建设置已恢复。IPA 需要用户自行签名；尚未执行签名后的真机安装、运行或真机折叠形态验证，不把模拟器构建、运行结果算作真机验收。正式发布地址为 [HachiMiku39/mikuflick64 的 Releases](https://github.com/HachiMiku39/mikuflick64/releases)，README 和 HANDOFF 的发布页及下载链接均指向该仓库。


## 2026-10-06 r3 本轮检查

### 构建与静态验证

Xcode 27.2 beta 2 原生构建在 iPhone Air / iOS 27.2、iPad Pro 11-inch (M5) / iPadOS 27.2、iPhone Duo / iOS 27.1 启动成功。真机 Release Archive 成功，版本 0.2.0（4），最低 iOS 17.0。临时禁签设置在归档后恢复。

InterfaceTests 通过：逐位计数从个位开始、逐行延迟、零值和跳过的最终值；164 项六语言文本及格式、字体策略、系统语言选择、最佳评级、MV 顺序、字幕查询与 schema。IPA 打包校验确认 arm64 原生 iOS platform 2、iphoneos、六种界面语言、85 条字幕轨／2395 条译文、OCR-A 注册、九章六语言帮助、新背景与源码 SHA-256 一致、Payload 结构及 ZIP CRC。

### 本轮模拟器观察

| 设备／形态 | 已执行检查 |
|---|---|
| iPhone Air 竖屏 | Legacy 音游／MV Logo 放大、居中封面、MUSIC SELECT 与返回同高；SEKAI-A 标题；左右手 Gauge 加宽；零输入失败结算、重开和点按跳过。最终共同导航修改后再次检查 Legacy。 |
| iPad 横／竖屏 | Legacy 选曲、失败结算、音量设置；横屏游戏、键盘设置；设置子项返回不转场。最终导航修改后再次检查横屏 Legacy。 |
| Duo 折叠横／竖屏 | Legacy 选曲；修复并复查横屏 HARD／EXTREME 被裁切、竖屏系统自动返回移到右侧的问题，明确使用原生左侧 toolbar 返回。 |
| Duo 展开横／竖屏 | 最终 Legacy 标题、封面、最高分、难度、Play 的分区及间距实屏复查。 |
| Duo Book 横屏 | 游戏及 INPUT TIMING 暂停面板；失败结算曾溢出，改为较矮屏幕紧凑布局和 ScrollView 后复查全部数字与底部按钮可见。 |
| Duo Book 竖屏／类 3DS | 加高 MV、左下竖向 Gauge、中央键盘、右下 Score／Combo／Pause；实际播放中观察布局。 |

RESULT 移除背景原字母，顶部使用 OCR-A；进入时先渐变为黑，再显示结果，淡入淡出共 1 秒。成绩按行、从个位向高位快速滚动；实屏观察点按跳过可立即得到最终数值，首个点按不会误触底部按钮。减弱动态效果的立即显示与取消任务保护由代码和逻辑测试覆盖，未在系统设置中实际切换验证。Loading 背景移除旧字，菜单与歌曲加载均使用 OCR-A Loading...；新素材经视觉检查，短暂加载未在所有机型截图捕获。

本轮为模拟器布局和失败流程冒烟：未进行所有歌曲全曲手动通关、签名后的真机安装、真实硬件折叠、触摸／音频时延或长期性能测试。不能将上述检查扩大为全部机型／菜单／玩法均完成验收。


## 1.1.6 本轮检查（2026-10-06）

- 六种界面语言逐一核对固定英文术语：EASY、NORMAL、HARD、EXTREME、BREAK THE LIMIT、NORMAL MODE、COOL、FINE、SAFE、SAD、WORST、FAST、LATE。无判定为空字符串，避免被翻译成文字。设置中 FAST／LATE 的名称也保持英语。
- `InterfaceTests.swift` 通过；同时保留数字动画、MV 顺序、语言回落和字幕格式测试。
- `Tools/verify_assets.py` 再次验证 11 首影片、一条 H.264 画面与两条 AAC 音轨、6,818 条谱面事件及 5,280 条假名事件，133 种假名键盘覆盖通过。
- Xcode 原生编译并运行新 Bundle ID 的应用到 iPhone Air / iOS 27.2 模拟器。首次启动三页教程、Legacy 选歌、NORMAL《恋は戦争》播放、失败结算正常；结算显示总音符 83、WORST 17、E 评级。
- 真机归档包要求 Bundle ID `jp.sbga.mikuflick`、版本 `1.1.6`（build 5）；`package_ipa.py` 检查原生 arm64 iPhoneOS、11 首内置曲目的影片／谱面／预听／封面／Logo 与源码 SHA-256 一致、85 条字幕轨及 2,395 条译文、六语言帮助与 OCR-A 注册，以及 ZIP CRC。
- iPad／iPhone Duo 本轮未重新逐形态执行；沿用 r3 的实际布局检查记录。本轮没有修改布局。尚未验证用户签名后的真机安装、旧包名数据迁移、所有歌曲手动通关或全部语言实屏排版。


## build 6 最终回归（2026-10-06）

### 逻辑、资源与原版资料

- RulesTests 覆盖 11 首内置歌的五档谱面，共 55 个自动演奏组合；全部可判定音符 COOL，暂停／恢复、追赶与手动输入隔离通过。
- InterfaceTests 10 组通过：普通 iPhone 仅竖屏、Duo／iPad 允许横屏；六语言英文难度／判定；原版字图、结果数字、MV 顺序和字幕 schema。
- 从 11 首内置谱面提取 133 种假名，核对三种样式的 active／inactive 原版字图索引、78×78 裁切范围及有效透明度，全部通过。浊音、半浊音、小假名、长音和组合浊点覆盖；原素材没有独立字图的极少数字形按原版基础字等比缩小。
- 11 首内置影片、谱面、封面、Logo、预听已验证；85 条字幕轨共 2395 条译文。确切数量及逐文件摘要由 IPA 打包再次核验。
- 原版 Ghidra 导出确认 100 当前连击切换彩虹轨道，不绑定难度或 AP；效果层数与字图表详见 EFFECTS-RESEARCH.md、Docs/OriginalNoteGlyphs.md。完整观看用户提供的官方宣传片并作视觉对照；不宣称复刻每一帧动画。

### 最新原生构建与实屏检查

最新代码分别通过 Xcode 原生编译并运行到三台独立模拟器：iPhone Air / iOS 27.2、iPad Pro 11-inch (M5) / iPadOS 27.2、iPhone Duo / iOS 27.1。

| 设备／形态 | 本轮观察 |
|---|---|
| Air 竖屏 | 原版 gauge 等比三层、轨道原版片假名字图；Legacy 约 00:40 间奏隐藏 gauge、TAP 在整个输入区居中，约 01:09 恢复键盘与 gauge。音量页完整显示 MUSIC／SFX／FAIL SOUND。物理旋转横屏后应用仍保持竖屏。 |
| iPad 竖屏→横屏 | 竖屏音量页完整居中。最新 Legacy 约 00:44 的 TAP 居中、gauge 隐藏；旋转横屏后约 01:05 键盘与 gauge 恢复，横屏分区正常。 |
| Duo 折叠横屏 | 最新方向策略下外屏仍可旋转横屏；音量页左右分区完整。Legacy 约 00:38 间奏隐藏 gauge、TAP 居中，约 00:59 恢复正常输入。 |
| Duo 折叠竖屏 | 音量页收紧装饰与间距，MUSIC／SFX／FAIL SOUND 全部可见；系统右侧状态栏／摄像头安全区保留。 |
| Duo 展开／Book | 本轮此前检查主页、游戏、原版等比 gauge 和音量设置。自动演奏过程中连续切换展开、Book、折叠与旋转保持连续时钟，完整结算正常。最新 TAP 改动未逐一重新执行这些形态的完整歌曲。 |

Air 和 Duo 的本轮完整 EXTREME 自动演奏《恋は戦争》均得到 Total notes 171、COOL 171、MAX COMBO 171、间奏 37、总分 114176（53176 + 61000），Perfect；返回选歌 HIGH SCORE 保持 0，测试成绩未保存。最新间奏布局变更之后另行重新检查上述间奏→正常输入切换，不把此前全曲观察扩大为所有机型／语言／形态完全验收。

Legacy 专用条件同时检查选择样式与间奏状态，SEKAI-A／B 保持原有 gauge 和输入布局；此隔离由代码检查确认，本轮未在两种现代样式分别重跑完整间奏歌曲。

### 交付边界

目标包名 jp.sbga.mikuflick，版本 1.1.6（6），最低 iOS／iPadOS 17.0。发布 IPA 为未签名的 arm64 iPhoneOS 构建，需要用户自行签名；模拟器检查不等于签名后真机安装、硬件触摸／音频延迟、真实折叠或全部歌曲真人通关验收。最终归档和压缩校验见随发布交付的 IPA-VALIDATION.json。

最终 Xcode 真机 Release Archive 成功（build 6），临时禁签设置已恢复。IPA 校验确认 arm64／iOS platform 2／iphoneos、包名 jp.sbga.mikuflick、版本 1.1.6（6）、最低系统 17.0、11 首完整内置曲、85 条字幕轨／2395 条译文、8 张原版轨道字图及 OCR-A。源码资源逐文件 SHA-256 匹配，Payload 和 ZIP CRC 通过；无签名或 provisioning profile。


## 2026-10-06 build 7 性能悬浮窗检查

### 逻辑与构建

- `PerformanceTests.swift`：真实进程 CPU 时间和非零 physical footprint；25% 单核心、200% 多核心；60／10 Hz 显示回调；缺失与无效样本、回退 CPU 时间、后台恢复重新建立基线，全部通过。CPU 没有截断到 100%；读取失败显示 —。
- `InterfaceTests.swift`：174 项动态文本六语言覆盖及格式校验通过，难度／判定／FAST／LATE 英文策略保持通过。
- Xcode 27.2 beta 2 原生构建成功，分别安装、运行于独立的 iPhone Air（27.2）、iPad Pro（27.2）、iPhone Duo（27.1）模拟器。新功能没有改变评分、谱面、影片、键盘或 gauge 布局。

### 实屏检查

- Air：性能开关默认关闭；打开后 CPU、RAM、UI FPS 持续更新。标题可收起、展开，× 可关闭，窗外的返回键／选项／PLAY 可正常操作。进入全屏游戏、暂停和返回选曲时仍保留性能窗；最高分仍为 0。进入系统主页后窗消失，重新进入应用恢复显示。关闭 Developer 后性能窗消失、Developer 入口隐藏。Apple Metal HUD 开关开启并重新启动后保持开启；当前 SwiftUI 页面没有出现官方图形 HUD，不能声称读取 GPU 成功。
- iPad：Developer 页面横／竖屏实际旋转检查，性能窗保持固定字号，所有数值、关闭按钮和图例完整落在窗口安全区内。
- Duo：外屏横／竖、展开横／竖、Book 横屏切换检查，性能窗适应窗口可用区域，避让系统状态／返回侧栏，固定字号不随主页面整体缩放。Developer 表单较短时可滚动。
- 本轮自动拖动没有产生有效位移，观测到手势起止而平移量为零；因此未将拖动实测标为通过。源码使用标准 UIKit pan，真机触摸拖动、长时间采样与折叠旋转后的拖动位置仍需人工验证。

截图随本地交付保存：`air-performance-game.png`、`ipad-performance-portrait.png`／`landscape.png`、`duo-performance-closed-portrait.png`／`landscape.png`、`duo-performance-open-portrait.png`／`landscape.png`、`duo-performance-book.png`。

### 数据含义与限制

CPU 为本应用进程的用户＋系统时间增量，100% 是一个核心；RAM 是进程 physical footprint。模拟器数值不能代表真机性能或耗电。UI FPS 为 CADisplayLink 回调频率，不是影片帧率、Metal GPU 渲染完成率，也不是系统全局 FPS。

Apple 官方 HUD 接入仅使用公开的 UserDefaults `MetalHUDForceEnabled` 与 `CAMetalLayer.developerHUDProperties`，改变开关后重启生效；需要支持的 Metal 表面与运行环境。本应用不使用私有 GPU 计数器，不生成无关的 Metal 工作负载来让 HUD 出现，不把 GPU 耗时估算成占用百分比。GPU 百分比明确为 —；详细分析使用 Xcode Instruments → Metal System Trace。全部性能采样只留在本机。

此次保留 1.1.6 与 `jp.sbga.mikuflick`，构建号为 7；11 首免费歌曲、六语言和原版假名字图保持完整。

原生 Release 设备归档成功（2026-10-06 16:37）；已导出 build 7 未签名 IPA。打包校验通过 iPhoneOS arm64、1.1.6／build 7／jp.sbga.mikuflick、完整 11 首影片／谱面／预听／封面／Logo 的源文件 SHA-256、85 份字幕、8 张原版假名字图、OCR-A、六语言及 ZIP CRC。IPA 为 420,532,054 bytes，SHA-256 `b90a345c5e820b6ad5c89f2d1b1dcbd252a721b6331a1ebd6f8d10e48d607779`。真机签名安装未验证。

## build 8 ProMotion 检查（2026-10-06）

- Info.plist：`CADisableMinimumFrameDurationOnPhone=true`；版本 1.1.6、build 8、Bundle ID `jp.sbga.mikuflick`。
- 播放更新使用 CADisplayLink + 当前窗口屏幕能力；最高请求 120 Hz，60 Hz 屏幕不请求超过能力的刷新率。HUD 使用自适应范围，菜单不固定请求 120。
- 每帧读取 AVPlayer.currentTime；原版 30 Hz 判定与影片帧率保持原样；暂停、进度拖动、退出及失去前台时停止显示更新，恢复重新配置。
- PerformanceTests：120／60／10 Hz、120→60 的动态回调样本、设备 60／120／240 Hz 的上限策略、CPU／RAM 采样及后台重置 PASS。RulesTests：31 个判定边界及原版规则 PASS。
- 真机 120 Hz、低电量及温控降频尚未验证；模拟器仅验证可构建与运行行为，不作为真机 120 FPS 证明。

### build 8 实际模拟器回归

- 三台模拟器均经 Xcode 原生编译／安装／启动：iPhone Air iOS 27.2、iPad Pro iPadOS 27.2、iPhone Duo iOS 27.1。构建 app 的 Info.plist 实测 build 8 与高刷新开关 true。
- Air：游戏推进；暂停后分数 9600、时钟 00:25 保持，恢复继续推进；Home 进入后台，再打开应用仍为 PAUSED，返回选曲正常。
- iPad：自动打歌、间奏与原版特效显示；00:40 暂停，旋转后保持暂停，再恢复推进到 00:44。性能悬浮窗仍在安全区内，显示回调通常约 58–60 FPS；不等于渲染 FPS 或真机 120 Hz。
- Duo：播放中折叠／展开／Book 切换继续推进；稳定后的 Book 画面中，视频、轨道、键盘、gauge 与小型悬浮窗正常显示。立即旋转抓取的中间帧不作为布局通过证据。
- Duo 完整《恋は戦争》NORMAL 自动打歌：83 COOL、MAX COMBO 83、15 间奏，Stage score 24954、Combo bonus 17600、TOTAL SCORE 42554、Perfect!；标记 Test result · not saved，返回选歌 HIGH SCORE 仍为 0。
- 本轮未重新宣称所有八种形态完成手动全曲验收，也未验证真机签名安装、GPU 占用和稳定 120 Hz。

### build 8 交付包

Xcode 原生 Archive（2026-10-06 16:56）生成 arm64 iPhoneOS，未签名；11 首免费歌曲的影片／谱面／预听／封面／Logo 逐文件 SHA-256 与源码一致。85 个字幕文件、2395 条译文、OCR-A、8 张原假名字图及 IPA ZIP CRC 校验通过。

`MikuFlick64-1.1.6-build8-unsigned.ipa`：420534180 bytes，SHA-256 `84ea23947a56b62c2069e591b2f219e2291d1d51b35d657216b805b0d5bcf380`。包名 `jp.sbga.mikuflick`，版本 1.1.6、build 8；`CADisableMinimumFrameDurationOnPhone=true`。build 7 为本轮追加 ProMotion 前的中间构建，未发布为新 Release。

- Duo MV：媒体与双行字幕推进至 01:42，暂停显示 Continue；恢复后进度继续至 01:48／01:50。原影片 02:37，不随 UI 请求刷新率改变。原生自动化的 AX 数值修改没有触发拖动手势，未将进度 seek 标记为已通过；手势 seek 仍需真机补测。
