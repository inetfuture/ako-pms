# Node.js的包管理系统

## 简介

Node.js是于2009年由 *Ryan Dahl* 发起的开源项目，是一个基于 *V8* 引擎的服务端JavaScript编程平台。该平台短短几年之内获得了巨大的成功，与它完善的包管理系统有密不可分的关系。

Node.js某种程度上属于无心之作，它的发起者一开始没有料想到会如此成功，所以它的包管理系统在一开始并非由官方提供，而是出现了多个第三方的方案。当然，多套方案并不利于整个社区的发展，所以优胜劣汰，现在只剩下一个 *npm（Node.js package manager）* ，而且从0.6.3版本开始，`npm`命令已经集成到Node.js的安装包中。其仓库地址是[npmjs.org]。

## 规范

npm规定所有使用它的项目应在根目录下建立一个 *package.json* 文件，使用`npm`的一些命令会依赖于这个配置文件。当然，这不是强制性的，如果你的项目没有依赖任何第三方的npm包，也不打算把你的项目发布到[npmjs.org]平台上，那你就用不着`npm`，也就不需要package.json文件。

很明显，这个配置文件是json格式的，比较常用的key有如下这些：

```javascript
{
  "name": "packagename",
  "version": "1.0.0",
  "dependencies": {
    "async": "~0.1.22"
  }
}
```
说明：
* 只有 *name* 和 *version* 是必需的，而且他们两个的组合应该是唯一的，如果你打算把这个包发布出去的话。如果你想重新发布一个经过修改的包，要记得修改 *version* 。
* *name* 不能使用中文，而且如果发布出去的话，他会出现在URL中，比如`https://npmjs.org/package/packagename`，所以不能使用非URL安全的字符。另外，当别人在使用这个包的时候，会通过 `require('packageName')`来加载，所以太长也不好。
* *version* 是有一定的格式要求的，一般都是采用 *major.minor.patch* 的格式，其中 *minor* 用偶数表示稳定版，用奇数表示开发版。
* 除了上面两个必需的key之外，最常用的应该是`dependencies`，它用来声明当前项目的依赖。其格式很简单，是一个对象，key是依赖的包名，value是对依赖的版本要求。

更详细的可以参考[这里](https://npmjs.org/doc/json.html)。该文件可以手动填写，也可以通过`npm init`以命令行交互的形式创建一个简略版本。

## 工具

### 安装

`npm`命令已集成到Node.js的安装包中，直接安装Node.js就好。

### 发布包

发布之前需要现在[npmjs.org]上注册一个账户，可以通过`npm adduser`注册，也可以直接到网站上注册。发布目前只能通过命令行来进行，先登陆，`npm login`，会提示输入用户名、密码和邮箱。登陆之后就可以`npm publish`了。发布之后，可以通过`npm show`查看包信息，包括已发布了哪些版本。

可能出现的问题：

- 包名不符合要求，比如包名必须全部小写，不能包含 *``* 等特殊字符，根据错误提示做出相应更改即可。
- 包名+版本已存在，这个不光是说跟自己已发布的包冲突，也有可能是跟别人的重了，所以定义包名的时候，一般先要看一下这个名字是否已被占用（在[npmjs.org]上搜索，或者，`npm search packagename`，注意这个命令第一次会很慢，因为它要在本地建立一个索引副本）。如果是自己的包，可以更新一下版本号，或者，将之前的`npm unpublish`。注意有多个版本的话，`https://npmjs.org/package/packagename`上只会显示版本最高的那个。

### 安装包

安装包主要通过`npm install`来实现，常用的有如下几种形式：

- `npm install packagename [--save]` 在当前项目安装指定的包，包文件会被放到 **项目根目录** 下的 **node_modules** 文件夹中。项目根目录的判定方式为：在当前目录及父目录中寻找package.json或node_modueles文件夹，第一个存在该文件（夹）的目录作为项目根目录，如果都找不到，将当前目录作为根目录，为了避免不可预期的行为，建议每开始一个新项目，都用`npm init`初始化一个package.json文件。注意node_modules目录一般不应该使用版本控制工具进行追踪，比如使用git的话，要把它加到.gitignore文件。如果带`--save`参数的话，`npm`会自动更新package.json中dependencies项，否则不会更新。如果你要把这个包作为一个依赖的话，应该带上这个参数，如果是忘了带，可以手动更新package.json，或者重新执行一下该参数的install命令即可（第二次会很快，因为`npm`对包文件有缓存，如果该包不需要编译的话）。
- `npm install packagename@version` 安装指定版本的包。
- `npm install packagename -g` 带`-g`表示安装到全局环境中，在我的Ubuntu中，是被安装到了`/usr/local/lib/node_modules/`目录中，全局安装一般用于带有命令行工具的包，安装后相应的可执行文件会被加到`PATH`中，这样可以方便使用，比如全局安装[express](https://npmjs.org/package/express)后，创建一个新的express项目，可以`express create expressproject`。值得注意的是， **全局安装并不表示其它项目再使用该包的时候就不需要安装了，恰恰相反，每一个项目都应该单独安装自己的依赖，全局安装仅仅适用于提供命令行工具的包** 。每个项目独立管理自己的依赖，可以防止出现依赖冲突的情况，比如A项目依赖包P的低版本，B项目却依赖包P的高版本时，这种方式就显的很灵活，这也是我觉得node.js平台比python平台友好的地方（当然python也有virtualenv等环境管理方案，只是没有node.js这么好用、自然）。
- `npm install packagename` 前面说过包文件的安装目录node_modules不应该被添加到版本控制工具中，那么当我从git clone出一个项目的时候，该如何安装依赖呢？这个时候之前手动或通过`npm install --save`参数放到package.json的dependencies中的依赖信息就起作用了，直接在项目目录中`npm install`，`npm`会自动检查相关依赖信息，然后进行安装，很方便！
- `npm install folder|tarball file|url` 前面的都是通过包名从[npmjs.org]安装，除此之外，也可以通过指定包所在的文件夹或.tar文件的路径/URL来安装。没有网络的话就可已先从别的地方下载下来直接拷贝到本地安装。

### 卸载包

`npm remvoe packagename [--save]` `--save`参数的含义跟安装包时一样。

### 更新包

`npm update [packagename]` 更新或安装package.json中列出的全部依赖，或者也可以指定其中的某几个包。未列在package.json中的包不会被更新，也就说更新是以package.json为准，而不是以node_modules文件夹中已安装的包。

### 其它常用命令 

- `npm help [command]` 显示总的帮助信息或某个具体命令的帮助信息。
- `npm link` 这个命令可以方便的在包的开发过程中就行测试。使用方法：先在正在开发的包，比如mypackage，的目录中执行`npm link`，这样会在全局生成一个当前包的 *链接* ，然后在用来测试这个包的项目中执行`npm link mypackage`，如此就把这个包 *链* 到了当前的测试项目，在mypackage当中的任何更改，都可以直接反应到该测试项目中。

### 关于代理

`npm`会自动使用 *http_proxy/https_proxy* 环境变量，所以需要代理的话直接设置这些变量就好，比如：`export http_proxy=http://host:port; export https_proxy=http://host:port`

[npmjs.org]: https://npmjs.org "npmjs.org"