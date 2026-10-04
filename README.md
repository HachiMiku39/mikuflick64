# mikuflick64
MikuFlick02 renew version for iOS26+ devices.
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
