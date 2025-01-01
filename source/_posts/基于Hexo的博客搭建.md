---
title: 搭建Hexo在Github Page部署博客
date: 2025-01-01 12:09:30
tags:
  - Hexo
  - 部署
  - 搭建
  - 配置
  - 博客
---

## 本地 Hexo 配置

- [Node.js](http://nodejs.org/) (Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本)
- [Git](http://git-scm.com/)

```bash
 npm install hexo-cli -g
 hexo init blog
 cd blog
 npm install
 hexo server
```

出现下面就是成功了

```shell
INFO  === Registering Hexo extensions ===
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/ . Press Ctrl+C to stop.
INFO  Good bye
```


## GitHub token配置

- 建一个仓库 `yourusername.github.io`
- 在GitHub个人设置建一个` GitHub token` 包含repo权限，添加到上面仓库`Actions secrets and variables` 中，名称为名称为`GH_TOKEN`。


## 仓库配置

- `_config.yml`
```yaml
title: 这里是TransisitorCat的博客~
subtitle: ''
description: ''
keywords: null
author: TransistorCat
language: en
timezone: ''
url: https://transistorcat.github.io
root: /
permalink: ':year/:month/:day/:title/'
permalink_defaults: null
pretty_urls:
  trailing_index: true
  trailing_html: true
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: ':lang'
skip_render: null
new_post_name: ':title.md'
default_layout: post
titlecase: false
external_link:
  enable: true
  field: site
  exclude: ''
filename_case: 0
render_drafts: false
post_asset_folder: true
relative_link: false
future: true
syntax_highlighter: highlight.js
highlight:
  line_number: true
  auto_detect: false
  tab_replace: ''
  wrap: true
  hljs: false
prismjs:
  preprocess: true
  line_number: true
  tab_replace: ''
index_generator:
  path: ''
  per_page: 10
  order_by: '-date'
default_category: uncategorized
category_map: null
tag_map: null
meta_generator: true
date_format: YYYY-MM-DD
time_format: HH:mm:ss
updated_option: mtime
per_page: 10
pagination_dir: page
include: null
exclude: null
ignore: null
theme: icarus
deploy:
  type: git
  repo: https://github.com/TransistorCat/TransistorCat.github.io.git
  branch: gh-pages

```

- `workflow/deploy.yml`
```yml
name: Deploy Hexo to GitHub Pages

on:
  push:
    branches:
      - main  # 当推送到 main 分支时触发

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          submodules: false  # 禁用子模块检查

      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '22'

      - name: Install Dependencies
        run: npm install

      - name: Install Hexo Git Deployer
        run: |
          npm install hexo-deployer-git --save
          npm install hexo-cli -g

      - name: Clean and Generate Static Files
        run: |
          hexo clean
          hexo generate

      - name: Configure Git
        run: |
          git config --global user.name 'github-actions[bot]'
          git config --global user.email 'github-actions[bot]@users.noreply.github.com'

      - name: Deploy to GitHub Pages
        env:
          GH_TOKEN: ${{ secrets.GH_TOKEN }}
        run: |
          cd public/
          git init
          git add -A
          git commit -m "Create by workflows"
          git remote add origin https://${{ secrets.GH_TOKEN }}@github.com/TransistorCat/TransistorCat.github.io.git
          git push origin HEAD:gh-pages -f
```
推送上传，在GitHub查看工作流是否正确。

## 配置Github Pages

在仓库settings中配置page来源为gh-pages分支即可。等待网站部署完毕，就可以看了。网站链接可以在settings的GitHub Pages看到，也可以去action里看到。

## 遇到问题
- `_congifg.yml` 配置问题，像参考文章中的配不成功
```yaml
url: https://transistorcat.github.io
root: /
```

## 参考文章
[如何优雅的使用Github Action服务来将Hexo部署到Github Pages](https://xiamu-ssr.github.io/Hexo/2024/06/19/2024-H1/2024-06-19-12-31-52/?t=1735694460620#10-%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0)
[9分钟零成本搭建自动化部署个人博客(Hexo + Github Action + Page)](https://www.bilibili.com/video/BV1xTgTemEDU/?spm_id_from=333.337.search-card.all.click&vd_source=b24628656ffe38823dbdc1fab44aa361)