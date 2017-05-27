关于const let var 三个声明变量的区别
=================================

在研究别人代码的时候看到变量可以通过const来声明，赶紧恶补一下。发现js中声明变量有三种方式 const let var 。下面记录下这三种声明方式的用法和区别。

## const

语法：

```js

const name1 = value1 [, name2 = value2 [, ... [, nameN = valueN]]];

```

> 语法：

* nameN --- 常量名称，可以使任意合法的identifer(标识符)
* valueN --- 常量值，可以使任意合法的表达式

> 描述：
> 这个声明创建了一个常量，可以在全局作用域或者函数内部声明常量，常量需要被初始化。这就是说，在定义常量的同事必须初始化（这是有意义的，鉴于常量的值在初始化后就不能改变）。
> 
> 常量拥有块作用域，和使用let定义的变量十分相似。常量的值不能通过在复制改变，也不能再次声明。
> 
>一个常量不能和它所在作用于内的其他变量或函数拥有相同的名称

```js

// 注意：常量在声明的时候可以用大小写，通常情况下会使用全部大写英文

// 定义常量MY_FAV并复制7
const MY_FAV = 7;

// 报错
MY_FAV = 20;

// 输出7
console.log("my favorite number is: "  + MY_FAV);

// 尝试重新声明会报错
const MY_VAV = 20;

// 也会报错
let MY_FAV = 20;

// 注意块范围的性质很重要
if(MY_FAV === 7){
	// 没问题，并创建一个块作用域变量 MY_FAV
	// (works equally well with let to declare a block scoped non const varibale)
	let MY_FAV = 20;

	// MY_FAV 现在为 20
	console.log('my favorite number is ' + MY_FAV);
	
	// 这被提升到全局上下文并引发错误
	var MY_FAV = 20;
}

// MY_FAV 依旧为 7 
console.log('my favorite number is ' + MY_FAV);

// 常量要求一个初始值
const FOO; // SyntaxError: missing = in const declaration

// 常量可以定义成对象
const MY_OBJECT = {'key': 'value'};

// 重写对象和上面一样失败
MY_OBJECT = {'OTHER_KEY': 'VALUE'};

// 对象属性并不在保护范围内，下面这个声明会执行成功
MY_OBJECT.key = 'otherValue';

// 也可以用来定义数组
const MY_ARRAY = [];
// It`s possible to push items into the array
// 可以向数组填充数据
MY_ARRAY.push('A'); // ['A']
// 但是，将一个新数组赋值给变量会引发错误
MY_ARRAY = ['B'];

``` 

## let

let语句声明一个块级作用域的本地变量，并且可选的将其初始化为一个值

语法：

```js

let name1 [= value1] [, name2 [= value2]] [,  ..., nameN [= valueN]];

```

### 参数：

* name1,name2,...nameN --- 变量名，可以使任意合法的标识符
* value1,value2,...valueN --- 变量的初始值，可以使任意合法的表达式。

###　作用域规则

let允许你声明一个作用域被限制在块级中的变量，语句或者表达式。与var关键字不同的是，var声明的变量只能是全局或者整个函数快

let名称的由来[https://stackoverflow.com/questions/37916940/why-was-the-name-let-chosen-for-block-scoped-variable-declarations-in-javascri](https://stackoverflow.com/questions/37916940/why-was-the-name-let-chosen-for-block-scoped-variable-declarations-in-javascri)

```js

function varTest() {
	var x = 1;
	if (true) {
		var x = 2; // 同样的变量！
		console.log(x); // 2
	}
	console.log(x); // 2
}

function letTest() {
	let x = 1;
	if (true) {
		let x = 2; // 不同的变量
		console.log(x); // 2
	}
	console.log(x); // 1
}

```

### let的暂存死去与错误

在同一个函数或同一个作用域中用let重复定义一个变量将引起TypeError

```js

if (x) {
	let foo;
	let foo; // TypeError thrown
}

```

在ECMAScript 2015中，let**将会提升**这个变量到语句块的顶部。然而，在这个语句块中，在变量声明之前引用这个变量会导致一个ReferenceError的结果，因为let变量在“暂存死区”（从块的开始到声明这段）。

```js

function do_something() {
	console.log(foo); //ReferenceError
	let foo = 2;
}

```

在switch声明中你可能会遇到这样的错误，因为一个switch只有一个作用快

```js

switch (x) {
	case 0:
		let foo;
		break;
	case 1:
		let foo; // TypeError for redecaration
		break;
}

```

let后跟一个函数传递的参数时将导致循环内部报错

```js

function go(n){
	for (let b of n.a) { // TypeError: n is undefined
		console.log(n);
	}
}

go({a:[1,2,3]});

```

### 循环定义中的let作用域

循环体中是可以引用在for声明时用let定义的变量，尽管let不是出现在大括号之间（注：该方法在火狐45.4.0，Centos7下，报错ReferenceError:can`t acccess lexical declaration i' before initialization）

```js

var i = 0;
for (let i = 1; i < 10; i += 1){
	console.log(i);
}

```

**注：以上声明的i将会变成undefined；chrome版本50.0.2661.102（64-bit）；推荐一下写法**

```js

var i = 0;
for (let l = i; l < 10; l += 1){
	console.log(l);
}

```

### 域作用规则

```js

for (let expr1; expr2; expr3) statement

```

在这个例子中，expr2,expr3和statement都是包含在一个隐含域块中，其中也包含了expr1

### let 对比 var

let的作用域是块，而var的作用域是函数

```js

var a = 5;
var b = 10;

if(a === 5){
	let a = 4; // The scope is inside the if-block
	var b = 1; // the scope is inside the function

	console.log(a); // 4
	console.log(b); // 1 
}

console.log(a); // 5
console.log(b); // 1

```

### let 在循环中

可以用let来替代var，在for定义块中使用块级变量

```js

for (let i = 0; i < 10; i += 1) {
	console.log(i); // 0,1,2,3,4,5,6,7,8,9
}
console.log(i); // i is not defined

```

### var  

变量语句声明一个变量，可选地将其初始化为一个值。

### 语法

```js

var name1 [= value1 [, name2 [, name3 ... [, nameN]]]];

```

* nameN --- 变量名，可以定义为任何合法标识符
* valueN --- 变量的初始化，改值可以为任何合法表达式

### 描述

变量声明无论出现在代码的任何位置，都会在任何代码执行之前。使用var语句声明的变量的最庸域是当前执行位置的上下文：一个函数的内部（声明的函数内）或者全局（声明在函数外）。

给一个非生命变量赋值会隐式创建一个全局变量（全局object的一个属性）。声明变量和非生命变量的区别：

1. 声明变量的作用域限制在其声明位置的上下文中，而非生命变量总是全局的

```js

function x() {
	y = 1; //严格模式（strict mode）下会抛出ReferenceError
	var z = 2;
}

x();

console.log(y); // 打印'1'
console.log(z); // 抛出ReferenceError：z为在x外部声明

```

2. 声明变量在任何代码执行前创建，而非声明变量只有在执行赋值操作的时候才会被创建。

```js

console.log(a); // 抛出ReferenceError
console.log('string going ...'); // 永不执行

```

```js

var a;
console.log(a); // 打印'undefined'或''（不同浏览器实现不同）
console.log('still going ...'); // 打印'still going ...'

```

3. 声明变量是它所在上下文环境的不可配置属性，非声明变量是可配置的（例如非生命变量可以被删除）

```js

var a = 1;
b = 2;

delete this a; // 在严格模式（strict mode）下抛出TypeError，其他情况下执行失败并无任何提示
delete this b;

console.log(a, b); // 抛出ReferenceError
// 'b'属性已经被删除

```

由于以上三个区别，声明变量的错误会导致不可预测的结果。于是，**无论是否在函数中，我们总是推荐1使用声明变量**。在ECMAScript 5的严格模式（strict mode）下，给一个为声明的变量赋值会抛出错误。

### 变量声明提升 （var hoisting）

由于变量声明（以及其他声明）总是在任意代码执行之前处理的，所以在代码中的任意位置声明变量总是等效于在代码开头声明。这意味着变量可以在声明之前使用，这个行为叫做“hoisting”。“hoisting”就像是把所有的变量声明移动到函数或者全局代码的开头位置。

```js

bla = 2;
var bla;

// 可理解为
var bla;
bla = 2;

```

由于这个原因，我们建议是在作用域的最开始（函数或者全局代码的开头）声明变量。这样可以使变量的作用域变得清晰。

### 声明并初始两个变量

```js

var a = 0, b = 0;

```

### 改两个变量赋值成字符串

```js

var a = 'A';
var b = a;

// 等效于
var a, b = a = 'A';

```

留意其中的顺序：

```js

var x = y, y = 'A';
console.log(x + y); // undefinedA

```

在这里，x和y在代码执行之前已经创建了，而赋值操作发生在创建之后。当“x = y”执行时，y已经存在，所以不抛出ReferenceError，并且它的值是“undefined”。所以x被赋予undefined值。然后，y被赋予“A”。于是在执行完第一行之后，x === undefined && y === 'A'才形成了undefinedA这样的结果。

[segmentfault javascript 连等赋值问题](https://segmentfault.com/q/1010000002637728)

### 多个变量的初始化

```js

var x = 0;
 
function f(){
	var x = y = 1; // x在函数内部声明，y不是！
}
f();

console.log(x, y); // 0, 1
// x是全局变量
// y是隐式声明的全局变量

```

### 隐式全局变量和外部函数作用域

看起来像是隐式全局作用域的变量也有可能是其他外部函数变量的引用。

```js

var x = 0; // x是全局变量，并且赋值为0；

console.log(typeof z); // undefined,因为z还不存在

function a() { // 当被调用时
	var y = 2; // y被声明成函数a作用域的变量，然后赋值为2
	
	console.log(x, y); // 0 2
	
	function b() { // 当b被调用时
		x = 3; // 全局变量x被赋值为3，不生成新的全局变量
		y = 4; // 已存在的外部函数的y变量被赋值为4，不生成新的全局变量
		z = 5; // 创建新的全局变量z，并且给赋值为5
		       // （在严格模式下（strict mode）会抛出ReferenceError）		
	}

	b();       // 调用b时创建了全局变量z
	console.log(x, y, z); // 3 4 5
}

a(); //调用a时同事调用了b
console.log(x, z); // 3 5
console.log(typeof y); // undefined，因为y是a函数的本地（local）变量。

```

## 参考资料：

*  https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/const
*  https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let
*  https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var
