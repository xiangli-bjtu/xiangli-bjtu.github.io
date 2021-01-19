---
title: Python开发环境搭建
date: 2019-10-28 10:12:17
index_img: /img/post_index_img/python.jpg
tags: 
- Python
- VsCode
categories: 技术
---

# 安装Python

## 下载并安装minicaonda

conda是一个开源的软件包管理工具和环境管理系统，可以轻松创建虚拟环境，并在多个环境之间轻松切换。例如，我们可以分别创建Python3和Python2的独立软件环境，并在需要该环境时轻松进行切换（根据工作目录）。

与 conda有关的配置软件有两个，miniconda（含基本工具占用空间约400M），anaconda（包括常用到的python库以及GUI工具，占用空间约3G）。因为我们只是需要conda这个包管理工具，仅需要下载miniconda即可。

直接下载[官方miniconda安装包](https://conda.io/miniconda.html)，在下载页面选择对应平台的安装包进行安装。 自带Python环境，可以根据自己长期使用的版本进行选择。下载好安装包后，安装时可以选择作为用户软件安装（储存到C盘下的`User/用户文件夹`），或者做为系统软件安装（所有用户都可以使用）

注意安装过程进行到到下面这一步时，需要勾选两个选项：添加miniconda至环境变量PATH，注册miniconda所带的python.exe为默认解释器

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/miniconda.png)

## 安装第三方库的方法

* 默认的conda源在国外，下载Python第三方包时由于大陆互联网的管制会很慢。因此我们可以使用清华的镜像，执行以下命令：

```text
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

此时，在本地目录` C:\Users<你的用户名> `下就会生成配置文件`.condarc`，可以查看conda的所有源。

如果想要还原conda默认源，执行以下命令：

```python
conda config --remove-key channels
```

* 除了conda，Python还有一个自带的包管理工具pip，也可以将其替换为国内源，直接在user目录中创建一个pip目录，如：C:\Users\xxxx\pip，新建文件pip.ini，内容如下：

```text
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

* 如果遇到在线下载一直失败（网速太慢），或者选择镜像源没有对应的包，可以尝试去以下两个网址下载离线文件进行解压：

https://repo.anaconda.com/pkgs/main/win-64/

https://www.lfd.uci.edu/~gohlke/pythonlibs/#ecos

## 虚拟环境的使用

虚拟环境的好处可以使我们在创建不同的Python项目时，避免相互之间的影响。

若我现在需要做一个项目使用 python3.6 的环境，可以在终端运行以下命令

```text
conda create --name 项目名 python=3.6
```

创建环境的过程中会让你确认该环境所需软件包，一般直接输入y后按回车就可以了。环境创建在miniconda安装路径下的`\env`子文件夹下：

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/env_location.JPG)

[点此查看更多虚拟环境的使用方法](https://blog.csdn.net/lyy14011305/article/details/59500819)



# VsCode配置

**Win10下最为省事的还是Pycharm，不过Pycharm比较吃内存我还是喜欢用轻量化的Vscode，并且Vscode有丰富的插件支持**

在windows平台下，由于VsCode打开的终端默认支持的是powershell，单因为某些原因在powershell内输入激活conda所创建的虚拟环境命令，并不能成功生效

* 一种解决办法是打开VsCode的terminal窗口，将默认的shell手动修改为cmd。重启之后终端输入`conda activate 虚拟环境名`激活所创建的虚拟环境

输入`conda deactivate`退出虚拟环境。

* 当然，如果熟悉在linux下工作的指令，可以尝试将Vscode的默认终端改成gitbash，gitbash可以通过下载git软件来获取。

下载好git后打开VsCode的用户设置文件`ctrl+shifp+p`，修改`setting.json`文件增加如下两行：

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/modify_setting.JPG)

重启VsCode后终端默认调用的shell就变成了gitbash：

不过此时还需要一个步骤，在终端首先输入`source activate`，可以看到此时已经激活了conda，这是还是base环境即默认的Python解释器

终端输入`conda activate 虚拟环境名`激活所创建的虚拟环境



# Jupyterlab

## 安装

Jupyter源于Ipython Notebook，是使用Python（也有R、Julia、Node等其他语言的内核）进行代码演示、数据分析、可视化、教学的很好的工具.

直接用conda安装，在命令行执行

```text
conda install -c conda-forge jupyterlab
```

接着使用`jupyter-lab`或`jupyter lab`命令，然后默认浏览器会自动打开Jupyter Lab。

更多的操作详见：https://zhuanlan.zhihu.com/p/154515490

## 指定kernel

为不同的项目指定python kernel，创建jupyterlab做分析：

```
conda install ipykernel

# 安装kernel
ipython kernel install --user --name 虚拟环境名称
# 查看已安装kernel
jupyter kernelspec list
# 删除kernel
jupyter kernelspec remove 虚拟环境名称
```

在默认浏览器打开Jupyter Lab的界面，选择：

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/jupyterlab_env.png)



# 深度学习平台的配置

深度学习需要在电脑上进行GPU上的计算，比如运行TensorFlow或者Pytorch等，首先需要解决的是正确安装CUDA和CUDNN，下面介绍如何在Win10系统上完成以上操作：

## 查看本机的CUDA驱动适配版本

系统具有 NVIDIA 显卡，则往往已经自动安装了 NVIDIA 显卡驱动程序。如未安装，直接访问 [NVIDIA 官方网站](https://www.nvidia.com/Download/index.aspx?lang=en-us) 下载并安装对应型号的最新公版驱动程序即可。

在桌面右键“NVIDIA控制面板”，点击帮助->系统信息->组件。（前提是你的电脑有英伟达的显卡）

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/nvidia_control.png" style="zoom:80%;" />

在打开的窗口中，我们可以看到本机目前最高支持的CUDA版本。如果你升级了显卡驱动，将来也可能会支持更高版本。

## 下载和安装CUDA和cuDNN

* CUDA下载页面：https://developer.nvidia.com/cuda-downloads

  如果需要选择CUDA版本，可从这里打开：https://developer.nvidia.com/cuda-toolkit-archive

  CUDA安装完成后打开命令行，执行`nvcc -V` ，成功的话会返回cuda版本号。

  接下来需要在系统环境变量的Path项下添加几个路径，需要添加的默认的安装路径如下：

  ```text
  C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0\bin
  C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0\libnvvp
  ```

* 下载cuDNN时必须选择和你安装的CUDA匹配的版本，下载页面：https://developer.nvidia.com/rdp/cudnn-download。下载cuDNN是需要登录英伟达开发者账户的，如果没有的话，需要注册一个并填写问卷，很简单。注册并登录后，即可打开如下页面，选择对应的文件并下载。

  安装cuDNN首先需要解压cuDNN压缩包，可以看到有`bin、include、lib`目录。

  打开 `“C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA”` 目录，找到你安装的版本目录，打开，将cuDNN压缩包内`bin、include、lib`目录下的文件分别复制过去。

## Tensorflow

安装tensorflow是基于Python的，所以我们先创建一个用于tensorflow开发的虚拟环境。

安装tenforflow需要注意各个依赖之间的版本适配，去官网可以查到对应关系：

https://tensorflow.google.cn/install/source_windows#gpu



