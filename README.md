# Miyabi for LazyCat

将 [Miyabi](https://github.com/ppxb/miyabi) 打包为 LazyCat LPK v2。Miyabi 是配合 115 网盘的一站式影片管理 & Emby 管理平台：支持直接对接 115 进行刮削、订阅，也可作为底层服务，为 Emby 生成 STRM 直链与元数据。

> 上游明确标注 NSFW：应用可能包含裸露、暴力等不适宜公众场合的内容，请勿在公共场合或面向未成年人使用，并自行确认所在地区的法律法规。

## 使用方式

1. 安装时设置「访问密码」（默认随机生成，建议保留门禁）。
2. 打开应用，输入访问密码进入。
3. 在「设置」中配置 115 网盘、Jd 刮削源以及 Emby（服务器地址、API Key、媒体库路径）。

## 数据与运行方式

- 上游镜像：`ghcr.io/ppxb/miyabi:v0.2.1`
- 运行镜像：`ghcr.1ms.run/ppxb/miyabi:v0.2.1`
- 容器数据目录：`/app/data`（SQLite、图片缓存、`emby/` 下的 STRM 与元数据）
- LazyCat 持久化目录：`/lzcapp/var/miyabi`
- 服务名：`miyabi`
- 主要环境变量：

| 变量 | 说明 |
| --- | --- |
| `MIYABI_ACCESS_PASSWORD` | 部署参数 `access_password` 注入的访问密码 |
| `MIYABI_JWT_SECRET` | `stable_secret` 生成的会话签名密钥 |
| `MIYABI_PUBLIC_URL` | `https://${LAZYCAT_APP_DOMAIN}`，用于生成 STRM 播放直链 |

未开启健康检查：镜像内没有 curl/wget，上游 Compose 同样未定义健康检查。

## 本地构建

```bash
lzc-cli project build -o dist/application.lpk
lzc-cli lpk info dist/application.lpk
```

## 自动发布

`.github/workflows/lazycat.yml` 使用 `ca-x/lazycat-github-action` 检查新的上游稳定 SemVer 镜像标签，创建版本化 GitHub Release Asset，并只发布到喵喵私有商店。需要为仓库配置：

- `APPSTORE_URL`
- `APPSTORE_TOKEN`
- `APP_ID`，可选
- `PRIVATE_STORE_GROUP_CODES`，可选，用于私有分组

镜像使用 GHCR 默认镜像加速前缀，并在发布前校验 `linux/amd64` 源镜像与镜像加速地址的 digest 一致。
