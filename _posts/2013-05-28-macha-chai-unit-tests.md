---
layout: post
title: "macha chai 单元测试"
description: "mocha作为测试框架,使用Chai作为断言工具….咖啡和茶…."
category: 单元测试
tags: [单元测试,mocha,chai]
---
{% include JB/setup %}

# mocha作为测试框架,使用Chai作为断言工具….咖啡和茶….

---

## mocha和chai的基本介绍

![mocha](http://ww4.sinaimg.cn/large/71aff86bjw1e537njj257j20cy04tweg.jpg)

Mocha是一个功能丰富的JavaScript测试框架,可以运行在node和浏览器上，使异步测试简单而有趣。Mocha允许灵活和准确的报告，可以把没有捕获的到的一样,映射到正确的测试用例上,因此可以可以顺畅的运行.   托管在[GitHub](http://github.com/visionmedia/mocha)上.

Mocha可以搭配任何断言类库使用，它只关注测试过程中有没有抛出异常.

![chai](http://ww2.sinaimg.cn/large/71aff86bjw1e537p4aei4j206x01tdfp.jpg)

Chai 是一个BDD/TDD模式的断言库.可以在node和浏览器环境下运行.可以高效的和任何js侧测试框架进行搭配.

Chai有很多种接口,可以让码农找到自己觉得舒服的方式..链式引用的BDD风格..提供了一种近乎于自然语言和可读性很强的断言风格..和TDD的"古典风格"形成了鲜明的对比…其实我觉得..还是用BDD吧..TDD快被人黑出翔了…

其实…我发现…貌似..就..没有什么文档可以看….只能看写别人写的博客,和github上使用Mocha测试的项目的tests目录来看看了

---
## why 单元测试….why mocha和chai

如果没有单元测试这种可以验证程序正确性的代码片段，难道每次都要起一个完整的系统才能观察修改效果？更别说单元测试可以减少以后来回返工的可能了，有了单元测试，无论是进行修改和提交都令人放心地很。

但既然要同时在Node.js和浏览器里进行测试，那么很自然的选择便是使用同时支持两者的单元测试工具。Jscex使用Mocha 作为单元测试框架，并使用Chai 作为断言类库

，而Chai只关注各种断言API，并再断言失败的时候抛出异常。



---

## HOW Mocha 和 chai 在node和浏览器端工作….


### 安装 和 使用

#### node.js中

```
	$ npm install -g mocha //mac下要用sudo...不知为何..
	$ npm install chai   //断言库
	$ mkdir test
	$ subl test/test.js	
	$ mocha	//或者你写上文件名
```

在Node.js中使用这两个组件基本只需require进来

```
require("chai").should();
//这里连Mocha的组件就不需要，因为在Node.js环境下，Mocha更像是一个“执行环境”，我们在安装了Mocha之后，会使用mocha命令，而不是node命令执行测试脚本
```
	
	
test.js的内容
	
	var assert = require("assert") //加载断言的库 chai吧~
	
	//定义一组测试
	describe('Array', function(){
	  describe('#indexOf()', function(){
	  	//某一类的测试
	    it('should return -1 when the value is not present', function(){
	    //测试的逻辑
	      assert.equal(-1, [1,2,3].indexOf(5));
	      assert.equal(-1, [1,2,3].indexOf(0));
	    })
	  })
	})


#### 浏览器中

在浏览器里使用Mocha时需要额外引入一个CSS文件，并在页面上放置一个id为mocha的div。同时，我们还需要使用代码设置Mocha的单元测试模式（例如上面是BDD模式），以及Chai的断言模式（例如上面是Should模式）。使用这种方式，便能得到一张漂亮的单元测试页面，它甚至可以查看当前测试的代码：

```
<html>
<head>
    <meta charset="utf-8">
    <title>Unit Testing with Mocha and Chai</title>
    <!-- Mocha -->
    <link rel="stylesheet" href="mocha.css" />
    <script src="mocha.js"></script>
    <script>mocha.setup('bdd');</script>
    <!-- Chai -->
    <script src="chai.js"></script>
    <script>chai.Should();</script>
</head>
<body>
    <div id="mocha"></div>
    <script>
    //这里可以引入你的test.js文件..
       describe("Array", function () {
            it("should return true for array", function () {
                _.isArray([]).should.equal(true);
            });
            ... more tests ...
        });
        ... more tests ...
    </script>
    <script>
        mocha.run();
    </script>
</body>
</html>
```

---

### interface

首先可能要先了解几个概念: `TDD, BDD.`

** TTD: **

TDD指的是`Test Drive Development`，很明显的意思是测试驱动开发，也就是说我们可以从测试的角度来检验整个项目。 

`适用于业务逻辑不是很依赖的项目.解耦性强的.`

- 先针对每个功能点抽象出接口代码
- 然后编写单元测试代码，接下来实现接口，运行单元测试代码
- 循环此过程，直到整个单元测试都通过

** BDD **

BDD指的是`Behavior Drive Development`，也就是行为驱动开发。`注意力集中在程序行为（而不是输出）时获得的效果`

---

Mocha支持多种风格的interface， 比如。。 **BDD**, **TDD**, and **exports**。

其实。。。除了 BDD 的其他的都不想看。。。

#### BDD


` describe(), it(), before(), after(), beforeEach(), afterEach() `

就像这个样子。。。和自然语言一样的流畅。。。**好评！！！**

```
describe('Array', function(){
  before(function(){
    // ...
  });
  describe('#indexOf()', function(){
    it('should return -1 when not present', function(){
      [1,2,3].indexOf(4).should.equal(-1);
    });
  });
});
```

```
//或者...root级别的hooks..在全局状态下..每次的测试用例都执行了..
beforeEach(function(){
  console.log('before every test')
})
```

其他两个就不说了。。反正我不用。。。哇咔咔咔~~

---


### 同步代码的测试
当测试同步的代码时,Mocha将要无视callback顺序执行下去
例子

```
describe('Array', function(){
  describe('#indexOf()', function(){
    it('should return -1 when the value is not present', function(){
      [1,2,3].indexOf(5).should.equal(-1);
      [1,2,3].indexOf(0).should.equal(-1);
    })
  })
})
```

---

### 异步的代码的测试
在测试异步的代码的时候,调用一个回调函数在测试完成的时候,done()等这个函数执行完成之后,再继续测试

```
		describe('User', function(){
		  describe('#save()', function(){
		    it('should save without error',
		    	function(done){
		      		var user = new User('Luna');
		      		user.save(function(err){
		        		if (err) throw err;
		        		done();
		      		});
		      		//或者这样调用 done() callback接受错误,可简写为user.save(done); 
		    	})
		  	})
		})
```	

before(), after(), beforeEach(), afterEach() 可以让你把同步和异步的测试一样的处理,
举个栗子 哇咔咔 数据库链接的例子吧…每次链接的的开始,或者终止,都进行一次测试.

```
describe('Connection', function(){
  var db = new Connection
    , tobi = new User('tobi')
    , loki = new User('loki')
    , jane = new User('jane');
  beforeEach(function(done){
  //每一次之前…db.clear之前都会调用.
    db.clear(function(err){
      if (err) return done(err); //异步
      db.save([tobi, loki, jane], done); //异步
    });
  })
  describe('#find()', function(){
    it('respond with matching records', function(done){
      db.find({ type: 'User' }, function(err, res){
        if (err) return done(err);
        res.should.have.length(3);
        done();
      })
    })
  })
})
```


---
		
### pending tests
只是一个没有回调的describe就好

	describe('Array', function(){
	  describe('#indexOf()', function(){
	    it('should return -1 when the value is not present') //显示正确..
	  })
	})
---

### Exclusive tests 独有测试..  Inclusive tests 包含测试.

在describe.only()  it.only()

使这个测试用例具有排他性..

.skip() 无视这个测试用例...比安曾pending状态,

---

---

## chai的使用

Chai的BDD的风格有两种风格!:expect和should。

使用一样的链式语言来定义一个断言.

### expect

```
var expect = require('chai').expect
  , foo = 'bar'
  , beverages = { tea: [ 'chai', 'matcha', 'oolong' ] };
expect(foo).to.be.a('string');
expect(foo).to.equal('bar');
expect(foo).to.have.length(3);
expect(beverages).to.have.property('tea').with.length(3);
//定义输出: AssertionError: topic [answer]: expected 43 to equal 42.
expect(answer, 'topic [answer]').to.equal(42);
```

### should

```
var should = require('chai').should() //actually call the the function
  , foo = 'bar'
  , beverages = { tea: [ 'chai', 'matcha', 'oolong' ] };
foo.should.be.a('string');
foo.should.equal('bar');
foo.should.have.length(3);
beverages.should.have.property('tea').with.length(3);
```

有木有...should很自然….

还有一点..shoule可以在Object.prototyoe上检测.

例如说,你想测试一个对象是否存在
```
var should = require('chai').should();
db.get(1234, function (err, doc) {
  should.not.exist(err);
  should.exist(doc);
  doc.should.be.an('object');
});
```

- should.exist
- should.not.exist
- should.equal
- should.not.equal
- should.Throw
- should.not.Throw

碉堡了…

**chai.Assertion.includeStack = true;最后要注意..这里设置是否显示堆栈!!!**

---

### 参考资料

老赵的博文 推荐mocha和chai的配合进行测试 [链接](http://blog.zhaojie.me/2012/06/jscex-unit-tests-with-mocha-chai.html)
[mocha](http://visionmedia.github.io/mocha/)
[chai](http://chaijs.com/)


