```bash
scoop install git hugo-extended go
git config --global http.proxy "http://127.0.0.1:7890"
git config --global --unset http.proxy
git config --global user.email "mial"
git config --global user.name "Vincent Zhong"
git submodule update --remote --merge
git submodule update --init
git submodule update --remote --merge --depth 1 themes/blowfish
```
git submodule update --init --depth 1 themes/blowfish

```powershell
git submodule deinit -f themes/blowfish
Remove-Item -Recurse -Force .git/modules/themes/blowfish
Remove-Item -Recurse -Force themes/blowfish
git rm --cached themes/blowfish
git submodule add --depth=1 https://github.com/nunocoracao/blowfish.git themes/blowfish
git submodule update --init --recursive
git submodule status
```
还有看看git子模块能不能拉取深度为1。

终端中发生的情况解释
您运行了 git submodule init 和 git submodule update，这些命令用于初始化和更新 Git 子模块。错误 "fatal: Unable to find current revision in submodule path 'themes/blowfish'" 表示 Git 无法在子模块 'themes/blowfish' 中找到指定的提交版本。这通常是因为：

子模块的远程仓库中缺少该提交（可能已被删除或仓库已更改）。
子模块配置（在 .gitmodules 文件中）指向的提交哈希无效。
本地子模块目录的状态不一致。
修复建议
检查 .gitmodules 文件：查看项目根目录的 .gitmodules 文件，确认 'themes/blowfish' 的 URL 和路径是否正确。
更新子模块引用：尝试运行 git submodule sync 来同步子模块的 URL，然后重新运行 git submodule update --init --recursive。
手动克隆子模块：如果上述失败，删除 blowfish 目录，然后运行 git submodule update --init --recursive 来重新克隆。
检查远程仓库：确认子模块的远程仓库（如 GitHub）中存在指定的提交。如果仓库已更改，更新 .gitmodules 中的 URL 或提交哈希。
强制更新：如果需要，可以使用 git submodule update --init --force 来强制更新，但这可能覆盖本地更改。
如果问题持续，请提供更多上下文，如 .gitmodules 文件的内容或仓库 URL