# Alist LoongArch64 专用镜像
[Alist](https://alist.nn.ci/) 多网盘文件管理服务 LoongArch64 架构镜像（适配龙芯 3A5000 / 3A6000 等新世代龙芯处理器）。

需在新世界 LoongArch64 Linux 主机上安装 Docker 运行。

Alist 官方文档：[https://alist.nn.ci/zh/guide/](https://alistgo.com/zh/guide/)

## 快速上手
预构建镜像已上传至 [Docker Hub](https://hub.docker.com/r/tangxindan1234/alist-loongarch64)，直接拉取启动即可：
```bash
docker run -d \
  --name alist \
  --restart=always \
  -p 5244:5244 \
  -v alist-data:/opt/alist/data \
  tangxindan1234/alist-loongarch64
```
容器启动后，访问 `http://主机IP:5244` 进入初始化页面，设置管理员密码即可使用。

> **重要说明**：配置、网盘密钥、数据库、缓存等持久化数据统一存放在 Docker 数据卷 `alist-data`，重启/重建容器不会丢失数据。
> 如需挂载宿主机本地目录替代数据卷，将 `-v alist-data:/opt/alist/data` 修改为 `-v /宿主机路径/alist-data:/opt/alist/data`。

## 端口说明
| 端口 | 协议 | 用途 |
|------|------|------|
| 5244 | TCP  | 主 HTTP 服务（必需，网页管理、文件访问） |

## 管理员初始密码
### 方式1：查看容器日志获取随机初始密码
```bash
docker logs 容器名
```
在日志中搜索 `password` 字段，首次启动生成的随机管理员密码会打印在日志内。

### 方式2：手动重置管理员密码
```bash
docker exec -it 容器名 ./alist admin set 新密码
```

## 可选环境变量
在 `docker run` 中追加 `-e 变量名=值` 自定义运行参数：
| 环境变量 | 默认值 | 说明 |
|----------|--------|------|
| PUID     | 0      | 指定容器内运行服务的用户ID，推荐非root运行提升安全性 |
| PGID     | 0      | 指定容器内运行服务的用户组ID |
| TZ       | Asia/Shanghai | 容器时区 |

## 本地编译

克隆官方仓库，按照下面的修改说明修改 `Dockerfile` 即可

## 修改说明

- 添加新世界专有镜像源到 `/etc/docker/daemon.json`
```json
{
  "registry-mirrors": [
    "https://cr.loongnix.cn"
  ]
}
```
- 将默认的基础镜像 `alpine` 改成了 `https://lcr.loongnix.cn/` 的 `alpine` 镜像
- 将默认的 `COPY --chmod=755` 拆成了两条指令 `COPY` 和 `RUN chmod 755`
