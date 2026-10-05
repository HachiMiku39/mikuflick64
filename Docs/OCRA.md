# OCR A 字体来源与使用记录

本项目使用 OCR A 绘制所有语言共用的英文大写标题 `MUSIC SELECT` 和 `OPTIONS`。标题正文保持这两个固定字符串；其它界面文字仍采用各自的本地化及字体策略。

## 文件与真实名称

| 项目 | 已读取的值 |
| --- | --- |
| 项目文件 | `GameAssets/OCRA.otf` |
| 文件大小 | 14,356 字节 |
| 格式 | OpenType |
| PostScript 名称 | `OCRA` |
| 字体家族 | `OCRA` |
| 完整显示名称 | `OCR A` |
| 样式 | `Regular` |
| 文件内版本 | `Version 2` |
| 字形数 | 115 |
| 文件内生成记录 | `FontForge 2.0 : OCR A : 27-9-2012` |
| Git blob SHA-1 | `86f56dcdc5cb1ccf7d94b79fea46d7a461ff6683` |
| SHA-256 | `585bd6f5b62ff87ee8eab8a541745d0a955fb873ce3746b8a90771291b75c9bc` |

名称来自字体 `name` 表，并以 CoreText 从该文件直接创建字体复核。注册此资源后，UIKit/SwiftUI 应使用 PostScript 名称 `OCRA`。这份文件的内部版本与日期见上表，并不表示它是上游目前发布的最新版。

## 作者、来源与授权

文件内署名为 Matthew Skala（2011–12），基于 Richard B. Wales（1988–89）及 Tor Lillqvist 的代码。下载副本来自 [Open Source Design 字体仓库的 OCRA.otf](https://github.com/opensourcedesign/fonts/blob/master/OCR/OCRA.otf)；[同目录](https://github.com/opensourcedesign/fonts/tree/master/OCR)也保存 OCR 文档。

[Matthew Skala / Tsukurimashou 的作者页面](https://tsukurimashou.org/ocr.php.en)允许免费使用，包括商业应用，并说明 Skala 自己的贡献属于公有领域。[作者的 OCR 文档](https://tsukurimashou.org/ocr.pdf)第 2 节追溯 OCR A 至 Tor Lillqvist 和 Richard B. Wales，记载 Wales 允许自由使用，但限制营利分发字体；Skala 未对自己的转换版本主张版权。原始来源的 [CTAN OCR-A 页面](https://ctan.org/tex-archive/fonts/ocr-a)将许可归类为“Do Not Sell Except by Arrangement”。

本项目的适用方式是：将字体用于应用标题，并随当前免费交付的应用分发；保留字体内部署名与本文来源记录，不售卖该字体文件或字体包。授权记录采用自定义免费使用许可，并保留 Wales 的非营利字体分发条件。

## 字形与宽度检查

2026-10-05 使用 CoreText 直接加载项目中的文件检测：`MUSIC SELECT` 的 12 个字符（包括空格）与 `OPTIONS` 的 7 个字符均映射至非零字形，未依赖其它字体回退。

下表为默认字形的排版宽度，单位为点；加字距的列按相邻字符间隔计算，未包括额外容器内边距。

| 字符串 | 字号 | 默认宽度 | 字距 5 | 字距 7 |
| --- | ---: | ---: | ---: | ---: |
| MUSIC SELECT | 22 | 190.872 | 245.872 | 267.872 |
| MUSIC SELECT | 26 | 225.576 | 280.576 | 302.576 |
| MUSIC SELECT | 32 | 277.632 | 332.632 | 354.632 |
| OPTIONS | 22 | 111.342 | 141.342 | 153.342 |
| OPTIONS | 26 | 131.586 | 161.586 | 173.586 |
| OPTIONS | 32 | 161.952 | 191.952 | 203.952 |

例如，228 点宽的横屏标题区域可容纳 22 点、字距 3 的 `MUSIC SELECT`（223.872 点）；26 点、字距 7 的版本需要至少 302.576 点。布局应先分配足够宽度或减少字距，再决定字号，并在最终设备形态下复核 SwiftUI 的实际渲染。
