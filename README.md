# 🔐 秘匣 Vault

> 纯本地、离线可用的个人密码安全保险箱。**一切数据留在本机，不上云、不联网**，你的密码只属于你。

秘匣（Vault）是一款基于 **Tauri 2 + Vue 3** 的桌面密码管理器。主密码使用 **AES-256-GCM** 加密，配合 PBKDF2 迭代派生，后端只存密文、不触任何明文。即使数据文件泄露，也无法反推出你的任何密码。

<!-- 本文件为对外发布版（用于 GitHub 仓库根目录）。桌面版开发说明见仓库根的 README.md 说明书。 -->

```
🔑 密码库  ·  🖥️ SSH/服务器  ·  🧩 API/开发者  ·  🛡️ 安全工具  ·  ⚙️ 设置
```

---

## ✨ Badges

![Tauri 2](https://img.shields.io/badge/Tauri%20-2-24c8db?style=flat-square)
![Vue 3.5](https://img.shields.io/badge/Vue-3.5-42b883?style=flat-square)
![TypeScript](https://img.shields.io/badge/TypeScript-5.7-3178c6?style=flat-square)
![Vite 6](https://img.shields.io/badge/Vite-6-646cff?style=flat-square)
![License MIT](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![离线可用](https://img.shields.io/badge/离线-纯本地-0d9488?style=flat-square)

---
## 📦 项目演示
---
### 首页
![首页](./img/1.png)

---
### 密码库
![首页](./img/2.png)

---
### 安全工具
![首页](./img/3.png)

---
### 设置
![首页](./img/4.png)

---

## ✨ 功能特性

| 模块 | 能力 |
|---|---|
| 🔑 密码库 | 平台账号增删改查、一键复制、标签、搜索、收藏 |
| 🖥️ SSH / 服务器 | 服务器信息管理、一键复制 `ssh` 连接命令 |
| 🧩 API / 开发者 | 接口与密钥管理、一键复制 `curl` 命令 |
| 🛡️ 安全工具 | 安全密码生成器、账号安全体检、剪贴板自动清理 |
| ⚙️ 设置 | 分组导入导出（CSV / Markdown）、修改主密码 |
| 🔐 锁屏 | 首次设置主密码、主动锁定/解锁、**重启后仍锁定** |

---

## 🔐 数据安全模型

- **主密码从不明文落盘**：首次设置时随机生成 16 字节盐（salt）。
- **PBKDF2（SHA-256，210,000 次迭代）** 派生密钥加密钥匙（KEK）。
- 数据密钥为 32 字节随机数，用 KEK 通过 **AES-256-GCM** 封装。
- 主密码正确性以 16 字节校验值验证，采用**常数时间比较**，规避侧信道攻击。
- 修改主密码只需重新封装数据密钥，**无需重加密已有数据**。
- Rust 后端仅负责密文存取，**不触碰任何明文**；文件泄露也无法反推内容。

---

## 💾 存储设计

| 运行环境 | 落盘位置 |
|---|---|
| 打包运行（Tauri） | 写入系统应用数据目录（Windows ≈ `%APPDATA%\com.secretbox.vault`）|
| 纯 Web 预览（`vite dev`） | 降级使用 `localStorage`，便于跑通交互闭环 |

上层业务统一走 `storage.ts` 抽象层，两种模式无缝切换，**数据真实落盘、重启不丢**。


---

## 🧬 导入导出

- **导出**：CSV（UTF-8 BOM，Excel 可直接打开）/ Markdown，支持**脱敏或明文**。
- **导入**：CSV，自动识别编码（UTF-8 → GBK 回退）、校验表头，并按**业务唯一键去重**跳过重复：
  - 密码库：平台名称 + 网址 + 用户名
  - SSH：服务器名称 + 主机 + 端口 + 用户名
  - API：接口名称 + 方法 + 请求地址

---

## 🛠 技术栈

- **桌面框架**：Tauri 2（Rust 后端）
- **前端**：Vue 3.5 + TypeScript + Vite 6 + Pinia + Vue Router
- **图标**：[Lucide](https://lucide.dev/)（lucide-vue-next）
- **加密**：Web Crypto（浏览器原语），零第三方加密依赖

---

## 🚀 快速开始

**环境要求**：Node.js ≥ 20、Rust（含 cargo）、系统 WebView2（Windows）。

```bash
npm install            # 安装依赖
npm run dev            # 纯前端预览（浏览器 localhost:5173）
npm run tauri dev      # 桌面应用开发模式（前端热更新 + Rust 后端）
npm run build          # 前端类型检查 + 产物构建
```
---


## 🤝 参与贡献

欢迎提 Issue、提 PR。请保持**单一强调色**设计体系（深青 `#0D9488`）、Lucide 线性图标，并遵循「纯本地、零广告、不收集任何数据」的产品原则。

---

## 📜 许可证

本项目基于 **MIT License** 开源。

⚠️ **免责声明**：本项目面向个人密码管理场景开源，使用前请自行评估风险；请务必妥善保管主密码，忘记将无法找回数据。