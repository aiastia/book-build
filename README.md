# book-build

墨语（book）系统的 Docker 镜像构建仓库。**源码不在本仓库**，在私有仓库 `aiastia/book`，
本仓库只放构建 workflow（公开仓库标准 runner 免费不限分钟）。

## 工作方式

1. 只接受手动触发（`workflow_dispatch`），不接受 push / PR。
2. 构建时用只读 deploy key 拉取私有源码仓，构建多架构镜像（amd64 + arm64 原生编译后合并）推送到 Docker Hub `aiastia/book`。
3. 不使用 GHA 构建缓存（含源码的层会泄入公开仓缓存）。

## 本地触发

在私有源码仓库执行：

```bash
scripts/trigger-build.sh              # 构建当前 HEAD，前后端全量，正常推送
scripts/trigger-build.sh HEAD backend # 只构建后端
scripts/trigger-build.sh HEAD all true # dry_run：只构建不推送（验证链路）
```

## 安全说明

- 源码始终留在私有仓库，本仓库与构建日志不包含源码内容。
- 镜像仓库 `aiastia/book` 为私有，请勿改回公开（镜像内含编译后源码）。
