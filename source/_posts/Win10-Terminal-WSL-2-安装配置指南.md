---
title: Win10 Terminal + WSL 2 安装配置指南
date: 2019-11-2 14:43:25
index_img: /img/post_index_img/WSL.png
tags:
- Linux
categories:
- 技术
---

# Windows Terminal 安装

## 介绍

众所周知，Windows系统自带的终端`cmd, PowerShell`一直遭受诟病，以傻、黑、粗闻名于世。不过微软在2019开发者大会上发布了新的终端模拟器——Windows Terminal：一个面向命令行工具和 shell（如命令提示符、PowerShell 和适用于 Linux 的 Windows 子系统 (WSL)）用户的新式终端应用程序。 它的主要功能包括多个选项卡、窗格、Unicode 和 UTF-8 字符支持、GPU 加速文本呈现引擎，你还可用它来创建你自己的主题并自定义文本、颜色、背景和快捷方式。

在介绍安装Windows Terminal之前，我们需要先对一些概念做了解：

[命令行界面 (CLI)、终端 (Terminal)、Shell、TTY，傻傻分不清楚？](https://printempw.github.io/the-difference-between-cli-terminal-shell-tty/)

## 安装

**注意：Windows Terminal 要求 Windows 10 1903 (build 18362) 及以上版本。**

直接在Microsoft Store 里搜索安装即可。

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/windows-terminal-microsoft-store.png" style="zoom:67%;" />

## 配置

默认情况下，安装后打开终端后的下拉菜单中包含 Windows PowerShell、Command Prompt 和 Azure Cloud Shell 配置文件，在打开新的选项卡时会以 PowerShell 作为默认配置文件启动。

 和 VS Code 一样，使用 JSON 文件配置相关选项。默认情况下，每个配置文件使用不同的命令行可执行程序（即对应打开的终端），但是您可以根据自己的喜好，创建任意数量的使用同一可执行程序的配置文件。

Windows Terminal 中包含两个设置文件。一个是**defaults.json**，可以通过按住 `Alt` 键并点击下拉菜单中的 Settings 按钮打开，这是一个不可更改的文件，其中包含 Windows Terminal 的所有默认设置。另一个是 **settings.json**，可以通过点击下拉菜单中的 Settings 按钮访问，您可以在其中应用所有的自定义设置。

settings.json配置文件主要包含了如下的几个部分：

- **全局设置**：位于 settings.json 文件的最外侧，这一部分的属性都将影响整个终端窗口。主要包括：默认配置程序的GUID 或配置文件名称、终端启动大小、鼠标滚动速度等

- **配置文件设置`"profiles"`**，`"profiles"` 对象分为两个部分：`"defaults"` 和 `"list"`。

  ```json
  "defaults":
  {
      // SETTINGS TO APPLY TO ALL PROFILES
  },
  "list":
  [
      // PROFILE OBJECTS
  ]
  ```

   `"list"` 中是出现在 Windows 终端下拉菜单中的所以程序，你可以为其中的每个唯一的配置文件单独进行配置， 如果希望将某个设置应用于所有配置文件，则应该将其添加到 `"defaults"` 部分。

  可以为终端设置背景图片、透明度、字体等显示。

- **配色主题 `schemes`**：可以在 settings.json 文件的 `schemes` 数组中定义配色方案，一个好的的配色方案网址是 [Windows Terminal Themes](https://windowsterminalthemes.dev/)

  当然，windows终端在其 defaults.json 文件中有一些预设的主题，可按住 alt 并选择设置按钮来访问该文件。 如果要在一个命令行配置文件中设置配色方案，请添加 `colorScheme` 属性，并将配色方案的 `name` 作为值。

- **快捷键绑定 `actions`**：自定义快捷键。

更多配置详情见官网文档：https://docs.microsoft.com/zh-cn/windows/terminal/



## Git-bash 置入 Windows Terminal

我们之前在搭建hexo博客时已经安装好了git for windows。

下载一个git 的图标，地址见 [gwindows_logo](https://gitforwindows.org/img/gwindows_logo.png)。将在下载的图标保存到任意一个文件夹

在settings.json的“profiles”部分1添加如下一段内容：

```json
{
	"guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b7}",
	"hidden": false,
    "name": "git bash",
	"icon": "C:\\Users\\XiangLi\\Pictures\\gwindows_logo.png",
"commandline": "D:\\Git\\bin\\bash.exe" 
}
```



# WSL2

## 安装

Windows Subsystem for Linux（适用于 Linux 的 Windows 子系统）的简写。它有两个版本，WSL 1 和 WSL 2。 建议使用 WSL 2，它具有更好的整体性能。

* 安装WSL2要求你的电脑运行的是 Win10 ，且版本号为 2004（内部版本19041）或更高。使用 `win + r` 输入 `winver` 可快速查看 Windows 版本。如果你的 Win10 版本号低于 2004，可使用 Windows 10 易升工具手动升级。

* 启用 Windows 子系统功能，使用管理员权限打开一个 PowerShell 窗口，输入以下命令后重启系统：

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

* 由于当前Windows 默认启用的是 WSL1，还需要再启用虚拟机平台功能，在 PowerShell 中输入以下命令后再次重启系统

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

* 下载[用于x64机器的WSL2 Linux内核更新程序包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)，如果您使用的是ARM64计算机，请改为下载[ARM64软件包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_arm64.msi)。如果不确定所用的机器类型，请打开“命令提示符”或“ PowerShell”并输入：`systeminfo | find "System Type"`
* 在 PowerShell 中输入以下命令，将 WSL 默认版本改为 WSL2:

```powershell
wsl --set-default-version 2
```

* 接下来在 Microsoft Store 中找一个 Linux 发行版进行安装

* 可以通过打开PowerShell命令行并输入命令来检查分配给已安装的每个Linux发行版的WSL版本`wsl -l -v`

  若要为安装的不同发行版设置为任一版本的WSL支持，请运行：

  ```powershell
  wsl --set-version <distribution name> <versionNumber>
  ```

  确保`<distribution name>`用发行版的实际名称和`<versionNumber>`数字“ 1”或“ 2”代替。


**注意**：WSL默认安装在Windows的C盘，单单一个WSL不会造成C盘太大的负担，但是当你对WSL使用增多，尤其一通`apt-get install`操作后，C盘容量可能变得捉急，因此使用WSL后C盘使用容量这点需要大家注意。

## 设置

### 文件系统互通

WSL2 访问 Windows 文件系统依然通过挂载分区的方式，WSL里面访问windows文件很简单，你只需要输入`cd /mnt/d`，就是d盘了，以此类推。

相比于 WSL1，这次增加了 Windows 访问 Linux 分区的能力，可以在资源管理器中输入 `\\wsl$\<子系统名>` 访问对应的子系统分区，为了方便也可以在资源管理器中把 Linux 分区挂载成一个磁盘。

### GPU 支持

https://zhuanlan.zhihu.com/p/149517344

### 使用 Docker

后续学习Docker时再补充……

