<div align="center">

# [网腾无限AI - 辩论擂台]

**[一个支持立场设定与五种特色对手风格的 AI 辩论模拟器，具备深色玻璃拟态自适应交互与微信端 H5 体验]**

[Vue 3] · [TypeScript] · [Vite] · [Vanilla CSS] · [开源协议 MIT]

[![GitHub stars](https://img.shields.io/github/stars/WT-Agent/ai-bianlun?style=social)](https://github.com/WT-Agent/ai-bianlun)
[![GitHub license](https://img.shields.io/github/license/WT-Agent/ai-bianlun)](https://github.com/WT-Agent/ai-bianlun/blob/main/LICENSE)

[在线演示](#在线演示) · [快速启动](#快速启动) · [参与贡献](#参与贡献) · [支持一下](#支持一下)

</div>

## 关于我们

团队成员均来自 C9 等顶尖学府，在字节、腾讯、阿里的工程师组成，全职创业研发开源 AI 应用产品，让所有人感受 AI 的魅力。

本项目模拟了高水平的辩论对抗，用户可自由输入辩题，设定自己的立场（正方、反方或中立裁判），并挑选对手流派（九巨擘圆桌辩论、奇葩杠精、儒雅学者、暴躁老哥、赛博朋克）进行智能辩驳逻辑演练。

**我们不搞概念，不卖课，只写能跑起来的代码。**

欢迎 Star、Fork、提 Issue，一起让这个项目变得更好用。

核心特性：
- **极简自适应交互**：提供毛玻璃质感的深色玻璃拟态自适应 Web 界面，高度适配移动端 H5 微信浏览器与 PC 体验。
- **交互立场设定**：支持选择站位，可扮演捍卫观点的正方、驳斥观点的反方、或中立评判的裁判。
- **五大辩论对抗流派**：
  - **九巨擘圆桌辩论**：一次性模拟马斯克、比尔盖茨、扎克伯格、贝索斯、乔布斯、柏拉图、爱因斯坦、特斯拉、秦始皇共 9 人展开辩论大混战。
  - **奇葩杠精**：无厘头抬杠、偷换概念、寻找逻辑漏洞强词夺理。
  - **儒雅学者**：引经据典，温文尔雅但字字诛心。
  - **暴躁老哥**：大白话、反问句连珠炮，风格强悍刚猛直击痛点。
  - **赛博朋克**：纯理性逻辑与数据优化，冷静中立拆解利弊。
- **演示案例与分享卡片**：内置 30 条辩论主题精彩演示样例，并支持一键卡片化截图分享。
- **一键零成本部署**：纯静态前端结构，支持零成本部署于 Vercel、GitHub Pages 或 CDN/OSS 静态托管服务。
- **安全开发代理**：本地开发支持使用个人 API 密钥发起代理请求，密钥由 Vite 服务器中转，无需担心前端泄露。
- **裂变解锁与留存**：内置微信朋友圈扫码分享拦截与额度重置机制，提升流量转化与留存。

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

## 脚手架集成说明

本模板由私有总控仓库 `ai.wuxian.xyz` 中的 `@wuxian/cli` 脚手架统一管理，支持以下批量运维操作：

### 初始化或更新单个子项目

```bash
node bin/cli.js ai-bianlun
```

脚手架将自动：
1. 读取子仓库的 `README.md` 首行作为 Prompt 主题。
2. 注入 Vue 3 静态页面结构及标准配置文件。
3. 保留原有的 `.git` 配置与 `README.md`，不覆盖个性化内容。

### 批量同步所有子项目

```bash
node bin/cli.js all
```

将模板的最新变更（如 SSO 逻辑、额度控制）一键同步至全部 31 个子项目。

### Agent 配置维护接口

```bash
# 读取子项目配置
node bin/cli.js get ai-bianlun

# 写入/更新配置（支持热更新 prompt、model、title、temperature 等）
node bin/cli.js set ai-bianlun prompt "你是一个精通逻辑学与辩论技巧的AI辩论对抗大师..."
node bin/cli.js set ai-bianlun model deepseek-chat
```

## 联系方式

- GitHub Issues: [提交反馈](https://github.com/WT-Agent/ai-bianlun/issues)
- 邮箱: us@wuxian.xyz

## 打赏支持

如果本项目对您有帮助，欢迎请作者喝杯咖啡。您的支持是持续维护与更新的动力。

<div align="center">

**微信支付** | **支付宝**
:---:|:---:
<img src="./asset/tenpay.png" width="200" alt="微信支付"> | <img src="./asset/alipay.png" width="200" alt="支付宝">

</div>

## 版权与许可

本项目基于 MIT License 开源协议。

Copyright (c) 2026. All rights reserved.
