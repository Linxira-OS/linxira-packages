# linxira-packages · Agent 开发规范

> **档位:S(治理与发布核心)**
> 本仓职责:托管签名 `[linxira]` pacman 包仓库(镜像站点),由 GitHub Pages 提供。
> 通用条款一律见工作区总纲 `f:\Linxira-OS\AGENTS.md` 与发布规范 `linxira-os/docs/RELEASE_STANDARD.md`。
> 冲突时以本文件为准。

## 职责与边界
- 归属:`owns: [package-repository-hosting, repository-metadata]`,lifecycle `active`。
- **本仓是发布产物落点,不是构建仓**:包由 `packages` 仓 CI 构建 + 签名后,以 `publish(sync)` 提交推入本仓;
  Pages 侧无独立构建步骤,按原样发布目录树。
- URL:`https://linxira-os.github.io/linxira-packages/x86_64`。
- 签名密钥指纹:主钥 `E1A4155F457D1481CA85EE6BF9D157739534BC29`,签名子钥 `D477286C296F1E1C35735E52B22DF3E636274191`,
  UID `Linxira OS Release Signing <release@linxira.org>`。

## 目录布局
- `x86_64/*.pkg.tar.zst` + `*.pkg.tar.zst.sig` —— 每个包及其分离签名。
- `x86_64/linxira.db`、`linxira.db.tar.zst`(+`.sig`)—— pacman 数据库。
- `x86_64/linxira.files`、`linxira.files.tar.zst`(+`.sig`)—— 文件列表。
- `x86_64/linxira.gpg` —— 仓库签名公钥。
- `x86_64/SHA256SUMS` —— 覆盖全部包的 SHA-256 清单。
- `.github/workflows/pages.yml` —— 部署到 GitHub Pages(触发分支 `main`)。

## 构建与校验
```bash
# 本仓只读校验:核对清单与包的 SHA-256(清单路径相对 x86_64/)
cd x86_64
sha256sum -c SHA256SUMS
```
- 消费方获取:`linxira-iso-direct/scripts/fetch-linxira-packages.sh` 从本仓拉取自研包闭包(默认即本 Pages 地址)。
- 若清单路径与本目录不一致,以 `README.md` 与实际 `SHA256SUMS` 为准。

## 发布与签名
- 包与 `.db` / `.files` 均须签名(`.sig`);`SHA256SUMS` 覆盖包。
- 安装方:先装 `linxira-keyring`(随包分发签名密钥),再在 `/etc/pacman.conf` 启用:
  ```
  [linxira]
  Server = https://linxira-os.github.io/linxira-packages/$arch
  SigLevel = Required DatabaseOptional
  ```
- 发布自动化提交 `publish(sync)` 后由 `pages.yml` 发布;来源见 `packages/scripts/deploy-to-linxira-packages.sh`。

## 禁区
- **不要手工修改本仓内容**(包、db、`.files`、签名、`SHA256SUMS`)—— 一律经 `packages` 仓 CI 同步(见发布规范 §2)。
- 不要在本仓执行构建/签名;不要提交来源不明的包。
- 分支 `main`。

## 关联文档
- `README.md`;工作区总纲;发布规范。
- `packages/docs/AGENT-GUIDELINES.md`、`packages/RELEASE.md`;`linxira-keys/README.md`。