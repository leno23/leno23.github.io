---
layout: post
title: 如何在github.io上搭建自己的博客
category: Web
bigimg: [/assets/images/2019-05-19/githubpages.png, /assets/images/2019-05-19/jekyllrb.jpg]
# no-post-nav: true
---

&#160; &#160; &#160; &#160;本文主要讲述如何利用GitHub Pages搭建自己的博客和利用Jekyll设置不同的主题。

**欢迎大家使用我的主题[Trice Jekyll](https://github.com/leno23/Trice-Jekyll)。具体使用方法可以查看我的博客[Jekyll主题Trice-Jekyll](https://leno23.github.io/web/2019/06/02/trice-jekyll.html)或`README`文档。**

## 利用Github Pages搭建自己的博客

### 什么是Github Pages

&#160; &#160; &#160; &#160;[GitHub Pages](https://pages.github.com/)是Github为开发者和其项目提供搭建静态网页的系统。你所搭建的静态网页会存储在特定的Github repository中。所以要在GitHub上搭建博客，首先你要有一个自己的仓库。  
&#160; &#160; &#160; &#160;GitHub Pages提供两种方式，一种是个人或组织的站点，类似博客；另一种是项目的站点，类似项目详细的介绍页面。我们只介绍第一种。

### 开通自己的博客

1. 创建仓库  
&#160; &#160; &#160; &#160;创建一个自己的站点，其实就是在GitHub上创建一个特殊的仓库。进入GitHub，点击`create a new repository`，创建一个以`username.github.io`命名的仓库。其中`username`是你的GitHub的用户名。
![create a new repository](https://github.com/leno23/leno23.github.io.assets/raw/master/images/2019-05-19-How-to-build-blog-in-github/user-repo.png)  
2. clone仓库  
使用git工具，把仓库拉到本地。  
```bash
git clone https://github.com/username/username.github.io.git
```
3. 编写首页  
进入本地项目，并添加一个`index.html`文件。
```bash
cd username.github.io
echo "Hello World" > index.html
```
4. 上传文件到远程仓库  
`Add`, `commit`, `push`你的文件。
```bash
git add --all
git commit -m "Initial commit"
git push -u origin master
```
5. 完成  
现在你可以打开浏览器，访问自己的博客了`https://username.github.io`。

## 利用Jekyll设置个性化主题

### 什么是Jekyll

&#160; &#160; &#160; &#160;[Jekyll](https://github.com/jekyll/jekyll)是一个基于[Ruby](https://github.com/ruby/ruby)静态网页生成器。是一款当前火热的开源的静态网站建站工具，拥有非常庞大的使用群体和社区，拥有丰富的插件和丰富的主题。  
&#160; &#160; &#160; &#160;`Github Pages`支持使用`Jekyll`作为建站工具。官方帮助文档[Using Jekyll as a static site generator with GitHub Pages](https://help.github.com/en/articles/using-jekyll-as-a-static-site-generator-with-github-pages)。  

### 使用Jekyll主题

&#160; &#160; &#160; &#160;前文提到，Jekyll拥有丰富的主题。这里推荐一个Jekyll主题站，[Jekyll Themes](http://jekyllthemes.org/)。你可以在里面挑选自己心仪的主题使用。  
&#160; &#160; &#160; &#160;一般使用Jekyll主题的步骤：  
1. fork主题  
Fork喜欢的主题到自己的账户下，方便后面管理。
2. clone主题  
Clone自己仓库的主题到本地。
```bash
git clone https://github.com/username/jekyll-theme.git
```
3. 修改远程仓库地址  
将此仓库的远程地址指向自己的博客仓库地址。
```bash
git remote add origin https://github.com/username/username.github.io.git
```
4. 修改配置文件  
根据自己的需要修改Jekyll的配置文件`_config.yml`。包括`title`、`img`、`url`等。  

5. 上传到远程仓库  
由于使用了不同的源进行上传，所以应该要使用`--force`指令。
```bash
git commit 'Use Theme'
git push origin master --force
```
6. 完成  
访问自己的博客`https://username.github.io`，看看新主题的效果吧！  

&#160; &#160; &#160; &#160;当然，对于不同的主题，可能需要做一些的特别的操作。  
&#160; &#160; &#160; &#160;比如我自己的主题[Trice Jekyll](https://github.com/leno23/Trice-Jekyll)，因为用到了很多三方库，所以除了修改`_config.yml`文件外，还需要下载同步一些第三方库。  
1. 首先，要安装ruby环境。
2. 运行`gem install bower`安装bower。
3. 运行`bower install`安装`bower.json`文件中的依赖项。
4. 运行`gem install bundler`安装bundle。
5. 运行`bundle install`安装`Gemfile`文件中的依赖项。

&#160; &#160; &#160; &#160;详细内容可参考该主题`README`文档。

### 编写发布博文

&#160; &#160; &#160; &#160;Jekyll博文要求放在`_posts`目录下面，可以使用`Markdown`语法编写。   
&#160; &#160; &#160; &#160;Markdown是一个Web上使用的文本到HTML的转换工具，可以通过简单、易读易写的文本格式生成结构化的HTML文档，[Markdown语法说明](http://www.markdown.cn/)。  
&#160; &#160; &#160; &#160;Jekyll对博文的命名有严格的规定，必须使用year-month-day-title.md格式。例如，2019-05-19-How-to-build-blog-in-github.md。   
&#160; &#160; &#160; &#160;写完后，就可以上传到远程仓库了：   
```bash
git add 2019-05-19-How-to-build-blog-in-github.md
git commit -m "How-to-build-blog-in-github"
git push
```

&#160; &#160; &#160; &#160;成功后，等待一段时间，就可以访问自己的博客查看博文了。

### 本地查看博客

&#160; &#160; &#160; &#160;如果你想要在提交到github之前，在本地看看博文的效果，那就需要在本地安装Jekyll环境。

1. 安装`Ruby`  
根据不同的系统选择不同的安装方式[downloads](https://www.ruby-lang.org/en/downloads/)。   
2. 安装`Github pages`   
打开Ruby终端，运行：  
```bash
gem install github-pages
```
3. 开启Jekyll本地服务  
进入本地仓库，运行：
```bash
jekyll serve --watch
```
&#160; &#160; &#160; &#160;默认情况下，该服务会侦听在本地4000的端口上，可以打开浏览器访问http://127.0.0.1:4000，这样就可以在本地查看自己的博文效果了。

## 本人遇到的一些坑

### 不同版本冲突问题

&#160; &#160; &#160; &#160;使用[Yummy Jekyll Theme](https://github.com/DONGChuan/Yummy-Jekyll)主题涉及到很多三方库，每个都对`ruby`版本或其他库版本有要求。而且有些三方库已经停止更新。所以除非对各个库十分熟悉，不然不建议更新三方库的版本。使用`Gemfile.lock`中的版本就好了。  
&#160; &#160; &#160; &#160;本博客后面修改使用了自己的主题[Trice Jekyll](https://github.com/leno23/Trice-Jekyll)。各版本都更新的最新，欢迎各位朋友尝试使用。

### certificate verify failed问题

&#160; &#160; &#160; &#160;这个问题是Ruby的net/http library在TLS握手时没有对SSL证书有效性进行检查。[此方案](https://gist.github.com/fnichol/867550)可完美解决问题。

### `Markdown`中文缩进

&#160; &#160; &#160; &#160;由于markdown语法主要考虑的是英文，所以对于中文的首行缩进并不太友好，可在首行输入如下代码实现缩进：
```bash
&#160; &#160; &#160; &#160;
```

***
最后，  
祝大家搭建好心仪的博客~