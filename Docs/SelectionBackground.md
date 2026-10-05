# 选歌背景修补与布局原则

2026-10-05。使用内置 `image_gen` 编辑 `GameAssets/bg_misicselect.png`，原图保留。输出 `GameAssets/bg_misicselect-adaptive.png`（1024×1536）。

去掉顶部烘焙的 MUSIC SELECT 字样，由代码单独排标题。完整图片一次等比铺满屏幕，禁止将顶部和主体裁成两条再分别缩放。检查图片无纯色横条和矩形接缝，角色、姿势、服装和蓝青半透明效果保留；局部重绘有轻微细节变化。

选歌页的原版框体继续使用，底部 Play 使用固定大小的独立操作区；高分和 BPM 使用17 pt，难度文字使用17 pt，不再随设备整体缩小。横屏调整为两列，竖屏上下排列，同一组操作保留相同顺序和可用性。背景延伸到安全区外，主要操作保留在安全区内。

设计依据：[Apple Layout](https://developer.apple.com/design/human-interface-guidelines/layout)、[Designing for games](https://developer.apple.com/design/human-interface-guidelines/designing-for-games/)。参考17 pt默认文字、44×44 pt触控区域、相关项目分组和稳定的自适应布局。

## 实际图像编辑提示词

Use case: precise-object-edit. Asset type: full-screen background bitmap for an iOS game song-selection screen. Input image 1 is the edit target, an original blue/cyan illustrated background with a softly translucent Hatsune Miku centered in a full-body pose and subtle floating cubes. Primary request: remove ONLY the large baked-in MUSIC SELECT letters running across the top of the image. Inpaint their entire area with a seamless continuation of the neighboring blue-to-cyan illustrated background, so that the top looks like one continuous original illustration without words. Preserve the rest of the source image as closely as possible: the same Miku identity, face, hairstyle, hair ornaments, body pose, costume, twin tails, long legs, floating cubes, thin abstract diagonal facets, exact cool blue/cyan palette, dim blue vignette near the edges and bottom, and the original low-contrast translucent veil over the character. Do not sharpen or make Miku opaque or vivid; this is a subdued background under controls, not a bright character poster. Keep a portrait 2:3 composition with the complete existing pose and artwork, with continuous natural blue/cyan painted background reaching every edge. If extra edge area is needed, extend the existing illustration smoothly, never using a solid rectangle or stripe. There must be no residual MUSIC SELECT letters, no new text, no title, no logo, no UI elements, no buttons, no bars, no abrupt rectangular patches, no seams and no added characters. The title will be rendered separately in code. Preserve the small costume markings that are intrinsic to Miku's outfit; remove only the baked-in UI heading. Maintain original softness and transparency intensity.
