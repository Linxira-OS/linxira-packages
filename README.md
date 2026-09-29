# Linxira Packages

Signed `[linxira]` package repository for Linxira OS, served by GitHub Pages.
It hosts the first-party packages (Shelly, Calamares, the software catalog,
the transaction backend, and the desktop tools) together with detached
signatures, a signed pacman database, and a `SHA256SUMS` manifest, so Linxira
OS installations and ISO builds consume released artifacts reproducibly.

- Repository URL: `https://linxira-os.github.io/linxira-packages/x86_64`
- Signing key fingerprint: `E1A4155F457D1481CA85EE6BF9D157739534BC29`
  (signing subkey `D477286C296F1E1C35735E52B22DF3E636274191`)

## Using the repository

Install `linxira-keyring` first (it ships the signing key), then enable the
repository in `/etc/pacman.conf`:

```
[linxira]
Server = https://linxira-os.github.io/linxira-packages/$arch
SigLevel = Required DatabaseOptional
```

## Repository layout

- `x86_64/*.pkg.tar.zst` and `x86_64/*.pkg.tar.zst.sig`: every package with its
  detached signature.
- `x86_64/linxira.db` / `x86_64/linxira.files`: pacman database and file list,
  published as `.tar.zst` and signed (`.sig`).
- `x86_64/linxira.gpg`: repository signing key.
- `x86_64/SHA256SUMS`: SHA-256 manifest over the packages.

## Publishing

`.github/workflows/pages.yml` deploys the repository to GitHub Pages on every
push to `main`. Release automation commits built packages and signatures to
this repository (`publish(sync)` commits) and the workflow publishes the tree
as-is; there is no separate build step on the Pages side.

## Fetching packages for ISO builds

`fetch-linxira-packages.sh` lives in
[`linxira-iso-direct`](https://github.com/Linxira-OS/linxira-iso-direct)
(`scripts/fetch-linxira-packages.sh`) and pulls the self-built package closure
from this repository, so `build-direct-iso.sh` runs reproducibly from released
artifacts instead of locally built `.pkg.tar.zst` files:

```bash
./scripts/fetch-linxira-packages.sh --output ./.linxira-packages
./build-direct-iso.sh \
  --shelly-package ./.linxira-packages/shelly-*.pkg.tar.zst \
  --calamares-package ./.linxira-packages/calamares-*.pkg.tar.zst
```

Usage: `fetch-linxira-packages.sh [--repo URL] [--arch x86_64] [--output DIR] [PKG...]`

- Default repository is this Pages URL (override with `--repo` or
  `LINXIRA_REPO_URL`; architecture via `--arch` or `LINXIRA_ARCH`).
- With no package arguments it fetches the full `build-direct-iso.sh` artifact
  set; named arguments fetch a subset.
- Each package is downloaded to `<name>.part` and verified against the
  repository database `%CSIZE%` and `%SHA256SUM%` before being moved into
  place, with retries, so a silently truncated download never pollutes a build.

---

## 简体中文

面向 Linxira OS 的签名 `[linxira]` 软件包仓库，由 GitHub Pages 托管。仓库保存
自研软件包（Shelly、Calamares、软件目录、事务后端与桌面工具），附带分离签名、
签名的 pacman 数据库与 `SHA256SUMS` 清单，使 Linxira OS 安装与 ISO 构建可以从
已发布产物可复现地获取。

- 仓库地址：`https://linxira-os.github.io/linxira-packages/x86_64`
- 签名密钥指纹：`E1A4155F457D1481CA85EE6BF9D157739534BC29`
  （签名子钥 `D477286C296F1E1C35735E52B22DF3E636274191`）

## 使用本仓库

先安装 `linxira-keyring`（随包分发签名密钥），然后在 `/etc/pacman.conf` 中启用：

```
[linxira]
Server = https://linxira-os.github.io/linxira-packages/$arch
SigLevel = Required DatabaseOptional
```

## 仓库结构

- `x86_64/*.pkg.tar.zst` 与 `x86_64/*.pkg.tar.zst.sig`：每个软件包及其分离签名。
- `x86_64/linxira.db` / `x86_64/linxira.files`：pacman 数据库与文件列表，以
  `.tar.zst` 形式发布并带签名（`.sig`）。
- `x86_64/linxira.gpg`：仓库签名密钥。
- `x86_64/SHA256SUMS`：覆盖全部软件包的 SHA-256 清单。

## 发布方式

`.github/workflows/pages.yml` 在每次推送到 `main` 时把仓库部署到 GitHub Pages。
发布自动化会把构建好的软件包与签名提交进本仓库（`publish(sync)` 提交），工作流
按原样发布整个目录树；Pages 侧没有独立构建步骤。

## 为 ISO 构建获取软件包

`fetch-linxira-packages.sh` 位于
[`linxira-iso-direct`](https://github.com/Linxira-OS/linxira-iso-direct)
（`scripts/fetch-linxira-packages.sh`），从本仓库拉取自研包闭包，使
`build-direct-iso.sh` 可以从已发布产物可复现地构建，而不依赖本地构建的
`.pkg.tar.zst`：

```bash
./scripts/fetch-linxira-packages.sh --output ./.linxira-packages
./build-direct-iso.sh \
  --shelly-package ./.linxira-packages/shelly-*.pkg.tar.zst \
  --calamares-package ./.linxira-packages/calamares-*.pkg.tar.zst
```

用法：`fetch-linxira-packages.sh [--repo URL] [--arch x86_64] [--output DIR] [PKG...]`

- 默认仓库即本 Pages 地址（可用 `--repo` 或 `LINXIRA_REPO_URL` 覆盖；架构用
  `--arch` 或 `LINXIRA_ARCH`）。
- 不带包名参数时拉取完整的 `build-direct-iso.sh` 产物集合；给定包名则只拉取子集。
- 每个包先下载为 `<name>.part`，对照仓库数据库中的 `%CSIZE%` 与 `%SHA256SUM%`
  校验通过后才移动到位，并带重试，因此静默截断的下载不会污染构建。
