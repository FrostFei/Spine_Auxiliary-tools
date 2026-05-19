# Spine Auxiliary Tools

离线可用的 Spine 辅助工具集合，用于从 Spine 资源中解包切图，以及从 Spine JSON 生成 Photoshop 可执行脚本。

This repository contains offline HTML utilities for working with Spine assets: extracting images from atlas files and rebuilding a Photoshop layer layout from Spine JSON.

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
- 输出 `images.zip`

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

### 2. spine-json-to-photoshop-jsx.html

从 Spine `.json` 生成 Photoshop 可执行的 `.jsx` 脚本，用于按 setup pose 视觉还原图层位置。

功能：

- 拖入 Spine `.json`
- 解析 bones、slots、skins、region、mesh
- 按 Spine slot 顺序生成 Photoshop 图层
- 生成可复制或下载的 `.jsx`
- 图层名保留切图原名称
- 对空图层或单层失败做容错，避免整个脚本中断

使用方式：

1. 用浏览器打开 `spine-json-to-photoshop-jsx.html`。
2. 拖入 Spine `.json`。
3. 点击 `下载 JSX`。
4. 在 Photoshop 中选择 `文件 > 脚本 > 浏览...`。
5. 运行下载的 `.jsx` 文件。
6. 弹窗出现时，选择对应的 `images` 文件夹。

注意：

- `region` 附件会按坐标还原。
- `mesh` 附件会拟合为 Photoshop 可执行的缩放、旋转和平移。
- 复杂 mesh 弯曲不会在 JSX 中做三角形变形，因此结果偏向视觉还原而非完全变形还原。

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
- Exports an `images.zip`

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

### 2. spine-json-to-photoshop-jsx.html

Generates a Photoshop `.jsx` script from a Spine `.json` file to visually rebuild layers in setup pose.

Features:

- Drag and drop a Spine `.json`
- Parses bones, slots, skins, regions, and meshes
- Preserves Spine slot draw order
- Generates a downloadable/copyable Photoshop JSX script
- Uses original image names as Photoshop layer names
- Skips empty or failed layers safely instead of stopping the whole script

Usage:

1. Open `spine-json-to-photoshop-jsx.html` in a browser.
2. Drop the Spine `.json` file.
3. Click `下载 JSX` / `Download JSX`.
4. In Photoshop, choose `File > Scripts > Browse...`.
5. Run the downloaded `.jsx` file.
6. When prompted, select the matching `images` folder.

Notes:

- Region attachments are positioned directly.
- Mesh attachments are approximated with Photoshop-supported scale, rotation, and translation.
- Complex mesh deformation is not triangulated in JSX, so the result is intended for visual reconstruction rather than perfect deformation reproduction.

## License

No license has been specified yet.
