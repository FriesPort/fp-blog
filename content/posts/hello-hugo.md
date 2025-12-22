---
title: "Hello, Hugo!"
date: 2025-12-22T02:45:19+08:00
draft: false
description: ""
showHero: false
---

启用一个hugo站点所花费的时间比我想象的要久，比wordpress麻烦多了。

但是我还是选择了hugo：
1. 希望通过静态网站减少维护成本，不用天天担心wordpress挂掉了打不开。
2. 能更轻松的开始记录，直接用obsidian写了移到hugo就行，而不是在wordpress打开古腾堡编辑器开始纠结排版。
3. 准备将go的技能树点亮，用Hugo的同时顺手学一学go。

## 准备工作

想要运行一个hugo站点，需要下载2-3个程序，分别是hugo、git，如果想作为go模块安装，则还需要安装go语言。因为目前我笔记本用Ubuntu，宿舍台式机用Windows 11，公司的电脑用MacOS，所以我3个系统的方法都会写。默认用bash语言，zsh和powershell重大调整会单独写出来。三个系统的默认包管理器分别是：scoop、brew、apt。

### 包管理器

Windows安装[scoop](https://scoop.sh)

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

MacOS安装[brew](https://brew.sh/zh-cn/)，运行下面的命令或者[下载pkg安装器](https://github.com/Homebrew/brew/releases/latest)。

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```


### git

安装git，可以用安装包或者直接命令行

```powershell
scoop install git
```

```zsh
brew install git
```

提交代码前要设置一些配置。默认主分支名等等。

```bash
git config --global user.email "teacup418@fp.ac.cn"
git config --global user.name "Vincent Zhong"
```

### proxy

设置代理为本地的clash，我的混合端口为`7890`，根据实际情况修改。

```bash
git config --global http.proxy "socks5://127.0.0.1:7890"
```

取消设置代理

```bash
git config --global --unset http.proxy
```

## 安装hugo

### 命令行安装hugo-entened

没有特殊理由就直接安装hugo扩展版。

```powershell
scoop install hugo-extended
```

MacOS的hugo已经是扩展版了，直接安装hugo就行。

```zsh
brew install hugo
```

<!-- ```bash
sudo apt install hugo
``` -->



### 安装主题

经过多轮评估，最后精挑细选了一个名叫blowfish的主题，比较符合我的审美，同时文章也配有侧边章节导航栏，可以快速跳转。有其他的例如paper等主题就是因为目录在文字头尾，看一半时不好跳转，所以没有被采用。

blowfish支持两种安装方法，因为不太熟悉go语言，所以暂时先添加为git子模块，后面熟悉了再换。在项目根目录允许下面的命令添加子模块。

```
git submodule add -b main https://github.com/nunocoracao/blowfish.git themes/blowfish
```

每次在新电脑拉取仓库后要额外在`/theme/blowfish`目录中单独拉取模块。

```bash
git submodule update --init --recursive
```

### 修改配置

将主题下的配置复制到根目录修改

```bash
cp -r /theme/blowfish/config/_default/ /config/_default/
```

然后就是漫长的修改过程了。看[blowfish的文档](https://blowfish.page/zh-cn/docs/configuration/)去，不单独写了。

## 部署 

github pages

### 绑定域名
绑定域名`blog.fp.ac.cn`到GitHub pages，由流水线直接构建、部署，我只需要负责写文章、推送提交就行了。

TODO: 讲解怎么绑定域名。

### action

在官方的工作流上进行修改，添加git拉取子模块的步骤。

```yaml
name: Build and deploy
on:
  push:
    branches:
      - main
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: false
defaults:
  run:
    shell: bash
jobs:
  build:
    runs-on: ubuntu-latest
    env:
      DART_SASS_VERSION: 1.97.1
      GO_VERSION: 1.25.5
      HUGO_VERSION: 0.153.1
      NODE_VERSION: 24.12.0
      TZ: Europe/Oslo
    steps:
      - name: Checkout
        uses: actions/checkout@v5
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Initialize submodules
        run: |
          git submodule update --init --recursive
      - name: Setup Go
        uses: actions/setup-go@v5
        with:
          go-version: ${{ env.GO_VERSION }}
          cache: false
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Create directory for user-specific executable files
        run: |
          mkdir -p "${HOME}/.local"
      - name: Install Dart Sass
        run: |
          curl -sLJO "https://github.com/sass/dart-sass/releases/download/${DART_SASS_VERSION}/dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
          tar -C "${HOME}/.local" -xf "dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
          rm "dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
          echo "${HOME}/.local/dart-sass" >> "${GITHUB_PATH}"
      - name: Install Hugo
        run: |
          curl -sLJO "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
          mkdir "${HOME}/.local/hugo"
          tar -C "${HOME}/.local/hugo" -xf "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
          rm "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
          echo "${HOME}/.local/hugo" >> "${GITHUB_PATH}"
      - name: Verify installations
        run: |
          echo "Dart Sass: $(sass --version)"
          echo "Go: $(go version)"
          echo "Hugo: $(hugo version)"
          echo "Node.js: $(node --version)"
      - name: Install Node.js dependencies
        run: |
          [[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true
      - name: Configure Git
        run: |
          git config core.quotepath false
      - name: Cache restore
        id: cache-restore
        uses: actions/cache/restore@v4
        with:
          path: ${{ runner.temp }}/hugo_cache
          key: hugo-${{ github.run_id }}
          restore-keys:
            hugo-
      - name: Build the site
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/" \
            --cacheDir "${{ runner.temp }}/hugo_cache"
      - name: Cache save
        id: cache-save
        uses: actions/cache/save@v4
        with:
          path: ${{ runner.temp }}/hugo_cache
          key: ${{ steps.cache-restore.outputs.cache-primary-key }}
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

去部署的地址看看吧。