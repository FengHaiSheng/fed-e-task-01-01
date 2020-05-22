一、简单题

### 1、请说出下列最终的执行结果，并解释为什么？

```
var a = [];
for (var i = 0; i < 10; i++) {
	a[i] = function () {
		console.log(i);
	}
}

a[6]();
```

1. 答案：10
2. 解释：因为var声明的变量的作用域是它当前的执行上下文, a[i]被赋值的函数产生了闭包，10个函数中访问到的都是相同的变量i，`a[6]()`是在for循环之后执行的，所以打印的i是最终的值10


### 2. 请说出下列最终的执行结果，并解释为什么？

```
var tmp = 123;

if (true) {
	console.log(tmp);
	let tmp;
}
```

1. 答案：报错，ReferenceError: tmp is not defined
2. 解释：let声明的变量作用域当前块级作用域，也就是if语句之内，而且let没有声明提升，所以打印tmp时会报错

### 3. 结合ES6新语法，用最简单的方式找出数组中的最小值？

```
var arr = [12, 34, 32, 89, 4];
```

答案：Math.min(...arr)


### 4. 请详细说明var，let、const三种声明变量的方式之间的具体差别？

1. var有作用域声明提升，声明之前就能访问到, 只不过是undefined；let、const没有作用域声明提升，声明之前都不能访问到，会报错
2. var声明的变量能在块级作用域之外使用；let、const的只能在块级作用域之内使用
3. const声明一个只读常量，声明时必须进行初始化定义值，如果这个值是对象，可以为对象内增删改查属性；var、let声明的变量可以随意更改，并且不需要声明时就初始化
4. 语法上，var可以重复声明同名变量；let、const只能声明一次同名变量

### 5. 请说出下列代码最终输出的结果，并解释为什么？

```
var a = 10;
var obj = {
	a: 20,
	fn: () => {
		setTimeout(() => {
			console.log(this.a);
		})
	}
}
```

1. 答案：浏览器环境中是10；node xx.js时，是undefined；node开个REPL时，是10;
2. 解释：
	1. 浏览器环境中，全局作用域下用var声明的变量，最后都属于window，所以啊a、obj都在window中。fn是箭头函数，setTimeout内的也是箭头函数，所以this.a中的this指向的是执行obj的环境对象，也就是window。
	2. node xxx.js时，var声明的变量也不会在global中，this指向的是当前模块为{}, 所以this.a打印的是undefined
	3. node开个REPL时, this指向global，var声明的变量会贴在global中，所以最终this.a打印的是10

### 6. 简述Symbol有哪些用途

1. 表示一个独一无二的值
2. 解决对象不能使用非字符串类型当做键值的问题
3. 为对象添加私有成员
4. 对不可以迭代的数据类型(不可以使用for...in)，添加可迭代接口([Symbol.iterator]、[Symbol.asyncIterator])
5. 用Symbol.hasInstance来自定义 instanceof 操作符在某个类上的行为
6. 用Symbol.toStringTag来更改Object.prototype.toString()的结果
7. 用Symbol.toPrimitive来自定义对象转化为对应的原始值时的返回值

### 7. 说说什么是浅拷贝，什么是深拷贝？

1. 浅拷贝: 在拷贝时，如果遇到引用对象A，拷贝的新对象与老对象都引用了同一个对象A，当对象A发生变化时，拷贝前后的新老对象内的这个元素都会发生变化。
2. 深拷贝：在拷贝时，如果遇到引用对象A，拷贝后的新对象会从对象A复制一份新的对象B，所以对象A发生变化后，拷贝后的新对象不会受到影响。如果对象A内也存在引用对象，深拷贝也会逐层往下去进行复制一份全新的引用。

### 8. 谈谈你是如何理解JS异步编程的，Event Loop 是做什么的，什么是宏任务，什么是微任务？

1. JS异步编程

JS是单线程的。早期的JS主要做DOM操作，此时的单线程可以保障安全。随着JS的发展，出现了执行时间长的任务，单线程的模式就会导致阻塞，后续代码不能立马执行，页面上的表现就是卡住了。
JS异步编程就是用来解决长时间任务造成的卡顿的，它以异步回调的方式，让单线程的JS语法可以同时处理大量耗时任务，而不阻塞后续逻辑。

2. Event Loop

它负责监听"调用栈"和"执行队列"，当"调用栈"中的任务结束了，就会从"执行队列"中取出第一个任务压入到"调用栈"，开始执行。

主线程执行代码时:
	1. 遇到同步任务时，就入栈执行然后出栈；
	2. 遇到异步任务时，它们一般是执行环境的API，就交由执行环节处理，比如Node下的I/O，Web下的setTimeout。当异步任务结束后，就会将回调函数放入“执行队列”中

异步任务并不是等待同步任务执行完成后才开始执行的，它一直在执行，并且执行完之后就会把回调函数放入“执行队列”中；
只不过JS单线程要等待同步任务执行完之后才能去“执行队列”中拿取异步任务的回调函数进行执行。

3. 宏任务与微任务

执行宿主发起的任务是宏任务。JS引擎发起的任务是微任务；宏任务中包含了许多微任务。Event Loop相当于在一直拿取宏任务进行执行

注意：Promise产生的是JS引擎的微任务，永远在当前执行栈尾部添加微观任务

### 9. 将下面异步代码使用Promise改进？

```
setTimeout(function () {
	var a = 'hello';
	setTimeout(function () {
		var b = 'lagou';
		setTimeout(function () {
			var c = 'I 💖 U';
			console.log(a + b + c);
		}, 10);
	}, 10);
}, 10);
```

答案：
```
new Promise((resolve, reject) => {
	setTimeout(() => resolve('hello'), 10);
}).then(a => {
	return new Promise((resolve, reject) => {
		setTimeout(() => resolve(a + 'lagou'), 10);
	});
}).then(b => {
	setTimeout(() => {
		console.log(b + 'I 💖 U');
	}, 10);
});
```

### 10、请简述TypeScript与JavaScript之间的关系

1. TypeScript拓展了JavaSript的类型和面向对象；
2. TypeScript完全可以写纯原生的JavaScript语法，因为它是渐进式的
3. 在不用Babel的情况下，typescript帮助我们更好的使用ECMASCript的新特性

### 11、请谈谈你所认为的TypeScript优缺点？

优点：
1. typescript帮助我们更好的使用ECMASCript的新特性
2. 功能更强大，生态也更健全、更完善
3. 更优秀的类型系统，和面向对象编程的特性
4. 它是渐进式的，内部单纯的写JS语法也是可以的
5. 可以在书写阶段就发现问题，避免指导运行时才发现错误

缺点：
1. 相对于JS多了很多概念，比如接口、泛型、枚举、抽象类，所以学习成本高，在项目初期会增加一些成本
2. 不适合短期的小众项目，只适合长期的项目


