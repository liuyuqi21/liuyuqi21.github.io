---
title: 用VS code写C/C++
date: 2017-07-25 17:23:12
tags: C++
categories: 工具
---
## 废话

嗯，我的第一篇博客，由于我实在是太菜了，导致现在还没有一篇博客。。虽然这篇也没有什么营养（；´д｀）ゞ

刚开始写代码的时候过sublime这样的编辑器，确实很不错，后来有人强力推荐PhpStorm，我就发现IDE实在太好用了，不解释(●ˇ∀ˇ●)。前段时间发现很多人推荐VS code，而且有天我找了一个很靠谱的sublime教程，结果他开头就。。
[![img](https://liuyuqi21.github.io/images/2.png)](https://liuyuqi21.github.io/images/2.png)
于是我下了一个，就慢慢变成变成它的脑残粉了，关于它的诸多好处，网上说了很多,我也在慢慢学会使用。

## 编译器下载

因为C/C++运行需要编译器，所以要去下载。
MinGW下载地址在这里
https://sourceforge.net/projects/mingw/files
安装完以后弹出界面
[![img](http://upload-images.jianshu.io/upload_images/2419083-3b225e8185a546ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://upload-images.jianshu.io/upload_images/2419083-3b225e8185a546ac.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)
把这些选中之后按左上角Installationt->Apply Changes进行安装。

## 配置环境变量

之后还要配置环境变量，右键此电脑点击属性->高级系统设置->环境变量
[![img](https://liuyuqi21.github.io/images/1.png)](https://liuyuqi21.github.io/images/1.png)点击红色的那一项编辑，然后新建，把下载好的编译器的路径填上去，我的是C:\MinGW\bin，不用加gdb.exe。然后win+r打开命令行，输入gcc -v查看是否有效。注意，成功以后还要重启一遍计算机，因为虽然它在命令行里有用但是在其他地方仍然没有生效。

## 安装插件

CtrlL+Shift+S打开扩展，安装C/C++和Code Runner这两个插件。
不安装Code Runner也是可以的，当时我上网查了很多配置的方法，下面这个不错。
https://www.zhihu.com/question/30315894/answer/154979413
但是折腾了很久也没有配置成功T_T，好在我发现了Code Runner 这个插件，真的超级好用啊，他可以运行多种语言，比如php也是可以的，亲测有效。这个插件不需要什么配置，只要按右上角的运行图标就可以。但是直接在这下面输出可能会乱码，所以点击文件->首选项->设置，找到”code-runner.runInTerminal”将它设为true，这样就是在终端运行，直接在终端运行更方便。运行的键盘快捷方式有点复杂，可以自己更改，在文件->首选项->键盘快捷方式里搜索Run Code。

## 其他插件

VS code用来写Markdown也很方便，可以边写边在右侧试图看到效果。
Markdown PDF 这个插件可以将它转化为pdf格式，在编辑器里右键可以看到选项，点击后转化为pdf格式保存到同一文件夹下

markdownlint可以帮助查看语法有没有错误，并且给出提示