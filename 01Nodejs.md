# Nodejs

## 了解Nodejs

Nodejs是2009由Ryan Dahl推出的运行在服务端的 JavaScript（类似于java,php,python,.net等服务端语言）, 基于Google的V8引擎（控制台），V8引擎执行Javascript的速度非常快，性能非常好。

### 版本

> 在命令窗口中输入 `node -v` 可以查看版本

- 0.x 完全不技术 ES6
- 4.x 部分支持 ES6 特性
- 5.x 部分支持ES6特性（比4.x多些），属于过渡产品，现在来说应该没有什么理由去用这个了
- 6.x 支持98%的 ES6 特性
- 8.x 支持 ES6 特性⭐⭐⭐
- 10.x 最新稳定版本

### 安装偶数版本与配置

1. [下载](https://nodejs.org/en/download/)安装文件 下载nodejs网站 `https://nodejs.org/en/` 偶数版本相对稳定
2. 下载完后进行安装，建议安装到默认路径（注意不要有中文路径）
3. 配置环境变量（安装时勾选）
4. 在命令窗口中输入 `node -v`，如果正常显示版本号则表示安装成功

## Nodejs的使用

http://nodejs.cn/api/  api使用文档

### 黑窗口=REPL(Read Eval Print Loop 交互式解释器)

> 类似于在浏览器的开发人员工具的控制台，一般用于在服务端执行一些简单的javascript语句
>
> 打开命令行，到达node.js安装的目录，键入node 回车

- 进入REPL：node并回车

- 退出：两次 Ctrl + C

  ​	可在这敲写node.js代码

  ​		如：node 回车

  ​			let url = require('url');

  ​			url.resolve('http://www.qq.com','/weixin')

### 执行js文件代码

REPL 只适用于一些简单的 Javascript 语法，对于稍复杂的程序，可以直接写到 js 文件当中，并执行以下命令

```
node hello.js
```

不同于前端js代码，没有浏览器安全级别的限制，提供很多系统级别的API（内置模块），如： - 文件的读写 - 进程的管理 - 网络通信 - ……

### NPM

**(Node Package Manager包管理)**

Node.js 的包管理器 npm，是全球最大的开源库生态系统，随着nodejs一起安装（`npm -v`查看安装版本）

### 模块化开发

为了让Nodejs的文件可以相互调用，Nodejs提供了一个简单的模块系统，一个文件即一个模块，采用同步的commonJS规范

#### 模块分类

> 模块系统是 Nodejs 最基本也是最常用的。一般情况模块可分为四类：

- 原生模块（Nodejs内置模块）
- 文件模块（json文件等）
- 第三方模块
- 自定义模块

#### 创建自定义模块

```
//hello.js

function hello(){
    return 'hello laoxie';
}

//对外暴露接口
module.exports = hello;
```

##### 导出模块

> 如果没有这句话，引入模块时 就会得到 undefined。

- module.exports【ES5语法】

  > 对外暴露单个接口，一个模块中只能有一个 module.exports，多个会被覆盖。

- exports【ES6语法】

  > 在一个模块中暴露多个接口

```
    //person.js
    function setName(){
        return 'laoxie'
    }

    function setAge(){
        return 18
    }

    // 对外暴露接口
    exports.setName = setName;
    exports.setAge = setAge;
```

##### 引入模块

**require()【ES5语法，引入这里没有介绍ES6语法import】**

> 引入模块，使用nodejs内置的require()方法

```
    //page.js

    //得到一个对象，包含暴露的setName,setAge方法
    let person = require('./person.js');

    // 既然是得到对象，也可以直接解构
    let {setName,setAge} = require('./person.js');
```

- require 方法中的文件查找策略

![](D:\mdpic\nodejs\模块加载过程.jpg)

#### 原生模块(内置模块)

> 原生模块不需要安装【不要要黑窗口npm i xxx】，直接引入使用
>
> 直接引用模块名字的，不是内置模块，就是第三方模块

##### 	http 模块

- ##### http 模块

  > 利用nodejs创建http服务器
  >
  > http模块下的方法createServer（）创建服务器
  >
  > 服务器
  >
  > 请求request
  >
  > ​	请求头
  >
  > ​	URL--请求路径
  >
  > ​	method--请求类型
  >
  > 响应response
  >
  > ​	响应头response.writeHead
  >
  > ​	响应类型
  >
  > ​		text/plain(默认，纯文本)
  >
  > ​		text/html(服务器返回html)
  >
  > ​		application/json(服务器返回json)
  >
  > 端口
  >
  > ​	配置监听端口listen（2**16=65536）

  ```
      //引入原生模块
      const http = require('http');
      const url = require('url');
  
      http.createServer(function(reqeust, response){
      	//设置响应头content-Type,用于告诉浏览器响应内容的类型
      	response.writeHead(200,{'Content-Type':'text/plain;charset=utf-8'});
      	
          // url.parse() 把url格式化为对象
          let params = url.parse(request.url,true).query;
          console.log('params:',params)
          
          // 路由
          switch(request.url){
              case '/home':
                  response.end('首页');
                  break;
              case '/list':
                  response.end('列表页');
                  break;
              case '/cart':
                  response.end('购物车');
                  break;
              default:
                  response.end(`你访问的页面 ${request.url} 不存在`);
          }
      	response.write('hello');
      	response.write('<p>hello laoxie</p>');
      	
      	//标记响应结束
          response.end('服务器启动成功，端口为8080');
          
      }).listen(8080);
  ```

  - 参数处理之GET请求

    > get请求的参数在url地址中，所以需要对url进行处理，需要用到url模块（详情请查看[url模块](http:///F://mydoc/kphone/h5/Section3/Outline3/week1_Nodejs/doc/link) 和 [querystring模块](http:///F://mydoc/kphone/h5/Section3/Outline3/week1_Nodejs/doc/link)）

    ```
        var http = require('http');
        var url = require('url');
    
        http.createServer(function(req, res){
            //请求地址通过req.url获取
            //获取url中?后的参数：query
            var params = url.parse(req.url, true).query;
            res.end(params);
    
        }).listen(3000);
    ```

  - 参数处理之POST请求

    > 不同于 GET 请求，POST 请求不能通过 url 进行获取，需要用到请求体（request body）的事件进行监听获取，需要用到querystring模块（[querystring模块](http:///F://mydoc/kphone/h5/Section3/Outline3/week1_Nodejs/doc/link)）

    ```
    var http = require('http');
        var querystring = require('querystring');
    
        http.createServer(function(req, res){
            // 定义了一个post变量，用于暂存请求体的信息
            var post = '';     
    
            // 通过req的data事件监听函数，每当接受到请求体的数据，就累加到post变量中
            req.on('data', function(chunk){    
                post += chunk;
            });
    
            // 在end事件触发后，通过querystring.parse将post解析为真正的POST请求格式，然后向客户端返回。
            req.on('end', function(){    
                post = querystring.parse(post);
            });
        }).listen(3000);
    ```

  

  

##### url 模块

- ##### url 模块

  > 向服务器发起请求的 url 地址都是字符串类型，url 所包含的信息也比较多，如：协议、主机名、端口、路径、参数、锚点等，直接操作字符串会相对麻烦，使用Nodejs 的 url 原生模块可轻松解决这一问题 
  >
  > url地址的组成部分：http://localhost:3004/cart?username=laoxie&age=18#xxx
  >
  > ​				    协议、域名、端口、路径、参数、hash(锚点)

  - ###### url常用属性（url组成部分）

    - href： 解析前的完整原始 URL，协议名和主机名已转为小写          '/cart?username=laoxie&age=18'
    - protocol： 请求协议，小写   http://
    - host： url 主机名，包括端口信息，小写
    - hostname: 主机名，小写
    - port: 主机的端口号
    - pathname: URL中路径，下面例子的          /cart
    - search: 查询对象，即：queryString，包括之前的问号“?”          '?username=laoxie&age=18'
    - path: pathname 和 search的合集          '/cart?username=laoxie&age=18'
    - **query**: 查询字符串中的参数部分（问号后面部分字符串），或者使用 querystring.parse() 解析后返回的对象           'username=laoxie&age=18'
    - hash: 锚点部分（即：“#”及其后的部分）

  - ###### url常用方法

    url.parse(url, boolean)：把url信息转成对象

    - url：字符串格式的 url

    - boolean：是否把url参数(url.query)转为对象，

      - false(默认)：参数为字符串
      - true：将参数转转对象

      ```
          var url = require('url');
      
          //第二个参数为 true 
          var params = url.parse('http://www.laoxie.com/one?a=index&t=article&m=default#laoxie', true);
          //params.query 为一个对象=> {a: 'index', t: 'article', m: 'default'}
          console.log(params.query);
      
          //第二个参数为 false
          params = url.parse('http://www.laoxie.com/one?a=index&t=article&m=default#laoxie', false);
          //params.query 为一个字符串 => a=index&t=article&m=default
          console.log(params.query);
      ```

    - url.format(params)：对象转字符串，url.parse的反过程 

    - url.resolve(): 以一种 Web 浏览器解析超链接的方式把一个目标 URL 解析成相对于一个基础 URL 

      ```
      var url = require('url');
      url.resolve('http://laoxie.com/', '/one') // 'http://laoxie.com/one'
      url.resolve('http://laoxie.com/', '/one','/gz') // 'http://laoxie.com/one'
      url.resolve('http://laoxie.com/one', '/one') // 'http://laoxie.com/one'自动合并one
      ```

  

##### querystring模块

- ##### querystring模块： 查询字符串处理模块

  > 用于处理url地址中的参数信息

  - parse()：字符串转对象

    ```
     let qs = require('querystring');
        let str = 'firstname=laoxie&url=http%3A%2F%2Flaoxie.com&lastname=xie&passowrd=123456';
        let param = qs.parse(param);//{firstname:"laoxie", url:"http://laoxie.com", lastname: 'xie', passowrd: 123456};
    ```

  - stringify()：对象转字符串

    ```
        let qs = require('querystring');
    
        let obj = {firstname:"laoxie", url:"http://laoxie.com", lastname: 'xie', passowrd: 123456};
        //将对象转换成字符串
        let param = qs.stringify(obj);//firstname=laoxie&url=http%3A%2F%2Flaoxie.com&lastname=xie&passowrd=123456
    ```

  

##### path模块

- ##### path模块

- ##### path里面有个非常好用的方法 截取文件名path.basename(path)  获取：名字.后缀

  > ##### 用于处理**文件**与目录的路径

  - path.parse(path) 返回一个对象，其中对象的属性表示 path 的元素

    - root

    - dir

    - base

    - name

    - ext文件的扩展名   .jpg 带点.

      ![](D:\mdpic\nodejs\1.png)

      ```
      const http = require('http');
       const fs = require('fs');
       const url = require('url');
       const path = require('path');
      
      //  创建http服务器
      http.createServer((req,res)=>{
          let pathname = url.parse(req.url).pathname;
          let pathObj = path.parse(pathname);
          console.log(pathname);//  /img/tiantian.jpg
          console.log(pathObj);//
       })
      ```

      - path.basename(path) 返回 path 的最后一部分（同path.parse()中的base 获取文件名 即名字.后缀）
      - path.dirname(path) 返回 path 的目录名（同path.parse()中的dir）
      - path.extname(path) 返回 path 的扩展名，（同path.parse()中的ext   获取的后缀名带点 .jpg）
      - path.join([…paths]) 使用平台特定的分隔符把所有 path 片段连接到一起，并规范化生成的路径
      - path.normalize(path) 规范化指定的 path，并处理 ‘..’ 和 ‘.’ 片段



##### events 模块

- ##### events 模块

  > 事件模块在 Nodejs 中有很多好处，但用法却可以很简单

  - Nodejs 是单进程单线程应用程序，但是通过事件和回调支持并发，所以性能非常高。
  - Nodejs的每一个API都是异步的，并作为一个独立线程运行，使用异步函数调用，并处理并发。
  - Nodejs 基本上所有的事件机制都是用设计模式中观察者模式实现。
  - Nodejs 单线程类似进入一个while(true)的事件循环，直到没有事件观察者退出，每个异步事件都生成一个事件观察者，如果有事件发生就调用该回调函数.

  ![](D:\mdpic\nodejs\2.png)

  

  ​	 `nodejs没有dom的概念，但nodejs仍然有事件，如请求开始、请求成功、请求结束；再例如传一张图片到后台，图片如果很少，一下子就传过去，但如果传输的图片内存很大或者传输的是视频，那么不可能马上传过去`

  - 用法

    - 【创建】实例化一个事件实例 new events.EventEmitter();

    - 【绑定】在实例对象上定义事件 on(eventname, function(){})

    - 【触发】emit 方法触发事件 emit(eventname)

      ```
       .on('事件名'，function（）{}） ---相当于 bind，但是不会触发
       .emit('事件名'）--- 相当于触发事件
      ```

  - 意义

    - 后面学到图片上传
    - post请求等都是用事件对象实现效果

    ```
    	// 引入 events 模块
        var events = require('events');
        
        // 创建 eventEmitter 对象（实例化new一个事件实例）
        var eventEmitter = new events.EventEmitter();
    
    	//创建完上面一个对象，但里面基本为空，我们自己可以操作
    	//如开始定义事件，定义事件同jQuery一样，用on('自定义名字'，fn)
        // 绑定事件及事件的处理程序
        eventEmitter.on('connection', function(){
            console.log('连接成功。');
            // 触发 data_received 事件 
            eventEmitter.emit('data_received');
        });
    
        // 使用匿名函数绑定 data_received 事件
        eventEmitter.on('data_received', function(){
            console.log('数据接收成功。');
        });
    
        //用 eventEmitter 对象的 emit 方法来调用事件
        eventEmitter.emit('connection');
        console.log("程序执行完毕。");	
    ```

  

##### fs 模块

- ##### fs 模块 (file system)涉及到文件操作的都用fs模块

  > 出于安全原因，前端的javascript不允许操作本地文件(即不能操作硬盘上的文件)，所以文件的处理都会放到服务端去处理。Node.js作为一门后端语言，同样具备了操作文件的能力，这一操作需要用到fs模块。（laoxie Tips：⭐⭐⭐工作中写代码的时候有异步尽量用异步，同步的操作没有一个大概的时间，同步可能存在阻塞

  - readFile(): 读取文本（异步方法读取）

  - ```
    const fs = require('fs');
        // 异步读取
        // 参数1：文件路径，
        // 参数2：本次学习忽略
        // 参数3：读取文件后的回调函数
        			//回调函数的参数1：err  错误信息（如读取失败）
        			//回调函数的参数2：data <string字符串>||<Buffer二进制代码>
        fs.readFile('about.txt', function (err, data) {
           if (err) {
               return console.error
           }
           console.log("异步读取: " + data.toString());
        });
        
        //ES6箭头函数的方法写回调函数
        fs.readFile('about.txt',(err, data)=>{
           if (err) {
               return console.error
           }
           console.log("异步读取: " + data.toString());
        });   
        
        ///////假设将上面的fs.readFile赋值给一个变量 let a = fs.readFile('about.txt')
        ///////console.log(a变量)==>结果为undefined
        ///////因为fs.readFile是异步方法，不能直接获取，必须放到回调函数里才能获取
    ```

  - readFileSync():读取文本（同步读取）少采用 正常情况一般用异步读取

    ```
        const fs = require('fs');
        var data = fs.readFileSync('demoFile.txt');
        console.log("同步读取: " + data.toString());
    ```

    

  - writeFile(): 写入文本（覆盖写入）只是负责把内容写入，不负责原来有没有东西

    ```
        const fs = require('fs');
        // 异步写入
        // 参数1：文件路径，
        // 参数2：想写入的内容
        // 参数3：读取文件后的回调函数
        			//回调函数的参数1：err  错误信息（如读取失败）
        			//回调函数的参数2：data <string字符串>||<Buffer二进制代码>
        
        //每次写入文本都会覆盖之前的文本内容
        fs.writeFile('about.txt', '我们不一样',  function(err) {
           if (err) {
               //throw Error()该方法会显示错误且显示错误的行数(工作中更常用)
               return throw Error('写入失败');
               
               //return console.error(err);
           }
           console.log("数据写入成功！");
           console.log("--------我是分割线-------------")
           console.log("读取写入的数据！");
    
           // 写入覆盖后重新读取文件内容
           fs.readFile('about.txt', function (err, data) {
              if (err) {
                 return console.error(err);
              }
              console.log("异步读取文件数据: " + data.toString());
           });
        });
    ```

  - appendFile(): 写入文本（追加写入）

    ```
        var fs = require('fs');
        fs.appendFile('about.txt', '有啥不一样\n一样的坚强', function (err) {
           if (err) {
               return console.error(err);
           }
           console.log("数据写入成功！");
        });
        
       		 //打开about.txt 里面的内容变成
       		 		//我们不一样
       		 		//有啥不一样
       		 		//一样的坚强
    ```

    

  - 图片读取（http服务器）

    - 即利用fs模块与http模块制作本地相册

    > 图片读取不同于文本，因为文本读出来可以直接用 console.log() 输出，但图片则需要在浏览器中显示，所以需要先搭建 web 服务，然后把以字节方式读取的图片在浏览器中渲染。

    ```
        const http = require('http');
        const fs = require('fs');
        const url = require('url');//上课+  自己+
        const path = require('path');//上课+
    
    //=====================原笔记代码，fs.readFile('读取固有文件'）=====================
        //图片读取是以字节的方式
        var content =  fs.readFile('001.jpg', "binary");
    	//创建好http服务器
        http.createServer((request, response)=>{
            //图片在浏览器的渲染因为没有 img 标签，所以需要设置响应头为 image
            response.writeHead(200, {'Content-Type': 'image/jpeg'});
            response.write(content, "binary");
            response.end();
        }).listen(3000,function(){
            console.log('Server running at http://localhost:3000/');
        });	
        
     //====================上课演示操作的代码，fs.readFile('读取url路径中相关的文件'）=========
        //  创建http服务器
        http.createServer((req,res)=>{
            let pathname = url.parse(req.url).pathname;
    		//url.parse（请求路径）一堆属性里面，只有pathname路径名=文件名  '/images/01b1.jpg'
    
            // 获取绝对路径，用于读取图片内容  当前路径+路径名
            let realPath = path.join(__dirname,pathname);//f:xx/xxx/xxx/img/tiantian.jpg
            console.log(__dirname);//得到当前目录的绝对路径
            
            console.log('path：',pathname);
            console.log('realpath：',realPath);
    
            // 根据路径读取图片内容
            fs.readFile(realPath,(err,data)=>{
                res.writeHead(200,{'Content-Type':'text/jpeg'})
                res.end(data);
            });
        }).listen(3005,()=>{
            console.log('server is running on port 3005')
        })
        
      //==========================自己操作的代码==================================
      // 创建服务器(链式链接)
        http.createServer((request,response)=>{
    
            let path = url.parse(request.url).pathname;
            //url.parse（请求路径）一堆属性里面，只有pathname路径名=文件名  '/images/01b1.jpg'
            
            console.log('..'+path);
            // 根据路径读取图片
            fs.readFile('..'+path,(err,data)=>{
                console.log('data:',data);
                response.writeHead('200',{'content-Type':'image/jpeg'});
                response.end(data);
            });
    
            /*console.log(111);
            console.log(__dirname);//得到当前目录的绝对路径
            console.log(222);*/
    
        }).listen('3005',()=>{
            console.log('server is running on port 3005');
        })
    ```

  - 目录操作 

    - 创建一个目录：fs.mkdir()
    - 列出目录内容：fs.readdir()
    - 重命名目录或文件：fs.rename()
    - 删除目录与文件：fs.rmdir(),fs.unlink()
    - 得到文件与目录的信息：fs.stat()
      - stats.isDirectory() 当前是否为文件夹
      - stats.isFile() 当前是否为文件
    - 检查文件或目录是否存在 
      - fs.access()
      - fs.existsSync(path)

  - stream流（流=液体）

    > Stream 是一个抽象接口，往往用于打开大型的文本文件，创建一个读取操作的数据流。Node 中有很多对象实现了这个接口。如http 服务器发起请求的request 对象就是一个 Stream。
    - 所谓大型文本文件，指的是文本文件的体积很大，读取操作的缓存装不下，只能分成几次发送，每次发送会触发一个`data事件`，发送结束会触发`end事件`。
    - 读取流（以流的方式读取文件内容）
      - setEncoding('UTF8')：设置读取编码格式
      - data事件：读取数据中
      - end事件：数据读取完毕

    ```
        //引入fs模块，只要涉及文件的都与fs有关，即使最后变成了液体的状态
        const fs = require("fs");
    
        // 创建一个读取流 // 读取流：以流的方式读取input.txt文件中的内容
        var readerStream = fs.createReadStream('input.txt');
        // console.log(readerStream);
    
        // 设置编码为 utf8。
        readerStream.setEncoding('UTF8');
        
    
    	// 处理流事件 --> data, end,  error
    	
    	//如何最终得到想要的数据，一定有个事件.on;并且读取的时候是用流的方式一点一点的读取【data数据、chunk一小块一小块】
        let data = '';
        readerStream.on('data', function(chunk) {
          	//chunk为数据块（不完整的数据）
          	
          	//把chunk一个个拼接起来
           data += chunk;
        });
    
        readerStream.on('end',function(){
        	//数据读取完毕，执行这里的代码
           console.log(data);
        });
    
        readerStream.on('error', function(err){
           console.log(err.stack);
        });
    ```

    - 写入流(以流的方式写入文件)
      - write()方法：写入内容方法
      - end()方法：标记写入结束
      - finish事件：写入完成后执行

    ```
        //创建一个可以写入的流，写入到文件 output.txt 中
    
        const fs = require("fs");//如果接着上方读取流继续写代码，可以省略这句
        var data = '中国';
    
        // 创建一个可以写入的流，相当于创建一个文件,最终写入到文件 output.txt 中
        // var writerStream = fs.createWriteStream('output.txt', {'flags': 'a'}); //追加文本
        var writerStream = fs.createWriteStream('output.txt');
    
        // 使用 utf8 编码写入数据
        writerStream.write(data,'UTF8');
        writerStream.write('我们不一样；');
    	writerStream.write('有啥不一样；');
    	writerStream.write('其实也一样；');
    
        // 标记文件末尾
        writerStream.end();
    
        writerStream.on('finish', function() {
            console.log("写入完成。");
        });
    
        writerStream.on('error', function(err){
           console.log(err.stack);
        });
    ```

    - 管道流pipe

      > 管道提供了一个输出流到输入流【或者 入到出】的机制。通常我们用于从一个流中获取数据并将数据传递到另外一个流中。我们把文件比作装水的桶，而水就是文件里的内容，我们用一根管子(pipe)连接两个桶使得水从一个桶流入另一个桶，这样就慢慢的实现了大文件的复制过程。

    ```
        var fs = require("fs");
    
        // 创建一个可读流 【读取流】
        var readerStream = fs.createReadStream('input.txt');
    
        // 创建一个可写流 【写入流】
        var writerStream = fs.createWriteStream('output.txt');
    
        // 管道读写操作
        // 读取 input.txt 文件内容，并将内容写入到 output.txt 文件中
        // 读取流.通过管道pipe(流进写入流)
        readerStream.pipe(writerStream);
    ```

    - 链式流（多个pipe）

      > 链式是通过连接输出流到另外一个流并创建多个对个流操作链的机制。链式流一般用于管道操作。接下来我们就是用管道和链式来压缩和解压文件。

    - 压缩

    ```
        var fs = require("fs");
        //引入压缩和解压的模块【原生模块】
        var zlib = require('zlib');
    
        // 压缩 input.txt 文件为 input.txt.gz
        // 以流的方式读取文本
        fs.createReadStream('input.txt')
          .pipe(zlib.createGzip()) //新增+++ 把读取出来的文本调用压缩模块进行压缩
          .pipe(fs.createWriteStream('input.txt.zip'));//把压缩好的流进行保存【记得带上原文件后缀名 .txt .json等】
    ```

    - 解压 

    ```
        var fs = require("fs");
        //引入压缩和解压的模块【原生模块】
        var zlib = require('zlib');
    
        fs.createReadStream('input.txt.zip')
          .pipe(zlib.createGunzip())//
          .pipe(fs.createWriteStream('input1.txt'));
    ```

    - 上课补充（浏览器自动下载图片）

    ```
    //……
    //想自动下载的话 修改响应头；只需写一个浏览器不识别的即可自动下载
    res.writeHead(200,{'Content-Type':'zlib/zlib'})
    //……
    ```

    

### 路由（根据浏览器URL地址的不同，显示不同的内容）

> 在 BS 架构中，路由的概念都是一样的，可理解为根据客户端请求的 URL 映射到不同的方法实现，更多的一般都是针对 URL 中的路径，或者是参数，又或者是锚点这些信息进行映射。

![](D:\mdpic\nodejs\3.png)

#### 应用场景

- 注册一个账户
  - 请求类型：post
  - url地址：<http://localhost:88/register>
- 注册成功的情况下跳转到登录界面进行登录 
  - 请求类型：post
  - url地址：<http://localhost:88/login>
- 登录成功进行获取学生信息 
  - 请求类型：get
  - url地址：<http://localhost:88/students>
- 同时可以获取订单信息 
  - 请求类型：get
  - url地址：<http://localhost:88/orders>
- 如何访问不存在的路由则抛出错误信息。 
  - 404页面

```
//原课件文档
	const http = require('http')
    const url = require('url')
    const qs = require('querystring');
    const util = require('util');

    http.createServer((request, response) => {
        // 处理url地址
        let urlObj = url.parse(request.url, true);

        // 获取访问路径
        let pathname = urlObj.pathname;

        // 获取请求类型
        let method = request.method.toUpperCase();

        // 获取参数
        let params = urlObj.query;

        // POST请求处理方式
        if(method == 'POST'){
            let postData = '';
            request.on('data', (_data) => {
                postData += _data;
            })
            request.on('end', () => {
                postData = qs.parse(postData);
                let result = {};
                switch(pathname){
                    case '/login':
                        //连接数据库，实现登陆逻辑
                        result = {status: true};
                        break;
                    case '/register':
                        //连接数据库，实现注册逻辑
                        result = {status: true};
                        break;
                    default :
                        result = {status: false, message: '没有对应的请求'};
                        break;                  
                }
                response.end(util.inspect(result))
            })
        } 

        // get请求处理方式
        else if(method === 'GET'){
            let result = {};
            switch(pathname){
                case '/students':
                    //连接数据库，获取学生信息
                    result = {status: true, data: [], params};
                    break;
                case '/orders':
                    //连接数据库，获取订单信息
                    result = {status: true, data: [], params};
                    break;
                default :
                    result = {status: false, message: '没有对应的请求', params};
                    break;
            }
            response.end(util.inspect(result))
        }
    }).listen(88);
```

```
//上课老师操作 路由 虚拟目录
/**
 * 利用原生模块搭建静态资源服务器
 *  支持：css,html,js,image
 */

 const http = require('http');
 const fs = require('fs');
 const url = require('url');
 const path = require('path');

//  引入mime类型
const mime = require('./mime');


//  创建http服务器
http.createServer((req,res)=>{
    let pathname = url.parse(req.url).pathname;

    // 获取绝对路径，用于读取图片内容
    let realPath;

    res.writeHead(200,{'Content-Type':`text/plain;charset=utf-8`});
    // 路由Router
    switch(pathname){
        case '/':
            realPath =  path.join(__dirname,'index.html');
            break;
        case '/login':
            realPath =  path.join(__dirname,'html/login.html');
            break;
        case '/reg':
            realPath =  path.join(__dirname,'html/reg.html');
            break;
        case '/usercenter':
            realPath =  path.join(__dirname,'html/usercenter.html');
            break;
        default:
            realPath = path.join(__dirname,pathname);
    }


    // 获取后缀名
    let ext = path.extname(realPath).slice(1);

    // 根据路径读取静态资源文件内容
    fs.readFile(realPath,(err,data)=>{
        if(err){
            res.writeHead(404,{'Content-Type':`text/plain;charset=utf-8`});
            res.end(`你访问你的url:${pathname} 不存在`);
        }
        res.writeHead(200,{'Content-Type':`${mime[ext]};charset=utf-8`});
        res.end(data);
    });
}).listen(3005,()=>{
    console.log('server is running on port 3005')
})
```



### 【案例】

- 利用原生模块搭建静态资源服务器

  ```
  //mime.js
  //MIME (Multipurpose Internet Mail Extensions) 是描述消息内容类型的因特网标准。
  //文件扩展名的 Mime 类型列表
  //新建以下对象自定义模块
  module.exports = {
    "css": "text/css",
    "gif": "image/gif",
    "html": "text/html",
    "ico": "image/x-icon",
    "jpeg": "image/jpeg",
    "jpg": "image/jpeg",
    "js": "text/javascript",
    "json": "application/json",
    "pdf": "application/pdf",
    "png": "image/png",
    "svg": "image/svg+xml",
    "swf": "application/x-shockwave-flash",
    "tiff": "image/tiff",
    "txt": "text/plain",
    "wav": "audio/x-wav",
    "wma": "audio/x-ms-wma",
    "wmv": "video/x-ms-wmv",
    "xml": "text/xml"
  };
  ```

  ```
  /**
   * 利用原生模块搭建静态资源服务器
   *  支持：css,html,js,image
   */
  
   const http = require('http');
   const fs = require('fs');
   const url = require('url');
   const path = require('path');
  
  //  引入mime类型
  const mime = require('./mime');
  
  //  创建http服务器
  http.createServer((req,res)=>{
      let pathname = url.parse(req.url).pathname;
  
      // 获取后缀名  两种获取后缀名的方式都带点  .jpg
      let ext = path.extname(pathname).slice(1);
  
      
      // switch(ext){
      //     case 'jpeg':
      //     case 'jpg':
      //         mime = 'image/jpeg';
      //         break;
      //     case 'css':
      //         mime = 'text/css';
      //         break;
      // }
  
      // 获取绝对路径，用于读取静态资源文件内容
      let realPath = path.join(__dirname,pathname);   
      
      // 根据路径读取静态资源文件内容
      fs.readFile(realPath,(err,data)=>{
          if(err){
              res.writeHead(404,{'Content-Type':`text/plain;charset=utf-8`});
              res.end(`你访问你的url:${pathname} 不存在`);
          }
          res.writeHead(200,{'Content-Type':`${mime[ext]};charset=utf-8`});
          res.end(data);
      });
  }).listen(3005,()=>{
      console.log('server is running on port 3005')
  })
  ```

  

- 制作本地相册

  > 应用到目录操作

- 编写数据接口，实现注册登录获取用户信息等功能

【练习】

- 实现文件的上传、在线压缩与解压

# 黑窗口命令

### 打开某目录下的命令窗口

​	 `cd/d 粘贴路径` 

### 查看目录下有哪些文件

​	 `ls`

### npm下载后要保存，需新建package.json

​	 `npm init` 

​	1.package.json不存在时 

​		   运行命令: npm init可自动创建package.json文件
	2.package.json存在时 

  		   运行命令:npm install 或者 npm install –save-dev会自动将package.json中的模块安装到node-modules文件夹下。

![](D:\mdpic\nodejs\4.png)

### npm下载bootstrap，保存在package.json，且多了文件夹node_modules

​	 `npm install bootstrap --save`

​	下载完后东西已存放在node_modules文件夹，提取放到新建文件夹assets里，主要用于存放静态资源类的第三方，由于bootstrap基于jquery，所以引用bootstrap时

![](D:\mdpic\nodejs\5.png)



### npm下载 jquery

​	 `npm install jquery --save`

![](D:\mdpic\nodejs\6.png)



### 清除黑窗口全部代码

​	 `cls`

​                ![](D:\mdpic\nodejs\7.png)

### cd:change directory切换文件夹  

​	 cd c:/test/ 表示进入c盘的test文件夹中

​	 cd .当前目录

​	 cd ..进入上一级目录

​	 cd ../.. 退回道上一级的上一级目录	



​	 

​	

