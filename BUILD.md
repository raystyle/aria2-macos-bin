# aria2 macOS 预编译发布（GitHub Actions）

本目录是 **独立小仓库模板**：在 GitHub 上用 Actions 编出 `aria2c`，发布到 Releases；  
dotfiles 的 `aria2/install.sh` 只从该仓库 **下载二进制**（不 brew、不在本机编）。

## 为什么自己维护

- 上游 `aria2/aria2` 近年 release **几乎不带** macOS 包  
- Intel Monterey 上 brew **常无 bottle**，会拉源码编很久  
- 在 **GitHub-hosted macOS runner** 上有 bottle / 依赖，适合 CI 出包

## 一、建仓库并推送工作流

```bash
# 建议私有或公开均可
gh repo create raystyle/aria2-macos-bin --private --clone
cd aria2-macos-bin

# 从 dotfiles 拷模板
cp -R ~/dotfiles/aria2/build/.github .
cp ~/dotfiles/aria2/build/README.md .

git add -A
git commit -m "ci: build aria2c for darwin x86_64 + arm64"
git push -u origin main
```

## 二、触发构建与发布

```bash
# 手动跑（推荐先试）
gh workflow run build-release.yml -R raystyle/aria2-macos-bin

# 或打 tag 自动发 release
git tag v1.37.0
git push origin v1.37.0
```

产物命名（install.sh 按此匹配）：

| 文件 | 架构 |
|------|------|
| `aria2c-darwin-x86_64.tar.gz` | Intel |
| `aria2c-darwin-arm64.tar.gz` | Apple Silicon |

包内为单文件 `aria2c`（可执行）。

## 三、本机安装（dotfiles）

```bash
# 默认仓库: raystyle/aria2-macos-bin（可用环境变量覆盖）
export ARIA2_GITHUB_REPO=raystyle/aria2-macos-bin
~/dotfiles/aria2/install.sh
# 或
~/dotfiles/deploy.sh --only aria2
```

## 四、与下载链的关系

`lib/download.sh`：① `gh` → ② `aria2c` → ③ `curl`  
`cli/install.sh` 开头会 `ensure_download_toolchain`，无 aria2c 时调本 install。

## 维护

- 上游版本升了：改 workflow 里 `ARIA2_VERSION` 或 `brew upgrade` 后再 `workflow_dispatch`  
- 验证：`./aria2c -v` · `otool -L aria2c`（动态库是否过新）  
- 若 Monterey 跑不起来：在 workflow 里把 `MACOSX_DEPLOYMENT_TARGET` 调到 `12.0` 并从源码编（见 workflow 注释）
