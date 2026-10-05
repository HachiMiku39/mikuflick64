# 原版 INPUT TIMING 换算

证据来自用户提供 MikuFlick2 1.1.5 ARMv7 可执行文件的静态分析；没有原开发者源码。

- TouchInputTiming::touchMoved:（0x7da78，边界 0x7db34…0x7db42）每次越过滑动阈值调整一档，范围 −10…+10；原界面五个标记不是五档。
- MusicData::GetInputTiming（0x1a164）／SetInputTiming:（0x1a174）读写歌曲对象 m_InputTiming（偏移 0x95c），因此当前实现按歌曲持久化，同曲难度共享。
- NoteNormal::initWithType:::（0x21fba…0x22062）与 NoteInterlude::initWithType:::（0x2fa48…0x2faee）将校准值加到 m_JustFrame。
- GetCenterTime（0xe57c）按 BPM 查表，参与换算；7 号 cue 是预读时间，8 号 cue 是 BPM。SetDelay 的预读设置不能替代 INPUT TIMING。

保持原 ARMv7 Float32 和向零取整：

```text
center = BPM 对应的秒数
frames = trunc(center × 30)
speed = abs(−642 / center)
pixelsPerTick = Float32(Double(speed) / Double(frames))
ticksPerPosition = (39 / pixelsPerTick) / 10
offsetTicks = trunc(ticksPerPosition × clamp(position, −10, +10))
displayMilliseconds = offsetTicks × 1000 / 30
```

查表 BPM 上界为 80、90、100、110、120、130、140、150、160、170、180、190、200、210、220、230、240（严格小于）；秒数依次为 3、2.9、2.8、2.6、2.4、2.2、2.1、2、1.9、1.8、1.7、1.6、1.5、1.4、1.3、1.2、1.1，否则 1。

正值把判定时刻移后，负值移前。由于原版按 30 Hz 量化，数个档位可能显示相同 ms。BPM 175 的 −10…+10 映射 tick 为 −5、−4、−4、−3、−3、−2、−2、−1、−1、0、0、0、1、1、2、2、3、3、4、4、5；最大约 ±166.7 ms。BPM 100 最大 ±400 ms。

界面即时更新当前音符的判定、漏音处理和 FAST／LATE 参考时刻，MV 音频与歌词时轴不平移。独立测试覆盖全部 21 档、不同 BPM、正负方向、边界钳位、普通及间奏判定。真实设备音频与触摸延迟仍需实机对照。
