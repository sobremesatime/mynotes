# Docker 学习笔记

## 一、核心概念

Docker 是一种软件部署技术，利用容器化技术为应用程序封装独立的运行环境。每个运行环境即为一个**容器**，承载容器运行的计算机称为**宿主机**。

容器与虚拟机的区别：

- Docker 容器：多个容器共享同一个系统内核。
- 虚拟机：每个虚拟机包含一个操作系统的完整内核。
- 优势：Docker 容器比虚拟机更轻量、占用空间更小、启动速度更快。

**镜像**（Image）是容器的模板，可类比为软件安装包。镜像类似于制作糕点的模具，可用于创建多个糕点（容器），并可分享给他人。

- 容器是基于镜像运行的应用程序实例，可类比为安装好的软件。

**Docker 仓库**（Registry）：用于存放和分享 Docker 镜像的场所。

- Docker Hub：Docker 的官方公共仓库，存储了大量用户分享的 Docker 镜像。

## 二、Docker 安装

Docker 是基于 Linux 的容器化技术。在 Windows 和 Mac 电脑上，Docker 通过虚拟化一个 Linux 子系统来运行。

Linux 系统宿主机是最佳的 Docker 实战环境。

### Linux 系统安装

1. 访问 `getdocker.com` 获取安装脚本。
2. 执行安装脚本（例如，通过 `curl -fsSL https://get.docker.com -o get-docker.sh` 下载脚本，然后执行 `sudo sh get-docker.sh`）。
3. 安装完成后，若非 `root` 用户，需在所有 `docker` 命令前添加 `sudo` 以获取管理员权限。

### Windows 系统安装

1. 启用 Windows 功能：勾选“Virtual Machine Platform”（虚拟机平台）和“适用于 Linux 的 Windows 子系统”（WSL）。
2. 重启电脑：根据提示完成重启。
3. 安装 WSL：
   - 以管理员身份打开命令提示符（CMD）。
   - 执行 `wsl --set-default-version 2` 将 WSL 默认版本设为 2。
   - 执行 `wsl --update` 安装 WSL（国内网络建议添加 `--web-download` 参数减少下载失败）。
4. 下载并安装 Docker Desktop：从官方网站下载对应 CPU 架构的安装包（Windows 通常为 AMD64），按提示完成安装。
5. 启动 Docker Desktop：需保持 Docker Desktop 软件运行。
6. 验证安装：在 Windows 终端输入 `docker --version`，若能打印版本号则表示安装成功。

### Mac 系统安装

1. 根据 Mac 电脑的芯片类型（Intel 或 Apple Silicon）下载对应的 Docker Desktop 安装包。
2. 按提示完成安装。

命令行使用：尽管 Docker Desktop 提供可视化界面，但命令行在各操作系统上通用性更强，因此教程主要通过命令行讲解。

## 三、docker pull 下载镜像命令

用于从 Docker 仓库下载镜像到本地。

一个完整的镜像名称包含四部分内容：**registry/namespace/image_name:tag**

- **registry**：仓库地址。`docker.io` 表示 Docker Hub 官方仓库，官方仓库可省略。
- **namespace**：命名空间，通常是作者或组织名称。`library` 是 Docker 官方仓库的命名空间，可省略。
- **image_name**：镜像的名称。
- **tag**：镜像的标签名，通常表示版本号。`latest` 表示最新版本，可省略。

示例：

- `docker pull nginx`：从 Docker Hub 官方仓库下载最新版 Nginx 镜像。
- `docker pull docker.n8n.io/n8nio/n8n`：从 n8n 的私有仓库下载 n8n 镜像。

**Docker Hub 网站**：Docker 官方仓库，可搜索、查看镜像详情（如官方镜像、版本号、使用说明）。

**Registry（仓库地址）与 Repository（镜像库）**：

- repository = registry/namespace/image_name
- 一个镜像库中存放同一镜像的不同版本
- 整个 Docker Hub 网站可视为一个 Registry。
- Nginx 可视为一个 Repository。

下载镜像时因网络问题报错（Linux/Windows/Mac）：（视频参考 06:57）

**docker images**：列出所有已下载到本地的 Docker 镜像。

**docker rmi image_name 或 docker rmi image_id**：删除镜像

- 可指定镜像名称或 ID。

**docker pull --platform=xxx nginx**：拉取特定 CPU 架构的镜像

- Docker 镜像作为软件，在不同的 CPU 架构下（如 AMD64、ARM64）有不同的版本。
- `docker pull` 命令默认会自动选择最适合当前宿主机 CPU 架构的镜像，因此大部分情况下不需要关注这个选项。
- 特殊情况：对于某些低功耗迷你主机（如香橙派），其 CPU 架构通常为 ARM64，需提前确认所需镜像是否提供 ARM64 版本。

## 四、docker run 运行容器

**docker run 镜像名或镜像 ID**：使用镜像创建并运行容器（最重要命令）

- 用模具制造一个糕点 / 进行类的实例化

**docker ps**：查看正在运行的容器。`docker run` 后，原窗口被占用，可新开窗口执行 `docker ps`

- ps：process status（进程状态）
- 输出信息包括：Container ID（容器唯一 ID）、Image（基于哪个镜像创建）、Names（容器名称，如果 `docker run` 没有手动设置名字，则系统会自动分配一个随机的名字）。
- `docker ps` 默认只查看运行中的容器。
- `docker ps -a`：支持查看运行和已停止的容器。

**docker run -d 镜像名或镜像 ID**

- d：detached mode，分离模式，表示让容器在后台运行，不会阻塞当前窗口。控制台只打印容器 ID，容器日志不会直接输出到终端。
- 执行后打印的是容器的长 ID，而 `docker ps` 会输出容器的一个短 ID，短 ID 就是截取的长 ID，作用是一样的。

**自动拉取**：可以跳过 `docker pull` 命令，直接执行 `docker run`，如果本地不存在指定镜像，`docker run` 会先自动拉取镜像，再创建并运行容器。

**docker run -p 端口号1:端口号2 镜像名**

- 每个容器运行在独立的虚拟网络环境中，与宿主机的网络隔离，默认无法直接从宿主机访问容器内部网络。
- `-p` 参数将宿主机的端口映射到容器内部的端口。
- `-p 宿主机端口:容器内部端口`（先外后内）
  - **示例**：`-p 80:80` 将宿主机的 80 端口转发到容器内的 80 端口。

## 五、挂载卷

**【绑定挂载】docker run -v 宿主机目录:容器内目录 镜像名**

- 功能：将宿主机的文件目录与容器内的文件目录进行绑定。使得在任一方修改该文件夹时，另一方都会同步修改。
- 挂载卷：绑定的目录就称为挂载卷。
- 目的：实现数据的**持久化保存**。当容器被删除时，容器内的数据也会被删除，但**挂载卷可确保容器删除时，数据仍保存在宿主机上**。

**【命名卷挂载】docker run -v 卷的名字:容器内目录 镜像名**

- 让 Docker 自动创建一个存储空间，并为其命名：`docker volume create 卷的名字`
- 查询挂载卷在宿主机的真实目录：`docker volume inspect 卷的名字`
- 命名卷挂载的好处：命名卷第一次使用的时候，Docker 会把容器目录中的内容同步到命名卷中，进行一个初始化；而绑定挂载没有这个功能。

**docker volume list**：查看所有创建过的卷

**docker volume rm 卷名**：删除卷

**docker volume prune -a**：删除所有没有任何容器在使用的卷

**小结：**

- `docker run` = 使用镜像创建 + 运行容器
- 每次执行都会创建一个新的容器

## 六、docker run 的其他参数

**docker run -e xxx**：传递环境变量，可多次 `-e`

- 可在 Docker Hub 上搜索容器对应镜像，可传递的环境变量有哪些。

**docker run --name 容器名**：指定容器的名字，该名字在整个宿主机上必须唯一，不能重复

**docker run -it**：控制台可以进入容器内部交互，类似于从宿主机进入了容器的 cmd

**docker run --rm**：当容器停止时，删除容器

- `-it` 常常和 `--rm` 搭配使用，用于临时调试容器。

**docker run --restart xxx**：用于配置容器停止时的重启策略

- always：容器停止就立即重启
- unless-stopped：意外停止重启，但手动停止不重启

**docker rm -f 容器 ID 或容器名**

- 删除容器
- `-f`：force，强制删除，对正在运行的容器需要加上

## 七、调试容器

如果不想创建容器，只想对已有容器进行启停，使用 stop / start

- `docker stop 容器 ID 或容器名`：停止一个正在运行的容器，停止后，`docker ps` 查询不到该容器。
- `docker start 容器 ID 或容器名`：重新启动一个已停止的容器。
- 使用 stop / start 重新启停容器时，不需要再写已写过的端口映射、挂载卷和环境变量，Docker 已自动记录并保存，可通过 `docker inspect 容器 ID 或容器名` 查看，让 AI 分析输出结果。

**docker create**：使用和 `docker run` 相同，但只创建容器，不立即启动（回顾：`docker run` = 创建 + 启动），如果要启动，用 `docker start`

**docker logs 容器 ID 或容器名**：查看容器的日志

- `-f`：follow，滚动查看日志，实时刷新。

### Docker 技术原理简述

利用 Linux 内核的两大原生功能实现容器化

- Cgroups（Control Groups）：用于限制和隔离进程的资源使用（为每个容器设置 CPU、内存、网络带宽等），确保容器资源消耗不影响宿主机或其他容器。
- Namespaces：用于隔离进程的资源视图，使得容器只能看到自己内部的进程 ID、网络资源和文件目录，而看不到宿主机的。
- 本质：Docker 容器本质上是一个特殊的进程，但进入容器内部后，其表现看起来像一个独立的操作系统。每个 Docker 容器都是一个独立的运行环境，每个容器内部表现的都像一个独立的 Linux 系统。

**docker exec 容器 ID 或容器名 Linux 命令**：在一个正在运行的 Docker 容器内部执行 Linux 命令。

- 示例：`docker exec my_nginx ps -ef` 查看容器内进程。由于资源视图隔离，看不见宿主机进程。

**docker exec -it 容器 ID /bin/sh**：进入容器内部获得交互式命令行环境，可进行文件系统查看、进程管理或深入调试。

- 可以直接在命令行环境里执行 cd、ls 等。
- Docker 为尽量压缩镜像的大小，容器内部通常是极简操作系统，可能缺失 vi 等常用工具，需要自行安装。（视频参考 24:18）

## 八、构建镜像

Dockerfile：是一个文本文件，详细列出了如何制作 Docker 镜像的步骤和指令

- Dockerfile：图纸
- 镜像：模具
- 容器：糕点

使用 Dockerfile 制作一个镜像并推送到 Docker Hub 上（以将一个 Python 程序打包为 Docker 镜像为例）：（视频参考 25:43）

- **准备 Dockerfile 文件**（这段好多看不懂啊）
- `FROM 基础镜像`：所有 Dockerfile 的第一行，选择一个基础镜像，表示新镜像在此基础上构建。
- `WORKDIR 目录路径`：设置镜像内的工作目录，后续命令在此目录下执行。
- `COPY 源路径 目标路径`：将宿主机的文件或目录拷贝到镜像内的指定路径。
- `RUN 命令`：在镜像构建过程中执行的命令（例如安装依赖）。
- `EXPOSE 端口号`：声明镜像提供服务的端口（仅为声明，非强制，实际端口映射仍由 `-p` 参数决定）。
- `CMD 命令`：容器运行时默认执行的启动命令。一个 Dockerfile 只能有一个 CMD 指令。
- `ENTRYPOINT 命令`：与 CMD 类似，但优先级更高，不易被 `docker run` 命令覆盖。

**构建镜像**：`docker build -t 镜像名称:版本号 Dockerfile所在目录`

- `docker build -t docker-test .`（在当前目录构建名为 `docker-test` 的镜像）。
- 镜像构建好之后，可以使用 `docker run` 基于该镜像创建一个容器。

**将镜像推送至 Docker Hub**

- `docker login`
- 构建镜像：要带上 namespace【`docker build -t <用户名>/<镜像名称>[:<版本号>]`】。推送时镜像名称必须包含用户名作为命名空间。
- `docker push <用户名>/<镜像名称>[:<版本号>]`
- 在 Docker Hub 上用 `<用户名>/<镜像名称>` 搜索镜像。

## 九、docker 网络

### 1. 桥接模式

Docker 网络默认是 Bridge（桥接模式），所有容器默认连接到此网络。每个容器被分配一个内部 IP 地址（通常是 `172.17.x.x` 开头）。

在内部子网中，**容器可以通过内部 IP 地址互相访问。但容器网络与宿主机网络隔离**，需通过端口映射（`-p`）才能从宿主机访问。

**创建子网**：`docker network create <子网名称>`

- 该子网默认也是桥接模式。

**指定容器加入不同的子网**：`docker run -d --name <容器名称> --network <子网名称>`

- 同一个子网的容器可以互相通信，而跨子网则不行。
- 同一子网内的容器可以使用**容器名称**互相访问，而不必使用内部 IP 地址（Docker 内部 DNS 机制可以把容器名字转换成 IP 地址）：
- `docker run -e` 加入环境变量时，可以使用容器名而不是 IP 地址。
- ping 容器名字。

### 2. Host（主机模式）

Docker 容器直接共享宿主机的网络命名空间、直接使用宿主机的 IP 地址，无需端口映射（`-p` 参数），容器内的服务直接运行在宿主机的端口上，通过宿主机的 IP 和端口即可访问容器。

- 语法：`docker run --network host`
- 在浏览器直接访问服务器的 IP 地址加端口 80 就可以访问到容器。

在容器内部查询 IP 地址：（视频参考 32:40）

- 安装工具 iproute2
- 命令：`ip addr show`
- 可以发现容器的内网地址 = 云服务器内网地址，说明容器共享了宿主机的网络空间。

### 3. None（不联网模式）

容器不连接任何网络，完全隔离。

**docker network list**：展示出所有网络

- NAME 中的 bridge、host、none 是三种默认网络，不能删除。
- `docker network rm <网络 ID>` = `docker network remove <网络 ID>`：删除自定义子网。

## 十、Docker Compose（多容器编排）

当一个完整的应用由多个模块（如前端、后端、数据库）组成时

- 若将所有模块打包成一个巨大容器，会导致故障蔓延、可伸缩性差、扩容只能将整个大容器复制一份，做不到针对某个模块的精准性扩容。
- 最佳实践是把每个模块独立容器化，但是管理多个容器（创建、网络配置）会增加使用成本（多次执行 `docker run` 并需要配置容器间网络）——引入容器编排技术。

**docker compose 使用 yml 文件管理多个容器**

- yml 文件列出了容器如何创建、如何协同工作的说明。可理解为多个 `docker run` 命令。
- `docker run` 命令和 docker compose 文件的对比关系：（视频参考 34:56）

- `services`：顶级元素，每个服务对应一个容器。
- `image`：对应 `docker run` 中的镜像名。
- `environment`：对应 `docker run` 的 `-e` 参数。
- `volumes`：对应 `docker run` 的 `-v` 参数（挂载卷）。
- `ports`：对应 `docker run` 的 `-p` 参数。

网络：Docker Compose 会自动为每个 Compose 文件创建一个默认子网，文件中定义的所有容器都会自动加入此子网，并可通过服务名称互相访问。不像 `docker run` 命令那样需要显式地为容器指定子网。

**docker compose 可以自定义容器的启动顺序，确保依赖服务先启动**（视频参考 35:58）

- `depends_on`

AI 辅助：可借助 AI 工具生成等价的 Docker Compose 文件。（创建 docker compose 文件：视频参考 36:19）

- `docker compose up`：启动 YAML 文件中定义的所有服务（容器）。
- 会自动创建子网和容器。
- `-d`：后台运行。
- `docker ps` 检查正在运行的容器。
- 如果容器已经在运行，再执行一次 `docker compose up` 并不会启动新的容器，没有任何效果。
- `docker compose down`：停止并删除由 Compose 文件定义的所有服务和网络。
- `docker ps -a`
- `docker compose stop`：仅停止服务，不删除容器。
- `docker compose start`：启动已停止的服务。
- `docker compose -f <目录/文件名.yml> up`：指定非标准文件名（docker-compose.yaml）的 Compose 文件进行操作。

**小结：**

Docker Compose 是一个轻量级的容器编排技术，适合个人使用和单机运行。

企业级服务器集群大规模容器编排：可以使用软件 Kubernetes。

## 十一、总结

1. Docker 的核心概念：容器、镜像、镜像仓库。
2. Docker 的安装方法。
3. 下载镜像、配置镜像站（上传镜像）的方法。
4. 使用 `docker run` 创建、运行容器，及重要参数。
   - `-p`：端口映射
   - `-v`：挂载卷
   - `-e`：环境变量
5. 进入容器内部进行调试。
6. Docker 网络：bridge、host、none，如何创建子网。
7. Dockerfile。
8. Docker Compose。