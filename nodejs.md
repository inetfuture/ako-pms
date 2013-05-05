# Node.js的包管理系统

## 简介

Node.js是于2009年由 *Ryan Dahl* 发起的开源项目，是一个基于 *V8* 引擎的服务端JavaScript编程平台。该平台短短几年之内获得了巨大的成功，与它完善的包管理系统有密不可分的关系。

Node.js某种程度上属于无心制作，它的发起者一开始没有料想到会如此成功，所以它的包管理系统在一开始并非由官方提供，而是出现了多个第三方的方案。当然，多套方案并不利于整个社区的发展，所以优胜劣汰，现在只剩下一个 *npm（Node.js package manager）*，而且从0.6.3版本开始，`npm`命令已经集成到Node.js的安装包中。其相应的在线包管理平台是[npmjs.org](https://npmjs.org)。

## 规范

npm规定所有使用它的项目应在根目录下建立一个 *package.json*文件，使用`npm`的一些命令会依赖于这个配置文件。当然，这不是强制性的，如果你的项目没有依赖任何第三方的npm包，也不打算把你的项目发布到npmjs.org平台上，那你就用不着npm，也就不需要package.json文件。

很明显，这个配置文件是json格式的，比较常用的键有如下这些：

```javascript
{
  "name": "packageName",
  "version": "1.0.0",
  "dependencies": {
    "async": "~0.1.22"
  }
}
```
说明：
* 只有 *name* 和 *version* 是必需的，而且他们两个的组合应该是唯一的，如果你打算把这个包发布出去的话。如果你想重新发布一个经过修改的包，要记得修改 *version*。
* *name* 不能使用中文，而且如果发布出去的话，他会出现在URL中，比如https://npmjs.org/package/packageName，所以不能使用非URL安全的字符。另外，当别人在使用这个包的时候，会通过`require('packageName')`来加载，所以太长也不好。
* *version*是有一定的格式要求的，一般都是采用 *major.minor.patch* 的格式，其中 *minor*用偶数表示稳定版，用奇数表示开发版。
* 除了上面两个必需的键之外，最常用的应该是 `dependencies`，它用来声明当前项目的依赖。其格式很简单，是一个对象，键是依赖的包名，值是对依赖的版本要求。

更详细的可以参考[这里](https://npmjs.org/doc/json.html)。该文件可以手动填写，也可以通过`npm init`以命令行交互的形式创建一个简略版本。

## 工具

### 安装

`npm`命令已集成到Node.js的安装包中，直接安装Node.js就好。

### 常用命令

* `npm init`
*  `npm install [package --save]`
*  `npm update [package]`
*  `npm publish`

## 发布