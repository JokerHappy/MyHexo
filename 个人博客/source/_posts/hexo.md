
title: 利用 Hexo 和 Github 创建个人博客
date: 2016-02-14 
categories: Skill  
tags: [Hexo]

---

[Hexo](https://hexo.io/) 是一款基于 `Node.js` 的静态博客框架。`Hexo` 生成的静态页面可以部署在 `Github` 或 `Coding` 上，并且能够免费绑定自己的域名，可以用来很方便地搭建个人博客。



## Github

1. 注册 [github](https://github.com/)。
2. 在主页右下角创建New repository，name必须和用户名一致,如username.github.io。
3. 首次创建耐心等待10分钟左右审核，之后即可访问静态主页如http://username.github.io

个人博客初步搭建完成，接下来利用 `Hexo` 修改博客框架

## Hexo

### 安装 Node.js

选择对应的系统安装[Node.js](http://nodejs.cn/download/)
 
### 安装Git

最简单的方法 安装  [GitHub Desktop](https://desktop.github.com/) 

对 `Git` 还不太熟悉的小伙伴可以参考下 [Git 教程（廖雪峰的官方网站）](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

里面有详细的`Git`安装方法及使用方法

### 安装Hexo

首先将 `npm` 的源替换成淘宝的源

	npm config set registry http://registry.npm.taobao.org/

在 `Git` 中输入 `npm` 命令即可安装

	npm install -g hexo

在 `OS X`系统下 因为可能有文件读写权限的问题， 加上前缀`sudo`

安装过程中可能会出现错误，可以参考[Hexo 常见问题](https://xuanwo.org/2014/08/14/hexo-usual-problem/) 或自行`Google`查找。

### 建立本地博客

安装完成后，新建一个目录如 MyBlog 用于存放博客，切换到该目录下执行以下指令，Hexo 即会在目标文件夹初步生成博客所需要的所有文件：

	hexo init

如果对 `Hexo` 自带的模板不满意的话可以看一下[这里](http://www.zhihu.com/question/24422335)，自定义个性化的主题模板。

个人推荐：

* Jacman - https://github.com/wuchong/jacman
* Next - http://theme-next.iissnan.com/

**注意：**修改 ` Hexo ` 配置文件及主题配置文件时，按照YAML语法，参数冒号 `:` 后一定要留空格。

### 本地效果预览

在终端使用 `cd` 命令切换到博客所在目录 `MyBlog`，执行如下命令生成静态博客页面（public 文件夹）并启动本地服务器

	hexo generate
	hexo server

或者如下的缩写形式也可以：

	hexo g
	hexo s

若成功可在浏览器中访问 [http://localhost:4000/](http://localhost:4000/) 进行预览。

### 部署到Github

1. 利用  `Git`  将 `Github` 中的 `username.github.io` 项目    `clone` 到本地新建文件夹。
2. 将 `MyBlog` 中执行 `hexo g` 命令生成 `public` 文件夹，复制到`clone`的文件夹中。
3. 运行 `GitHub` 确认修改信息 `commit` 后执行右上角的 `Sync` 同步。（或者利用 `Git` 命令将修改内容 `pull` 到 `Github`）
4. 最后访问主页（http://username.github.io）观察效果，可能会存在延迟，请稍等几分钟。

### 编写博文

* 利用命令行 `cd` 到 MyBlog 中利用`Hexo`命令生成初始文章。

		hexo n "new"

* 在`hexo\source\_posts`中编辑生成出来的`new`文件。
 
* 利用 `Markdown` 文本编辑器修改`new`文件，编写博文。 对`Markdown`不熟悉的小伙伴可以查看 [Markdown 语法](http://wowubuntu.com/markdown/) 。















