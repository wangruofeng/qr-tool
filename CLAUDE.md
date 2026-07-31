# qr-tool — 项目约定（给 AI 协作时参考）

纯前端、单文件的二维码生成与识别工具。面向人类的说明见 `README.md`；本文件记录**改代码时必须知道的红线与约定**。项目与同级目录 `../emoji-picker`、`../country-info` 等保持同一套设计与代码风格。

## 红线（不要破坏）

- **单文件、零构建**：全部 HTML / CSS / JS 都在 `index.html` 一个文件里。禁止引入打包器、框架、npm 依赖或构建步骤。
- **纯前端、无后端**：生成与识别全在浏览器本地完成，内容不上传。
- **唯一例外依赖**：`index.html` 内嵌了 qrcode-generator（Kazuhiko Arase，MIT）的单文件源码（`/* 生成：qrcode-generator 库开始 */` 附近），这是算法库代码内嵌，不是运行时外部依赖。升级时用新版单文件整段替换，不要拆散或引入 npm 版本。
- **渲染用户输入一律 `createElement` / `textContent`**，禁止 `innerHTML` 拼接动态内容（XSS 红线）。静态 SVG 图标常量除外。
- 主题用 `html.dark` 类 + CSS 变量，不要硬编码颜色。

## 关键实现点

- 生成：`qrcode(0, ec)`（typeNumber 0 自动扩容），预览画布按模块数自适应 cellSize；下载/复制走 `fullCanvas()`（带边距高分辨率）；SVG 用 `<path>` 逐模块拼 `d`。
- 识别：`BarcodeDetector({formats:['qr_code']})`，三种输入源：文件选择/拖拽、document paste 事件、摄像头 `getUserMedia` + 400ms 轮询；识别成功自动停摄像头。Firefox 不支持 BarcodeDetector，UI 已做降级提示。
- 复制图片用 `ClipboardItem`，不支持的浏览器降级为提示下载。
- **内嵌库替换脚本必须用 `html.replace(placeholder, () => lib)` 函数形式**：qrcode.js 源码含 `case '$'`，用字符串作替换值时 `$'` 会被展开成「匹配点之后的全部内容」，导致文件被注入成两份。验证方式：生成 → `fullCanvas()` → BarcodeDetector 回环识别应得到原文。

## 在线地址

已部署到 GitHub Pages：https://blog.wangruofeng007.com/qr-tool/
