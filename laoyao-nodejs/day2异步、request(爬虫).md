# 参考教程

[runoob菜鸟教程](https://www.runoob.com/nodejs/nodejs-tutorial.html)

# 异步

### 前端（浏览器端）

同步：队列【低效、稳定】同步有回调函数 和 没有回调函数是一样的

异步：【高效、不稳定、没回调的异步是没意义的、故存在回调地狱】有回调函数【函数套函数】99.00%有异步

前端异步只有以下几种情况是异步
```
ajax xmlhttprequest
setInterval/setTimeout
jsonp
```

### 后端（服务器端node）

比前端多很多，很多方法都是异步的

```js
fs.readFile //异步
fs.readFileSync //同步
```

异步一般配合回调函数,回调函数能让异步变得有意义

同步比异步少了回调函数

同步阻塞，相对稳定，不需要回调

异步非阻塞，相对不稳定，配合回调才有意义

node里面没有sync就是异步

```js
//同步
var data = fs.readFileSync('./test.txt');
console.log(data.toString());

//异步
fs.readFile('./test.txt',function(err,data2){
	console.log(data2.toString());
});
```

# 回调嵌套

回调嵌套=回调地狱=回调金字塔

## promise then

如果出现多个回调嵌套的时候，建议是用`promise`来去解决这个回调地狱，promise执行顺序取决于`then`

a().then(b).then(c);

```js
function buyPizza() {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
			console.log("买披萨1");
			resolve()
		}, 1000);
	})
}
function buyDrink() {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
			console.log("买饮料2");
			resolve()
		}, 1000);
	})
}
function eatMeal() {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
			console.log("吃东西3");
			//resolve()
		}, 1000);
	})
}
buyPizza().then(buyDrink).then(eatMeal);//买披萨1、买饮料2、吃东西3
buyDrink().then(buyPizza).then(eatMeal);//买饮料2、买披萨1、吃东西3
eatMeal().then(buyPizza).then(buyDrink);//吃东西3、买披萨1、买饮料2
```
## await和async

await和async也是解决异步嵌套的一个方法，它建立在promise的基础上
[promise&&await&&deferred和event loop](https://github.com/Wscats/node-tutorial/issues/12)

```
(async function(){
    var drink = await buyDrink();
    var pizza = await buyPizza();
    var eat = await eatMeal();
})();
```



## event和观察者模式

```js
myEmitter.on('buypiza', () => {
    setTimeout(() => {
        console.log('买披萨');
        myEmitter.emit('eatpiza');
    }, 1000)
});

myEmitter.on('eatpiza', () => {
    setTimeout(() => {
        console.log('吃披萨');
        myEmitter.emit('meeting');
    }, 2000)
});

myEmitter.on('meeting', () => {
    console.log('约会');
});
myEmitter.emit('buypiza');
```

# node-wifi

```js
var wifi = require('node-wifi');
```

# request

以前我们是自己写前端页面触发ajax请求来获取后端信息，但是有了request模块，我们可以伪造这个ajax请求。

ajax一次一次发

nodejs伪造请求，只要碰对了，就可以了。

措施，验证码，超3次锁死



任何前端请求都有

请求头（浏览器信息），请求体（POST请求，请求参数会放在这个地方）   用户不可见的

响应头（服务器信息），响应体（页面的内容，用户可见）

- [request模块文档](https://www.npmjs.com/package/request)

### 爬虫（获取歌曲）

爬取网站的内容，然后可以保存到本地，或者分析页面获取有价值的信息
```js
var request = require('request');
var fs = require('fs');
request('http://www.umei.cc/', function(error, response, body) {
	//console.log('error:', error); // Print the error if one occurred
	//console.log('statusCode:', response && response.statusCode); // Print the response status code if a response was received
	console.log('body:', body); // Print the HTML for the Google homepage.
//	fs.writeFile('test.html', body, function(err) {
//		console.log("成功保存")
//	})
});
console.log("开始请求");
```


# cheerio

- [cheerio使用文档](https://www.npmjs.com/package/cheerio)

实现网页内容分析，用法类似于jQuery，node版本jQuery,可以用它爬取文字，图片，音频

```js
var request = require('request');
var cheerio = require('cheerio');
request('http://www.umei.cc/', function(error, response, body) {
	//console.log('body:', body); // Print the HTML for the Google homepage.
	const $ = cheerio.load(body);
	$("img").each((i,e)=>{
		console.log($(e).attr("src"))
	})
});

console.log("开始请求");
```

并非所有网站都是能爬，有些网站是防爬虫，还有一些网页是前端JS动态生成

