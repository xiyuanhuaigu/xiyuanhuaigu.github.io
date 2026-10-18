---
title: "WSL2 docker 配置"
date: 2022-07-24 20:45:00
---
# 先决条件：

- 确保计算机运行的是 Windows 10（[已更新到版本 2004](ms-settings:windowsupdate) 的内部版本 18362 或更高版本）。
- [安装 WSL，并为在 WSL 2 中运行的 Linux 发行版设置用户名和密码](https://docs.microsoft.com/zh-cn/windows/wsl/install)。

```plaintext
WSL 可以在 WSL 版本 1 或 WSL 2 模式下运行发行版。 可通过打开 PowerShell 并输入以下内容进行检查：`wsl -l -v`。 通过输入 `wsl --set-version  <distro> 2`，确保发行版设置为使用 WSL 2。 将 `<distro>` 替换为发行版名称（例如 Ubuntu 18.04）。

在 WSL 版本 1 中，由于 Windows 和 Linux 之间的根本差异，Docker 引擎无法直接在 WSL 内运行，因此  Docker 团队使用 Hyper-V VM 和 LinuxKit 开发了一个替代解决方案。 但是，由于 WSL 2  现在在具有完整系统调用容量的 Linux 内核上运行，因此 Docker 可以在 WSL 2 中完全运行。 这意味着 Linux  容器可以在没有模拟的情况下以本机方式运行，从而在 Windows 和 Linux 工具之间实现更好的性能和互操作性。
```

# 安装 Docker Desktop：

借助 Docker Desktop for Windows 中支持的 WSL 2 后端，可以在基于 Linux 的开发环境中工作并生成基于 Linux 的容器，同时使用 Visual Studio Code 进行代码编辑和调试，并在 Windows 上的 Microsoft Edge 浏览器中运行容器。

若要安装 Docker（在已[安装 WSL](https://docs.microsoft.com/zh-cn/windows/wsl/install) 之后）：

1. 下载 [Docker Desktop](https://docs.docker.com/docker-for-windows/wsl/#download) 并按照安装说明进行操作。
2. 安装后，从 Windows 开始菜单启动 Docker Desktop，然后从任务栏的隐藏图标菜单中选择 Docker 图标。 右键单击该图标以显示 Docker 命令菜单，然后选择“设置”。

   ![Docker Desktop dashboard icon](https://docs.microsoft.com/zh-cn/windows/wsl/media/docker-starting.png)

   3.确保在“设置”>“常规”中选中“使用基于 WSL 2 的引擎”。

![Docker Desktop general settings](https://docs.microsoft.com/zh-cn/windows/wsl/media/docker-running.png)

4.通过转到“设置”>“资源”>“WSL 集成”，从要启用 Docker 集成的已安装 WSL 2 发行版中进行选择。

![Docker Desktop resource settings](https://docs.microsoft.com/zh-cn/windows/wsl/media/docker-dashboard.png)

5.若要确认已安装 Docker，请打开 WSL 发行版（例如 Ubuntu），并通过输入 `docker --version` 来显示版本和内部版本号

6.通过使用 `docker run hello-world` 运行简单的内置 Docker 映像，测试安装是否正常工作
