# MV 歌词翻译

当前提供英语、简体中文、法语、西班牙语、韩语五种译文，各覆盖 17 首歌曲的 479 条不同游戏歌词 cue；重复段落复用对应译文。共 85 份字幕 JSON、2395 条译文，包含 11 首基础曲和 Mov_11／Mov_99 的六首歌曲。额外曲包歌曲的视频与谱面需要自行导入，内置译文不会安装曲包音视频。

## 来源和署名

英语与简体中文为项目自译，依据用户提供的游戏日语歌词制作，分别署名 `Miku Flick 64 project translation` 和 `Miku Flick 64 项目自译`。法语、西班牙语、韩语为本项目 AI 翻译，参考同一批日语原文和既有项目译文，各文件的 `translator`／`license` 明确记录此来源。

这些文件不是萌娘百科或 Vocaloid Wiki 的转载译文，也没有借用这些网站的译者署名。原歌词及其作者的权利不因项目翻译改变。AI 译文尚需语言使用者结合歌曲语境审校；技术覆盖检查只确认 cue 对应和数据完整，不证明译文质量。

| 歌曲 ID | 不同歌词 cue |
|---|---:|
| `Just_be_friends` | 38 |
| `cloverclub` | 32 |
| `hajimete_no_oto` | 25 |
| `hatsune_miku_no_gekisyou` | 53 |
| `koi_wa_sensou` | 14 |
| `magnet` | 21 |
| `migikata_no_tyou` | 22 |
| `pack11.double_lariat` | 29 |
| `pack11.from_y_to_y` | 25 |
| `pack11.hello_planet` | 26 |
| `pack99.electric_angew` | 22 |
| `pack99.finder` | 21 |
| `pack99.romio_to_cinderella` | 44 |
| `promise` | 20 |
| `roshin_yuukai` | 27 |
| `tajyuumirai_no_quartet` | 19 |
| `ura_omote_lovers` | 41 |
| **合计** | **479** |

## 显示与匹配

日语模式只显示原日语；其他五种语言显示原文和对应译文，没有匹配译文时省略第二行，不使用英语替代另一种语言。现代 MV 的画面字幕和横屏歌词侧栏使用同一份映射，暂停或拖动进度后按实际媒体时钟定位。

字幕以游戏里的日语 cue 为键，而非完整歌词的行号或网上文本的段落。`SubtitleMap` 查找时做 Unicode 兼容规范化，并删除空白和换行；标点仍有意义。译文空值视为缺失。五种内置翻译的原文键均来自 `Tests/Fixtures/lyric-cues.json`，保留该 cue 清单的内容。

## 导入格式

在主菜单资源包导入页选择字幕 JSON。单文件最多 5 MB；歌曲 ID 必须已在曲库中。重复歌曲／语言，以及规范化后指向不同译文的冲突原文键，会被拒绝。

```json
{
  "formatVersion": 1,
  "translations": [{
    "songID": "koi_wa_sensou",
    "language": "es",
    "translator": "Your name",
    "lines": {"テストです。": "Esto es una prueba."}
  }]
}
```

可用语言代码为 `en`、`zh`／`zh-Hans`、`fr`、`es`、`ko`，其中 `zh-Hans` 归一为 `zh`。可选的 `sourceURL`（http／https）和 `license` 用于保留真实来源。应用不会下载该网址内容；有来源网址的译文可在播放页打开署名链接。

优先级为 `Library/LyricTranslations` 的导入文件 → 歌曲所在曲包文件 → `GameAssets` 内置文件。曲包可在 `Mov_<编号>` 中包含 `<song.id>.lyrics.<language>.json`，例如 `pack99.finder.lyrics.es.json`；本地文件也兼容“原文 → 译文”的平面字典。下载歌曲使用完整 ID，例如 `pack11.hello_planet`。

## 覆盖校验

`Tests/SubtitleAssetsTests.swift` 对每种语言的 17 份文件逐个解码并校验 ID、语言和署名。所有 479 条 cue 都必须有非空译文，词典条数需与原文清单一致；日语模式的翻译查找必须为空。完整命令见 [测试说明](../Tests/README.md)。

这一检查针对当前 17 首歌的游戏版本歌词，不承诺未来新增曲包或整首歌曲的其他版本已经具有翻译。
