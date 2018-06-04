# Object in JavaScript
---
## Object 从哪来
- 声明形式
```javascript
var myObj = {
	key: value
	// ...
};
```
- 构造形式
```javascript
var myObj = new Object();
myObj.key = value;
```
- 声明形式和构造形式两者产生完全相同的对象
---
## 类型
- object类型是javascript的6种主要类型中的一种
- string number boolean null undefined object
- 简单基本类型(string、number、boolean、null、和 undefined)自身不是object
```javascript
typeof null == object
```
- null此时会被当作一个object对象但是实际上他还是null自己的对象
---
## 复杂的基本类型
- 存在几种特殊的对象子类型
- **function**是对象的一种子类型, 技术上讲叫做"可调用对象"
- 函数在 JS 中被称为"头等[first class]"类型
- 因为它们基本上就是普通的对象(附带有可调用的行为语义) 而且它们可以像其他普通的对象那样被处理
- 数组也是一种形式的对象 带有特别的行为 在内容的组织上要稍稍比一般的对象更加结构化
---
### 内建对象
- String Number Boolean Object Function Array Date RegExp Error
- 在 JS 中，它们实际上仅仅是内建的函数
- 这些内建函数的每一个都可以被用作构造器(也就是一个可以通过 new 操作符调用的函数) 
- 其结果是一个新 构建 的相应子类型的对象
---
#### example
```javascript
var strPrimitive = "I am a string";
typeof strPrimitive;							// "string"
strPrimitive instanceof String;					// false

var strObject = new String( "I am a string" );
typeof strObject; 								// "object"
strObject instanceof String;					// true

// 考察 object 子类型
Object.prototype.toString.call( strObject );	// [object String]
```
---
#### explanation
- 基本类型值"I am a string"不是一个对象
- 是一个不可变的基本字面值
- 为了对它进行操作(检查长度,访问它的各个独立字符内容),需要一个String对象去实现
---
### Consider this
```javascript
var strPrimitive = "I am a string";

console.log( strPrimitive.length );			// 13

console.log( strPrimitive.charAt( 3 ) );
```
- 在字符串的基本类型上调用属性和方法 引擎会自动地将它强制转换为 String 对象 所以这些属性/方法的访问可以工作
---
## Content 内容
- 对象的内容由存储在特定命名的位置上的(任意类型的)值组成，我们称这些值为属性
- 看起来:当说"内容"时 似乎暗示着这些值存储在对象内部 但那只不过是表面现象
- 实际上:引擎会根据自己的实现来存储这些值 而且通常都不是把它们存储在容器对象内部
- 在容器内存储的是这些属性的名称 它们像指针(实际上是引用Reference)一样指向值存储的地方
---
### consider
```javascript
var myObject = {
	a: 2
};

myObject.a;		// 2

myObject["a"];	// 2
```
- .a 语法通常称为属性[property]访问
- ["a"]语法通常称为键[key]访问
---
### ["..."]的优势
```javascript
var wantA = true;
var myObject = {
	a: 2
};

var idx;

if (wantA) {
	idx = "a";
}

console.log( myObject[idx] ); // 2
```
- 程序可以动态地组建字符串的值
---
### rules
```javascript
var myObject = { };

myObject[true] = "foo";
myObject[3] = "bar";
myObject[myObject] = "baz";

myObject["true"];				// "foo"
myObject["3"];					// "bar"
myObject["[object Object]"];	// "baz"
```
- 在对象中属性名总是字符串
- 如果你使用 string 以外的(基本)类型值它会首先被转换为字符串
### 计算型属性名
- 如果需要将一个计算表达式作为一个键名称 那么刚刚描述的 myObject[..] 属性访问语法是十分有用的
- ES6 加入了计算型属性名 在一个字面对象声明的键名称位置可以指定一个表达式[]括起来
```javascript
var prefix = "foo";

var myObject = {
	[prefix + "bar"]: "hello",
	[prefix + "baz"]: "world"
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```
---
#### Symbol in  计算属性名
- 计算型属性名的最常见用法 可能是用于ES6的Symbol
```javascript
var myObject = {
	[Symbol.Something]: "hello world"
};
```
---
## 属性（Property） vs. 方法（Method）
- 如果在访问到一个对象的属性是一个函数的情况下 人们倾向于在此时对一个对象的属性访问作出区分 是 属性还是方法
- 这诱使人们认为函数属于这个对象 而且在其他语言中 属于对象（也就是"类"）的函数被称作"方法" 这是很常见的事情
- 有些函数内部确实拥有this引用 而且有时这些 this 引用指向调用点的对象引用
- 但这个用法确实没有使这个函数比其他函数更像"方法" 因为**this**是在运行时在调用点动态绑定的 这使得它与这个对象的关系至多是间接的
---
### consider
- 无论从对象里得到什么类型的值 每次访问一个对象的属性都是一个[属性访问]

```javascript
function foo() {
	console.log( "foo" );
}

var someFoo = foo;	// 对 `foo` 的变量引用


var myObject = {
	someFoo: foo
};

foo;				// function foo(){..}

someFoo;			// function foo(){..}

myObject.someFoo;	// function foo(){..}
```

---
### explanation
- someFoo 和 myObject.someFoo 只不过是同一个函数的两个分离的引用
- 它们中的任何一个都不意味着这个函数很特别或被其他对象所"拥有"
```javascript
var myObject = {
	foo: function foo() {
		console.log( "foo" );
	}
};

var someFoo = myObject.foo;

someFoo;		// function foo(){..}

myObject.foo;	// function foo(){..}
```
- 即使声明一个函数表达式作为字面对象的一部分 那个函数也不会属于这个对象 —— 仍然仅仅是同一个函数对象的多个引用
---
### Array 数组
- 存储值的方式和位置上它们的组织更加结构化
- 在存储值的**类型**上没有限制
- 数组采用数字索引 意味着值被存储的位置通常称为下标 一个非负整数
```javascript
var myArray = [ "foo", 42, "bar" ];

myArray.length;		// 3

myArray[0];			// "foo"

myArray[2];			// "bar"
```
---
### 在数组上添加属性
```javascript
var myArray = [ "foo", 42, "bar" ];

myArray.baz = "baz";

myArray.length;	// 3

myArray.baz;	// "baz"
console.log(myArray.length);

```
- 即使你在数组内添加属性也不会修改数组的长度length
---
### 小心
- 如果你试图在一个数组上添加属性 但是属性名看起来像一个数字
- 那么最终它会成为一个数字索引 aka改变了数组的内容
```javascript
var myArray = [ "foo", 42, "bar" ];

myArray["3"] = "baz";

myArray.length;	// the length increase to 4

myArray[3];		// "baz"
```
---

  
