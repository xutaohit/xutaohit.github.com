---
layout: post
title: "ARC_iOS_Object-c.md"
description: "ios的开发学习只内存管理..关于ARC"
category: ios
tags: [ios, arc, object-c]
---
{% include JB/setup %}


# iOS/MAC 开发中的ARC问题。。

刚刚看完APRESS的那本 OC基础教程，其实把 我觉得不同的语言或者平台，相同的东西很多。。特别是对于现在的计算机或者软件专业的学生来说，我们大学期间涉及的技术种类很多。。可以说是课程的目标是让我们先通后精。。好吧。。起码我们专业是这个样子。。很多种类的语言都涉及的好处就是接触到一门新的语言，上手可能很快，这门语言无非就是动态的或者静态的，不是面向对象，就是面向过程，不是脚本语言，就是编译语言。。。除非是很奇葩的Lisp确实没有接触过，我觉得最近的语言貌似都不是很难学。。。

看完了OC ，我就觉得，要留意的只有几点：

1. 面向对象这方面，不同的语言具体的要求有些不同，顶多是是否允许多重继承，和一些实现上面的不同。。
2. 内存管理。。。
3. 具体的平台和平台使用的库

先讲讲
![进化](http://ww1.sinaimg.cn/large/71aff86bjw1e61bg3jhxoj20xp09iwg6.jpg)

## 传统内存管理.

所谓的内存管理,其实吧我觉得最主要就是理解OC内存管理中的Reference Counting机制…其实很多语言的内存管理都是这样的..像我了解的pythone和Ruby都是这样的,不同的是..后两者都是自动的..对人家是自动的…还好ARC的出现改变了这种悲催的情况.
但是呢,有很多人又开始担心内存管理不在自己的手上会出问题…其实吧…我觉得,要是对ARC有了深入的了解就回打消这个年头..而且..要相信apple的能力.



#### 原理 

- oc中对象生于堆,需要一个指针引用,
- 当不使用的时候,需要调用dealloc来释放空间,否则就内存泄露了, 而且还有一个问题,两个指针指向一个对象的时候,一个指针调用了 dealloc,那另外一个指针回怎样?无效指针…  so 我们需要一个机制来避免这个问题.
- 这个机制就是引用计数规则, 每一个生成的对象都保留了一个数字,表示了被引用的次数…如果被两个对象应用 他的retain数就是2~ 需要销毁的时候,调用的不是dealloc而是 release~表示这个指针,对这个对象解除了契约! 这个对象自己也知趣的把自己的引用数-1,只道等于0的时候,就自动的调用dealloc了
- 但是呢!!!!尼玛这个过程要手动…自己来retain...除非是用alloc来生成的,通过赋值的方式引用的对象指针都需要手动的调用 retain…
- 是不是有点麻烦呢…其实..还是有点优化(这点优化和ARC相比就是渣渣…)这就是autorelease pool自动释放池子, 在遵循某种规则的情况下.可以自动释放对象的引用.ClassA *obj1 = [[[ClassA alloc] init] autorelease]; //retain count = 1 但无需调用release,但是通过赋值的那个还是要手动的release...

另起一坨讲讲 这个池子的原理…

- 其实吧,人家不叫autorelease pool  是 'NSAutoreleasePool' NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];一般你建立项目的时候就有了...xcode做的.
- NSAutoreleasePool内部维护一个数组:NSMutableArray 用来保存声明为autorelease的所有的对象..
- 当NSAutoreleasePool自身在销毁的时候,先遍历这个数组,release数组中的每一个成员.if此时成员的retain count == 1
release之后就被销毁了,当>1的时候…你rerelease之后还是不等于0,就内存泄露了!!!!!
- 可以嵌套使用
- NSRunLoop 可以自动生成NSAutoreleasePool

还有就是在 @property (retain)和@synthesize的默认实现

```
@interface ClassA : NSObject{
	ClassB *objB;
}
-(ClassB *) getObjB;
-(void) setObjB:(ClassB *) value;
@end
@implementation ClassA
-(ClassB*) getObjB{
    return objB;
}
-(void) setObjB:(ClassB*) value{
	if (objB != value){
  		[objB release];    //先取消再重新赋值并且添加retain
      objB = [value retain];
  }
}
-(void) dealloc
{//由于我们在class内部retain了一次（虽然是默认实现的），所以我们要在dealloc方法中release这个成员变量
	[objB release];
  	[super dealloc];
}
```


#### 怎么做
传统的内存管理就是手动的内存管理,MRC好吧..其实只要做到三点,就可以比年内存泄露或者引用未知内存:

1. if需要保持一个对象,就对其发送retain
2. 不用了就release or autorelease
3. 对于每一个retain,alloc,和new的调用,都要有对应的release和autorelease对应!

但是呢...说的简单..只知道这些规则..难免在实际的使用中会犯错…那个屎代..有很多的插件可以帮助开发者发现问题..

## 啥是ACR

擦擦擦...说了这么久终于说到了ARC了…..

Automatic Reference Counting，自动引用计数，即ARC，WWDC2011年iOS5引入的,LLVM3.0编译器的特性.

###　怎么使用？　

在工程中使用ARC非常简单：只需要像往常那样编写代码，只不过永远不写`retain`,`release`和`autorelease`三个关键字就好～

ARC会自动在需要的地方插入这三个关键字.

但是我们怎么知道他是不是被正确的插入了呢...而且..效率如何…?

###  ARC工作原理

ARC是Objective-C编译器的特性，而不是运行时特性或者垃圾回收机制，ARC所做的只不过是在代码编译时为你自动在合适的位置插入release或autorelease，就如同之前MRC时你所做的那样。因此，至少在效率上ARC机制是不会比MRC弱的，而因为可以在最合适的地方完成引用计数的维护，以及部分优化，使用ARC甚至能比MRC取得更高的运行效率.(你总不会自信的认为,你做的能比编译器做的好吧…突然想到了有人在写CPP的时候不用STL而自己写…一个道理…)

#### ARC的工作机制…

用一个指针指向这个对象，只要指针没有被置空，对象就会一直保持在堆上。当将指针指向新值时，原来的对象会被release一次。(对于实例变量,synthesize变量和局部变量都有用..)

---

解释下:


	//这里我们吧输入框中的问题,引用给colleague这个指针.
	NSString *colleague = self.textField.text;

	
![step1](http://ww2.sinaimg.cn/large/71aff86bjw1e61g5vapgtj20bk06v3yk.jpg)

如图所示想在..两个指针指向的一个对象...现在这个对象的retainCount=2了!

---

突然!!!textField中输入了其他的对象:

![step2](http://ww2.sinaimg.cn/large/71aff86bjw1e61g9v2m9qj20ec07ejrl.jpg)

这个时候ARC自动为我们修改不同对象的引用计数…春寒的yin'yong引用改成了1,同样,文彬的引用改成了1~

---

但是,物是人非,当colleague指向别的的时候(新值,超出了作用域,),春寒这个对象就会被ARC进行回收.内存释放.
![step3](http://ww4.sinaimg.cn/large/71aff86bjw1e61ggip3rgj20e906nq38.jpg)

---

类似于colleague和self.textField.text这样的指针使用关键字strong进行标志，它意味着只要该指针指向某个对象，那么这个对象就不会被销毁。

反过来说，ARC的一个基本规则即是，只要某个对象被任一strong指针指向，那么它将不会被销毁。

如果对象没有被任何strong指针指向，那么就将被销毁。在默认情况下，所有的实例变量和局部变量都是strong类型的。

----

但是呢...有个weak类型的 `weak` 类型的指针也可以指向对象，但是并不会持有该对象.


	__weak NSString *weakName = self.textField.text

	
![弱类型的引用](http://ww3.sinaimg.cn/large/71aff86bjw1e61i0qotzqj20a106ywei.jpg)

这里声明了一个weak的指针weakName，它并不持有@“呵呵”。如果self.textField.text的内容发生改变的话，根据之前提到的“只要某个对象被任一strong指针指向，那么它将不会被销毁。

如果对象没有被任何strong指针指向，那么就将被销毁”原则，此时指向@“呵呵”的指针中没有strong类型的指针，@”onevcat”将被销毁。

同时，在ARC机制作用下，所有指向这个对象的weak指针将被置为nil。这个特性相当有用，相信无数的开发者都曾经被指针指向已释放对象所造成的EXC_BAD_ACCESS困扰过，使用ARC以后，不论是strong还是weak类型的指针，都不再会指向一个dealloced的对象，从根源上解决了意外释放导致的crash。

![消除后](http://ww2.sinaimg.cn/large/71aff86bjw1e61i4wigq9j20cu0b574k.jpg)

---

不过呢...我是觉得我不是很常用这样的类型:

有一个比较常用的情景:两个对象之间的包含关系…对象一`strong`指向对象2,并且占有之, 对象2 有个`weak`的指针指回对象1, 避免了循环持有,造成引用计数的混乱….和委托模式很像吧..

这里借用网上一个图:

![例子!](http://ww3.sinaimg.cn/large/71aff86bjw1e61i9oowlbj20nd08ogm8.jpg)

viewController中有一个strong指针指向它所负责管理的UITableView，而UITableView中的dataSource和delegate指针都是指向viewController的weak指针。

可以说，weak指针的行为和MRC时代的assign有一些相似点，

但是考虑到weak指针更聪明些（会自动指向nil)

---

property也可以用strong或weak来标记，简单地把原来写retain和assign的地方替换成strong或者weak就可以了。


	@property (nonatomic, strong) NSString *firstName; 
	@property (nonatomic, weak) id delegate; 

	
	
#### ARC的缺陷…

- 只适用于object-c的对象(继承自NSObject的对象.) 底层的无力...malloc()free()等…自行料理…
- 不能忘记内存管理...`strong`类型的指针在不用的时候,要手动设置为nil.ARC不会帮你做这个...
