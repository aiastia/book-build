# book-build

墨语（book）系统的 Docker 镜像构建仓库。**源码不在本仓库**，在私有仓库 `aiastia/book`，
本仓库只放构建 workflow（公开仓库标准 runner 免费不限分钟）。

## 工作方式

1. 两种触发：
   - **自动轮询**（北京时间 6-24 点每小时一次）：读私有仓 `dev` 分支 HEAD，
     与 `build-state` 分支记的已构建 SHA 比对，有新 commit 才构建，成功后
     记账。push 私有仓 dev 后什么都不用做，最长等 ~1 小时自动建。
   - **手动 dispatch**：私有仓 `scripts/trigger-build.sh`（立即构建、插队用）；
     main 发版不走轮询，也走这条（`source_branch=main`）。
2. 构建时用只读 deploy key 拉取私有源码仓，构建多架构镜像（amd64 + arm64 原生编译后合并）推送到 Docker Hub `aiastia/book`。
3. 不使用 GHA 构建缓存（含源码的层会泄入公开仓缓存）。
4. upstream 仓库（aiastia-dockerhub/book）另有一个 relay workflow，push 到
   upstream 时即时 dispatch 本仓构建（秒级延迟），轮询作其兜底。

## 安全说明

- 源码始终留在私有仓库，本仓库与构建日志不包含源码内容。
- 镜像仓库 `aiastia/book` 为私有，请勿改回公开（镜像内含编译后源码）。
