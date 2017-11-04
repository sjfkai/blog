---
title: 通过travis自动部署hexo博客到github pages
date: 2017-11-04 17:19:00
tags:
---

## 背景

如果你曾了解过[Hexo](https://hexo.io/zh-cn/), 相信你已经可以通过`hexo deploy`部署自己的博客到`github pages`了。

但是，这样我们仅仅可以通过当前电脑来发布博客，如果电脑不在身边，或者不小心把文件删除了，就会很麻烦。

下文教你如何实现把自己写的博客和生成的静态文件同时托管在`github`：

## 将博客源码托管到github

细心的人可以发现，通过`hexo init`生成的文件中，已经包含了`.gitignore`文件。说明`hexo`开发者也是希望大家把博客源码托管到`git`上的。

我们可以通过分支来实现，将源码放在`master`(看个人喜好)分支、将`hexo deploy`的分支指定为`gh-pages`分支。具体实现如下：

1. 如果没有`github repository`首先需要在`github`新建一个仓库，点击`new repository`。

2. `hexo init` 生成一个新的博客项目

3. 根据[hexo 文档](https://hexo.io/zh-cn/docs/configuration.html)修改配置，使博客可以通过`git server`预览。

4. `git init` 

5. `git remote add origin git@github.com:account/blog_repo.git` 请将`git`地址改为`1`中说到的你自己的仓库地址

6. `git add .`

7. `git commit -m "init"`

8. `git push origin master -f` 将代码推到`github` `master` 分支


这时候，我们就把项目托管到了`github`，当你换了一台电脑，或者不小心把文件删除了的时候，只要重新`git clone`就可以了。

但是这仅仅只是将代码托管到了`github`。当我们新完成一篇博客，并把代码`push`到`github`上的时候，并不会自动`deploy`。

## 通过travis自动部署

[Travis CI](https://travis-ci.org/)是在软件开发领域中的一个在线的，分布式的持续集成服务，用来构建及测试在GitHub托管的代码。

1. 使用 `github` 授权登录 [Travis CI](https://travis-ci.org/)

2. 在 `Travis CI` 中打开博客项目仓库的开关。并在配置中打开`Build only if .travis.yml is present`选项

3. 在 `github` 中创建`access token`，详细教程：[Creating a personal access token for the command line](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)

4. 在`travis`博客仓库的配置中将刚刚生成的`token`添加到`Environment Variables`中，name为`REPO_TOKEN`

![travis_setting](/images/travis_setting.png)

5. 在项目根目录新增`.travis.yml`文件。配置如下

```yml
language: node_js
node_js: stable
branches:
  only:
  - master
cache:
  directories:
  - node_modules
before_install:
- git config --global user.name "sjfkai"
- git config --global user.email "sjfkai@163.com"
- npm install -g hexo-cli
- export HEXO_DEPLOYER_REPO=https://$REPO_TOKEN@github.com/sjfkai/blog.git
install:
- npm i
script:
- hexo clean
- hexo generate
- hexo deploy
```

6. 修改hexo的配置文件`_config.yml`的`deploy`:

```yml
# 注意，这里注释掉了repo, 因为我们需要在ci中通过环境变量 HEXO_DEPLOYER_REPO 配置
deploy:
  type: git
  # repo:
  branch: gh-pages
```

7. `git add .`

8. `git commit -m "add travis ci"`

9. `git push origin master`

这时候你会发现`travis ci`显示该项目处于`running`状态。 等最后变为 `passed` 状态后。`github pages`就已经自动部署成功了。

## 博主源码

[https://github.com/sjfkai/blog](https://github.com/sjfkai/blog)

