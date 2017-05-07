title: hexo所使用的模块
categories:
  - hexo
---

hexo中中模块和文件一一对应,这个文件可能是JavaScript代码、JSON或者编译过的C/C++扩展。
<!-- more -->
#hexo使用到的node.js原生模块(核心模块)
>核心模块部分在node源码的编译过程中,编译进了二进制执行文件,在Node进程启动时,部分核心模块就被直接加载进内存中,所以这部分核心模块引入时,文件定位和编译执行这两个步骤可以省略,并且在路径分析中优先判断,所以它的**加载速度是最快的**.

##1. events
Hexo 继承了 EventEmitter，您可以用 on 方法监听 Hexo 所发布的事件，也可以使用 emit 方法对 Hexo 发布事件.EventEmitter 类由 events 模块定义和开放的.
大多数 Node.js 核心 API 都是采用惯用的异步事件驱动架构，其中某些类型的对象（称为触发器）会周期性地触发命名事件来调用函数对象（监听器）。
例如，一个 net.Server 对象会在每次有新连接时触发一个事件；一个 fs.ReadStream 会在文件被打开时触发一个事件；一个 stream 会在数据可读时触发事件。
所有能触发事件的对象都是 EventEmitter 类的实例。 这些对象开放了一个 eventEmitter.on() 函数，允许将一个或多个函数附加到会被对象触发的命名事件上。 事件名称通常是驼峰式的字符串，但也可以使用任何有效的 JavaScript 属性名。
当 EventEmitter 对象触发一个事件时，所有附加在特定事件上的函数都被同步地调用。 被调用的监听器返回的值都会被忽略并丢弃。
##2. http
要使用 HTTP 服务器与客户端，需要require('http')。
Node.js 中的 HTTP 接口被设计成支持协议的多种特性，这些特性以往都较难使用。 比如，大块编码的消息。 这些接口从不缓存整个请求或响应，用户能够以流的形式处理数据。
##3. util 
模块主要用于支持 Node.js 内部 API 的需求。 大部分实用工具也可用于应用程序与模块开发者。
##4.module 
Node.js 有一个简单的模块加载系统。 在 Node.js 中，文件和模块是一一对应的（每个文件被视为一个独立的模块）。
##5. vm (虚拟机)

vm 模块提供了一系列 API 用于在 V8 虚拟机环境中编译和运行代码。 它可以通过以下方式使用

---
#hexo 使用的文件模块
>其中如hexo-fs其实是Promise化的Node.js自带模块中的fs的api.NodeJS的Promise的用法,Javascript的特点是异步，Javascript不能等待，如果你实现某件需要等待的事情，你不能停在那里一直等待结果回来，相反，底线是使用回调callback：你定义一个函数，这个函数只有等到结果可用时才能被调用。
>这种回调模型对于好的代码组织是没有问题的，但是也可以通过从原始回调切换到promise解决很多问题，将promise看成是一个标准的数据容器，这样会简化你的代码组织，可以成为基于promise的架构。
##hexo-fs
和原生模块fs类似,被promise化.
##warehouse
Hexo引入了json数据库warehouse，也是作者自己开发的一个数据库驱动，API用法与Mongoose相差无几，在架构中的角色是充当一个中介者，存储临时数据，或者持久化数据存储，如博客的发表时间等，还可以作为缓存层，比对文件的修改时间，跳过无修改文件的编译过程，减少二次编译的时间
##box
是 Hexo 用来处理特定文件夹中的文件的容器，在 Hexo 中有两个 Box，分别是 hexo.source 和 hexo.theme，前者用于处理 source 文件夹，而后者用于处理主题文件夹。
##bluebird
 bluebird号称速度是所有 Promise 库里最快的
node.js被称为回调地狱,多个回调形成了嵌套回调，或者称为回调陷阱。使用bluebird便是简化代码组织.
##minimist
minimist是命令行处理的组件，比如下面这个命令：
$ init blog --verbose --cwd /usr/local/
会处理成：
{ _: [ 'init', 'blog' ], verbose: true, cwd: '/usr/local/' }
以–开头的参数，会处理成key/value；其他的参数会以数组的形式放到里。后续可以很容易地从中按顺序取出参数，或者判断某参数是否存在
##abbrev
abbrev也是个命令行处理组件：
var commands = ["generate", "init", "help"];
var shorthands = abbrev(commands);
会得到：
{ g: 'generate',
  ge: 'generate',
  gen: 'generate',
  gene: 'generate',
  gener: 'generate',
  genera: 'generate',
  generat: 'generate',
  generate: 'generate',
  h: 'help',
  he: 'help',
  hel: 'help',
  help: 'help',
  i: 'init',
  in: 'init',
  ini: 'init',
  init: 'init' }
可以方便用户输入命令
##tildify
tildify可以把用户的目录处理成~
var path = "/Users/apple/git_local/";
var short = tildify(path);// ~/git_local/
##chalk
chalk可以给stdout增加文字特效，比如改变文字颜色，增加下划线等

---
hexo目录下的package.json中列出了基本的第三方模块,在执行npm install的时候,会联网安装.如果代码较复杂，在 node_modules 文件夹中建立文件夹，文件夹名称开头必须为 hexo-，如此一来 Hexo 才会在启动时载入否则Hexo将会忽略它。
文件夹内至少要包含 2 个文件：一个是主程序，另一个是 package.json，描述插件的用途和所依赖的插件。
```
.
├── index.js
└── package.json
```
package.json 中至少要包含 name, version, main 属性，例如：
```
package.json
{
  "name": "hexo-my-plugin",
  "version": "0.0.1",
  "main": "index"
}
```

```
"dependencies": {
    "abbrev": "^1.0.7",
    "archy": "^1.0.0",
    "bluebird": "^3.4.0",
    "chalk": "^1.1.3",
    "cheerio": "^0.20.0",
    "deep-assign": "^2.0.0",
    "hexo-cli": "^1.0.2",
    "hexo-front-matter": "^0.2.2",
    "hexo-fs": "^0.1.5",
    "hexo-i18n": "^0.2.1",
    "hexo-log": "^0.1.2",
    "hexo-util": "^0.6.0",
    "js-yaml": "^3.6.1",
    "lodash": "^4.13.1",
    "minimatch": "^3.0.0",
    "moment": "~2.13.0",
    "moment-timezone": "^0.5.4",
    "nunjucks": "^2.4.2",
    "pretty-hrtime": "^1.0.2",
    "strip-indent": "^1.0.1",
    "swig": "1.4.2",
    "swig-extras": "0.0.1",
    "text-table": "^0.2.0",
    "tildify": "^1.2.0",
    "titlecase": "^1.1.2",
    "warehouse": "^2.2.0"
  },
  "devDependencies": {
    "chai": "^3.5.0",
    "chai-as-promised": "^5.3.0",
    "eslint": "^2.12.0",
    "eslint-config-hexo": "^1.0.4",
    "babel-eslint": "^7.2.1",
    "hexo-renderer-marked": "^0.2.10",
    "istanbul": "^0.4.3",
    "jscs": "^3.0.4",
    "jscs-preset-hexo": "^1.0.1",
    "mocha": "^2.5.3",
    "rewire": "^2.5.1",
    "sinon": "^1.17.4"
  }
```
#hexo常用指令对应的模块
hexo init //加载 'hexo'模块,加载插件和配置文件.
```js
var Hexo = require('hexo');
var hexo = new Hexo(process.cwd(), {});

hexo.init().then(function(){
  // ...
});
```
hexo generate//Hexo编译模块首先往Hexo扩展对象Console中注册generate函数
```js
console.register('generate', 'Generate static files.', {
  options: [
    {name: '-d, --deploy', desc: 'Deploy after generated'},
    {name: '-f, --force', desc: 'Force regenerate'},
    {name: '-w, --watch', desc: 'Watch file changes'}
  ]
}, require('./generate'));
```
generate函数用于生成目标文件夹，从Hexo的路由模块中取得所有需要生成目标文件的路径，调用fs输出文件，在此之前，首先得对源文件进行预处理，把路径写入路由。由于Hexo本身设计的特点，源文件又分为内容和主题两部分，分别存放在source和theme文件夹中，所以得调用process函数分别对它们进行预处理。