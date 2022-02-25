---
title: biliroaming-go-server 搭建教程
date: 2022-02-17 13:48:31
tags:
---

# 前言

[biliroaming-go-server](https://github.com/JasonKhew96/biliroaming-go-server)是个用 go 语言写的哔哩哔哩解析服务器的后端。为了减少 Nginx 直接反代的压力（比如请求过多被限制 `-412`），这后端会处理用户发过来的参数，根据地区分配代理（可以利用 clash 的 `load-balance` 做负载），然后把返回内容缓存起来，下一位用户请求同一个内容，会直接返回已缓存的内容。

目前[哔哩漫游 Xposed 模块](https://github.com/yujincheng08/BiliRoaming)和[解除B站区域限制](https://greasyfork.org/zh-CN/scripts/25718)支持这个后端

# 需求

- 脑子
- PostgreSQL
- Go (可选)
- Nginx (可选)
- Docker (推荐)

# 搭建方式

## Docker (推荐)

先下载 [biliroaming-go-server-docker](https://github.com/JasonKhew96/biliroaming-go-server-docker/archive/refs/heads/master.zip) 并解压

{% asset_img "config.png" "设置文件夹" %}

复制 `config/config.example.yaml` 到 `config/config.yml`，然后修改配置 `config/config.yml`

{% asset_img "config_port.png" "设置端口" %}

{% asset_img "config_docker_port.png" "docker 设置端口" %}

这里的 `端口` 必须与 `docker-compose-http.yml` 的 `映射端口` 一致

{% asset_img "config_docker_external.png" "docker 设置域名" %}

{% asset_img "config_proxy_external.png" "docker 设置域名" %}

如果代理运行在容器外面，可以在 `docker-compose-http.yml` 里使用 `extra_hosts` 的规则，然后 `config/config.yml` 里的代理设置为 `socks5://host.docker.internal:端口`

修改数据库密码 `db/password.txt` （不推荐使用默认密码）

当前文件夹里执行 `docker-compose -f docker-compose-http.yml up -d --build` (更新也一样使用这个指令)

设置好 Nginx 对着映射端口就完成了

## 直接运行

先下载 [biliroaming-go-server](https://github.com/JasonKhew96/biliroaming-go-server)

复制 `config.example.yml` 到 `config.yml`，然后编辑配置

启动 PostgreSQL 并导入 `sql/initdb.sql`

配置好 Nginx 反代到刚设置的端口

自己编译，当前文件夹运行 `go build`

启动刚编译好的 `./biliroaming-go-server`

可以用 `screen` 或 `systemd` 之类的，在后台执行（我就不详细说了，有很多种方法）

# 写在后面

另外，可以在 `html` 文件夹里放自己的网页文件，已提供用户一个捐赠的方式，贡献服务器费用

遇到 bug 可以到 [biliroaming-go-server](https://github.com/biliroaming-go-server/issues) 开一个 issue

