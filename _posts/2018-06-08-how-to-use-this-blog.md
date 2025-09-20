---
layout: post
title: 使用本博客项目的简明指南
subtitle: 从本地预览到发布上线的最少步骤
date: 2025-09-19
categories: 文档
tags: jekyll blog guide
cover: 
---

本文简要说明如何克隆、预览、写作并发布到 GitHub Pages。按步骤操作即可完成部署。

## 1. 获取代码

```bash
git clone https://github.com/se7enfive/se7enfive.github.io.git
cd se7enfive.github.io
```

## 2. 本地预览

先安装 Jekyll 与分页插件（需 Ruby 环境）。

```bash
gem install jekyll jekyll-paginate
jekyll serve
```

启动后访问: http://127.0.0.1:4000

## 3. 写一篇文章

在 `_posts/` 下新建 Markdown 文件，命名格式为 `YYYY-MM-DD-title.md`：

```markdown
---
layout: post
title: 我的第一篇文章
date: 2018-06-19
categories: 随笔
tags: jekyll blog
---

这里是正文内容。
```

## 4. 关键配置

编辑 `/_config.yml` 并确认：

- `url: https://se7enfive.github.io`
- `baseurl: ''`

如需自定义域名：新建 `CNAME` 文件并在 DNS 指向 GitHub Pages。

## 5. 发布上线（GitHub Pages）

仓库名称应为 `se7enfive.github.io`。在 GitHub 仓库：

- Settings → Pages → 选择 “Deploy from a branch”
- 分支 `master`，目录 `/`

本地推送后等待 1–2 分钟即可访问：

```
https://se7enfive.github.io
```

—— 完 ——


