# NapCat Docker (arm64) — 离线镜像归档

上游 `NapNeko/NapCatQQ` 于 2026-11-12 停止活动，Docker Hub 上的官方镜像 `mlikiowa/napcat-docker` 已被删除（404）。本仓库保存一份**此前导出的官方 arm64 构建**，供无法拉取上游镜像的环境使用。

## 这是什么

| 项 | 值 |
|---|---|
| 镜像 | `mlikiowa/napcat-docker:latest` |
| 镜像 ID | `sha256:b101b8968cc31ae428ec0f8e117386e4ced7773b2d8226ed9f7ddfcec55f1b7e` |
| 架构 | `linux/arm64` |
| 构建时间 | 2026-10-21T08:44:56Z |
| 上游 revision | `60dc2d72126890a0ae88a36f64000c08b7b8bf7a`（`NapNeko/NapCat-Docker`） |
| 归档大小 | 566 MiB（593,788,584 字节） |
| SHA256 | `b3d49bef271e90973693e4eb4791bb25ebda0108a6e4df92a4ddd8d4f3743879` |

镜像本体在 **Releases** 里，不在 git 里（566MB 超过 GitHub 单文件 100MB 限制）。

## 下载

```bash
curl -L -o napcat-docker-latest.tar.gz \
  https://github.com/SevenvxCN/napcat-docker-arm64/releases/latest/download/napcat-docker-latest.tar.gz

shasum -a 256 napcat-docker-latest.tar.gz
# b3d49bef271e90973693e4eb4791bb25ebda0108a6e4df92a4ddd8d4f3743879
```

SHA256 对不上就别导，包在传输里坏了或被换过。

## 导入与启动

Docker 25+（归档是 OCI layout）。

```bash
docker load -i napcat-docker-latest.tar.gz
docker image inspect mlikiowa/napcat-docker:latest --format '{{.Id}} {{.Architecture}}'
# sha256:b101b8968cc3... arm64

mkdir -p qq config
docker run -d --name napcat --restart unless-stopped \
  -p 127.0.0.1:3000:3000 -p 127.0.0.1:3001:3001 -p 127.0.0.1:6099:6099 \
  -e ACCOUNT=10001 -e NAPCAT_UID=501 -e NAPCAT_GID=20 -e TZ=Asia/Shanghai \
  -v "$PWD/qq:/app/.config/QQ" -v "$PWD/config:/app/napcat/config" \
  mlikiowa/napcat-docker:latest

docker logs -f napcat
```

有 compose 插件的用仓库里的 `docker-compose.yml`，`ACCOUNT` 用对方自己的 QQ 号。

## 架构限制

**只适用 arm64**：Apple Silicon Mac、arm64 Linux。

Windows x64 / Intel Mac / x86 服务器跑不了，`docker run` 会 `exec format error`。x86 需要 amd64 构建，本仓库不含。

## 不含登录态

归档里没有 `qq/`（QQ 会话）和 `config/`（OneBot token、WebUI 凭据）。导入后是干净环境，首次启动需扫码登录。

## 来源与免责

镜像由上游项目构建，本仓库只做归档分发，未做任何修改。校验值即上表，与 `manifest.json` 一致。

NapCat 与腾讯无隶属关系；使用请遵守 QQ 用户协议与当地法律。
