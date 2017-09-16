---
layout: post
title: "在Github Pages创建博客"
date:   2015-10-09 14:57:20 +0000
category: 教程
---
# Github Pages本地环境设置

## 注册GitHub帐号

去[GitHub](https://github.com)网站注册一个帐号，
然后在帐号设置中添加本机的ssh公钥，用于推送博客到GitHub服务器。

接下来需要在自己的GitHub帐号中创建名为 username.github.io的仓库，
后面的博客地址即为 <http://username.github.io>
如果不想用这个地址，也可以注册一个域名然后绑定到GitHub帐号。

## 安装本地预览环境

安装ruby环境：

```bash
sudo apt-get install ruby-dev
sudo gem sources -a https://ruby.taobao.org/ -r https://rubygems.org/
sudo gem install bundler
```

可使用rvm脚本管理ruby版本：

```bash
curl -L https://get.rvm.io > rvm-installer.sh
gpg --keyserver hkp://keys.gnupg.net:80 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
sh rvm-installer stable
rvm install 2.4
rvm create default ruby-2.4.1
source .rvm/scripts/rvm
gem install bundler
```

使用bundler安装github-pages依赖环境，会一并安装好Jekyll：

```bash
echo "source 'https://ruby.taobao.org/'" >> Gemfile
echo "gem 'github-pages', group: :jekyll_plugins" >> Gemfile
bundler install
```

## 克隆博客

```bash
git clone git@github.com:username/username.github.io.git
cd username.github.io
jekyll new . --force
```

## 4. 本地预览博客

完成之后，启动jekyll本地web服务器

```bash
jekyll b
jekyll serve -w -I
```

使用浏览器打开地址 <http://localhost:4000> 即可预览博客内容。

下载下来的博客使用了默认账户信息，因此需要修改根目录下的 _config.yml 这个配置文件。

## 5. 撰写博文

在 *_posts* 目录下创建文件名为 2016-mon-day-title.md 的文件，
重新编译即可发布文章

- [ ] 添加rakefile示例

使用rakefile创建文章

```bash
rake post title="new post"
```

## 6. 发表博文

写好博文之后，将文章提交并推送到GitHub服务器，几分钟之后访问自己的博客地址，应该可以看到内容

```bash
git add .
git commit
git push orignal master
```

参考文献：

1. [Jekyll-Bootstrap 文档](http://jekyllbootstrap.com)
2. [Jekyll 文档](http://jekyllcn.com)
3. [Markdown 语法](http://wowubuntu.com/markdown)
