# WT-Agent AI Debate Arena

## Project Introduction

<div align="center">

# [WT-Agent AI Debate Arena]

**[A simulated AI debate and argumentation simulator, supporting stance selections and 5 unique opponent styles, built with Vue 3 + Vite + Vanilla CSS]**

[Screenshot or GIF if available]

[Vue3] · [TypeScript] · [Vite] · [Node.js] · [MIT License]

[![GitHub stars](https://img.shields.io/github/stars/WT-Agent/ai-bianlun?style=social)](https://github.com/WT-Agent/ai-bianlun)
[![GitHub license](https://img.shields.io/github/license/WT-Agent/ai-bianlun)](https://github.com/WT-Agent/ai-bianlun/blob/main/LICENSE)

[🚀 Live Demo](#-live-demo) · [📖 Quick Start](#-quick-start) · [💬 Contribute](#-contribute) · [☕ Support Us](#-support-us)

</div>

## About Us

Developed by a team of alumni mostly from C9 universities and previously worked at ByteDance, Tencent, and Alibaba.

We build open-source projects full-time, aiming to let everyone experience the welfare of AI.

This project simulates a high-quality debate match. Users input a debate topic (or choose a classic conflict) and select their stance (Affirmative, Negative, or Referee). The AI dynamically generates counter-arguments based on the selected opponent style (e.g. Sarcastic Trolls, Gentle Scholars, or Big Boss Minds).

**We do not pitch concepts or sell courses; we only write code that runs.**

Welcome to Star, Fork, and open Issues to build this project together.

Core Features:
- **Interactive Stance Selection**: Users can choose to defend a topic (Affirmative), attack it (Negative), or objectively judge it as a referee.
- **5 Opponent Debate Styles**:
  - **Sarcastic Trolls**: Uses absurd reasoning, nitpicking, and fallacies to argue.
  - **Gentle Scholars**: Polite and elegant, quoting historical classics and philosophy.
  - **Gritty Veteran**: Passionate, direct, using colloquial analogies to expose flaws.
  - **Cyberpunk AI**: Cold, logical, calculating pros and cons without any human emotion.
  - **Big Boss Round-Table** (Feature): Simulates a joint round-table debate featuring Elon Musk, Bill Gates, Mark Zuckerberg, Jeff Bezos, Steve Jobs, Plato, Albert Einstein, Nikola Tesla, and Qin Shi Huang arguing simultaneously.
- **Minimalist Interaction**: Glassmorphism dark themed responsive Web UI, highly optimized for mobile H5 experience.
- **One-Click Deployment**: Pure static frontend structure, supporting zero-cost hosting on Vercel, GitHub Pages, or CDN/OSS/COS.
- **Development Proxy**: Supports using your own API key locally during development via Vite dev server proxy to avoid frontend leakage.

## Quick Start

### 1. Clone the Project
```bash
git clone https://github.com/WT-Agent/ai-bianlun.git
cd ai-bianlun
```

### 2. Install Dependencies
The project enforces using pnpm as the package manager:
```bash
pnpm install
```

### 3. Configure Local Environment Variables
Copy and modify the environment configuration file:
```bash
cp .env.example .env
```
Configure your developer API keys in `.env` based on the application type:
- `DEEPSEEK_API_KEY`: Your DeepSeek developer API key (for text generation tasks)
- `DASHSCOPE_API_KEY`: Your DashScope developer API key (for multimodal and image generation tasks)

### 4. Start Local Development Service
```bash
pnpm dev
```
Open the address printed in the console in your browser.

### 5. Production Build
```bash
pnpm build
```
The compiled files in `dist` can be uploaded directly to static hosting services.

### 6. Deploy to GitHub Pages

Since this project is a pure static frontend application, you can host it for free on GitHub Pages:

1. **Configure Base Path**:
   When deploying to a non-root domain (e.g., `https://<your-username>.github.io/ai-bianlun/`), make sure the `base` path in `vite.config.ts` is configured correctly. You can set `base` to `process.env.NODE_ENV === 'production' ? '/ai-bianlun/' : '/'` or `./` in `vite.config.ts`.
2. **Build Static Files**:
   Run the following command to generate the production bundle:
   ```bash
   pnpm build
   ```
   The compiled assets will be output to the `dist` directory.
3. **Push to Deployment Branch**:
   Push the contents of the `dist` directory to the `gh-pages` branch of your repository, then go to **Settings -> Pages** in your repository and set the source branch to `gh-pages`. Alternatively, you can use deployment helper tools (e.g., the `gh-pages` npm package):
   ```bash
   # Install deployment tool
   pnpm add -D gh-pages
   # Execute deployment
   npx gh-pages -d dist
   ```
4. **Automate via GitHub Actions** (Recommended):
   Create a `.github/workflows/deploy.yml` workflow file in the project root to automatically build and deploy to GitHub Pages on every push to the `main` branch.

## Contact Us

- GitHub Issues: [Submit Feedback](https://github.com/WT-Agent/ai-bianlun/issues)
- Email: your_email@example.com

## Support Us

If this project helps you, feel free to buy the authors a coffee. Your support keeps us maintaining and updating this project.

<div align="center">

**WeChat Pay** | **Alipay**
:---:|:---:
<img src="./asset/tenpay.png" width="200" alt="WeChat Pay"> | <img src="./asset/alipay.png" width="200" alt="Alipay">

</div>

- Buy Me a Coffee: https://buymeacoffee.com/your_profile

## License

This project is open-sourced under the MIT License.

Copyright (c) 2026. All rights reserved.
