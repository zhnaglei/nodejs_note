# nodejs笔记
## Node.js不是JS应用、而是JS运行平台  ##
Node.js 采用C++语言编写而成， 是一个 Javascript 的运行环境。

Node.js 采用了 Google Chrome 浏览器的 V8 引擎， 性能很好， 同时还提供了很多系统级的 API，
如文件操作、网络编程等。浏览器端的 Javascript 代码在运行时会受到各种安全性的限制，对客
户系统的操作有限。相比之下，Node.js 则是一个全面的后台运行时，为 Javascript 提供了其他
语言能够实现的许多功能。
## Node.js采用事件驱动、异步编程，为网络服务而设计  ##
Node.js 的网络模块特别多， 包括 HTTP、 DNS、
NET、UDP、HTTPS、TLS 等，开发人员可以在此基础上快速构建 Web 服务器。以简单的
helloworld.js 为例： 

    var http = require('http'); 
    http.createServer(function (req, res) { 
    res.writeHead(200, {'Content-Type': 'text/plain'}); 
    res.end('Hello World\n'); 
    }).listen(80, "127.0.0.1"); 
## Node.js的特点  ##
1. 事件驱动
1. 非阻塞I/O
1. 单线程


## Node.js应用案例 ##
善于I/O，不善于计算。因为Node.js最擅长的就是任务调度，如果你的业务有很多的CPU计算，实际上也相当于这个计算阻塞了这个单线程，就不适合Node开发。

当应用程序需要处理大量并发的I/O，而在向客户端发出响应之前，应用程序内部并不需要进行非常复杂的处理的时候，Node.js非常适合。Node.js也非常适合与web socket配合，开发长连接的实时交互应用程序。
比如：

- 用户表单收集
- 考试系统
- 聊天室
- 图文直播
- 提供JSON的API（为前台Angular使用）


## Node基础 ##
### 1.node的安装 ###
NodeJS提供了一些安装程序，都可以在nodejs.org这里下载并安装。
Windows系统下，选择和系统版本匹配的.msi后缀的安装文件。Mac OS X系统
### 2.运行node ###
打开终端，键入node进入命令交互模式，可以输入一条代码语句后立即执行
并显示结果，例如：

    $ node
    console.log('Hello World!');
    Hello World!

### 3.模块 ###
编写稍大一点的程序时一般都会将代码模块化。在NodeJS中，一般将代码合理拆分到不同的JS文件中，每一个文件就是一个模块，而文件路径就是模块名。
在编写每个模块时，都有require、exports、module三个预先定义好的变量可供使用。

**require**

require函数用于在当前模块中加载和使用别的模块，传入一个模块名，返回一个模块导出对象。模块名可使用相对路径（以./开头），或者是绝对路径（以/或C:之类的盘符开头）。另外，模块名中的.js扩展名可以省略。以下是一个例子。

    var foo1 = require('./foo');
    var foo2 = require('./foo.js');
    var foo3 = require('/home/user/foo');
    var foo4 = require('/home/user/foo.js');

// foo1至foo4中保存的是同一个模块的导出对象。

另外，可以使用以下方式加载和使用一个JSON文件。

    var data = require('./data.json');

**exports**

exports对象是当前模块的导出对象，用于导出模块公有方法和属性。别的模块通过require函数使用当前模块时得到的就是当前模块的exports对象。
以下例子中导出了一个公有方法。
    
    exports.hello = function () {
    	console.log('Hello World!');
    };

**module**

通过module对象可以访问到当前模块的一些相关信息，但最多的用途是替换当前模块的导出对象。例如模块导出对象默认是一个普通对象，如果想改成一个函数的话，可以使用以下方式。

    module.exports = function () {
    	console.log('Hello World!');
    };

以上代码中，模块默认导出对象被替换为一个函数。

**模块初始化**

一个模块中的JS代码仅在模块第一次被使用时执行一次，并在执行过程中初始化模块的导出对象。之后，缓存起来的导出对象被重复利用。

**主模块**

通过命令行参数传递给NodeJS以启动程序的模块被称为主模块。主模块负责调度组成整个程序的其它模块完成工作。例如通过以下命令启动程序时，main.js就是主模块。

    $ node main.js

完整示例
例如有以下目录。

    - /home/user/hello/
    - util/
    	counter.js
    	main.js

其中counter.js内容如下：

    var i = 0;
    	function count() {
       	 	return ++i;
    }
    
    exports.count = count;

该模块内部定义了一个私有变量i，并在exports对象导出了一个公有方法count。
主模块main.js内容如下：

    var counter1 = require('./util/counter');
    varcounter2 = require('./util/counter');
    console.log(counter1.count());
    console.log(counter2.count());
    console.log(counter2.count());

运行该程序的结果如下：

    $ node main.js
    1
    2
    3

可以看到，counter.js并没有因为被require了两次而初始化两次。

### 4.二进制模块 ###

虽然一般我们使用JS编写模块，但NodeJS也支持使用C/C++编写二进制模块。编译好的二进制模块除了文件扩展名是.node外，和JS模块的使用方式相同。
虽然二进制模块能使用操作系统提供的所有功能，拥有无限的潜能，但对于前端同学而言编写过于困难，并且难以跨平台使用，因此不在本教程的覆盖范围内。
## 代码的组织和部署 ##
### 1.模块路径解析规则 ###
我们已经知道，require函数支持斜杠（/）或盘符（C:）开头的绝对路径，也支持./开头的相对路径。但这两种路径在模块之间建立了强耦合关系，一旦某个模块文件的存放位置需要变更，使用该模块的其它模块的代码也需要跟着调整，变得牵一发动全身。因此，require函数支持第三种形式的路径，写法类似于foo/bar，并依次按照以下规则解析路径，直到找到模块位置。
#### 1. 内置模块 ####
如果传递给require函数的是NodeJS内置模块名称，不做路径解析，直接返回内部模块的导出对象，例如require('fs')。
#### 2. node_modules目录 ####
NodeJS定义了一个特殊的node_modules目录用于存放模块。例如某个模块的绝对路径是/home/user/hello.js，在该模块中使用require('foo/bar')方式加载模块时，则NodeJS依次尝试使用以下路径。

     /home/user/node_modules/foo/bar
     /home/node_modules/foo/bar
     /node_modules/foo/bar

#### 3. NODE_PATH环境变量 ####
与PATH环境变量类似，NodeJS允许通过NODE_PATH环境变量来指定额外的模块搜索路径。NODE_PATH环境变量中包含一到多个目录路径，路径之间在Linux下使用:分隔，在Windows下使用;分隔。例如定义了以下NODE_PATH环境变量：

     NODE_PATH=/home/user/lib:/home/lib

当使用require('foo/bar')的方式加载模块时，则NodeJS依次尝试以下路径。
    
     /home/user/lib/foo/bar
     /home/lib/foo/bar

### 2.包（package） ###
我们已经知道了JS模块的基本单位是单个JS文件，但复杂些的模块往往由多个子模块组成。为了便于管理和使用，我们可以把由多个子模块组成的大模块称做包，并把所有子模块放在同一个目录里。
在组成一个包的所有子模块中，需要有一个入口模块，入口模块的导出对象被作为包的导出对象。例如有以下目录结构。

    - /home/user/lib/
    - cat/
    head.js
    body.js
    main.js

其中cat目录定义了一个包，其中包含了3个子模块。main.js作为入口模块，其内容如下：
    
    var head = require('./head');
    var body = require('./body');
    exports.create = function (name) {
	    return {
		    name: name,
		    head: head.create(),
		    body: body.create()
	    };
    };

在其它模块里使用包的时候，需要加载包的入口模块。接着上例，使用require('/home/user/lib/cat/main')能达到目的，但是入口模块名称出现在路径里看上去不是个好主意。因此我们需要做点额外的工作，让包使
用起来更像是单个模块。

**index.js**
当模块的文件名是index.js，加载模块时可以使用模块所在目录的路径代替模块文件路径，因此接着上例，以下两条语句等价。

    var cat = require('/home/user/lib/cat');
    var cat = require('/home/user/lib/cat/index');

这样处理后，就只需要把包目录路径传递给require函数，感觉上整个目录被当作单个模块使用，更有整体感。

**package.json**
如果想自定义入口模块的文件名和存放位置，就需要在包目录下包含一个package.json文件，并在其中指定入口模块的路径。上例中的cat模块可
以重构如下。

    - /home/user/lib/
	    - cat/
	    + doc/
	    - lib/
		    head.js
		    body.js
		    main.js
	    + tests/
	    package.json

其中package.json内容如下。

    {
    "name": "cat",
    "main": "./lib/main.js"
    }

如此一来，就同样可以使用require('/home/user/lib/cat')的方式加载模块。NodeJS会根据包目录下的package.json找到入口模块所在位置。
### 3.命令行程序 ###
使用NodeJS编写的东西，要么是一个包，要么是一个命令行程序，而前者最终也会用于开发后者。因此我们在部署代码时需要一些技巧，让用户觉得自己是在使用一个命令行程序。
例如我们用NodeJS写了个程序，可以把命令行参数原样打印出来。该程序很简单，在主模块内实现了所有功能。并且写好后，我们把该程序部署在/home/user/bin/node-echo.js这个位置。为了在任何目录下都能运行该程序，我们需要使用以下终端命令。

    $ node /home/user/bin/node-echo.js Hello World
    Hello World

这种使用方式看起来不怎么像是一个命令行程序，下边的才是我们期望的方式。

    $ node-echo Hello World
    Linux

### 4.工程目录 ###
了解了以上知识后，现在我们可以来完整地规划一个工程目录了。以编写一个命令行程序为例，一般我们会同时提供命令行模式和API模式两种使用方式，并且我们会借助三方包来编写代码。除了代码外，一个完整的程序也应该有自己的文档和测试用例。因此，一个标准的工程目录都看起来像下边这样。
    - /home/us
    - er/workspace/node-echo/   # 工程目录
    - bin/  						# 存放命令行相关代码
    node-echo
    + doc/ 					    # 存放文档
    - lib/  						# 存放API相关代码
    echo.js
    - node_modules/ 				# 存放三方包
    + argv/
    + tests	/					# 存放测试用例
    package.json				# 元数据文件
    README.md   				# 说明文件

其中部分文件内容如下：
    
    /* bin/node-echo */
    var argv = require('argv'),
    echo = require('../lib/echo');
    console.log(echo(argv.join(' ')));
    /* lib/echo.js */
    module.exports = function (message) {
    return message;
    };
    /* package.json */
    {
    "name": "node-echo",
    "main": "./lib/echo.js"
    }

以上例子中分类存放了不同类型的文件，并通过node_moudles目录直接使用三方包名加载模块。此外，定义了package.json之后，node-echo目录也
可被当作一个包来使用。

### 5.NPM ###
NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题，常见的使用场景有以下几种：
1. 允许用户从NPM服务器下载别人编写的三方包到本地使用。
1. 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
1. 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。
