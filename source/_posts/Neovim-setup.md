---
title: Neovim setup
tags: [vim,折腾,分享]
date: 2023-09-13 08:00:00
updated:
categories: 记录
keywords: neovim,折腾,lua
description: '提供一个可用的 neovim 配置'
top_img: '/image/neovim.png'
comments:
cover: '/image/neovim.png'
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
mathjax:
katex:
aplayer:
highlight_shrink:
aside:
ai: true
swiper_index: 5
top_group_index: 5
background: "#fff"
---

# 记录自己配好的neovim

今天花了一下午的时间把neovim改造了一下🥵，还记得去年今天实习的时候还不小心当这一群博士的面问出了经典的_vim怎么退出_这个经典问题🫣。

本文将会介绍一下我的neovim配置，文件会放在[仓库](https://github.com/Joviisaus/my.neovim.git)里，还有一些start up插件的自定义主题未公开。过几天看看怎么发布比较好。

* * *

# why neovim

为什么会选择neovim，或者更简单一点，为什么会使用vim，这种看起来不属于这个时代的东西呢。

很多人给出的理由可能是”双手可以不用离开键盘“，“什么都可以自定义”，“用起来跟手”，“相比起笨重的IDE资源占用更少”

但对于我而言，肯使用neovim最主要的一个原因应该就是一个:**帅** --这是一辈子的事情🐶。

可能这就是一些低成本的快乐，就像拼拼图，上海拉鲁神庙解密，甚至是做数独，这些看起来又累又费脑的东西，似乎根本和快乐沾不上边的东西，往往是最容易让人忘记时间的。

也许探索未知,创造无限可能,就是人类多巴胺分泌的G点吧。

* * *

# 详细配置

大部分的配置都是按照b站的[技术蛋老师](https://github.com/eggtoopain)配置的，这里主要针对自定义部分详细介绍,此外本人使用Macos Ventura系统，会对一些插件不适配的问题解决做出一些记录。

## dashboard

这里没有使用最热门的 _dashboard_ 插件，而是使用相对冷门的 _start up_ ,这涉及到一些可能是适配的问题，由于插件由 _packer_ 管理，而 _dash board_ 的插件默认安装在opt目录下， _neovim_ 启动时只会搜索同级下的 _start_ 目录，虽然可以手动移动，但由于packer的机制，每次对_plugin.lua_文件修改时都会更新这两个文件夹。

![](https://github.com/Joviisaus/my.neovim/blob/main/docs/zelda.png?raw=true)
![](https://github.com/Joviisaus/my.neovim/blob/main/docs/dashboard.png?raw=true)
![](https://github.com/Joviisaus/my.neovim/blob/main/docs/ikun.png?raw=true)

## Glow

这是一个 markdown 预览插件，可能还是由于系统不适配的原因，启动后没有编译出二进制文件😭，解决方案是通过 _homebrew_ 安装后直接定向到 _bomebrew_ 的路径下,每次在 _normal_ 下输入 _:Glow_ 即可预览当前buffer的md文件。额外一说：这个插件不仅对markdown本身进行 preview ，他似乎是可以将文件里的html代码渲染回markdown文件的样式。

## Telescope

经典的文件检索插件，这里额外配置了一个 _telescope-media\_files_ 的插件，有一点需要注意的是 Macos 默认没有fd命令，使用 homebrew 额外安装即可，如果是 ubuntu 的话可能就得改配置文件了。
