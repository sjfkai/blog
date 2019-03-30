---
title: 955.HOLIDAY 从0到1
date: 2019-03-30 17:16:44
tags:
  - 前端
  - Github
---


一个静态网站（955.holiday），从域名申请到上线发布的全过程。

## 背景

[996.ICU](https://996.icu) 一夜之间火爆了码农圈。当然也成了我们的饭桌话题。同事说：“朝九晚五”的公司也不在少数，我们可以同样做一个类似的网站来让一些正能量的公司得到曝光。

于是 [955.holiday](https://955.holiday) 就出现了。

Github（求star）: [https://github.com/955holiday/955.holiday](https://github.com/955holiday/955.holiday)


它是如何一步步上线的呢？下面进入正题：

## 域名申请

*tips: 你也可以跳过此步，直接使用 github 提供的默认访问地址*

当你有一个好的 `idea` ，第一时间就要去看一看合适的域名有没有被注册。

推荐两个注册域名的网站：

国内服务商：[阿里云](https://wanwang.aliyun.com/)
  
  **优点：** 相对便宜、备案方便  **缺点：** 需要实名认证

国外服务商：[GoDaddy](https://sg.godaddy.com/zh/)

  **优点：** 无须实名认证、支持支付宝 **缺点：** 价格偏高

如果你有备案的需求，就选择阿里云吧，当然并不是所有的域名都可以备案的，哪些域名可以备案请查询：[工业和信息化部域名行业管理信息公示](http://xn--eqrt2g.xn--vuq861b/#)

如果没有备案需求，价格相差又不是很多，就选择 GoDaddy 吧。

## 创建github pages

[github pages](https://pages.github.com/) 可以为我们每个 git 仓库提供静态网站的托管功能。

下面让我们创建一个可访问的静态网站：

### 新建项目
首先需要为我们的网站新建一个仓库（New repository）,比如： `hello-pages`

{% asset_img "create_repo.png" "create_repo" %}

### 生成静态网站

然后让我们在本地生成一个静态网站

```  
mkdir hello-pages && cd hello-pages
```

新建 `index.html`

```html
<html>
  <head>
    <title>hello pages</title>
  </head>
  <body>
    hello pages
  </body>
</html>
```

在本地构建 git repo 并上传到 github

```sh
$ echo "# hello-pages" >> README.md # 增加 readme 文件
$ git init
$ git add .
$ git commit -m "first commit"
$ git remote add origin git@github.com:<your-name>/hello-pages.git # 这里要修改为你自己的仓库地址
$ git push -u origin master
```

访问github，打开 hello-pages 仓库，确保我们的项目已经推送了上去。

### 开启 github pages

在 github hello-pages 项目页面点击 `Setting`，向下找到 `GitHub Pages` -> `Source` 选择 `master branch`

页面刷新，返回 `GitHub Pages` 显示发布成功。页面可以正常访问了。

{% asset_img "enable_pages.png" "enable_pages" %}


[你可以在这里查看此阶段源码](https://github.com/sjfkai/hello-pages/tree/init)

## 绑定域名

至此，我们的 `hello-pages` 已经可以正常访问了，只不过访问地址还是 github 的域名。

下面我们进一步将域名解析到我们的网站。

### 配置DNS

在你购买域名的网站，找到域名的DNS解析配置页面，根据你的需求，选择以下一种配置方式。（各网站解析记录配置方式可能略有不同，请查看站内帮助）

#### 1. 访问地址为顶级域名 

如：example.com

新建 `A` 记录，将记录指向下面四个ip

    185.199.108.153
    185.199.109.153
    185.199.110.153
    185.199.111.153

详细信息，请参考：[Setting up an apex domain](https://help.github.com/en/articles/setting-up-an-apex-domain)

#### 2. 访问地址为二级域名 

如：www.example.com blog.example.com

新建 `CNAME` 记录， 将记录指向 `<your-github-name>.github.io` 

详细信息，请参考：[Setting up a custom subdomain](https://help.github.com/en/articles/setting-up-a-custom-subdomain)


#### 3. 访问地址为顶级域名和www二级域名 

如 example.com 和 www.example.com 可以同时访问 ，但是这里的二级域名只能是 www.example.com

这种方式需要同时配置上面的 `1` 和 `2`

详细信息，请参考：[Setting up an apex domain and www subdomain](https://help.github.com/en/articles/setting-up-an-apex-domain-and-www-subdomain)

### 增加CNAME文件

在git仓库中新增 `CNAME` 文件，内容为自定义域名的访问地址。

请注意，该文件只能包含唯一一个地址。

如果的 DNS 配置为`顶级域名`和 `www 二级域名`可以同时访问，CNAME 中只需要填写 `顶级域名`即可。

将修改 `push` 到 github

### 查看效果

访问你配置的地址，页面应该已经可以打开了。

DNS解析是有延迟的，如果无法打开，可能需要稍等片刻。

如果长时间依旧无法打开，就需要排查一下是不是哪一步除了问题。

### HTTPS

GitHub Pages 同时支持 http 和 https 协议，但是并不会自动跳转。

我们可以通过配置，强制跳转到 https 站点。

在 github hello-pages 项目页面点击 `Setting`，向下找到 `GitHub Pages` 勾选 `Enforce HTTPS `

至此，你已经可以通过修改 `HTML`, 增加 `CSS` 来美化你的网站了。

[你可以在这里查看此阶段源码](https://github.com/sjfkai/hello-pages/tree/custom-domain)

## 自动构建并发布

现如今，大多数的前端项目都是基于框架（比如 `React` 、 `VUE`）构建的。

对于这些项目，我们需要通过 `build` 才能产生浏览器可以解析的 `HTML` 、 `CSS` 、 `JS`.

如果每次项目变动，我们都需要修改源码，然后 `build`，然后再将 `build` 结果推送到 `github-pages` 项目。其实还是很繁琐的。

我们能不能减少这些无畏的工作量呢？

答案是有的

### 使用gh-pages分支

GitHub Pages 允许将静态文件放在三个地方：

1. master 分支
1. master 分支的 docs 目录下
1. gh-pages 分支

我们可以将项目源码放在 `master` 分支下，将 `build` 结果放在 `gh-pages` 分支, 每次`build`完成后，更新 `gh-pages` 分支。

如果上述操作依旧通过手动执行的话，其实并不会减少什么工作量。

我们需要借助`持续集成服务`（Continuous Integration，简称 CI）来实现进一步的自动化。


### 使用 Travis CI 自动部署

    Travis CI 提供的是持续集成服务（Continuous Integration，简称 CI）。它绑定 Github 上面的项目，只要有新的代码，就会自动抓取。然后，提供一个运行环境，执行测试，完成构建，还能部署到服务器。

了解[Travis CI](https://travis-ci.org/)可以参考官方文档，也可以阅读阮一峰的[持续集成服务 Travis CI 教程](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html)。

每当我们 push 代码到 github 时，可以借助 Travis CI 自动 build 并将 build 结果更新到 gh-pages 分支。刚好 `Travis CI` 也提供了这样的功能。 详见：[GitHub Pages Deployment](https://docs.travis-ci.com/user/deployment/pages/)

接下来我们就开始操作了

#### 创建 GitHub access_token

点击右上角自己的头像 -> `Settings` -> `Developer settings` -> `Personal access tokens` -> `Generage new token`

填写 `Token description`

勾选 `repo` 确认创建

`access_token` 创建成功，临时保存一下，后面会用到。

#### 设置 Travis CI

* 使用 `Github` 账号登录 [Travis CI](https://travis-ci.org/)

* 点击 `+` 

* 点击 `Sync account`

* 打开 hello-pages 项目仓库的开关。

* 点击 `Setting` 

* 关闭 `Build pushed pull requests`

* `Environment Variables` 下面新增一项 `name`为 GIT_REPO `Value`为刚刚申请的 `access_token`

#### 修改项目

我们继续在之前的 hello-pages 项目上继续修改，这里以 `React` 项目为例

```sh
$ npm i create-react-app -g

# create-react-app 创建项目前，需要删除之前的文件
$ rm -rf index.html CNAME

$ create-react-app .
```

在 `public` 下面新建和之前一样的 `CNAME` 文件。 目的是为了最终 `build` 目录包含 `CNAME` 文件。

新建 `travis.yml`

```yml
language: node_js
node_js:
  - "10"
install: yarn

script: npm run build

deploy:
  provider: pages
  local_dir: ./build
  skip_cleanup: true
  github_token: $REPO_TOKEN
  keep_history: true
  on:
    branch: master
```

更改完成后，`commit`，然后推送到 github 。

稍等片刻，就可以在 Travis CI 看到项目已经开始构建了。构建成功后 `gh-pages` 分支就已经自动推送到 github 了。

回到 github hello-pages 项目页面点击 `Setting`，向下找到 `GitHub Pages` -> `Source` 选择 `gh-pages branch`。

切换成功后就可以正常访问了。

[最终代码](https://github.com/sjfkai/hello-pages)

## SEO

`Github pages` 虽好，但一直有一个头疼的问题，就是网站无法被百度收录。据说是因为 Github 屏蔽了百度爬虫。

有搜索需求的可以考虑使用 [coding.net](https://coding.net/)。功能基本上和 github 是一致的，这里就不做深入介绍了。
