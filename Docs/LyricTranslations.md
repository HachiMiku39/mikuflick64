# MV 歌词翻译

当前提供英语、简体中文、法语、西班牙语、韩语五种译文，各覆盖 71 首歌曲的 2008 条不同游戏歌词 cue；重复段落复用对应译文。共 355 份字幕 JSON、10,040 条译文，包含 11 首基础曲和用户提供的 20 个曲包中的 60 首 DLC。DLC 的视频与谱面需要自行导入；安装后自动匹配内置译文，不需要另装翻译包。

## 来源和署名

既有英语与简体中文为项目自译，分别署名 `Miku Flick 64 project translation` 和 `Miku Flick 64 项目自译`。本轮新增五语言译文以及既有法语、西班牙语、韩语为本项目 AI 翻译，依据用户提供的游戏日语原文制作，各文件的 `translator`／`license` 明确记录此来源。

这些文件不是萌娘百科或 Vocaloid Wiki 的转载译文，也没有借用这些网站的译者署名。原歌词及其作者的权利不因项目翻译改变。AI 译文尚需语言使用者结合歌曲语境审校；技术覆盖检查只确认 cue 对应和数据完整，不证明译文质量。

| 歌曲 ID | 不同歌词 cue |
|---|---:|
| `koi_wa_sensou` | 14 |
| `hajimete_no_oto` | 25 |
| `roshin_yuukai` | 27 |
| `migikata_no_tyou` | 22 |
| `Just_be_friends` | 38 |
| `cloverclub` | 32 |
| `magnet` | 21 |
| `promise` | 20 |
| `ura_omote_lovers` | 41 |
| `hatsune_miku_no_gekisyou` | 53 |
| `tajyuumirai_no_quartet` | 19 |
| `pack11.double_lariat` | 29 |
| `pack11.from_y_to_y` | 25 |
| `pack11.hello_planet` | 26 |
| `pack99.finder` | 21 |
| `pack99.romio_to_cinderella` | 44 |
| `pack99.electric_angew` | 22 |
| `pack1.colorful_sexy` | 25 |
| `pack1.gemini` | 29 |
| `pack1.kantarera` | 19 |
| `pack2.Yellow` | 23 |
| `pack2.colorful_melody` | 25 |
| `pack2.kotti_muite_baby` | 54 |
| `pack3.dear_cocoa_girls` | 31 |
| `pack3.iyaiya_seijin` | 28 |
| `pack3.soiyassa` | 45 |
| `pack4.lukaluka_nightfever` | 20 |
| `pack4.mikumiku_ni_siteyannyo` | 18 |
| `pack4.nightmare_partynight` | 28 |
| `pack5.VOiCE` | 13 |
| `pack5.hinekuremono` | 17 |
| `pack5.kokoro` | 22 |
| `pack6.kyodai_syoujyo` | 22 |
| `pack6.rinrin_signal` | 40 |
| `pack6.tsugaikogarashi` | 30 |
| `pack7.meiteki_cybernetics` | 11 |
| `pack7.puzzle` | 22 |
| `pack7.sekiranunn_graffiti` | 31 |
| `pack8.houkai_utahime` | 30 |
| `pack8.musunde_hiraite_rasetsuto_mukuro` | 26 |
| `pack8.saa_dotti` | 30 |
| `pack9.hajimeteno_koiga_owarutoki` | 41 |
| `pack9.kogane_no_seiya_sousetsu_ni_kuchite` | 28 |
| `pack9.strobo_nights` | 15 |
| `pack10.amata_no_mai` | 15 |
| `pack10.iroha_uta` | 19 |
| `pack10.jugemu_sequencer` | 35 |
| `pack12.koiiro_byoutou` | 35 |
| `pack12.mikumiku_kinn_ni_gotyuui` | 30 |
| `pack12.nekomimi_switch` | 47 |
| `pack13.hontoha_wakatteru` | 34 |
| `pack13.spica` | 37 |
| `pack13.utani_katachiha_naikeredo` | 27 |
| `pack14.innocence` | 16 |
| `pack14.worlds_end_dancehall` | 48 |
| `pack14.yumeyume` | 42 |
| `pack15.mousou_sketch` | 27 |
| `pack15.no_logic` | 29 |
| `pack15.on_the_rocks` | 28 |
| `pack17.kodokunohate` | 25 |
| `pack17.rolling_girl` | 29 |
| `pack17.toumei_suisai` | 20 |
| `pack96.hatsunemiku_no_syoushitsu` | 57 |
| `pack96.koi_suru_vocaloid` | 27 |
| `pack96.popipo` | 33 |
| `pack97.melt` | 25 |
| `pack97.moon` | 20 |
| `pack97.stargazer` | 22 |
| `pack98.anata_no_utahime` | 27 |
| `pack98.timelimit` | 16 |
| `pack98.world_is_mine` | 36 |
| **合计** | **2008** |

覆盖曲包编号为 1–15（含 11）、17、96、97、98、99。用户提供的目录没有 Mov_16，因此不宣称该包三首歌曲已有译文。

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

`Tests/SubtitleAssetsTests.swift` 对每种语言的 71 份文件逐个解码并校验 ID、语言和署名。所有 2008 条 cue 都必须有非空译文，词典条数需与原文清单一致；日语模式的翻译查找必须为空。完整命令见 [测试说明](../Tests/README.md)。

这一检查针对当前 71 首歌的游戏版本歌词，不承诺未来新增曲包或整首歌曲的其他版本已经具有翻译。
