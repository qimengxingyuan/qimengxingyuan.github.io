---
title: Hello World
tags:
  - 建站
abbrlink: 16107
categories:
  - 踩坑记录
---
欢迎光临[祈梦星缘的Blog](https://yhzhao.cn/)! 这是本站的第一篇文章，在这篇文章中会手把手的教你搭建完成 ✅ 属于自己的blog空间。快跟随我开始吧！ 

## Quick Start
我们的博客搭建是基于Hexo和Githup Pages来完成的。搭建完成后的资源部署到github.io上。同时可以给我们的博客来设置一个自己的访问域名。

### 为什么使用 GitHub Pages
如果你想要搭建一个轻量级的个人博客服务，GitHub Pages 相较 WordPress 之类的建站服务有什么优势呢？
- 首先他是完全免费的，相较其他的同类产品，他能替你省下一笔服务费，节约下的钱可以让你买一些其他的会员服务；
- 无须自己购买云服务进行搭建，只需按步骤一步步操作即可，即使你不懂他的技术细节；
- 支持的功能多，玩法丰富，你可以绑定你的域名、使用免费的 HTTPS、自己 DIY 网站的主题、使用他人开发好的插件等等；
- 当完成搭建后，你只需要专注于文章创作就可以了，其他诸如环境搭建、系统维护、文件存储的事情一概不用操心，都由 GitHub 处理

当然了，作为一款免费的服务，我们也是要遵守 GitHub 官方使用建议和限制，在使用的时候项目和网站的大小不要超过 1GB，也不要过于频繁的更新网站的内容（每小时不超过 10 个版本），每个月的也要注意带宽使用上限为 100GB。

综合来看，GitHub Pages 依旧可以说是中小型博客或项目主页的最佳选项之一。

### 一、准备Github账号和仓库
首先你需要注册Github账号。并在个人主界面里选择创建一个新的 Repository。要注意的是仓库名称的格式形式必须是：`username.github.io`。

![](http://image.yhzhao.cn/img20210911151737.png)

仓库创建完成之后转到Setting页面。找到 Pages 选项，选择一个 GitHub 官方提供的主题。选择完毕之后 GitHub Pages 就会自动帮你生成好网站，在他跳转的界面点击 Commit changes 按钮，网站就可以访问了。

![](http://image.yhzhao.cn/img20210911151840.png)

### 二、使用Hexo进行网站开发和部署
#### 安装
1. 首先要安装Node.js, 访问国内[Nodejs](https://nodejs.org/en/)网站。下载后安装即可
2. 安装hexo，打开终端输入命令
```bash
npm i hexo-cli -g
npm config set registry https://registry.npm.taobao.org // 添加国内镜像源
```
3. 后续可以使用`npm`安装各种插件。


4. 初始化, 我这里用到的是Github上的一个开源主题：[matery](https://github.com/blinkfox/hexo-theme-matery)。参照Readme一步步安装即可。

#### 写文章

```bash
hexo new post "article title" // 新建一篇文章
```

#### 本地调试
```bash
hexo g // 生成静态资源
hexo s // 启动服务，在 http//:localhost:4000/
hexo clean // 清理生成的文件
```

#### 推送
将编译好的文件推送到github即可，但不建议直接推送。参考下一章的使用CI集成。

### 三、使用CI集成工具
每次写完一篇文章,都会手动执行hexo g和hexo d去生成静态网页后在进行部署到Github page上去。而且为了保存文章的源码md文件还需要push到对应的仓库分支上,比较麻烦。那么能不能只保存原md文件push上去,其他的操作都让它自动去完成呢 ？

答案肯定是可以的,下面就来讲解一下CI具体的实现方法:

首先来介绍利用的工具:
`travis`: 是在线托管的CI服务,用Travis来进行持续集成,不需要自己搭服务器[官方网站](https://travis-ci.org/)

#### 接入Travis过程:
1. 登录Travis网站用github授权登录
2. 登录后在个人主页选择你需要CI的仓库
3. 点击你选择的hexo博客的仓库进行配置。点击左上角红色框的More options按钮,选择Settings打开配置页面进行配置。第一个配置项:`Build only if .travis.yml is present`代表的意思是:只有在`.travis.yml`文件中配置的分支改变了才构建。第二个配置项:`Build pushes`代表当推送完这个分支后开始构建

![](http://image.yhzhao.cn/img20210911151951.png)

到了这一步,我们已经开启了要构建的仓库,但是还有个问题就是,构建完后,我们怎么将生成的文件推送到github上呢? 我们只要想github一push,他就自动构建并push静态文件到githubpages,那么下面要解决的就是Travis CI怎么访问github了

4. 在Travis CI配置Github的`Access Token` 用来访问Github。首先我们进入github的设置页面,然后点击`Developer settings`选项进入开发者设置,然后字点击`Personal access tokens`
![](http://image.yhzhao.cn/img20210911152129.png)

点击右上角的`Generate new token`会让你输入密码确定,然后进入一个生成token的页面。输入token的描述,选择这个token权限,然后然后点击生成就可以了,然后复制保存下来,下次在进来就看不到了。


5. 在 Travis 仓库配置界面setting里面 环境变量`Environment Variables`进行配置token方便在构建文件中引用: 如下图
![](http://image.yhzhao.cn/img20210911152229.png)

6. 在博客的源码文件分支下添加`.travis,yml`配置文件,决定怎么执行构建任务,下面是.travis.yml的内容:
``` yml
sudo: false
language: node_js
node_js:
  - 10 # use nodejs v10 LTS
cache: npm
branches:
  only:
    - master # build master branch only
script:
  - hexo generate # generate static files
deploy:
  provider: pages
  skip-cleanup: true
  github-token: $GH_TOKEN
  keep-history: true
  on:
    branch: master
  local-dir: public
```
7. 将 .travis.yml 推送到 repository 中。Travis CI 应该会自动开始运行，并将生成的文件推送到同一 repository 下的 gh-pages 分支下
8. 在 GitHub 中前往你的 repository 的设置页面，修改 GitHub Pages 的部署分支为 `gh-pages`。
![](http://image.yhzhao.cn/img20210911152323.png)

### 四、进阶玩法
#### 绑定域名
前提，你得有一个域名，有些域名需要备案后才能用。

1. 在域名解析添加记录
如果你用你顶点域名（如：`baidu.cn`)，就添加一条主机记录为`@`的，如果你用www子域名（如：`www.baidu.cn`, 就添加一条主机记录为`www`的, github绑定自己的域名只支持这两种，不支持其他子域名。
- 记录类型一定要为 CNAME 这种类型，只有这样你的域名才能指向你的github
- 记录值填 `yourname.github.io`
![](http://image.yhzhao.cn/img20210911153236.png)


2. 在github添加自定义域名
![](http://image.yhzhao.cn/img20210911153111.png)
3. 配置hexo的_config.yml。找到url设置，添加你的域名
``` yml
url: http://www.baidu.cn // 这里替换下
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:
```
4. 上传CNAME文件
光执行上面三个步骤还是不够，每次你上传更新时，你在github设置的域名可能会丢失，所以要上传一个`CNAME`文件，让github记住你添加的域名：
先创建一个名为`CNAME`的文件，没有后缀，再在文件中写上你的域名（如：`www.baidu.cn`）,然后把这个文件放在`/source`目录下，上传就行了。



#### 文章头设置
为了新建文章方便，建议将/scaffolds/post.md修改为如下代码：
``` markdown
---
title: {{ title }}
date: {{ date }}
top: false
cover: false
password:
toc: true
mathjax: true
summary:
tags:
categories:
---
``` 
这样新建文章后不用你自己补充了，修改信息就行。