# 数据库操作

| MySQL          | MongoDB        |
| -------------- | -------------- |
| database数据库 | database数据库 |
| table表        | collection集合 |
| row数据行      | document文档   |



## MySQL（关系型数据库）

> 关系型数据库
>
> 关系型数据库包含一个个表， 表里面包含一条条数据，类似于表格去展现，每个表之间可能存在一定的联系
>
> MariaDB
>
> mySQL
>
> SQLServer	 				MS（微软）
>
> Oracle（相对比较难）			甲骨文
>
> ![](D:\mdpic\nodejs\26.png)

### mySQL的安装配置

> 请自行查找相关资料

### 在Nodejs中使用mySQL

- 安装mysql模块

  ```
      npm install mysql
      npm install --save mysql【要敲打--save前必须先npm init建立一个package.json】
  ```

  

- 连接数据库【在src文件夹新建一个mysql.js】

  ```
      var mysql = require('mysql');
  
      //创建连接池
      var pool  = mysql.createPool({
        host     : 'localhost',
        user     : 'root',
        password : '',
        port: 3306,
        database: 'edusys',//【数据库的名字】
        multipleStatements: true
      });
  
      pool.query('select * from student', function(error, rows){
            console.log(rows);
      });
  ```

  

- 数据库操作

  > 格式：<数据库>.query(sql,callback)

  - 查看有哪些数据库名字，进如mysql的黑窗口控制台 `show databases`

    ![](D:\mdpic\nodejs\27.png)

  - 增 `insert into <表名> [(<字段名1>[,..<字段名n > ])] values ( 值1 )[, (值n )];` `sql insert into MyGuests (firstname, lastname, email) values ('John', 'Doe', 'john@example.com');`

  - 

  - 删 `delete from <表名> where <条件>`

    ```
        --删除MyGuests表中id为1的数据
        DELETE FROM MyGuests where id=1;
    
        --删除所有数据
        DELETE FROM MyGuests
    ```

    

  - 改 `update <表名> set 字段=新值,… where 条件`

    ```
        update MyGuests set name='Mary' where id=1;
    ```

    

  - 查 `select <字段1, 字段2, ...> from <表名> where < 表达式 >`

    ```
        --查看表 MyGuests 中所有数据
        select * from MyGuests;
    
        --查看表 MyGuests 中前10行数据：
        select * from MyGuests order by id limit 0,10;
    ```

- 条件控制语句 

  - WHERE 语句： 

    > SELECT * FROM tb_name WHERE id=3; 

  - 相关条件控制符： 

    - =、>、<、<>、IN(1,2,3......)、BETWEEN a AND b
    - AND、OR、NOT
    - LIKE用法中
      - % 匹配任意、
      - _ 匹配一个字符（可以是汉字）

  - LIMIT idx,qty：数量控制 
    - SELECT * FROM goods LIMIT 2,5
  - IS NULL 空值检测
  - 排序ORDER BY
    - asc 升序（默认）

    - desc 降序

       ![](D:\mdpic\nodejs\28.png)

       ![](D:\mdpic\nodejs\29.png)![](D:\mdpic\nodejs\30.png)

### 封装模块

```
//原课件内容
	module.exports = {
        select: function(tsql, callback){
            pool.query(tsql, function(error, rows){
                  if(rows.length > 1){
                    callback({rowsCount: rows[1][0]['rowsCount'], data: rows[0]});
                  } else {
                    callback(rows);
                  }
            });
        }
    }
```

```
//==============老谢操作封装模块=====================
const mysql = require('mysql');

var pool  = mysql.createPool({
    host     : 'localhost',
    user     : 'root',
    password : '',
    port: 3306,
    database: 'edusys'
});

// 单一原则
// 耦合：低耦合
// 查：
exports.query = sql=>{
    return new Promise((resolve,reject)=>{
        pool.query(sql, function(error, rows){
            let data;
            if(error){
                 data = {
                     status:0,
                     data:error
                 }

                 reject(data);
                 return;
            }
            data = {
                 status:1,
                 data:rows
            }
            resolve(data);
         });
    })
   
}

```

```
//==============老谢操作 引用上面封装的模块=================
const express = require('express');

const mysql = require('mysql');
const _sql = require('./sql');//自定义模块名

// 创建express应用
let app = express();

// 路由
app.get('/city',async (req,res)=>{
    // 编写mysql语句
    let sql = `select * from city`;
    
    // 利用async, await实现数据获取
    let data = await _sql.query(sql);
    res.send(data);
})

app.get('/city/:city',async (req,res)=>{
    // 编写mysql语句
    let sql = `select * from city where name='${req.params.city}'`;
    
    // 利用async, await实现数据获取
    let data = await _sql.query(sql);
    res.send(data);
})

app.get('/student',async (req,res)=>{
    // 编写mysql语句
    let sql = `select * from student`;

	// 利用async, await实现数据获取
    let data = await _sql.query(sql);
    res.send(data);

})

app.get('/student/:className',async (req,res)=>{
    // 获取className对应的id
    let className = req.params.className;
    
    // 编写mysql语句【联表查询】
    let sql = `select * from student where class_id in (select id from class where name=${className})`;
    
    // 利用async, await实现数据获取
    let data = await _sql.query(sql);
    res.send(data);
})

app.listen(3000,()=>{
    console.log('server is running on http://localhost:3000');
})
```



## MongoDB（非关系型数据库）

> ![](D:\mdpic\nodejs\31.png)
>
> MongoDB有数据库的概念==database->数据库下面不是表了，而是集合
>
> 但MongoDB没有表的概念，而是集合==collection
>
> 里面的每一条数据用==BSON存储（类似于JSON的东西）
>
> 格式：以对象数组的格式，对象数组有一个特点，每个｛｝之间可以不一样，可以多一个id，少一个description
>
> ![](D:\mdpic\nodejs\32.png)

MongoDB是一个基于分布式文件存储的数据库，由C++语言编写【与nodejs一样，nodejs也是c++编写】，旨在为WEB应用提供可扩展的高性能数据存储解决方案，是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。它支持的数据结构非常松散，是类似json的bson格式

> bson:是一种类json的一种二进制形式的存储格式，全称Binary JSON

### 下载与安装

- 下载地址：<https://www.mongodb.com/download-center/community>
- 安装路径尽量简单，不要有中文，记得配置系统环境变量`;C:\Program Files\MongoDB\Server\4.0\bin`![查看是否安装成功](D:\mdpic\nodejs\33.png)
- 安装可视化窗口`Robo 3T`

### 配置数据库（每个项目的数据库都不一样）

- 配置数据库保存目录【一次性方法】

  ```
      mongod.exe --dbpath D:\data\mongodb\db
  ```

  ![](D:\mdpic\nodejs\34.png)

  > 启动成功后，可通过 <http://127.0.0.1:27017> 访问
  >
  > **⭐⭐⭐MongoDB安装后，还要连接它，并且制定了相应的数据库目录**

- 配置为 windows 服务【 windows 服务的作用：一开机马上运行】【一劳永逸的方法】

  ​	【新建下图的mongod.cfg】

  ​		path:意思是关于这个mangoDB的日志放在哪

  ​		dbPath:设置我们的数据库目录在哪里

  ```
      systemLog:
          destination: file
          path: d:\data\mongodb\log\mongod.log
      storage:
          dbPath: d:\data\mongodb\db
  ```

  ```
  mongod.exe --config c:\mongodb\mongod.cfg --service --serviceName MongDB --install
  ```

  按照下面的路径，C盘新建一个文件夹为mongodb

  文件夹里面有一个文件mongod.cfg

  文件的内容复制粘贴

  ![](D:\mdpic\nodejs\35.png)

  ![](D:\mdpic\nodejs\36.png)

  打开本地服务，找不到就在控制面板搜  **服务**

  ![](D:\mdpic\nodejs\37.png)

  ![](D:\mdpic\nodejs\38.png)

  

  ![](D:\mdpic\nodejs\39.png)

  

  ![](D:\mdpic\nodejs\40.png)

  ![](D:\mdpic\nodejs\41.png)

- 命令行执行

  ```
      mongod.exe --config c:\mongodb\mongod.cfg --service --serviceName MongoDB --install
  ```

  

- 配置环境变量

  > 安装mongodb默认自动配置环境变量，方便在命令行中使用相关命令

 ![查询是否安装过](D:\mdpic\nodejs\42.png)



### 连接数据库

- `mongo` 连接到数据库并进行操作`help`
- `mongod` 显示数据库信息

### 常用命令

> 输入help可以看到基本操作命令

#### 数据库操作(Database)

- 查看所有数据库： `show dbs`
- 查看当前使用的数据库 
  - `db.getName()`
  - `db`

- 显示当前db状态: `db.stats()`

- 查看当前db的链接地址: `db.getMongo()`

- 创建/切换数据库: `use DBNAME`

  > 如果数据库不存在，则创建数据库，否则切换到指定数据库。

- 删除当前使用数据库: `db.dropDatabase()`

#### 集合操作(Collection)

> 利用use DBNAME 切换到当前数据库后，可以进行集合与文档的操作

- 查询所有集合: 
  - `db.getCollectionNames()` ![](D:\mdpic\nodejs\43.png)主要写于node.js
  - `show collections` ![](D:\mdpic\nodejs\44.png)主要用于命令行

- 创建集合： 

  - `db.createCollection('自己取个集合名如user')`; 

    > PS:只有创建了集合，数据库才能成功创建 

- 删除集合：
  - `db.集合.drop();`

#### 文档操作(Document)

> 文档就是数据，这里的所有操作都是针对数据 格式：`db.集合.方法()`

- 增（插入数据）： 
  - insert()
  - insertOne()
  - inertMany()
  - db.user集合名.insert({name:'laoxie',password:123456,age:18,gender:'男',reg_time:Date.now()})
  -  ![](D:\mdpic\nodejs\45.png)

- 删（删除数据） 
  - deleteOne()
  - deleteMany()
  -  ![](D:\mdpic\nodejs\46.png)

- 改（更新数据）
  - update(query,newData)
  - updateOne()
  - updateMany()
  - 覆盖原有数据的修改 ![](D:\mdpic\nodejs\47.png)
  - 更新特定字段的修改 ![](D:\mdpic\nodejs\48.png)

```
 //更新指定字段
     //查找name属性为tiantian的数据，并更新age属性为27
     db.user.update({name:'tiantian'},{$set:{age:27}})
```

查（查询数据）：

- 查询所有：`db.集合.find()`
- 按条件查询（支持多条件）：`db.集合.find(条件)`     db.user.find({gender:'男'})
- 查询第一条（支持条件）：`db.集合.findOne(条件)`

```
 //查询user下所有数据
    db.user.find();

    // 查询user下年龄为38的
    db.user.find({age:38})

    // 查询user下年龄大于38的
    db.user.find({age:{$gt:38}})
```

![](D:\mdpic\nodejs\49.png)

#### 条件与筛选

- 限制数量：`db.表名.find().limit(数量);`
- 跳过指定数量：`db.表名.find().skip(数量)`
- 比较查询
  - 大于：$gt

  - 小于：$lt

  - 大于等于：$gte

  - 小于等于：$lte

  - 非等于：$ne

    ![](D:\mdpic\nodejs\50.png)

#### 备份与恢复

备份：mongodump -h 127.0.0.1:27017 -d h51809 -o d:\mongobackups\

恢复：mongorestore -h 127.0.0.1:27017 -d h51809 --dir c:\mongobackups\h51809

![](D:\mdpic\nodejs\mongoDB备份与恢复.PNG)

![](D:\mdpic\nodejs\mongoDB备份命令行.PNG)

### NodeJS中使用mongodb

- 安装mongodb模块

```
npm insntall mongodb
npm install --save mongodb【要敲打--save前必须先npm init建立一个package.json】
```

package.json里面项目同名，![修改即可](D:\mdpic\nodejs\51.png)



- 连接mongoDB【不知道地址可以先查看当前db的链接地址: `db.getMongo()`】

  - 默认地址：mongodb:localhost:27017

  - 默认地址：127.0.01:27017

    >  如果数据库不存在，MongoDB 将创建数据库并建立连接。 

```
  	//引入模块
    const mongodb = require('mongodb');
    //获取mongo客户端
    const MongoClient = mongodb.MongoClient;

    //连接MongoDB并连接数据库laoxie，无则自动创建
    MongoClient.connect("mongodb://localhost:27017/laoxie", function(err, database) {
      //连接成功后执行这个回调……
      
      if(err) throw err;

    });
```

- 使用/创建数据库 

```
    const mongodb = require('mongodb');
    const MongoClient = mongodb.MongoClient;

    //使用数据库也可以使用以下方式
    //1.连接mongoDB
    MongoClient.connect("mongodb://localhost:27017", function(err, database) {
      if(err) throw err;
        // 连接、使用具体数据库，无则自动创建
        let db = database.db('h51809');
    });
```

- 创建/删除集合

  - 创建集合：createCollection()

    > 格式：db.createCollection(name, options)

  - 删除集合：drop()

    > 格式：drop(callback)

```
    dbase.createCollection('site', function (err, res) {
        if (err) throw err;
        console.log("创建集合!");
        db.close();
    });
```
```
//====================老谢操作、切换到具体的某个集合开始操作=================
//……接上【使用/创建数据库】里的代码
//……
	MongoClient.connect("mongodb://localhost:27017", function(err, database) {
      if(err) throw err;
        // 连接、使用具体数据库，无则自动创建
        let db = database.db('h51809');
        
        //切换到具体的某个集合
        let user = db.collection('user');

        let allAges = [18,28,20,24,30,26,28];
        let allgenders = ['男','女','保密'];

        let data = []
        for(var i=0;i<5;i++){
            data.push({
                name:`用户${i}`,
                age:allAges[parseInt(Math.random()*allAges.length)],
                gender:allgenders[parseInt(Math.random()*allgenders.length)],
                reg_time:Date.now()
            })
        }
        
        // 插入
        user.insertMany(data,(err, result)=>{
            // result:插入数据成功的信息
            //  * ops  插入的所有数据（带id）
            //  * insertedCount  插入的数量
            console.log(result)
        });
        
        // 删除
        user.deleteOne({name:'用户1'},(err,result)=>{
            console.log(result)
        });
        
        // 修改数据
        user.updateOne({
            name:'用户2'
        },{$set:{name:'lemon'}},(err,result)=>{
            console.log(result);
        });
        
        // 查询
        user.find({gender:'女'}).toArray((err,result)=>{
            // result：数据查询结果
            console.log(result)
        })
    });
```

### robo可视化窗口的查询

- **插入**

  user.insertMany(data,(err, result)=>{
              // result:插入数据成功的信息
              //  * ops  插入的所有数据（带id）
              //  * insertedCount  插入的数量
              console.log(result)
          });

   ![](D:\mdpic\nodejs\52.png)

   ![](D:\mdpic\nodejs\53.png)



- **查**

    ![](D:\mdpic\nodejs\54.png)

   ```
   user.find({gender:'女'},(err,result)=>{
   
     // result：数据查询结果
   
     console.log(result)
   
   })
   
   ```

   获取得到的数据查询结果指针        
​        ![](D:\mdpic\nodejs\55.png)



  - ```
       user.findOne({gender:'女'},(err,result)=>{
       
         // result：数据查询结果
       
         console.log(result)
       
       })
       
       ```

  - 得到一条完整的查询结果，一条结果不用toArray

         ![](D:\mdpic\nodejs\56.png)
  -  
     user.find({gender:'女'}).toArray((err,result)=>{
        
      // result：数据查询结果【可变，老谢第二次参数改data，数据】
        
      console.log(result)
        
     })

    将结果转成数组

 ![](D:\mdpic\nodejs\57.png)

 ![](D:\mdpic\nodejs\58.png)

![](D:\mdpic\nodejs\59.png)



- **删**

  ![](D:\mdpic\nodejs\60.png)

  n:0没有成功删除：![](D:\mdpic\nodejs\61.png)

  n:1成功删除1条：![](D:\mdpic\nodejs\62.png)



- **改**

  n:1,nModified:1 ![代表成功修改一条](D:\mdpic\nodejs\63.png)



## 用户注册页面（使用MongoDB）

- 首页index.html

![](D:\mdpic\nodejs\64.png)

```
//===================index.html老谢操作===================


```



- 注册页reg.html

   ![](D:\mdpic\nodejs\65.png)

   ![](D:\mdpic\nodejs\66.png)



  后端获取前端请求的名字

   ![](D:\mdpic\nodejs\67.png)

  连接数据库

   ![](D:\mdpic\nodejs\68.png)

  ![](D:\mdpic\nodejs\69.png)



```
//====================老谢操作 nodejs=======================
//===================注册 验证用户名========================
//===================注册 插入==============================
//===================登录==================================
const express = require('express');
const mongodb = require('mongodb');

// 获取Mongo客户端
const MongoClient = mongodb.MongoClient;

let app = express();

// 静态资源服务器
app.use(express.static('./'));


// 路由
// 注册
let urlencodedParser = bodyParser.urlencoded({ extended: false })//中间件
app.post('/reg',urlencodedParser ,(req,res)=>{
    let {username,password,gender} = req.body;

    MongoClient.connect('mongodb://localhost:27017',(err, database)=>{
        //连接成功后执行这个回调函数
        if(err) throw err;

        // 使用某个数据库，无则自动创建
        let db = database.db('h5_1809');

        // 使用集合
        let user = db.collection('user');


        // 插入数据
        user.insert({name:username,password,gender,reg_time:Date.now()},(err,result)=>{
            let data;
            if(err){
                data={
                    code:0,
                    data:[],
                    msg:err
                }
            }else{
                data = {
                    code:1,
                    data:result.ops,
                    msg:'插入成功'
                }
            }

            res.send(data);
        });
       

        // 关闭数据库，避免资源浪费
        database.close();

    });
});


// 登录
app.post('/login',urlencodedParser,(req,res)=>{
    let {username,password} = req.body;
    MongoClient.connect('mongodb://localhost:27017',(err, database)=>{
        //连接成功后执行这个回调函数
        if(err) throw err;

        // 使用某个数据库，无则自动创建
        let db = database.db('h5_1809');

        // 使用集合
        let user = db.collection('user');

        // 处理password为数字的情况
        password = isNaN(password) ? password : password*1;

        // 查询是否存在数据
        user.findOne({name:username,password},(err,result)=>{

            if(result){
                // 登录成功后，给前端发送用户表示：token
                res.send({
                    code:1,
                    data:result,
                    msg:'ok'
                })
            }else{
                res.send({
                    code:0,
                    data:[],
                    msg:'fail'
                })
            }
        });

        // 关闭数据库，避免资源浪费
        database.close();

    });
});

// 验证用户名是否能注册
app.get('/checkusername',(req,res)=>{
    // 获取传来的用户名
    let {username} = req.query;
    MongoClient.connect('mongodb://localhost:27017',(err, database)=>{
        //连接成功后执行这个回调函数
        if(err) throw err;

        // 使用某个数据库，无则自动创建
        let db = database.db('h5_1809');

        // 使用集合
        let user = db.collection('user');


        // 查询是否存在数据
        user.findOne({name:username},(err,result)=>{
            if(result){
                res.send({
                    code:0,
                    data:[],
                    msg:'fail'
                })
            }else{
                res.send({
                    code:1,
                    data:[],
                    msg:'ok'
                })
            }
        });
        
       /*
                //【补充，不是本题的find({}).toArray((err,data)=>{})】
                //【查询所有分类】
                user.find({gender:'女'}).toArray((err,result)=>{
                    if(err){
                        res.send({
                            code:0,
                            msg:'查询失败'，
                            data:err
                        });
                    }
                    res.send({
                        code:1,
                        msg:'success',
                        data
                    });
                    //关闭数据库，避免资源浪费
                    database.close();
                })
		*/
		
		
        // 关闭数据库，避免资源浪费
        database.close();

    });



})


app.listen(3008,()=>{
    console.log('server is running on http://localhost:3008');
})
```

```
//===================老谢操作 注册===============================
//====================reg.html==================================
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>用户注册</title>
    <link rel="stylesheet" href="../assets/bootstrap/css/bootstrap.css">
</head>
<body>
    <div class="container-fluid">
        <h1>用户注册</h1>
        <form>
            <div class="form-group">
                <label for="username">用户名：</label>
                <input type="text" class="form-control" id="username" placeholder="请求输入有效的用户名">
                <div class="invalid-feedback">该用户名已存在</div>
            </div>
            <div class="form-group">
                <label for="password">密码：</label>
                <input type="password" class="form-control" id="password" placeholder="请输入密码">
            </div>
            <div class="form-group">
                <label for="confirmPassword">确认密码：</label>
                <input type="password" class="form-control" id="confirmPassword" placeholder="请再次输入密码">
                <div class="invalid-feedback">两次输入密码不一致</div>
            </div>
            <div class="form-group">
                <label for="gender">性别</label>
                <select class="form-control" id="gender">
                    <option value="男">男</option>
                    <option value="女">女</option>
                    <option value="不详">不详</option>
                </select>
                </div>
            <button type="submit" class="btn btn-primary btnReg">注册</button>
        </form>
    </div>
    <script>
        document.addEventListener('DOMContentLoaded',()=>{
            // 获取元素
            let username = document.querySelector('#username');
            let password = document.querySelector('#password');
            let confirmPassword = document.querySelector('#confirmPassword');
            let gender = document.querySelector('#gender');
            let btnReg = document.querySelector('.btnReg');

            let status = [200,304];

            // 验证用户是否被注册
            username.onblur = ()=>{
                let xhr = new XMLHttpRequest();
                xhr.onload = ()=>{
                    if(status.includes(xhr.status)){
                        let res = JSON.parse(xhr.responseText);
                        if(res.code === 1){
                            // 可注册
                            username.classList.remove('is-invalid');
                            username.classList.add('is-valid');
                        }else{
                            username.classList.remove('is-valid');
                            username.classList.add('is-invalid');
                        }
                    }
                }
                xhr.open('get','/checkusername?username='+username.value,true);
                xhr.send();
            }


            // 确认密码
            confirmPassword.onblur = ()=>{
                let _confirmPassword = confirmPassword.value;
                let _password = password.value;

                if(_confirmPassword != _password){
                    confirmPassword.classList.remove('is-valid');
                    confirmPassword.classList.add('is-invalid');
                }else{
                    confirmPassword.classList.remove('is-invalid');
                    confirmPassword.classList.add('is-valid');
                }
            }

            // 点击注册
            btnReg.onclick = (e)=>{
                let _username = username.value;
                let _password = password.value;
                let _gender = gender.value;

                let xhr = new XMLHttpRequest();
                xhr.onload = ()=>{
                    if(status.includes(xhr.status)){
                        let res = JSON.parse(xhr.responseText);
                        if(res.code === 1){
                            location.href = 'login.html'
                        }else{
                           alert('注册失败，原因请看控制台')
                           console.log(res.msg);
                        }
                    }
                }
                xhr.open('post',`/reg`,true);
                xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
                xhr.send(`username=${_username}&password=${_password}&gender=${_gender}`);

                e.preventDefault();
            }
        })
        
    </script>
</body>
</html>
```

```
//===================老谢操作 登录===============================
//====================login.html================================
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>用户登陆</title>
    <link rel="stylesheet" href="../assets/bootstrap/css/bootstrap.css">
</head>
<body>
    <div class="container">
        <h1>用户登陆</h1>
        <form>
            <div class="form-group">
                <label for="username">用户名：</label>
                <input type="text" class="form-control" id="username" placeholder="请求输入有效的用户名">
                <div class="invalid-feedback">用户名或密码错误</div>
            </div>
            <div class="form-group">
                <label for="password">密码：</label>
                <input type="password" class="form-control" id="password" placeholder="请输入密码">
                <div class="invalid-feedback">用户名或密码错误</div>
            </div>
            <div class="form-check">
                <input type="checkbox" class="form-check-input" id="exampleCheck1">
                <label class="form-check-label" for="exampleCheck1">7天免登陆</label>
            </div>
            <button type="submit" class="btn btn-primary btnLogin">登录</button>
        </form>
    </div>
    <script>
        document.addEventListener('DOMContentLoaded',()=>{
            let username = document.querySelector('#username');
            let password = document.querySelector('#password');
            let btnLogin = document.querySelector('.btnLogin');

            let status = [200,304];

            btnLogin.onclick = (e)=>{
                let _username = username.value;
                let _password = password.value;

                let xhr = new XMLHttpRequest();
                xhr.onload = ()=>{
                    if(status.includes(xhr.status)){
                        let res = JSON.parse(xhr.responseText);
                        if(res.code === 1){
                            location.href = '/index.html';
                        }else{
                            username.classList.add('is-invalid');
                            password.classList.add('is-invalid');
                        }
                    }
                }
                xhr.open('post','/login',true);
                xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
                xhr.send(`username=${_username}&password=${_password}`);

                e.preventDefault();
            }


        })
    </script>

</body>
</html>
```

