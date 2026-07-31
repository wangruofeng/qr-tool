# qr-tool — 项目约定（给 AI 协作时参考）

纯前端、单文件的二维码生成与识别工具。面向人类的说明见 `README.md`；本文件记录**改代码时必须知道的红线与约定**。项目与同级目录 `../emoji-picker`、`../country-info` 等保持同一套设计与代码风格。

## 红线（不要破坏）

- **单文件、零构建**：全部 HTML / CSS / JS 都在 `index.html` 一个文件里。禁止引入打包器、框架、npm 依赖或构建步骤。
- **纯前端、无后端**：生成与识别全在浏览器本地完成，内容不上传。
- **两个内嵌库（算法库代码内嵌，非运行时依赖）**：`index.html` 内嵌了两份单文件源码，均无 npm/CDN 运行时依赖，升级时用新版单文件整段替换、不要拆散：
  - **qrcode-generator**（Kazuhiko Arase，MIT）—— 二维码编码，库区在 `var qrcode = function() {` 到对应 UMD 包装 `}));` 之间（无显式开始注释）。
  - **JsBarcode**（Johan Lindell，MIT）—— 条形码生成，用 `/* ===== JsBarcode 条形码库开始 ===== */` / `结束` 一对注释包裹，全局暴露 `window.JsBarcode`。
- **渲染用户输入一律 `createElement` / `textContent`**，禁止 `innerHTML` 拼接动态内容（XSS 红线）。静态 SVG 图标常量除外。
- 主题用 `html.dark` 类 + CSS 变量，不要硬编码颜色。

## 关键实现点

- 生成：顶部「二维码 / 条形码」模式切换（`#genCard` 的 `data-mode`）控制 `draw()` 分发。二维码用 `qrcode(0, ec)`（typeNumber 0 自动扩容），预览画布按模块数自适应 cellSize；条形码用 `JsBarcode(canvas, text, {format,...})` 直接渲染。下载/复制走 `fullCanvas()`（二维码带边距高分辨率 / 条形码放大条宽）；SVG 二维码用 `<path>` 逐模块拼 `d`，条形码用 `JsBarcode(svgEl,...)` 取 `outerHTML` 并补 `xmlns`。条形码码制合法性交给 JsBarcode（非法抛错，catch 取 `e.text` 显示）。
- 识别：`BarcodeDetector`，formats 由 `getSupportedFormats()` 动态取本浏览器支持的子集（`WANT_FORMATS` = 二维码 + 7 种条形码），结果带码制标签（`results[0].format`）。三种输入源：文件选择/拖拽、document paste 事件、摄像头 `getUserMedia` + 400ms 轮询；识别成功自动停摄像头。**上传图片必须先 `createImageBitmap(File)` 再 `detect`**——`detect()` 不接受 File/Blob，否则报 "Unsupported source"。Firefox 不支持 BarcodeDetector，UI 已做降级提示。
- 复制图片用 `ClipboardItem`，不支持的浏览器降级为提示下载。
- **内嵌库源码注入必须绕开 `String.replace` 对 `$` 的解释**：库源码（qrcode.js 含 `case '$'`、JsBarcode min 含大量 `$`）作为 `replace` 替换字符串时，其中的美元符序列会被展开，导致文件被注入成两份。改用 `indexOf` + `slice` 拼接写入，或 `replace(marker, () => lib)` 函数形式。验证方式：生成 → 下载 PNG → BarcodeDetector 回环识别应得到原文（二维码与 CODE128 / EAN-13 均需通过）。

## 在线地址

已部署到 GitHub Pages：https://blog.wangruofeng007.com/qr-tool/
