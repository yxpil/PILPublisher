# PiLPublisher

局域网文件共享工具，基于 Tauri v2 + Rust + TypeScript。

## 功能

- **果绿色无边框圆角窗口**，白色药丸关闭/最小化/全屏按钮
- **HTTP 文件服务器**（端口 9726），局域网内任意设备浏览器访问
- **文件夹共享**：添加文件夹，一键开启/关闭共享
- **网页端浏览下载**：手机扫码即可访问，支持子目录浏览
- **可选的访问密码**保护
- **权限控制**：上传/重命名/删除需在桌面端单独开启（默认关闭）
- **文件夹/密码持久记忆**，重启自动恢复
- **二维码扫码访问**，自动获取局域网 IP
- **操作日志**

## 开发

```bash
npm install
npm run tauri dev
```

## 打包

```bash
npm run tauri build    # 或直接用 Inno Setup 编译 installer.iss
```

## 技术栈

- Tauri v2 (Rust + WebView2)
- Vite + TypeScript
- tiny_http (内嵌 HTTP 服务器)
- 果绿色 #A8E6CF 主题

---

<div align="center">

<a href="https://github.com/yxpil/PILPublisher">
  <img width="100%" src="https://alittlecatgirlpanel.yxp.hk/card?repo=yxpil/PILPublisher" alt="gh-card · yxpil/PILPublisher" />
</a>

<sub>Powered by <a href="https://alittlecatgirlpanel.yxp.hk"><b>gh-card</b></a> · 粉色手写体 README 仓库名片</sub>

</div>
