# 猴子也能学会的部署方式

>**什么**，你是否在阅读文档后依旧晕头转向，束手无策？抑或者因开发经验不足而难以理解文档内容？  
那么你来对地方了。  
本文档旨在使用较为口语化的方式详细为您阐述从安装依赖，部署环境，到测试与开发的全套流程   
因为是面向所有人的教程，笔者将会尽可能地把每一步都掰开揉碎。若您觉得部分内容太简单，太罗嗦，可以参照下方目录自行跳转至有需要的部分进行查看   
~~保证~~ 即使是猴子，也能在阅读后轻松地完成部署！=P

## 环境准备

### Rust
微生活的后端使用 rust 进行编写，因此需要你至少对 rust 有一定了解  
因此我们默认您已经成功配置好了 rust 的开发环境 

若你目前对此还无头绪，笔者建议你先粗略地了解一番 rust 基础，并且可以自行前往B站搜索相关的下载教程

### Git

Git 是一个版本控制工具，可以用来下载项目代码、记录修改历史，并与其他开发者协作

你可以前往[Git 官方下载页面]("https://git-scm.com/downloads")，并按照提示下载

### Docker

Docker 是一个容器工具，可以把软件及其运行所需的依赖打包起来，在相对独立的环境中运行，减少“换台电脑就运行不了”的问题  

在微生活项目中，后端需要使用 MySQL、Redis、RabbitMQ 等服务。如果逐个手动安装，就需要分别处理版本、配置和启动方式。使用 Docker 后，可以直接通过镜像创建容器，快速运行这些服务，减少环境搭建的步骤  

可以把镜像理解为软件的安装模板，把容器理解为根据模板启动的软件实例。容器中的服务通过端口映射供后端访问，数据库等需要长期保存的数据则通过数据卷保存  

#### 1. 准备 WSL 2

WSL 可以理解为 Windows 提供的 Linux 运行环境，Docker Desktop 可以借助它运行本项目需要的 Linux 容器。

打开 **终端（管理员）**或 **PowerShell（管理员）**，执行：

```powershell
wsl --install --no-distribution
```

这会安装 WSL，且不额外安装 Ubuntu 等发行版。按照提示完成操作即可  

如果已经安装了 WSL，可以执行下面的命令更新：

```powershell
wsl --update
```

#### 2. 下载 Docker Desktop

打开 [Docker Desktop 的 Windows 安装页面](https://docs.docker.com/desktop/setup/install/windows-install/)，下载安装程序。

- 普通 Intel 或 AMD 电脑选择 **x86_64 / AMD64**。
- Windows ARM 电脑选择 **Arm**。

> **提示：** AMD64 也适用于普通的 Intel 64 位电脑，不是只有 AMD 处理器才能使用。


双击安装程序，按照提示安装。

如果出现选择后端的选项，使用 **WSL 2**。不同版本的安装界面可能略有区别，保留 WSL 2 的默认选择即可。

打开 Docker Desktop，完成首次启动提示，等待 Docker 引擎启动完成。

> **注意：** 安装完成不代表 Docker 已经运行。后面启动中间件时，需要先打开 Docker Desktop，并等待引擎就绪。

如果提示 WSL 需要更新，执行：

```powershell
wsl --update
```

然后重新打开 Docker Desktop。

#### 3. 检查安装结果

重新打开 PowerShell，依次执行：

```powershell
docker --version
docker compose version
docker info
```

前两个命令应该显示版本号；`docker info` 应该能显示包含 **Server** 部分的信息，且没有连接失败的报错。

还可以运行一个测试容器：

```powershell
docker run --rm hello-world
```

第一次执行会先下载测试镜像。如果最后看到：

```text
Hello from Docker!
```

说明 Docker 已经可以下载镜像并运行容器，可以继续下一步了。