# 安装完整优化版 / Install the integrated fork

默认 `master` 包含在线唤醒按钮、命令完成修复、回归测试和 Mac 指南。代码已就绪，Mac 配置不会自动完成。

Default `master` includes the manual-wake UI, command-completion fix, regression tests and Mac guide. The Mac still needs your configuration.

## 构建 / Build

在支持 Docker 的构建电脑执行。路由器资源有限时，在另一台电脑构建，再导入镜像。不要用默认 `Dockerfile`：它下载上游发布版。

Build on a Docker-capable computer. For a small router, build elsewhere and import the image. Use `Dockerfile.dev`; the default `Dockerfile` downloads an upstream release.

```sh
git clone --branch master https://github.com/nanomassai/UpSnap.git
cd UpSnap
# Match your Docker host architecture. ARM64 routers use linux/arm64.
docker buildx build --platform linux/arm64 --load -f Dockerfile.dev -t local/upsnap:mac-wake .
docker save -o upsnap-mac-wake-arm64.tar local/upsnap:mac-wake
```

`linux/arm64` 要替换为部署主机架构（如 `linux/amd64`）；文件名也随之修改。构建使用仓库锁定的前端依赖。尚未发布此 fork 的预构建镜像；不要把上游镜像视为本版。

Replace `linux/arm64` with the deployment architecture (for example `linux/amd64`) and adjust the archive name. No prebuilt fork image is published by this change; an upstream image is not this build.

## 新安装 / New installation

下面适用于 Linux Docker 主机。镜像导入后运行；路径和监听地址必须替换成你自己的值。

On a Linux Docker host, import and run the image. Replace both example values below.

```sh
docker load -i upsnap-mac-wake-arm64.tar
DATA_DIR='/REPLACE/WITH/PERSISTENT/UPSNAP/DATA'
LISTEN_IP='REPLACE_WITH_HOST_LAN_IP'
docker run -d --name upsnap --restart unless-stopped --network host \
  -e UPSNAP_HTTP_LISTEN="$LISTEN_IP:8090" \
  -v "$DATA_DIR:/app/pb_data" local/upsnap:mac-wake
```

路由器管理界面：选择导入镜像、host 网络、原有数据卷映射到 `/app/pb_data`，设置重启策略。不要为了部署关闭主机授权插件。

Router UI: import the image, select host networking, map the existing data mount to `/app/pb_data`, and set a restart policy. Do not disable host authorization plugins.

## 升级现有安装 / Upgrade an existing installation

1. 记录旧镜像、网络、环境变量和挂载，停止旧容器后备份数据。 / Record the old image and configuration; stop the old container before backing up its data.
2. 新容器复用原数据卷及设置，只更换镜像。两个实例不能同时写同一数据目录。 / Reuse the existing data mount and settings. Never run both instances against one data directory.
3. 按 [Mac 指南](macos-full-wake.md) 配置目标、主机密钥与受限授权。 / Configure the target and restricted key using the guide.
4. 验证在线“发送唤醒”、权限、确认弹窗，以及睡眠后的真实远控；再检查容器重启。 / Verify the manual action, permissions, confirmation, real remote access after sleep, and container restart.
5. 回退时停止新容器，按需恢复备份，再启动旧版；保管好备份中的密钥。 / To roll back, stop the new container, restore the backup if needed, then start the old image. Treat key-containing backups as credentials.

## 访问 / Access

在家直接访问内网 UpSnap；在外先连接 Tailscale，再访问同一私有服务（须事先配置相应路由或私有转发）。第三方远控工具有自己的连接链路，不必依赖 Tailscale。

At home, use the private UpSnap URL. Away, connect Tailscale first and use the configured private route or forward. Your remote-control app has its own connectivity requirements.

关机开机属于独立方案：Wi-Fi 智能插座需要匹配并验证 Mac 来电启动配置。不要给正在运行或睡眠的 Mac 直接断电；本指南的 SSH 方案用于睡眠状态。

Power-on from shutdown is separate: a Wi-Fi smart plug requires a compatible, verified power-restoration setup. Do not cut power to a running or sleeping Mac. The SSH recipe handles sleep.
