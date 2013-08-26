# 简介

[Ubuntu](http://www.ubuntu.com/)(oo-boon-too)的包管理系统其实是传承自[Debian](http://www.debian.org/)(de-bi-an)的，但是我还没玩过Debian，所以这里只讲Ubuntu。

关于包管理的Ubuntu官方文档：https://help.ubuntu.com/12.04/serverguide/package-management.html

按照上面的文档所描述的，用于包管理的命令行工具主要有三种：

- `dpkg` 这个是最基础的，后面两个工具都构建在它之上，它其实是一系列工具，常用的还有`dpkg-deb`、`dpkg-reconfigure`，各自实现某一块的功能。这一系列工具提供的功能都是比较底层和细粒度的，对用户不太有好，所以有了后面较为 *高级* 的`apt`和`aptitude`，相当于是对`dpkg`的抽象和封装，提供更为便捷和友好的使用方式。
- `apt` Advanced Packaging Tool，也是一系列工具，常用的有`apt-get`、`apt-cache`等。
- `aptitude` 这个更 *high-level* 一点: `dpkg`不能自动解决依赖关系，`apt-get`可以自动解决依赖但是无法处理依赖冲突的问题，这个时候`aptitude`就派上用场了，它可以分析冲突的依赖关系并给出解决方案。但是这个包默认是没有安装的，可以通过`apt-get install aptitude`安装，我用一个新系统一般会先装这个，免的哪天依赖关系坏掉啥都装不了。。。

`dpkg`可以看作包管理系统的后端，而`apt`是前端。

**什么是依赖冲突**

比如A依赖：`C >= 2.0`，B依赖： `C >= 1.0 < 2.0`，同时安装A和B的时候就会遇到依赖冲突，此时可以通过对A降级，或者强制安装的方式解决（即强制忽略对依赖C的版本要求）。所谓降级就是换一个低一点的版本，因为低版本的A可能对C的版本也相对较低。这种问题还是蛮复杂的，因为A和B同时也可能是其它包的依赖项，所以需要递归分析，`aptitude`就是干这个的。

# 包系统的工作原理

# 实用技巧

- 编译某个软件时提示缺少某个`.h`或`.so`文件

  如何google无果的话可以尝试用`apt-file search fileName`看一下有哪个包提供了这个文件，然后安装这个包即可。`apt-file`默认是没有安装的，用`apt-get`装上即可。

- 查看某个包都安装了哪些文件： `dpkg -L packageName`

  若果还没有安装，可以先把它下载下来：`apt-get download packageName`，然后`dpkg-deb -c packageName`。

- 查找某个文件是被哪个包安装的: `dpkg -S filePath`
- 使用`apt-get`安装某个包时提示依赖无法被安装，改用`aptitude install packgeName`，它会尝试提供一些解决方案，根据提示选择是否同意某个方案，如果不同意，它会继续寻找下一个方案，直到无可用的方案。一般需要对某个包降级（downgrade）来解决这个问题。


# 构建和发布

## 如何构建一个deb包

## 如何发布一个deb包

# 手动编译安装与deb包安装的比较

## apt-build
