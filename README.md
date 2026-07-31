# 二维码工具

一个纯前端的单文件二维码生成与识别工具，双击 `index.html` 即可使用，无需安装、无需后端、无任何外部依赖。

## 功能特性

- ▦ **即时生成**：输入文本/链接自动出码，支持 L/M/Q/H 四级容错、边距调节。
- 📱 **快捷模板**：链接、WiFi（`WIFI:T:WPA;S:名称;P:密码;;`）、邮箱、电话、短信一键填入。
- 💾 **多种导出**：下载 PNG（高分辨率带边距）、下载 SVG（矢量）、直接复制图片到剪贴板。
- 🔍 **本地识别**：选择/拖拽/粘贴图片识别二维码，或调用摄像头实时扫码（基于原生 BarcodeDetector，Chrome/Edge/Safari）。
- 🔗 **识别即跳转**：结果为链接时一键打开，任意结果一键复制。
- 🌓 **深浅色主题**：偏好保存在 `localStorage qr-tool-theme`。

## 使用方式

直接用浏览器打开 `index.html` 即可。摄像头扫码需要 HTTPS 或 localhost 环境并授权摄像头权限。

## 技术说明

- 单一 HTML 文件，零构建步骤。
- 生成器内嵌 [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator)（Kazuhiko Arase，MIT 协议）；识别使用浏览器原生 BarcodeDetector API。
- 所有生成与识别均在浏览器本地完成，内容不上传。
- 动态渲染一律使用 `createElement` / `textContent`，避免 XSS 注入风险。

## 在线访问

已通过 GitHub Pages 部署，可直接访问：

🔗 **https://blog.wangruofeng007.com/qr-tool/**

源码仓库：https://github.com/wangruofeng/qr-tool
