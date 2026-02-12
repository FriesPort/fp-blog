# 薯条港博客

URL: <https://blog.fp.ac.cn>

## 快速重构开发环境

### Windows

安装[scoop](https://scoop.sh/)

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

安装基础软件

```powershell
scoop bucket add extras
scoop install git hugo-extended go
```

### MacOS

安装[brew](https://brew.sh/zh-cn/)，使用下面的命令，或者使用[pkg安装包](https://github.com/Homebrew/brew/releases/latest)安装。

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装基础软件

```zsh
brew install hugo go git
```

### 配置git

配置提交用户和git代理

```bash
git config --global user.email "<Email>"
git config --global user.name "Vincent Zhong"
git config --global http.proxy "http://127.0.0.1:7890"
```

取消git代理

```bash
git config --global --unset http.proxy
```

### 配置hugo和blowfish

根据[blowfish安装文档](https://blowfish.page/zh-cn/docs/installation/)，运行草稿服务器，blowfish模块会自动下载。

```bash
hugo server -D
```

更新blowfish主题。

```bash
hugo mod get -u
```
