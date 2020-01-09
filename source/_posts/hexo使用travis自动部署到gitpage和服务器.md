---
title: hexo使用travis自动部署到gitpage和服务器
date: 2020-01-09 19:59:58
tags: [hexo,nginx,linux]
---


hexo使用了两年， 中途断断续续的写了一些博客。最近工作不忙，并且刚买了个乞丐版的腾讯云服务器，闲不下来，学习点新东西。给我那布满灰尘的hexo博客，配置了一下自动部署，感觉瞬间有内涵起来了呢！

<!--more-->

### 准备工作

首先你需要准备好以下方面的知识: 
- hexo的使用 
- 配置 gitpage
- 一台服务器
- 配置nginx
- 配置travis

这些东西, 网上搜索还是很容易上手的.

### 开始


#### 自动部署 gitPage

玩过hexo 的人多多少少听过用过 gitPage 吧,毕竟这免费的羊毛,不薅白不薅.

hexo 通常的玩儿法 ,就是本地先创建一个hexo项目, 然后上传到 github , 并且项目名称设置为 `[username].github.io` , 然后在项目的 `setting` 中设置 gitpage 对应的分支，这样当你访问 `[username].github.io` 的时候就会自动返回hexo打包文件根目录下的 `index.html` ， 就可以看到你的博客的静态文件了。

上面的也就是网上大多数hexo教程的步骤。在这个过程中， 每次提交前， 都需要本地跑一遍 `hexo g -d` 指令，来编译打包并且上传.

现在都2020年了， 用 travis 解放双手吧， 它可以帮助你线上打包自动部署。

首先， 我们的 **目标** 是： 省去本地打包的步骤， 我只要写一个md， 然后提交到我的博客源码项目里， 就会自动打包并把文件提交到博客页面项目里去，注意这里是两个项目， 但也可以用一个项目的两个分支来分别存这两份代码。


- travis简单使用

首先在项目根目录下新建一个文件 `.travis.yml` 这个配置文件， 上传代码之后， travis会根据这里面的配置去跑流程。
例如：
```yml
# 使用语言
language: node_js
# node版本
node_js: stable
# 设置只监听哪个分支
branches:
  only:
  - blog #我的事blog分支
cache:
  apt: true
  directories:
    - node_modules
# tarvis生命周期执行顺序详见官网文档
before_install:
- echo 'ready to run '
...
...

...

```

- 配置ssh

在 travis 上给hexo打包之后，文件提交到我们的github上， travis需要有权限提交。而travis又无法输入账号密码，所以就要用ssh连接。
具体步骤参考[这里](https://segmentfault.com/a/1190000009054888)

配置好ssh之后，将`.travis.yml` 配置好，如下：
```
# 使用语言
language: node_js
# node版本
node_js: stable
# 设置只监听哪个分支
branches:
  only:
  - blog
cache:
  apt: true
  directories:
    - node_modules
# tarvis生命周期执行顺序详见官网文档
before_install:
- git config --global user.name "LqqJohnny" #填好自己的name email
- git config --global user.email "XXXXX@qq.com"
- npm install -g hexo-cli
install:
- npm i 
script:
- hexo clean
- hexo generate
after_success:
- cd ./public
- git init
- git add --all .
- git commit -m "Travis CI Auto Builder"
# 这里的 REPO_TOKEN 即之前在 travis 项目的环境变量里添加的
- git push --quiet --force https://$REPO_TOKEN@github.com/LqqJohnny/LqqJohnny.github.io.git  master

```

配置好之后就，提交代码吧

然后去 https://travis-ci.org/ 上看看 运行结果。

看到是绿色成功之后， 去github项目里看看打包代码已经上传到gitpage对应的分支上了。


总结： 就是在原先hexo用法的基础上 加上一个 travis ，自动打包部署的事情就在travis上做，travis会按照配置文件在不同的生命周期执行对应的指令。
需要注意的就是travis要想上传文件到github必须要配置一下ssh

#### 自动部署服务器

在上面的基础上， 我们只需要在 `after_success` 里面配置好上传打包代码到服务器的指令就好了。

首先，需要让travis连接上我们的服务器，这里也是用到的ssh免密登录。
详细的步骤课参考 [这里](https://www.cnblogs.com/homehtml/p/11796836.html)

简单来说，就是在服务器上新建一个travis专用用户， 然后生成ssh密匙对，再安装travis工具，来将密匙提交到你的travis上，并设置到项目中去。然后在 `.travis.yml` 中配置一句命令来吧打包文件上传服务器。

期间会碰到很多关于权限的坑，新建的travis用户可能会没有读写权。需要多谷歌一下吧。

最终的`.travis.yml` 如下： 

```yml
# 使用语言
language: node_js
# node版本
node_js: stable
# 设置只监听哪个分支
branches:
  only:
  - blog
cache:
  apt: true
  directories:
    - node_modules
# tarvis生命周期执行顺序详见官网文档
before_install:
- openssl aes-256-cbc -K $encrypted_0fe679bc9ce6_key -iv $encrypted_0fe679bc9ce6_iv
  -in id_rsa.enc -out ~/.ssh/id_rsa -d
- chmod 600 ~/.ssh/id_rsa
- echo -e "Host 106.54.69.121\n\tStrictHostKeyChecking no\n" >> ~/.ssh/config
- git config --global user.name "LqqJohnny"
- git config --global user.email "1277601297@qq.com"
- npm install -g hexo-cli
install:
- npm i 
script:
- hexo clean
- hexo generate
after_success:
- rsync -rv --delete -e 'ssh -o stricthostkeychecking=no' public/ travis@106.54.69.121:/usr/local/webserver/nginx/html/hexoblog
- cd ./public
- git init
- git add --all .
- git commit -m "Travis CI Auto Builder"
# 这里的 REPO_TOKEN 即之前在 travis 项目的环境变量里添加的
- git push --quiet --force https://$REPO_TOKEN@github.com/LqqJohnny/LqqJohnny.github.io.git
  master

```

这样一来就配置好了， 一次提交， 自动部署到gitpage 和 服务器两个地方。

#### nginx 

接下来， 在服务器上起一个nginx ， 配个端口，就可以看到自己的hexo博客了。
对于nginx新手可以使用 [宝塔](https://www.bt.cn/) 来部署，它提供了一个可视化界面给你设置nginx ， 不需要频繁的手动修改nginx.conf 文件 。



### 参考文章 

[Travis-CI自动化测试并部署至自己的CentOS服务器](https://www.cnblogs.com/homehtml/p/11796836.html)

[使用 Travis 自动部署 Hexo 到 Github 与 自己的服务器](https://segmentfault.com/a/1190000009054888)

[宝塔](https://www.bt.cn/bbs/thread-19376-1-1.html)
