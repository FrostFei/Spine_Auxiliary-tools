# Spine Auxiliary Tools

离线可用的 Spine / Live2D 辅助工具集合，用于从 Spine 资源中解包切图、把修改后的切图还原成原图集，以及从 Spine JSON 生成 Photoshop 可执行脚本。

This repository contains offline HTML utilities for working with Spine and Live2D assets: extracting images from atlas files, rebuilding atlas textures from modified slices, and rebuilding a Photoshop layer layout from Spine JSON.

## 工具列表

### 1. spine-atlas-to-images.html

从 Spine `.atlas` 和合图 `.png` 中导出单张切图。

功能：

- 拖入或选择 `.atlas`
- 选择或拖入一张/多张 page PNG
- 可选加载 Spine `.json`
- 支持 `rotate: true` 的 atlas 区域旋回
- 支持按 `orig / offset` 还原透明画布
- JSON 模式下可按 attachment / UV 导出更细的切图
- 输出包含 `images/`、`atlas_manifest.json` 与说明文件的 ZIP

使用方式：

1. 用浏览器打开 `spine-atlas-to-images.html`。
2. 拖入 `.atlas` 文件。
3. 选择或拖入对应的合图 `.png`。
4. 如需更完整的 mesh/UV 解包，可选择对应的 `.json`。
5. 点击 `生成切图`。
6. 点击 `下载 images.zip`。

注意：

- 浏览器安全机制不允许网页自动把文件保存到 `.atlas` 所在目录。
- 下载位置由浏览器设置决定。
- JSON / UV 模式会按 Spine attachment 使用范围导出，可能比 atlas 原始 region 更多。
- 如需后续直接还原成原图集，建议关闭 JSON / UV 模式，使用 Atlas region 模式导出。

### 2. spine-images-to-atlas.html

把修改后的 Spine 切图按原 `.atlas` 的素材摆放烘回 page PNG，用于直接替换原图集。

功能：

- 导入原 `.atlas`
- 导入一张/多张原 page PNG
- 导入修改后的 `images/*.png` 与推荐的 `atlas_manifest.json`
- 支持 `rotate: true` 区域反向打包
- 支持按 `orig / offset` 从透明画布中裁回 packed region
- 可选择以原图集为底，只覆盖找到的切图区域
- 输出 page 路径与原 `.atlas` 一致的 ZIP

使用方式：

1. 用浏览器打开 `spine-images-to-atlas.html`。
2. 选择原 `.atlas` 文件。
3. 选择原图集 page PNG。
4. 选择修改后的 `images` 文件夹，建议同时包含 `atlas_manifest.json`。
5. 确认切图坐标类型，通常保持 `自动识别（推荐）`。
6. 点击 `生成还原图集 ZIP`。
7. 将 ZIP 内的 PNG 放入模型副本中替换原图集 PNG。

注意：

- 若修改后的切图仍是解包时的原始尺寸 / 透明画布，可直接回填。
- 若切图来自 Photoshop 姿势 PSD，请优先使用 `spine-json-to-photoshop-jsx.html` 同步生成的逆向 JSX。它会按图片名、原始 PNG 像素尺寸、摆放尺寸和旋转角反向导出。
- 不改变 `.atlas` 文件时，超出原 `origWidth / origHeight` 或原 packed 区域的新增内容会被裁掉。

### 3. spine-json-to-photoshop-jsx.html

从 Spine `.json` 生成 Photoshop 可执行的 `.jsx` 脚本，用于按 setup pose 视觉还原图层位置。

功能：

- 拖入 Spine `.json`
- 解析 bones、slots、skins、region、mesh
- 按 Spine slot 顺序生成 Photoshop 图层
- 生成可复制或下载的 `.jsx`
- 同步生成逆向 `.jsx`，可从修改后的 PSD 导出回 `images/*.png`
- 图层名保留切图原名称
- 默认以普通图层放置切图，并把图片名、原始 PNG 像素尺寸和变换写入 PSD 隐藏元数据层，方便逆向导出
- 可选使用智能对象放置切图
- 对空图层或单层失败做容错，避免整个脚本中断

使用方式：

1. 用浏览器打开 `spine-json-to-photoshop-jsx.html`。
2. 拖入 Spine `.json`。
3. 点击 `下载拼图 JSX`，也可以同时下载 `下载逆向 JSX`。
4. 在 Photoshop 中选择 `文件 > 脚本 > 浏览...`。
5. 运行拼图 `.jsx` 文件。
6. 弹窗出现时，选择对应的 `images` 文件夹。
7. 修改 PSD 后，保持该 PSD 打开，再运行逆向 `.jsx`，先选择输出目录；如 PSD 是旧脚本生成的或没有隐藏元数据，再选择原始解包 `images` 文件夹作为尺寸参考。

注意：

- `region` 附件会按坐标还原。
- `mesh` 附件会拟合为 Photoshop 可执行的缩放、旋转和平移。
- 复杂 mesh 弯曲不会在 JSX 中做三角形变形，因此结果偏向视觉还原而非完全变形还原。
- 如果后续要把修改内容回填到原 atlas，建议保持“以智能对象放置切图”关闭，使用普通图层编辑，再运行逆向 JSX 导出切图。
- 逆向 JSX 会导出 PSD 中当前图层像素，再按记录的图片名、原始 PNG 像素尺寸、摆放尺寸和旋转角做反向处理；普通图层和智能对象图层都会走同一套还原逻辑。
- 新拼图 JSX 会在 PSD 中写入 `__spine_roundtrip_manifest__` 隐藏文本层；如果使用旧 PSD，逆向 JSX 可通过原始解包 `images` 文件夹读取真实 PNG 宽高。
- 如果图层被用户额外自由变换，仍可能产生偏差。

### 4. live2d-psd-rebuilder.html

从 Live2D Cubism 导出包生成 Photoshop 可运行的 `.jsx` 和 ArtMesh 切图 ZIP，用于近似还原分层 PSD。

功能：

- 导入 `.model3.json`、`.moc3`、`textures/*.png` 和可选 `.motion3.json`
- 使用 Cubism Core 读取 drawable、网格、UV、透明度和 render order
- 按 Live2D 默认绘制顺序生成切图和 Photoshop 图层
- 支持 `motion3.json` 姿态采样，默认优先选择 `stand.motion3.json`
- 支持 UV V 轴翻转，适配 PNG Canvas 的坐标原点
- 输出包含 `.jsx`、`slices/` 和 `manifest.json` 的 ZIP

使用方式：

1. 用浏览器打开 `live2d-psd-rebuilder.html`。
2. 选择包含 `.model3.json`、`.moc3` 和 `textures` 的 Live2D 模型目录。
3. 根据需要选择动作姿态、采样时间和画布倍率。
4. 点击 `生成 JSX + 切图 ZIP`。
5. 解压下载的 ZIP，保持 `.jsx` 和 `slices` 文件夹在同一目录。
6. 在 Photoshop 中选择 `文件 > 脚本 > 浏览...`，运行 `.jsx`。

注意：

- 这是从运行时 `.moc3` 数据反推的近似分层 PSD，不是原始 PSD。
- 原始图层组、剪贴关系、隐藏原画层和 Cubism 工程信息无法完整恢复。
- 此工具内置 Live2D Cubism Core JavaScript 运行时，相关 Live2D 条款仍然适用。


### 5. live2d-texture-rebuilder.html

Live2D 专用的切图反向还原图集工具，用于把 `live2d-psd-rebuilder.html` 生成并修改后的 `slices` 烘回原始 Live2D texture atlas。

功能：

- 导入原 `.model3.json`、`.moc3`、`textures/*.png` 和可选 `.motion3.json`
- 导入修改后的 `slices/*.png` 与推荐的 `manifest.json`
- 根据 Cubism drawable 顶点、UV 和 texture index 逐三角形反向绘制回图集
- 输出路径与 `.model3.json` 中的 texture 引用一致，例如 `textures/texture_00.png`
- 可选择使用原图集为底，只覆盖导入切图覆盖到的区域
- 导入切图可用替换模式写入，透明度变化会覆盖原图集像素
- 可复用 PSD 重建工具的边缘清理逻辑，导入切图和未替换原始区域都会减少三角面接缝、黑边和透明采样边

使用方式：

1. 用浏览器打开 `live2d-texture-rebuilder.html`。
2. 选择原 Live2D 模型目录。
3. 选择修改后的 `slices` 文件夹，最好同时包含 `manifest.json`。
4. 确认动作姿态、采样时间、画布倍率和 UV V 轴设置与生成切图时一致。
5. 点击 `生成 Live2D 图集 ZIP`。
6. 将 ZIP 内的 `textures/*.png` 放入模型副本中替换原贴图。

## English

### 1. spine-atlas-to-images.html

Extracts individual PNG images from a Spine `.atlas` file and its packed page PNG.

Features:

- Drag and drop or select a `.atlas` file
- Select or drag and drop one or more page PNG files
- Optionally load the matching Spine `.json`
- Handles `rotate: true` packed regions
- Can restore transparent canvas using `orig / offset`
- JSON mode can export more precise attachment/UV-based crops
- Exports a ZIP containing `images/`, `atlas_manifest.json`, and notes

Usage:

1. Open `spine-atlas-to-images.html` in a browser.
2. Drop or select the `.atlas` file.
3. Select or drop the matching packed `.png` page image.
4. Optionally select the matching `.json` for attachment/UV-based extraction.
5. Click `生成切图` / `Generate images`.
6. Click `下载 images.zip` / `Download images.zip`.

Notes:

- A browser page cannot automatically save files into the original `.atlas` directory.
- The download location is controlled by your browser settings.
- JSON/UV mode may export more images than the raw atlas regions because it follows actual Spine attachments.
- For direct atlas rebuilding, Atlas region mode is recommended instead of JSON/UV mode.

### 2. spine-images-to-atlas.html

Bakes modified Spine slices back into the original atlas page layout so the exported PNGs can replace the original atlas textures directly.

Features:

- Loads the original `.atlas`
- Loads one or more original page PNG files
- Loads modified `images/*.png` and the recommended `atlas_manifest.json`
- Re-packs `rotate: true` regions
- Crops packed regions from restored `orig / offset` transparent canvases
- Can preserve the original atlas as the base and overwrite only matched slice regions
- Exports a ZIP whose page paths match the original `.atlas`

Usage:

1. Open `spine-images-to-atlas.html` in a browser.
2. Select the original `.atlas` file.
3. Select the original atlas page PNG files.
4. Select the modified `images` folder, preferably with `atlas_manifest.json`.
5. Keep the slice coordinate mode on `自动识别（推荐）` / auto unless you know the source type.
6. Click `生成还原图集 ZIP` / `Generate rebuilt atlas ZIP`.
7. Copy the exported PNGs into a duplicate model folder to replace the original atlas pages.

Notes:

- Slices that still match the extracted original size or transparent canvas can be baked back directly.
- If slices come from a Photoshop pose PSD, prefer the reverse JSX generated by `spine-json-to-photoshop-jsx.html`; it uses recorded image names, original sizes, and rotations to export slices back.
- If the `.atlas` is unchanged, new artwork outside the original `origWidth / origHeight` or packed region will be clipped.

### 3. spine-json-to-photoshop-jsx.html

Generates a Photoshop `.jsx` script from a Spine `.json` file to visually rebuild layers in setup pose.

Features:

- Drag and drop a Spine `.json`
- Parses bones, slots, skins, regions, and meshes
- Preserves Spine slot draw order
- Generates a downloadable/copyable Photoshop JSX script
- Also generates a reverse JSX script that exports edited PSD layers back into `images/*.png`
- Uses original image names as Photoshop layer names
- Places slices as regular raster layers by default while writing image names, original PNG pixel sizes, and transforms into a hidden PSD metadata layer for reverse export
- Can optionally place slices as Smart Objects
- Skips empty or failed layers safely instead of stopping the whole script

Usage:

1. Open `spine-json-to-photoshop-jsx.html` in a browser.
2. Drop the Spine `.json` file.
3. Click `下载拼图 JSX` / `Download build JSX`, and optionally download `下载逆向 JSX` / `Download reverse JSX`.
4. In Photoshop, choose `File > Scripts > Browse...`.
5. Run the build `.jsx` file.
6. When prompted, select the matching `images` folder.
7. After editing the PSD, keep it open, run the reverse `.jsx`, choose an output folder, then select the original unpacked `images` folder if the PSD was generated by an older script or has no hidden metadata.

Notes:

- Region attachments are positioned directly.
- Mesh attachments are approximated with Photoshop-supported scale, rotation, and translation.
- Complex mesh deformation is not triangulated in JSX, so the result is intended for visual reconstruction rather than perfect deformation reproduction.
- If you plan to rebuild the original atlas, keep Smart Object placement disabled, edit regular layers, then run the reverse JSX to export slices.
- The reverse JSX exports the current PSD layer pixels, then uses recorded image name, original PNG pixel size, placed size, and rotation to inverse-transform the layer. Regular layers and Smart Object layers use the same restore path.
- New build JSX files add a hidden `__spine_roundtrip_manifest__` text layer to the PSD. For older PSDs, the reverse JSX can read exact PNG dimensions from the original unpacked `images` folder.
- Extra manual free transforms may still introduce drift.

### 4. live2d-psd-rebuilder.html

Generates a Photoshop `.jsx` script and ArtMesh slice images from a Live2D Cubism runtime export package, for approximate layered PSD reconstruction.

Features:

- Loads `.model3.json`, `.moc3`, `textures/*.png`, and optional `.motion3.json`
- Reads drawable meshes, UVs, opacity, and render order through Cubism Core
- Exports a ZIP containing a JSX script, `slices/`, and `manifest.json`
- Supports motion pose sampling, defaulting to `stand.motion3.json` when available
- Supports UV V-axis flipping for PNG canvas sampling
- Runs offline as a standalone HTML file with Cubism Core embedded

Usage:

1. Open `live2d-psd-rebuilder.html` in a browser.
2. Select a Live2D model folder containing `.model3.json`, `.moc3`, and `textures`.
3. Choose a motion pose, sample time, and canvas scale if needed.
4. Click `生成 JSX + 切图 ZIP` / `Generate JSX + slices ZIP`.
5. Unzip the downloaded package, keeping the `.jsx` file next to the `slices` folder.
6. In Photoshop, choose `File > Scripts > Browse...` and run the `.jsx` file.

Notes:

- This is an approximate rebuild from runtime `.moc3` data, not the original PSD.
- Original layer groups, clipping relationships, hidden source art, and Cubism project data cannot be fully recovered.
- This tool embeds the Live2D Cubism Core JavaScript runtime; the applicable Live2D terms still apply.


### 5. live2d-texture-rebuilder.html

A Live2D-specific reverse texture rebuilder. It bakes modified `slices` generated by `live2d-psd-rebuilder.html` back into the original Live2D texture atlas layout.

Features:

- Loads the original `.model3.json`, `.moc3`, `textures/*.png`, and optional `.motion3.json`
- Loads modified `slices/*.png` and the recommended `manifest.json`
- Uses Cubism drawable vertices, UVs, and texture indices to draw each slice back into atlas space triangle by triangle
- Exports PNG paths matching the `.model3.json` texture references, such as `textures/texture_00.png`
- Can preserve the original texture as the base and overwrite only imported slice regions
- Can write imported slices in replace mode so alpha changes overwrite original atlas pixels
- Can reuse the PSD rebuilder edge cleanup pass on imported slices and untouched original regions to reduce triangle seams, dark fringes, and transparent sampling edges

Usage:

1. Open `live2d-texture-rebuilder.html` in a browser.
2. Select the original Live2D model folder.
3. Select the modified `slices` folder, preferably with `manifest.json` included.
4. Match the motion pose, sample time, canvas scale, and UV V-axis option used when the slices were generated.
5. Click `生成 Live2D 图集 ZIP` / `Generate Live2D texture ZIP`.
6. Copy the exported `textures/*.png` into a duplicate model folder to replace the original textures.

## License

No license has been specified yet.
