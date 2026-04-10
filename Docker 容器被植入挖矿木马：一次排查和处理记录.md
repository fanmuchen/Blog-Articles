---
profileName: fanmuchen
postId: "444"
postType: post
categories:
  - 3
---
### 发现问题

某天 SSH 到我用作服务器的Mac mini偶然跑了一下 `top`，发现 CPU 占用异常高。Mac mini 本身很安静，即使高负载运行也几乎没有噪音，靠物理感知很难发现问题。如果是云服务器就更不用说，没有任何外在表现，不主动看监控或登录检查根本不会察觉。SSH 登录后用 `docker stats` 看了一下，发现一个容器占了 413% CPU，基本吃满 4 个核心。再看这个容器 15 天的累计数据：磁盘读 359GB，写 347GB；但网络流量很小，入站 101MB，出站 114MB。网络流量小但 CPU 和磁盘占用极高，这是典型的挖矿特征。挖矿程序只需要很少的网络通信（拿任务、交结果），但会把本地算力全部吃掉。

### 排查过程

#### 确认入侵时间

用 `docker inspect` 查到容器启动时间是 2025-11-27，正常跑了大约 11 天。进容器查看文件时间戳，发现 `/usr/src/app/xmrig-6.24.0/xmrig` 这个文件的创建时间（Birth Time）是 2025-12-08 15:18:12。同一时间还有一个 `kal.tar.gz`，是攻击者下载的压缩包。之后攻击者还有后续动作：`/tmp` 下出现了 `runnv.tar.gz`（12月7日）、`4311`（12月11日），还尝试放了一个 `nezha-agent` 监控探针。

#### 找到攻击入口

跑 `docker logs` 看应用日志，发现大量这样的报错：

```
[Error: Command failed: test -f .env && echo EXISTS | base64 -w 0]
[Error: Command failed: test -f /var/www/.env && echo EXISTS | base64 -w 0]
```

这说明攻击者在通过应用的某个接口批量执行 shell 命令，试图找 `.env` 文件偷密钥。这是标准的命令注入（Command Injection）攻击。应用代码里大概率有某个地方把用户输入直接拼进了 `child_process.exec()` 或 `spawn()` 里，没做任何过滤。大部分探测命令虽然报错了，但至少有一条下载命令执行成功，把挖矿程序下到了 `/usr/src/app/` 目录。

#### 恶意文件清单

- `/usr/src/app/xmrig-6.24.0/xmrig` — XMRig 挖矿程序主体
- `/usr/src/app/kal.tar.gz` — 攻击者下载的压缩包
- `/tmp/alive.service`、`/tmp/lived.service` — 试图通过 systemd 做持久化（容器里一般不生效）
- 矿池地址：`auto.c3pool.org:80`（从进程参数里拿到的）

### 处理方法

停掉并删除容器：

```bash
docker stop fmc-tools-app-1 && docker rm fmc-tools-app-1
```

删掉旧镜像，重新构建。不要只删容器不删镜像，恶意文件可能已经写进了容器层。

### 后续要做的事

1. 审计代码。全局搜 `exec`、`spawn`、`eval` 这几个关键词，检查是否有地方直接执行了用户输入。这是这次被打的根本原因。

2. 不要用 root 跑应用。在 Dockerfile 里加 `USER node`，让 Node.js 进程以普通用户运行。攻击者即使拿到了命令执行权限，能做的事也会少很多。

3. 文件系统尽量只读。如果应用不需要写磁盘，用 `--read-only` 启动容器，或者在 docker-compose 里设 `read_only: true`。这样攻击者没法往磁盘写文件，挖矿程序根本落不了盘。

4. 对外暴露的服务要做好防护。这台是内网机器，但只要有端口映射到公网，就跟公网服务器没区别。该做的输入校验、权限控制一样都不能少。