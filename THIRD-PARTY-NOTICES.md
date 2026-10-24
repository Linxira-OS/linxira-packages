# Third-Party Notices（仓库级）

本仓库只是二进制分发点，**逐镜像的完整声明**在镜像内：

- `/usr/share/doc/linxira/THIRD-PARTY-NOTICES.md`
- `/usr/share/doc/linxira/SOURCE-OFFER`
- `/usr/share/doc/linxira/license-manifest.json`（机器可读，含每个包的名/版本/许可/许可目录）

上述文件由 `Linxira-OS/packages/scripts/generate-notices.py` 在构建时从 pacman
数据库生成；`linxira-os/releases/manifest.schema.json` 的 `notices` / `sourceOffer` /
`licenseManifest` 字段绑定其 SHA-256。

仓库级源码提供见同目录 `SOURCE-OFFER`。
