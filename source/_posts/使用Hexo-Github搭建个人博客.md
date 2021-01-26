---
title: Hexo博客搭建指南
date: 2019-10-21 23:23:01
index_img: /img/post_index_img/hexo.jpg
tags: Hexo
categories: 技术
---

# Hexo简介

Hexo是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub上，是搭建博客的首选框架。更多详细信息可以进入[hexo官网](https://link.zhihu.com/?target=https%3A//hexo.io/zh-cn/)进行查看，Hexo的创建者是台湾人，对中文的支持很友好。



# 安装准备

## 注册github账户

因为需要使用github page来托管我们的网站（当然后续也可以通过申请个人域名），因此首先你需要注册一个github账户，登录github网站按照网站提示的步骤进行操作。

## 本地安装并配置git

Git是目前世界上最先进的分布式版本控制系统，可以有效、高速的处理从很小到非常大的项目版本管理。也就是用来管理你的hexo博客文章，上传到GitHub的工具。Git非常强大，作为程序开发人员都应掌握git的使用。廖雪峰老师的[Git教程](https://link.zhihu.com/?target=https%3A//www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)写的非常好

在git官网下载安装 git for windows，下载后会有一个git bash的命令行工具，以后就用这个工具来使用git，在git bash执行：

```text
git config --global user.name "xiangli-bjtu"  // 换成自己的github用户名，非昵称 
git config --global user.email  "xavierlee.gy@gmail.com"  // 填写自己的github注册邮箱
```

## 配置ssh免密登录

提交代码需要提供你的github权限才可以，但是直接使用用户名和密码太不安全了，所以我们使用ssh key来解决本地和服务器的连接问题。右键git bash执行以下命令：
```text
ssh-keygen -t rsa -C "xavierlee.gy@gmail.com"
```
然后连续3次回车，会在用户目录下生成一个`.ssh`文件夹里面有公钥、私钥、主机列表三个文件。打开`.ssh\id_rsa.pub`文件，这个就是公钥。然后进入你的github主页，进入`个人设置 → SSH and GPG keys → New SSH key`将刚复制的内容粘贴到key那里，title随便填，保存。

输入以下命令检查是否配置成功：

```text
ssh -T git@github.com
```

出现提示，输入yes，如果之后看到` You've successfully authenticated, but GitHub does not provide shell access. `说明SSH已配置成功！

[关于ssh的原理](https://www.jianshu.com/p/33461b619d53)

## 安装node.js

创建博客的hexo工具基于node.js，所以需要安装一下node.js和包管理工具npm，直接去官网下载安装即可。

检测npm是否安装成功，在命令行中输入`npm -v `。另外，windows在git安装完后，就可以直接使用git bash来敲命令行了，不用自带的cmd

由于默认的npm源在国外，需要更换npm源为淘宝源：

```text
npm config set registry https://registry.npm.taobao.org
```

在用户目录下会生成一个`.npmrc`文件可以查看使用的源。



# Hexo博客搭建

## 安装hexo和创建博客目录

1. hexo是一个基于node.js的工具，能将用于撰写博客的markdown文档渲染为网页。在电脑一个合适的位置创建一个文件夹，可以命名为blog，hexo框架与博客有关内容都在这个文件夹中，进入这个文件夹下直接右键git bash打开，输入以下命令安装hexo。

```text
npm install -g hexo-cli
```

接下来进行博客的初始化

```text
hexo init 
```

初始化完成之后，我们可以看到多个文件目录：

- `node_modules`: 依赖包
- `public`：存放生成的页面
- `scaffolds`：生成文章的一些模板
- `source`：用来存放你的文章
- `themes`：主题
- `_config.yml`: 博客的配置文件

执行

```text
hexo clean
hexo generate
```

其中 `hexo clean`清除了你之前生成的东西，也可以不加，更为简略的命令是使用缩写用 `hexo c`

 `hexo generate` 顾名思义生成静态文章，可以用 `hexo g`缩写

将本地的markdown文件编译成静态文件（即显示网页必须的内容如html、css、js等）。这些内容会保存博客文件夹内。接下来执行`hexo s` 测试hexo是否安装成功，打开浏览器访问 [http://localhost:4000](http://localhost:4000/) 若能看到内容则说明安装成功

使用`ctrl+c`可以把服务关掉。

## 部署到github

上面只是在本地生成了博客文件，接下来要做的就是就是把博客内容发布到github上，这样就可以让其他的人进行访问。我们需要安装插件，用来将本地hexo博客的内容部署到github

```text
npm install hexo-deployer-git --save
```

在github创建一个名为`xiangli-bjtu.github.io`的远程仓库，如果你的github用户名是test，那么你就新建`test.github.io`的仓库（必须是你的用户名，这是固定要求其它名称无效），将来你的网站访问地址就是 [http://test.github.io](http://test.github.io/) 了

接下来我们需要修改本地hexo博客目录的配置文件`_config.yml`中有关deploy的部分

```text
deploy:
  type: git  
  repository: git@github.com:xiangli-bjtu/xiangli-bjtu.github.io.git
  branch: main  # 2021年修改：由于美国弗洛伊德事件引发的BLM运动，github已将默认分支的名称由原来带有正义的master修改为main
```

然后执行

```text
hexo clean
hexo generate
hexo deploy
```

就可以将本地public文件夹下的内容上传部署到github，过一会儿就可以在`xiangli-bjtu.github.io`这个网站看到你的博客了。



# Hexo配置

在文件根目录下的`_config.yml`，就是整个hexo框架的配置文件了。可以在里面修改大部分的配置。详细可参考[官方的配置](https://link.zhihu.com/?target=https%3A//hexo.io/zh-cn/docs/configuration)描述。

下面介绍几个常用的配置：

## **网站**信息

`title`：网站标题

`subtitle`网站副标题

`description`网站描述，主要用于SEO，告诉搜索引擎一个关于您站点的简单描述，通常建议在其中包含您网站的关键词。

`author`您的名字

`language`网站使用的语言

`timezone`网站时区。Hexo 默认使用您电脑的时区。[时区列表](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/List_of_tz_database_time_zones)。比如说：`America/New_York`, `Japan`, 和 `UTC` 。

## 博客图片内容管理

修改本地hexo博客目录下站点配置文件`_config.yml`中的`post_asset_folder:false`这个选项设置为`true`。这样在运行`hexo n "xxxx"`来生成marnkown文时，`/source/_posts`文件夹内除了xxxx.md文件还有一个同名的文件夹，用于存放markdown文件里需要插入的图片

## **Front-matter**

 [Front-matter](https://hexo.io/zh-cn/docs/front-matter)  是markdown文件最上方以 `---` 分隔的区域，用于指定个别文件的变量，举例来说：

```text
title: Hello World
date: 2013/7/13 20:46:25
---
```

下是预先定义的参数，您可在模板中使用这些参数值并加以利用。

`layout` 布局

`title `文章标题

`date `建立日期

`updated `更新日期

`comments `开启文章的评论功能

`tags `标签

`categories `分类

`permalink `覆盖文章网址

其中分类和标签的区别在于：分类具有顺序性和层次性，也就是说 `Foo, Bar` 不等于 `Bar, Foo`；而标签没有顺序和层次。

## 3种**layout（布局）**

Hexo 有三种默认布局：`post`、`page` 和 `draft`。在创建这三种不同类型的文件时，它们将会被保存到不同的路径：

| 布局    | 路径             |
| :------ | :--------------- |
| `post`  | `source/_posts`  |
| `page`  | `source`         |
| `draft` | `source/_drafts` |

- 默认的布局是`post`，当你每一次使用代码

```text
hexo n
```

它其实默认使用了`post`布局，也就是在`source`文件夹下的`_post`里面生成博客文件

- 如果你想另起一页，那么可以使用`page`布局

```text
hexo new page board
```

系统会自动给你在source文件夹下创建一个board文件夹，以及board文件夹中的index.md，这样你访问的board对应的链接就是`http://xxx.xxx/board`

- `draft`是Hexo 的一种特殊布局，也就是你如果想写文章，又不希望发布时被看到，可以使用该布局。若在写草稿文件的过程中，想要预览一下，那么可以使用以下命令，在本地端口中开启服务预览。

  ```text
  hexo server --draft
  ```

  如果想把草稿转为post，可通过 `publish` 命令将草稿移动到 `source/_posts` 文件夹，该命令的使用方式与 `new` 十分类似

  ```text
  hexo publish draft newpage
  ```

## 更换Hexo主题

如果对原始的hexo主题不满意，hexo还提供了丰富的主题进行选择，这里我使用的是[fluid主题](https://fluid-dev.github.io/hexo-fluid-docs/start/#%E6%90%AD%E5%BB%BA-hexo-%E5%8D%9A%E5%AE%A2)。这里仅介绍几个用到的主题配置步骤，更多的可以去看fluid主题的官网文档

### **安装主题**

下载[fluid](https://codeload.github.com/fluid-dev/hexo-theme-fluid/zip/v1.8.4)解压到 themes 目录，并将解压出的文件夹重命名为 `fluid`。

修改博客目录下的站点配置文件 `_config.yml`

```text
theme: fluid 
```

### **页面顶部大图**

* 图源

**主题配置文件**中，每个页面都有名为 `banner_img` 的属性，可以使用本地图片的相对路径，也可以为外站链接，比如：

指向本地图片：

```text
banner_img: /img/bg/example.jpg   # 对应存放在 /source/img/bg/example.jpg
```

本地图片时可自定义路径，但必须在 source 目录下。图片大小建议压缩到 1MB 以内，否则会严重拖慢页面加载。

指向外站链接：

```text
banner_img: https://static.zkqiang.cn/example.jpg
```

- 高度

鉴于每个人的喜好不同，开放对页面 `banner_img` 高度的控制。

**主题配置文件**中，每个页面对应的 `banner_img_height` 属性，有效值为 0 - 100。100 即为全屏，个人建议 70 以上。

- 蒙版透明度

**主题配置文件**中，每个页面对应的 `banner_mask_alpha` 属性，有效值为 0 - 1.0， 0 是完全透明（无蒙版），1 是完全不透明

### **博客标题**

页面左上角的博客标题，默认使用**博客配置**中的 `title`，这个配置同时控制着网页在浏览器标签中的标题。

如需单独区别设置，可在**主题配置**中设置：

```text
navbar:
  blog_title: 记录学习的点滴 # 导航栏左侧的标题，为空则按 hexo config.title 显示
```

### 文章在首页的略缩图

markdown文章开头的 `Front-matter` ，可添加 `index_img` 属性。

```text
---
title: 文章标题
tags: [Hexo, Fluid]
index_img: /img/example.jpg
date: 2019-10-10 10:00:00
---
文章内容
```

和 Banner 配置相同，`/img/example.jpg` 对应的是存放在 `/source/img/example.jpg` 目录下的图片（目录也可自定义，但必须在 source 目录下）。也可以使用外链 Url 的绝对路径。

### 创建「关于页」

在Fluid主题界面的菜单栏里，有一个about页面「关于页」，这个你现在点击进去时找不到网页的，因为你的文章中没有about这个东西。如果你想要的话，需要手动创建：

```text
hexo new page about
```

它就会在根目录下`source`文件夹中新建了一个`about`文件夹，以及`index.md`。修改 `index.md`，为其添加 `layout` 属性，并在里面写s

修改后的文件示例如下：

```text
---
title: about
date: 2019-10-21 21:23:01
layout: about
---

这里写关于页的正文，支持 Markdown, HTML
```

### 支持Latex公式

当需要使用 Latex 语法的数学公式时，可手动开启本功能，需要完成三步操作：

**1. 设置主题配置**

```text
post:
  math:
    enable: true
    specific: true
    engine: mathjax
```

`specific`: 建议开启。当为 true 时，只有在文章 `Front-matter` 里指定 `math: true` 才会在文章页启动公式转换，以便在页面不包含公式时提高加载速度。

`engine`: 公式渲染引擎，目前支持 `mathjax` 或 `katex`。

**MathJax**

优点

- 对 LaTeX 语法支持全面
- 右键点击公式有扩展功能

缺点

- 需要加载 JS，页面加载会比较慢，并且有渲染变化
- kramed 渲染器对内联公式的转义字符 `\` 支持不足

**KaTeX**

优点

- 没有 JS 不会影响页面加载
- 渲染器效果好 (相对 kramed 对 MathJax 的内联公式)

缺点

- 小部分 LaTeX 不支持

**2. 更换 Markdown 渲染器**

由于 Hexo 默认的 Markdown 渲染器不支持复杂公式，所以必须更换渲染器。

先卸载原有渲染器：

```text
npm uninstall hexo-renderer-marked --save
```

然后根据上方配置不同的 `engine`，推荐更换如下渲染器：

mathjax: `npm install hexo-renderer-kramed --save`

katex: `npm install @upupming/hexo-renderer-markdown-it-plus --save`

**3. 文章中设置**

如果你写的文章里面用到了数学公式，需要在文章Front-matter里打开mathjax开关。

```text
---
title: index.html
date: 2018-12-5 01:30:30
tags:
mathjax: true
--
```

### 社交链接

在主题配置中设置：

```yaml
about:
  icons: # 更多图标可从 https://hexo.fluid-dev.com/docs/icon/ 查找，class 代表图标的 css class
    - { class: 'iconfont icon-github-fill', link: 'https://github.com/xiangli-bjtu', tip: 'GitHub' }
    - { class: 'iconfont icon-bilibili-fill', link: 'https://space.bilibili.com/254950760', tip: 'b站' }
    - { class: 'iconfont icon-zhihu-fill', link: 'https://www.zhihu.com/people/li-xiang-43-89-60', tip: '知乎' }
    - { class: 'iconfont icon-wechat-fill', qrcode: '/img/wechat_QRcode.jpg' }  
```

- `class`: 图标的 css class，主题内置图标详见[这里](https://fluid-dev.github.io/hexo-fluid-docs/icon/)
- `link`: 跳转链接
- `tip`: 鼠标悬浮在图标上显示的提示文字
- `qrcode`: 二维码图片，当使用此字段后，点击不再跳转，而是悬浮二维码（需要指定图片）



# 为博客开设图床

图床简单来说就是个图片仓库，随着我们写博客越来越多，若将博客内插入的图片全都存在本地会很占空间。

在各家厂商提供的图床服务中，GitHub 图床是个不错的选择，还支持 jsDelivr CDN 加速访问（jsDelivr 是一个免费开源的 CDN 解决方案），我们可以使用PicGo 工具一键上传图片，操作简单高效，GitHub 和 jsDelivr 都是大厂，不用担心跑路问题，不用担心速度和容量问题，而且完全免费，可以说是目前免费图床的最佳解决方案！

### 创建Github图床仓库

首先我们创建仓库，命名为`Blog-images-hosting`

### 生成Token

在主页依次选择【Settings】-【Developer settings】-【Personal access tokens】-【Generate new token】，填写好描述，勾选【repo】，然后点击【Generate token】生成一个Token，注意这个Token只会显示一次，自己先保存下来，或者等后面配置好PicGo后再关闭此网页

### 配置PicGo

PicGo是一个用于快速上传图片并获取图片 URL 链接的工具，前往[下载PicGo](https://github.com/Molunerfinn/picgo/releases)，安装好后开始配置图床

- 设定仓库名：按照【github用户名/图床仓库名】的格式填写

- 设定分支名：【main】

- 设定Token：粘贴之前生成的【Token】

- 指定存储路径：填写想要储存的路径，如【ITRHX-PIC/】，这样就会在仓库下创建一个名为 ITRHX-PIC 的文件夹，图片将会储存在此文件夹中

- 设定自定义域名：它的作用是在图片上传后生成访问链接，自定义域名需要按照这样去填写：

  `https://raw.githubusercontent.com/账户名/仓库名/main`，

  比如我的是：`https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main`

### Typora集成PicGo

Hexo博客文档是markdown文件，推荐的一款编辑工具是Typora。点击`偏好设置 -> 图像 ->`，在上传服务，选择 PicGo(app)。PicGo路径，选择PicGo软件的安装路径。点击验证图片上传选项可以测试是否配置成功。



# 实现在多终端上更新博客

hexo 是一个优秀的静态博客工具，唯一的不足就是源文件无法同步，让人几乎只能在一台电脑上写博客，为了解决这个问题，我们可以利用git的分支系统进行多终端工作了，这样每次打开不一样的电脑，只需要进行简单的配置和在github上把文件同步下来，就可以无缝操作了。

由于`hexo d`上传部署到github的其实是hexo编译后的文件（即博客目录下的public文件夹内容），是用来生成网页的，不包含源文件。

因此我们的思路就是利用git的分支管理，将源文件上传到github的另一个分支即可。

* 首先在github仓库中新建一个分支`hexo-source`，用这个分支来存储博客的源文件，而`main`分支用来存放hexo编译后的网页文件。然后在这个仓库的settings中，选择默认分支为`hexo-source`（这样每次同步的时候就不用指定分支，比较方便）。
* 然后在本地的任意目录下，打开git bash，将默认分支克隆到本地

```text
git clone git@github.com:xiangli-bjtu/xiangli-bjtu.github.io.git
```

* 将上述克隆到本地的仓库中，除了.git 文件夹外的所有文件都删掉

  把之前我们写的博客源文件全部复制过来，除了`.deploy_git`。这里应该说一句，复制过来的源文件应该有一个`.gitignore`，用来忽略一些不需要的文件，如果没有的话，自己新建一个，在里面写上如下，表示这些类型文件不需要git：

  ```text
  .DS_Store
  Thumbs.db
  db.json
  *.log
  node_modules/
  public/
  .deploy*/
  ```

* 然后

```text
git add .
# 将hexo源文件映射到远程repo上
git commit -m 'commit source files'
# 将源文件push到分支
git push 
```

然后就可以在github上看到新建了分支，里面已经有博客的源文件了。

这样，我们在`source`文件夹中写好一篇markdown文件后，博客的部署分为两步：

1. 当添加新文章或更改配置后，需要将hexo源文件push到分支`hexo-source`进行备份

```text
git add .  //添加修改内容到本地仓储
git commit -m 'modify blog'  //提交修改内容到本地仓库
git push  //将本地分支和分支下的内容推送到远程
```

2. 接下来执行博客编译将网页静态文件上传:

```text
hexo c
hexo d -g 
```



## 更换设备后迁移博客

如果我们换了一台电脑，配置好 Hexo 的环境，[配置 Git SSH key](https://hsiaovv.github.io/2017/04/06/GitHub配置SSH-key/)，把博客源文件代码克隆下来:

```
git clone xxxxxxxxx.xx (你的 github page 的 repo 地址)
```

博客源文件下载下来之后，默认的分支是 master，需要切换到 `blogSource` 分支

```
git checkout origin/blogSource
```

然后cd到博客目录依次执行以下命令：

```
npm install hexo
npm install
npm install hexo-deployer-git --save
```

接下来就可以开始愉快的写博客了，写完之后记得把源文件代码 push 到 Github 上，然后用 Hexo 部署到自己博客上面



# 绑定域名

待补充……

