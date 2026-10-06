# Original effect behavior (MikuFlick /02 1.1.5)

Reference video: [Official SEGA play video — 恋は戦争](https://www.youtube.com/watch?v=vCf6JJ_K8YU). The complete 2:41 video was played; selected frames were visually inspected. The three panels demonstrate EASY / NORMAL / HARD. This is not a claim that every video frame was inspected.

The existing local Ghidra project was copied into an isolated project and opened read-only to export missing functions. Conditions below were verified against its ARM instruction listing, rather than inferred only from the video. Decompiled C contains inferred types.

| Effect | Verified condition | Original function |
| --- | --- | --- |
| Rainbow rail | Current combo >= 100, returns to ordinary rail after combo resets; no difficulty/AP gate | WindowGameWindow Render, 0x39db4 |
| Inner spinning ring | Combo >= 5 | WindowGameWindow Render, 0x39db4 |
| Outer spinning ring 1 / 2 | Combo >= 25 / 45 | WindowGameWindow Render, 0x39db4 |
| Firework layers | On input pulse, combo >= 15 / 35 / 85; hidden in interlude; displayed for first 8 of 10 pulse ticks | EffectPulse render, 0x2e3d8 |
| Colored note | Active normal note, cue-6 Crimax mark and current combo >= 100 | NoteNormal render, 0x22540 |
| Crimax bonus | Marked note judged COOL and post-hit combo >= 100; +200 stage score | NoteNormal checkResult, 0x22e10 |
| Interlude hit | FINE / COOL; rising quaver effect, 20 ticks | NoteInterlude checkResult, 0x2ffdc; EffectInterlude 0x62af0 / 0x62be8 / 0x62de4 |

Cue 6 selects the digit at difficulty + 1. A nonzero value marks the last eligible ordinary note among the last four created note objects. Supplementary lyric and interlude objects occupy slots but are ineligible. A gray ordinary note may receive the mark but cannot render the active-note rainbow effect. Thus difficulty can change marked notes, and shorter charts may never reach 100 combo, without directly changing the rail threshold.

Animation uses 30 Hz media ticks. Rainbow rail advances 32 / 640 of one tile per tick. Rings rotate at 17 / 13 / 12 degrees per tick. Ordinary rail remains stationary. Firework scaling uses the original combo-dependent thresholds 55, 65, 75 and 95. The original WindowGameWindow loadTexture (0x39a80) explicitly loads game_effect_01; the green Miku firework sprites are used accordingly. The other supplied color atlases are reference variants, not invented per-difficulty switches.

The 28 RGB entries were extracted from the original executable at virtual address 0x139310. Original gauge body and outline share their origin; fill is offset +11 x, +9 y. Dimensions are 146×388, 115×388 and 91×362. All three layers use one uniform scale, preserving the original aspect ratio and the +11/+9 fill offset, and clip fill from its bottom. On iPhone portrait, the full-width note lane sits above a side-by-side gauge and keyboard; both fit the available input area without stretching the gauge. It no longer shifts or fades the outline independently.

Developer: enable in OPTIONS → Display, then open Developer → Autoplay. Every playable note, including interludes, is automatically COOL at its calibrated tick. Manual gameplay input does not judge notes during the test. Pause freezes progression; resume catches up only as the media clock advances. Test scores, ranks and records are not persisted. AUTO PLAY is shown during gameplay and on the result screen. The option is off by default; disabling Developer clears Autoplay.

Original judgement graphics are a separate Display option. Enabling it replaces the modern judgement text with atlas evaluation_00…04 and hides FAST / LATE. The timing-indicator preference remains stored so it can resume when the original graphics option is turned off.

Original judgement backgrounds are `afterimage_00`–`04`, paired respectively with COOL/FINE/SAFE/SAD/WORST. `EffectFlickResult render` at 0x2eb34 draws `s_tblResultEffect_Back` before `s_tblResultEffect`; table addresses 0x13ac3c and 0x13ac54 confirm the pairing. Backgrounds are 102×102 and lettering 142×69. Both share size, position and opacity animation; only the background receives the horizontal stretch.
