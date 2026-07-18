# 网腾无限AI辩论擂台

## 项目介绍

<div align="center">

# [网腾无限AI辩论擂台]

**[一个支持立场设定与五种特色对手风格的 AI 辩论模拟器，基于 Vue 3 + Vite + Vanilla CSS 构建]**

[如果有的话，放一张极具代表性的项目截图/GIF动图]

[Vue3] · [TypeScript] · [Vite] · [Node.js] · [开源协议如 MIT]

[![GitHub stars](https://img.shields.io/github/stars/WT-Agent/ai-bianlun?style=social)](https://github.com/WT-Agent/ai-bianlun)
[![GitHub license](https://img.shields.io/github/license/WT-Agent/ai-bianlun)](https://github.com/WT-Agent/ai-bianlun/blob/main/LICENSE)

[🚀 在线演示](#-在线演示) · [📖 使用文档](#-快速启动) · [💬 参与贡献](#-参与贡献) · [☕ 支持一下](#-支持一下)

</div>

## 关于我们

团队成员大多来自C9等顶尖学府并在字节、腾讯、阿里的校友，研发的AI产品。

全职创业做开源项目，想让所有人感受AI的魅力。

本项目模拟了高水平的辩论对抗，用户可自由输入辩题或选择冲突（如“上班摸鱼被老板抓到怎么诡辩”），设定自己的立场（正方、反方或裁判），并挑选对手流派（奇葩杠精、儒雅学者、暴躁老哥、赛博朋克或九巨擘圆桌辩论大混战）进行智能辩驳逻辑演练。

**我们不搞概念，不卖课，只写能跑起来的代码。**

欢迎Star、Fork、提Issue，一起让这个项目变得更好用。

核心特性：
- **交互立场设定**：支持选择站位，可扮演捍卫观点的“正方”、驳斥观点的“反方”、或中立评判的“裁判”。
- **五大辩论对抗流派**：
  - **奇葩杠精**：无厘头抬杠、偷换概念、寻找逻辑漏洞强词夺理。
  - **儒雅学者**：引经据典，庄重沉稳，温文尔雅但句句致命。
  - **暴躁老哥**：大白话、反问句连珠炮，风格强悍刚猛直击痛点。
  - **赛博朋克**：纯理性逻辑与数据优化，冷静中立拆解利弊。
  - **九巨擘圆桌辩论**（特色）：一次性模拟马斯克、比尔盖茨、扎克伯格、贝索斯、乔布斯、柏拉图、爱因斯坦、特斯拉、秦始皇共9人针对该辩题展开激烈的唇枪舌战大混战，输出圆桌会议辩词纪实。
- **极简交互设计**：提供毛玻璃质感的深色玻璃拟态自适应 Web 界面，高度适配移动端 H5 体验。
- **一键部署托管**：纯静态前端结构，支持零成本部署于 Vercel、GitHub Pages 或 CDN/OSS/COS 静态托管服务。
- **开发代理服务**：本地开发支持使用个人 API 密钥发起代理请求，密钥由 Vite 服务器中转，无需担心前端泄露。

## 快速启动

### 1. 克隆项目
```bash
git clone https://github.com/WT-Agent/ai-bianlun.git
cd ai-bianlun
```

### 2. 安装依赖
项目强制使用 pnpm 作为包管理器：
```bash
pnpm install
```

### 3. 配置本地开发环境变量
复制并修改环境变量配置文件：
```bash
cp .env.example .env
```
根据微应用的功能类型，在 `.env` 中配置您的开发者密钥：
- `DEEPSEEK_API_KEY`: 您的 DeepSeek 开发者 API 密钥（用于文本生成任务）
- `DASHSCOPE_API_KEY`: 您的通义千问/通义万相开发者 API 密钥（用于多模态与生图任务）

### 4. 启动本地开发服务
```bash
pnpm dev
```
启动成功后在浏览器访问控制台输出的地址即可。

### 5. 生产构建打包
```bash
pnpm build
```
打包后生成的 `dist` 目录即为纯静态网页资源，可直接上传部署。

### 6. 部署至 GitHub Pages

由于本项目是纯静态前端应用，您可以将其零成本部署在 GitHub Pages 上：

1. **配置基础路径 (Base Path)**：
   在打包部署到非根域名（例如 `https://<your-username>.github.io/ai-bianlun/`）时，需确保 `vite.config.ts` 中的 `base` 路径配置正确。在 `vite.config.ts` 中可将 `base` 设定为 `process.env.NODE_ENV === 'production' ? '/ai-bianlun/' : '/'` 或 `./`。
2. **构建静态文件**：
   运行以下命令生成打包文件：
   ```bash
   pnpm build
   ```
   打包产物将输出在 `dist` 目录下。
3. **推送到分支部署**：
   将 `dist` 目录下的内容推送到您仓库的 `gh-pages` 分支，或在仓库的 **Settings -> Pages** 中将源分支设置为 `gh-pages`。也可以使用第三方的部署工具（例如 `gh-pages` npm 包）：
   ```bash
   # 安装部署工具
   pnpm add -D gh-pages
   # 执行部署
   npx gh-pages -d dist
   ```
4. **使用 GitHub Actions 自动部署**（推荐）：
   在项目根目录创建 `.github/workflows/deploy.yml` 工作流文件，配置在每次向 `main` 分支推送代码时自动构建并部署至 GitHub Pages 分支。

## 联系方式

- GitHub Issues: [提交反馈](https://github.com/WT-Agent/ai-bianlun/issues)
- 邮箱: your_email@example.com

## 打赏支持

如果本项目对您有帮助，欢迎请作者喝杯咖啡。您的支持是持续维护与更新的动力。

<div align="center">

**微信支付** | **支付宝**
:---:|:---:
<img src="./asset/tenpay.png" width="200" alt="微信支付"> | <img src="./asset/alipay.png" width="200" alt="支付宝">

</div>

- Buy Me a Coffee: https://buymeacoffee.com/your_profile

## 版权与许可

本项目基于 MIT License 开源协议。

Copyright (c) 2026. All rights reserved.
