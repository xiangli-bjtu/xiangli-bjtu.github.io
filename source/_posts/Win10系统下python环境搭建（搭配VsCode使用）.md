---
title: Win10系统下python环境搭建（搭配VsCode使用）
date: 2019-10-28 10:12:17
tags: 
- python
- VsCode
categories: 技术
---

# 安装python环境

## 下载并安装minicaonda

conda是一个开源的软件包管理工具和环境管理系统，可以轻松创建多个软件环境，并在多个环境之间轻松切换。例如，我们可以分别创建python3和python2的独立软件环境，并在需要该环境时轻松进行切换（根据工作目录）。与 conda有关的配置软件有两个，miniconda（含基本工具占用空间约400M），anaconda（包括常用到的python库以及GUI工具，占用空间约3G）。因为我们只是需要conda这个包管理工具，并且习惯使用VsCode作为python的编辑器。仅需要miniconda即可。

直接下载[官方miniconda安装包](https://conda.io/miniconda.html)，在下载页面选择对应平台的安装包进行安装。 自带python环境，可以根据自己长期使用的版本进行选择。

下载好安装包后，安装时可以选择作为用户软件安装（储存到C盘下的`User/用户文件夹`），或者做为系统软件安装（所有用户都可以使用）

到下面这一步时，勾选两个选项，即添加miniconda至环境变量PATH，注册miniconda所带的python为默认环境

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/miniconda.png)

## 修改为国内镜像源

默认的conda源在国外，下载python第三方包时由于大陆互联网的管制会很慢。

这里，我们使用清华的镜像：[https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/](https://link.jianshu.com/?t=https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/)

```text
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

conda config --set show_channel_urls yes
```

此时，目录 C:\Users<你的用户名> 下就会生成配置文件.condarc，可以查看conda的所有源。

除了conda，python还有一个自带的包管理工具pip，也可以将其替换为国内源，直接在user目录中创建一个pip目录，如：C:\Users\xxxx\pip，新建文件pip.ini，内容如下：

```text
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```



## 虚拟环境的使用

虚拟环境的好处可以使我们在创建不同的python项目时，避免相互之间的影响。

若我现在需要做一个项目使用 python3.6 的环境，可以在终端运行以下命令

```text
conda create --name 项目名 python=3.6
```

创建环境的过程中会让你确认该环境所需软件包，一般直接输入y后按回车就可以了。环境创建在miniconda安装路径下的`\env`子文件夹下：

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/env_location.JPG)

[点此查看更多虚拟环境的使用方法](https://blog.csdn.net/lyy14011305/article/details/59500819)

# 搭配VsCode使用

## 修改VsCode的终端以搭配虚拟环境

在windows平台下，VsCode打开的终端默认支持的是powershell，因为某些原因不支持不支持激活conda所创建的虚拟环境作为python项目的解释器。

* 一种解决办法是打开VsCode的terminal窗口，将默认的shell手动修改为cmd。重启之后终端输入`conda activate MARL_V2X`激活所创建的虚拟环境

输入`conda deactivate`退出虚拟环境。

* 当然，如果熟悉在linux下工作，可以常识将其改成gitbash，gitbash可以通过下载git软件来获取。

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/gitbash_location.JPG)

打开VsCode的用户设置文件（ctrl+shifp+p），修改`setting.json`文件增加如下两行：

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/modify_setting.JPG)

重启VsCode后终端默认调用的shell就变成了gitbash：

不过此时需要多一个步骤，在终端首先输入`source activate`，可以看到此时已经激活了conda，这是还是base环境即默认的python解释器

终端输入`conda activate MARL_V2X`激活所创建的虚拟环境

输入`conda deactivate`退出虚拟环境

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/entry_out_env.JPG)