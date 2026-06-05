# w04_web_app — 蓝牙下发协议 Web 验证页

一个纯前端的 **Web Bluetooth 探针**，用于在**安卓手机浏览器**上验证能否连接设备、订阅通知、并按 L1/L2 协议收发原始报文。这是“手机下发文件到设备”完整 PWA 的第一步——先用真机确认链路通，再做完整传输界面。

## 功能

- 连接设备（扫描即连，无需配对）：Service `FFD0` / 写 `FFD1` / 通知 `FFD2`（UUID 均可在页面修改）
- 订阅 `FFD2` 通知，自动按 L1 帧（`0xAB` 帧头 + CRC16 + seq）拆帧，并解析内层 L2（`0x0B` 文件命令 / `0x0C` 连接参数）
- 对收到的数据帧自动回 **L1 ACK**（与设备做双向握手验证）
- 可发送测试帧：连接参数请求 `0x0C`、或自定义 L2 hex（自动加 L1 帧头/CRC16/seq 并按分片大小写出）
- 可选「屏幕常亮」(Wake Lock)，防止息屏断连
- 实时报文日志（TX/RX/ACK 分色，hex + 协议字段解析）

协议字段定义见上游仓库的 `PROTOCOL.md`。

## 运行环境（重要）

- ✅ **安卓 Chrome / Edge**
- ❌ iOS Safari/Chrome 不支持 Web Bluetooth
- **必须 HTTPS**（或 `localhost`）。`file://` 直接打开不可靠，不要用。

## 本地调试

```bash
# 任选其一，在电脑上起一个本地静态服务器
python -m http.server 8000
# 然后用 Chrome 的 chrome://inspect 把安卓手机的页面连到电脑调试，
# 或把目录部署到任意 HTTPS 静态托管后用手机访问。
```

> 注意：手机和电脑的 `localhost` 不互通；真机访问必须是手机能到达的 **HTTPS** 地址。

## 部署

纯静态文件，丢到任意 HTTPS 静态托管即可（GitHub Pages / Vercel / Netlify / 内网 HTTPS）。
仓库根目录已含 `.nojekyll`，适配 GitHub Pages 直接发布根目录。

## 文件

| 文件 | 说明 |
|---|---|
| `index.html` | 自包含的验证页（UI + L1/L2 协议 + Web Bluetooth） |
| `manifest.json` | PWA 清单（可「添加到主屏幕」） |
| `icon.svg` | 应用图标 |
| `.nojekyll` | 让 GitHub Pages 原样发布静态文件 |
