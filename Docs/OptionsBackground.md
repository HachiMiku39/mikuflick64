# 设置页背景修复记录

2026-10-05 使用内置 `image_gen` 编辑工具，按 imagegen 技能流程先检查原图，再去除顶部烘焙的 OPTIONS 字样，以便界面动态绘制 OCR-A 标题时不出现重叠。

- 原图：`GameAssets/bg_options.png`，640×960，RGBA，alpha 全为 255。原图保留。
- 项目使用图：`GameAssets/bg_options-adaptive.png`，1024×1536，保持 2:3 比例和原有淡灰紫蓝色构图。
- 内置工具原始输出：`/Users/user/.codex/generated_images/01a10c43-9776-77b2-80fd-e779f9cbda53/exec-96035c51-d367-439a-8afd-4f9d901da3b0.png`。
- 采用非透明输出；原图的淡色观感来自颜色混合而非 alpha 透明。
- 目视检查：顶部字母已移除，没有新标题或水印；Miku 的姿势、位置和几何装饰保留。生成图是 AI 编辑版本，不声明与原图在未编辑区域像素完全一致。

实际提示词：

```text
Use case: precise-object-edit
Asset type: portrait background texture for an iOS game Settings screen.
Input image 1 is the edit target: bg_options.png.
Primary request: remove only the existing spaced-out O P T I O N S lettering along the very top of the image. Fill the letter shapes seamlessly by continuing the surrounding gray, purple and muted blue background, including any underlying hair or geometric shapes.
Composition/framing: preserve the full existing 2:3 portrait composition and framing.
Constraints: the edit is confined to the narrow top lettering band (approximately x=20–615, y=18–60 in the original 640×960 image). Keep Hatsune Miku's face, hair, body, pose, outfit, scale, position, all geometric decorations, and everything below that band unchanged. Preserve the original very pale, washed-out translucent-looking illustration over the gray-purple-blue gradient. The original image is fully opaque; keep a fully opaque output. Do not increase contrast or saturation, sharpen, restyle, extend, crop, or rearrange the artwork.
Text: no text or lettering anywhere. Do not add any title, labels, logo or watermark.
```

