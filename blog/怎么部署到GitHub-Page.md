---
title: "怎么部署到GitHub-Page"
date: 2026-06-21
---

我在GitHub上使用Build and deployment
Source
Branch方式时，只能选择两个文件夹，/(root)和/docs，但我构建好的静态资源在/build下，怎么办

你遇到的问题非常典型：GitHub Pages 默认只允许从 /(root) 或 /docs 目录发布网站，但 Docusaurus（或其他框架）构建的静态文件默认输出到 /build 文件夹，导致无法直接选择。

别担心！有 3 种主流解决方案，推荐按顺序尝试：

✅ 方案 1：【最推荐】改用 gh-pages 分支部署（自动推送 /build 内容）

这是 Docusaurus 官方推荐的方式，完全绕过 GitHub Pages 对目录的限制。

步骤如下：

安装 gh-pages 工具
      npm install --save-dev gh-pages
   

在 package.json 中添加部署脚本
      {
     "scripts": {
       "build": "docusaurus build",
       "deploy": "docusaurus deploy"  // 👈 关键！
     }
   }
   

在 docusaurus.config.js 中配置部署信息
      module.exports = {
     // 必须设置 url 和 baseUrl
     url: 'https://你的用户名.github.io',
     baseUrl: '/你的仓库名/', // 注意：如果是用户主页（<用户名>.github.io），这里应为 '/'

     // ... 其他配置

     // 添加部署配置（可选，新版 Docusaurus 可自动推断）
     deploymentBranch: 'gh-pages', // 默认就是 gh-pages
     trailingSlash: false,
   };
   

运行部署命令
      # 设置 Git 用户名（首次需配置）
   git config --global user.email "你的邮箱"
   git config --global user.name "你的用户名"

   # 部署（会自动构建 + 推送到 gh-pages 分支）
   npm run deploy
   

在 GitHub 仓库中启用 gh-pages 分支作为源
   进入仓库 → Settings → Pages
   在 Source 处选择：
     Branch: gh-pages
     Folder: /(root)
   Save

✅ 几分钟后，你的网站就会通过 https://<用户名>.github.io/<仓库名> 访问！

📌 优点：自动化、安全、不污染主分支、支持自定义域名。

✅ 方案 2：将构建输出改为 /docs 目录（适合简单项目）

如果你不想用新分支，可以让 Docusaurus 把文件生成到 /docs，这样就能直接选 /docs 作为 Pages 源。

修改 docusaurus.config.js：
module.exports = {
  // 将输出目录改为 docs
  staticDirectories: ['static'],
  // 👇 关键配置
  onBuildSuccess: () => {
    const fs = require('fs');
    const path = require('path');
    const shell = require('shelljs');

    // 构建完成后，把 build 内容复制到 docs
    shell.rm('-rf', 'docs');
    shell.cp('-r', 'build/*', 'docs/');
    console.log('✅ Copied build output to /docs');
  },

  // 或者更简单：直接修改构建输出路径（Docusaurus v3 支持）
  // 但官方不推荐，所以用上面的 hook 更稳妥
};

或者更简单的做法（无需改代码）：

手动复制（每次构建后执行）
npm run build
rm -rf docs
cp -r build docs
git add docs
git commit -m "Update docs for GitHub Pages"
git push

然后在 GitHub Pages 设置中选择：
Branch: main（或你的主分支）
Folder: /docs

⚠️ 缺点：/docs 目录会混入源码，Git 历史变乱。

✅ 方案 3：使用 GitHub Actions 自动构建并部署到 gh-pages（高级）

适合 CI/CD 场景，每次 push 自动部署。

在 .github/workflows/deploy.yml 创建：

name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]  # 你的主分支名

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      uses: actions/checkout@v4
      uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      run: npm ci
      run: npm run build

      name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build

✅ 推送后，Action 会自动构建并将 ./build 内容推送到 gh-pages 分支。

然后在 Settings → Pages 中选择 gh-pages 分支即可。

📌 总结：如何选择？
方案   难度   推荐度   适用场景
gh-pages 分支 + npm run deploy   ⭐⭐   ⭐⭐⭐⭐⭐   所有 Docusaurus 用户首选

输出到 /docs   ⭐   ⭐⭐   快速测试、不想新建分支

GitHub Actions   ⭐⭐⭐   ⭐⭐⭐⭐   团队协作、自动化需求

🔥 强烈建议你用 方案 1（gh-pages 分支）

只需 3 条命令：
npm install --save-dev gh-pages
确保 docusaurus.config.js 配置了 url 和 baseUrl
npm run deploy

然后去 GitHub Pages 设置里选 gh-pages 分支，搞定！

如果你需要，我可以提供完整的 docusaurus.config.js 示例。