# javaScript简介

## 特点

### 解释语言

类似于请客吃饭：
 编译语言：首先把所有菜做好，才能上
桌吃饭
 解释语言：好比吃火锅，边吃边涮，同
时进行

### **动态语言**

## 输出

```js
document.write("Hello,World!");
//向web控制台输出内容
console.log("输出一条日志");
//打印一个对象的所有属性和方法
console.dir(obj)
alert("Hello,World!");
prompt('请输入您的年龄');
```

## 注释

# 基础语法

## 标识符和字面量

### 字面量

字面量实际上就是一些固定的值，比如：1、2 、3、true、false、**null、NaN**、“hello”，字面量都是不可以改变的，由于字面量不是很方便使用，所以在JavaScript中很少直接使用字面量，使用的而是变量

## 变量

### 基本使用

**变量的声明：** 使用var关键字声明一个变量。

```javascript
var a;
```

**变量的赋值：** 使用=为变量赋值。

```javascript
a = 123;
```

**声明和赋值同时进行：**

```javascript
var a = 123;
//可以同时声明多个变量
var age = 10,  name = 'zs', sex = 2;
```

### **var 声明特点**

- 变量可以重复声明（覆盖原变量）。
- 变量未赋值时，默认值为 undefined。
- var 声明的变量会提升（Hoisting），但不会初始化。

### 声明特殊情况

![image-20250406112432295](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250406112432295.png)

### 变量命名规范

- 由字母(A-Za-z)、数字(0-9)、下划线(_)、美元符号( $ )组成，如：usrAge, num01, _name严格区分大小写。var app; 和 var App; 是两个变量
- 不能 以数字开头。 18age 是错误的
- 不能 是关键字、保留字。例如：var、for、while
- 变量名必须有意义。 MMD   BBD        nl   → age
- 遵守驼峰命名法。首字母小写，后面单词的首字母需要大写。 myFirstName

推荐翻译网站： 有道 爱词霸

## 数据类型

### 简介

JavaScript 是一种**弱类型或者说动态语言**。这意味着**不用提前声明变量的类型**，在程序运行过程中，类型会被自动确定。

```js
var age = 10;
// 这是一个数字型
var areYouOk = '是的'; // 这是一个字符串
```

在代码运行时，变量的数据类型是由 JS引擎 **==根据 = 右边变量值的数据类型==来判断 的，运行完毕之后， 变量就确定了数据类型。**

### typeof运算符

使用typeof操作符可以用来检查一个变量的数据类型。

```js
typeof 数据
```

### 分类

![image-20250406113438850](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250406113438850.png)

除了这些简单数据类型，还有一个object对象类型

#### Number

```js
var a = 1, b = 2;
```

Number 类型用来表示整数和浮点数，最常用的功能就是用来表示10进制的整数和浮点数。

##### **进制表示：**

- 二进制：0b 开头表示二进制，但是，并不是所有的浏览器都支持
- 八进制：0 开头表示八进制
- 十六进制：0x 开头表示十六进制

##### **数字范围**

Number表示的数字大小是有限的，如果超过了这个范围，则会返回 ±Infinity。

- 最大值：+1.7976931348623157e+308
- 最小值：-1.7976931348623157e+308
- 0以上的最小值：5e-324

##### **特殊的数字**

- Infinity：正无穷
- -Infinity：负无穷
- NaN：非法数字（Not A Number）

##### isNaN()函数

用来判断一个变量是否为非数字的类型，返回 true 或者 false

#### String

```js
var strMsg = "我爱北京天安门~";  // 使用双引号表示字符串
var strMsg2 = '我爱吃猪蹄~';    // 使用单引号表示字符串
```

因为 HTML 标签里面的属性使用的是双引号，JS 这里我们更推荐使用单引号。

##### 转义字符

![image-20250406114339992](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250406114339992.png)

##### 字符串长度

字符串是由若干字符组成的，这些字符的数量就是字符串的长度。通过字符串的 **length 属性**可以获取整个字符串的长度。

```js
var strMsg = "我是帅气多金的程序猿！";
alert(strMsg.length); // 显示 11
```

##### 字符串拼接

- 多个字符串之间可以使用 + 进行拼接，其**拼接方式为==字符串 + 任何类型 = 拼接之后的新字符串==**
- **拼接前会把与字符串相加的任何类型转成字符串**，再拼接成一个新的字符串

```js
alert('11' + 12); // 1112
```



#### Boolean

布尔型也被称为逻辑值类型或者真假值类型。

布尔型只能够取真（true）和假（false）两种数值。除此以外， 其它的值都不被支持。

#### Undefined和Null

Undefined 类型只有一个值，即特殊的 undefined。

**在使用 var 声明变量但未对其加以初始化时，这个变量的值就是 undefined。**

undefined值实际上是由null值衍生出来的，所以如果比较undefined和null是否相等，会返回true。**从语义上看null表示的是一个空的对象，所以使用typeof检查null会返回一个Object。**

### 强制类型转换

#### 转换为字符串

![image-20250406115038690](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250406115038690.png)

#### 转换为数字型

![image-20250406115114360](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250406115114360.png)

#### 转换为boolean型

![image-20250406115201076](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250406115201076.png)

- 代表空、否定的值会被转换为 false ，如 ''、0、NaN、null、undefined
- 其余值都会被转换为 true

## 运算符

### 优先级

## 流程控制

条件语句

循环语句

## 对象基础

## 函数

### 函数创建

#### 对象创建

```js
var 函数名 = new Function("执行语句");
```

```js
var fun = new Function("console.log('这是我的第一个函数');");
```

#### 声明创建

```js
function 函数名(形参1,形参2,...,形参N) {
    语句...
}
```

```js
function fun(){
    console.log("这是我的第二个函数");
}
```

#### 函数表达式创建

```js
var 函数名  = function([形参1,形参2,...,形参N]) {
    语句....
}
```

```js
var fun  = function() {
    console.log("这是我的第三个函数");
}
```

### 函数参数

#### 特点

- JS中的所有的**参数传递都是按值传递的**，也就是说把函数外部的值赋值给函数内部的参数，就和把值从一个变量赋值给另一个变量是一样的，在调用函数时，可以在()中指定实参（实际参数），实参将会赋值给函数中对应的形参
- 调用函数时，**解析器不会检查实参的类型**，所以要注意，是否有可能会接收到非法的参数，如果有可能，则需要对参数进行类型的检查，函数的实参可以是任意的数据类型
- 调用函数时，解析器也不会检查实参的数量，**多余实参不会被赋值**，**如果实参的数量少于形参的数量，则没有对应实参的形参将是undefined**

#### arguments的使用

当我们不确定有多少个参数传递的时候，可以用 arguments 来获取。在 JavaScript 中，arguments 实际上
它是当前函数的一个内置对象。所有函数都内置了一个 arguments 对象，arguments 对象中存储了传递的
所有实参。
arguments展示形式是一个伪数组，因此可以进行遍历。伪数组具有以下特点：

-  具有 length 属性
-  按索引方式储存数据
-  不具有数组的 push , pop 等方法

```js
function maxValue() {
	var max = arguments[0];
	for (var i = 0; i < arguments.length; i++) {
		if (max < arguments[i]) {
			max = arguments[i];
		}
	}
	return max;
}
console.log(maxValue(2, 4, 5, 9));
console.log(maxValue(12, 4, 9));
```



### 函数返回值

可以使用 return 来设置函数的返回值，不需要指定返回类型，**return后的值将会作为函数的执行结果返回，可以定义一个变量，来接收该结果。**

注意：在函数中return后的语句都不会执行，如果return语句后不跟任何值就相当于返回一个undefined，如果函数中不写return，则也会返回undefined，return后可以跟任意类型的值

语法格式：return 值

案例演示：

```js
function sum(num1, num2) {
    return num1 + num2;
}

var result = sum(10, 20);
console.log(result);
```

### 嵌套函数

嵌套函数：在函数中声明的函数就是嵌套函数，嵌套函数只能在当前函数中可以访问，在当前函数外无法访问。

案例演示：

```js
function fu() {
    function zi() {
        console.log("我是儿子")
    }
zi();
}
fu();
```



### 匿名函数

匿名函数：没有名字的函数就是匿名函数，它可以让一个变量来接收，也就是用 “函数表达式” 方式创建和接收。

案例演示：

```js
var fun = function () {
    alert("我是一个匿名函数");
}

fun();
```



### 立即执行函数

立即执行函数：函数定义完，立即被调用，这种函数叫做立即执行函数，立即执行函数往往只会执行一次。

案例演示：

```js
(function () {
    alert("我是一个匿名函数");
})();
```

### 对象中的函数

对象的属性值可以是任何的数据类型，也可以是个函数。

如果一个函数作为一个对象的属性保存，那么我们称这个函数是这个对象的方法，调用这个函数就说调用对象的方法（method）。

注意：方法和函数只是名称上的区别，没有其它别的区别

案例演示：

```js
var person = {
    name: "zhangsan",
    age: 18,
    sayHello: function () {
        console.log(name + " hello")
    }
}

person.sayHello();
```



### this对象

解析器在调用函数每次都会向函数内部传递进一个隐含的参数，这个隐含的参数就是this，this指向的是一个对象，这个对象我们称为函数执行的上下文对象，根据函数的调用方式的不同，this会指向不同的对象

以函数的形式调用时，this永远都是window
以方法的形式调用时，this就是调用方法的那个对象
案例演示：

```js
//创建一个全局变量name
var name = "全局变量name";

//创建一个函数
function fun() {
    console.log(this.name);
}

//创建一个对象
var obj = {
    name: "孙悟空",
    sayName: fun
};

//我们希望调用obj.sayName()时可以输出obj的名字而不是全局变量name的名字
obj.sayName();
```



## 对象进阶

## 作用域

### 声明提前

- 变量的声明提前：**使用var关键字声明的变量，会在所有的代码执行之前被声明（但是不会赋值）**，但是如果声明变量时不使用var关键字，则变量不会被声明提前
- 函数的声明提前：**使用函数声明形式创建的函数 function 函数名(){} ，它会在所有的代码执行之前就被创建**，所以我们可以在函数声明前来调用函数。使用函数表达式创建的函数，不会被声明提前，所以不能在声明前调用

### 全局作用域

直接编写在script标签中的JavaScript代码，都在全局作用域
全局作用域在页面打开时创建，在页面关闭时销毁
在全局作用域中有一个全局对象window，它代表的是一个浏览器的窗口，它由浏览器创建，我们可以直接使用
在全局作用域中：
创建的变量都会作为window对象的属性保存
创建的函数都会作为window对象的方法保存
全局作用域中的变量都是全局变量，在页面的任意的部分都可以访问的到

### 函数作用域

调用函数时创建函数作用域，函数执行完毕以后，函数作用域销毁
每调用一次函数就会创建一个新的函数作用域，它们之间是互相独立的
在函数作用域中可以访问到全局作用域的变量，在全局作用域中无法访问到函数作用域的变量
在函数中要访问全局变量可以使用window对象
作用域链：当在函数作用域操作一个变量时，它会先在自身作用域中寻找，如果有就直接使用，如果没有则向上一级作用域中寻找，直到找到全局作用域，如果全局作用域中依然没有找到，则会报错ReferenceError

### 作用域链

多个上下级关系的作用域形成的链，它的方向是从下向上的(从内到外)，查找变量时就是沿着作用域链来查找的。

查找一个**变量的查找规则**：**就进原则**

- 在当前作用域下的执行上下文中查找对应的属性，如果有直接返回，否则进入2
- 在上一级作用域的执行上下文中查找对应的属性，如果有直接返回，否则进入3
- 再次执行2的相同操作，直到全局作用域，如果还找不到就抛出找不到的ReferenceError异常

# 面向对象

## 对象基础

### 概述

Object类型，我们也称为一个对象，是JavaScript中的**引用数据类型**。它是一种复合值，它将很多值聚合到一起，可以通过名字访问这些值。对象也可以看做是属性的无序集合，每个属性都是一个名/值对。

对象除了可以创建自有属性，还可以通过从一个名为原型的对象那里继承属性。**除了字符串、数字、true、false、null和undefined之外，JavaScript中的值都是对象。**

### 创建对象

创建对象有两种方式：

```js
var person = new Object();

var person = {
    name: "孙悟空",
    age: 18
};
```

### 访问属性

访问属性的两种方式：

```javascript
对象.属性名
```

```javascript
对象['属性名']
```

### 添加属性

在创建一个对象后。我们可以直接用赋值为其添加属性或方法

```js
var obj = {};
obj.name = 'zs';
obj.sayHi = function() {
    console.log('hi');
}
```



### 删除属性

删除对象的属性可以使用delete关键字，格式如下：

```javascript
delete 对象.属性名
```

### 遍历对象

枚举遍历对象中的属性，可以使用for … in语句循环，对象中有几个属性，循环体就会执行几次。

语法格式：

```js
for (var 变量 in 对象) {

}
```

案例演示：

```js
var person = {
    name: "zhangsan",
    age: 18
}

for (var personKey in person) {
    var personVal = person[personKey];
    console.log(personKey + ":" + personVal);
}
```

### 栈和堆梳理

JavaScript在运行时数据是保存到栈内存和堆内存当中的。

简单来说**栈内存用来保存变量和基本类型，堆内存是用来保存对象。**

我们在声明一个变量时，实际上就是在栈内存中创建了一个空间用来保存变量。

如果是基本类型则在栈内存中直接保存，如果是引用类型则会在堆内存中保存，变量中保存的实际上对象在堆内存中的地址。

## 对象进阶

### 构造函数

#### 语法

```js
// 使用构造函数来创建对象
function Person(name, age) {
    // 设置对象的属性
    this.name = name;
    this.age = age;
    // 设置对象的方法
    this.sayName = function () {
        console.log(this.name);
    };
}

var person1 = new Person("孙悟空", 18);
var person2 = new Person("猪八戒", 19);
var person3 = new Person("沙和尚", 20);

console.log(person1);
console.log(person2);
console.log(person3);

```

#### 构造函数执行过程

1. 调用构造函数，它会立刻创建一个新的对象
2. 将新建的对象设置为函数中this，**在构造函数中可以使用this来引用新建的对象**
3. 逐行执行函数中的代码
4. 将新建的对象作为返回值返回

#### 注意

1. 构造函数**约定首字母大写，函数名就是创建出的对象名**
2. 函数内的属性和方法前面需要添加 this ，表示当前对象的属性和方法。
3. 构造函数中**不需要 return 返回结果。**
4. 当我们创建对象的时候，必须**用 new 来调用构造函数。**

### 原型

### toString方法

toString()函数用于将当前对象以字符串的形式返回。该方法属于Object对象，由于所有的对象都"继承"了Object的对象实例，因此几乎所有的实例对象都可以使用该方法，所有主流浏览器均支持该函数。

```js
// 使用构造函数来创建对象
function Person(name, age) {
    // 设置对象的属性
    this.name = name;
    this.age = age;
}

//创建对象的一个实例对象
var p = new Person("张三", 20);
console.log(p.toString()); //[object Object]
```

# 常用对象

## 数组对象

### 简介

数组也是对象的一种，数组是一种用于表达有顺序关系的值的集合的语言结构，也就是同类数据元素的有序集合。

### 创建

#### 使用对象创建

```js
var arr = new Array();
arr[3] = 1;
```

使用索引自动向数组添加元素

#### 使用字面量创建

```js
var arr = [1, 2, 3, 4, 5]
```

### 数组属性

constructor属性演示：返回创建数组对象的原型函数

```js
var arr = [1,2,3,4];
console.log(arr.constructor);
```


length属性演示：设置或返回数组元素的个数

```js
var arr = [1,2,3,4];
console.log(arr.length);
```

### 数组遍历

```js
for (var i = 0; i < arr.length; i++) {
    console.log(arr[i]);
}
```

### 数组方法

#### push()方法

**该方法可以向数组的末尾添加一个或多个元素，并返回数组的新的长度**

```js
var arr = ["孙悟空", "猪八戒", "沙和尚"];
var result = arr.push("唐僧", "蜘蛛精", "白骨精", "玉兔精");
console.log(arr);
console.log(result);
```

#### **pop()方法**

**该方法可以删除数组的最后一个元素，并将被删除的元素作为返回值返回**

```javascript
var arr = ["孙悟空", "猪八戒", "沙和尚"];
var result = arr.pop();
console.log(arr);
console.log(result);
```

#### unshift()方法

**该方法向数组开头添加一个或多个元素，并返回新的数组长度**

var arr = ["孙悟空", "猪八戒", "沙和尚"];
var result = arr.unshift("牛魔王", "二郎神");
console.log(arr);
console.log(result);

#### shift()方法

该方法可以删除数组的第一个元素，并将被删除的元素作为返回值返回

```js
var arr = ["孙悟空", "猪八戒", "沙和尚"];
var result = arr.shift();
console.log(arr);
console.log(result);
```

forEach()方法

该方法可以用来遍历数组

forEach()方法需要一个函数作为参数，像这种函数，由我们创建但是不由我们调用的，我们称为回调函数。数组中有几个元素函数就会执行几次，每次执行时，浏览器会将遍历到的元素，以实参的形式传递进来，我们可以来定义形参，来读取这些内容，浏览器会在回调函数中传递三个参数：

- 第一个参数：就是当前正在遍历的元素
- 第二个参数：就是当前正在遍历的元素的索引
- 第三个参数：就是正在遍历的数组

> 注意：这个方法只支持IE8以上的浏览器，IE8及以下的浏览器均不支持该方法，所以如果需要兼容IE8，则不要使用forEach()，还是使用for循环来遍历数组。

```js
var arr = ["孙悟空", "猪八戒", "沙和尚"];
arr.forEach(function (value, index, obj) {
    console.log(value + " #### " + index + " #### " + obj);
});
```

#### slice()方法

该方法可以用来从数组提取指定元素，该方法不会改变元素数组，而是将截取到的元素封装到一个新数组中返回

参数：

- 第一个参数：截取开始的位置的索引，包含开始索引
- 第二个参数：截取结束的位置的索引，不包含结束索引，第二个参数可以省略不写，此时会截取从开始索引往后的所有元素

> 注意：索引可以传递一个负值，如果传递一个负值，则从后往前计算，-1代表倒数第一个，-2代表倒数第二个。

```js
var arr = ["孙悟空", "猪八戒", "沙和尚", "唐僧", "白骨精"];
var result = arr.slice(1, 4);
console.log(result);
result = arr.slice(3);
console.log(result);
result = arr.slice(1, -2);
console.log(result);
```

#### splice()方法

该方法可以用于**删除数组中的指定元素**，该方法会影响到原数组，会将指定元素从原数组中删除，并将被删除的元素作为返回值返回

参数：

- 第一个参数：表示开始位置的索引

- 第二个参数：表示要删除的元素数量
- 第三个参数及以后参数：可以传递一些新的元素，这些元素将会自动插入到开始位置索引前边

```js
var arr = [1, 2, 3, 4, 5];
var result = arr.splice(1, 1); //1 3 4 5
console.log(arr);
console.log(result);
result = arr.splice(1, 0, "牛魔王", "铁扇公主", "红孩儿");
console.log(arr);
console.log(result);
```


concat()方法演示：该方法可以连接两个或多个数组，并将新的数组返回，该方法不会对原数组产生影响

var arr = ["孙悟空", "猪八戒", "沙和尚"];
var arr2 = ["白骨精", "玉兔精", "蜘蛛精"];
var arr3 = ["二郎神", "太上老君", "玉皇大帝"];
var result = arr.concat(arr2, arr3, "牛魔王", "铁扇公主");
console.log(result);

#### join()方法

该方法可以将数组转换为一个字符串，该方法不会对原数组产生影响，而是将转换后的字符串作为结果返回，在join()中可以指定一个字符串作为参数，这个字符串将会成为数组中元素的连接符，如果不指定连接符，则默认使用，作为连接符

```js
var arr = ["孙悟空", "猪八戒", "沙和尚"];
var result = arr.join("@-@");
console.log(result);
```

#### reverse()方法

该方法用来反转数组（前边的去后边，后边的去前边），该方法会直接修改原数组

```js
var arr = ["孙悟空", "猪八戒", "沙和尚"];
arr.reverse();
console.log(arr);
```

#### sort()方法

该方法可以用来对数组中的元素进行排序，也会影响原数组，默认会按照Unicode编码进行排序

```js
var arr = ["b", "c", "a"];
arr.sort();
console.log(arr);
```

>
> 注意：即使对于纯数字的数组，使用sort()排序时，也会按照Unicode编码来排序，所以对数字进排序时，可能会得到错误的结果。
>

```js
var arr = [1, 3, 2, 11, 5, 6];
arr.sort(); // 1 11 2 3 5 6
console.log(arr);
```

我们可以自己来指定排序的规则，我们可以在sort()添加一个回调函数，来指定排序规则，回调函数中需要定义两个形参，浏览器将会分别使用数组中的元素作为实参去调用回调函数，使用哪个元素调用不确定，但是肯定的是在数组中a一定在b前边，浏览器会根据回调函数的返回值来决定元素的顺序，如下：

- 如果返回一个大于0的值，则元素会交换位置

- 如果返回一个小于0的值，则元素位置不变
- 如果返回一个等于0的值，则认为两个元素相等，也不交换位置

经过上边的规则，我们可以总结下：

- 如果需要升序排列，则返回 a-b
- 如果需要降序排列，则返回 b-a

```js
var arr = [1, 3, 2, 11, 5, 6];
arr.sort(function (a, b) {
    return a - b;
});
console.log(arr);
```

## 函数对象

### call()和apply()

call()和apply()这两个方法都是函数对象的方法，需要通过函数对象来调用，当对函数调用call()和apply()都会调用函数执行，在调用call()和apply()可以将一个对象指定为第一个参数，此时这个对象将会成为函数执行时的this，call()方法可以将实参在对象之后依次传递，apply()方法需要将实参封装到一个数组中统一传递，如下演示

## Date对象

在JavaScript中使用Date对象来表示一个时间，如果直接使用构造函数创建一个Date对象，则会封装为当前代码执行的时间。

案例演示：

```js
var date = new Date();
console.log(date);

console.log(date.getFullYear());//获取当前日期对象的年份(四位数字年份)
console.log(date.getMonth());//获取当前日期对象的月份(0 ~ 11)
console.log(date.getDate());//获取当前日期对象的日数(1 ~ 31)
console.log(date.getHours());//获取当前日期对象的小时(0 ~ 23)
console.log(date.getMinutes());//获取当前日期对象的分钟(0 ~ 59)
console.log(date.getSeconds());//获取当前日期对象的秒钟(0 ~ 59)
console.log(date.getMilliseconds());//获取当前日期对象的毫秒(0 ~ 999)
```

![image-20201018130252223](D:\E\学习\Go\笔记\技术栈\前端\assets\bdce49b19b915ee531e1ca326b684f40.png)

## Math对象

Math和其它的对象不同，它不是一个构造函数，它属于一个工具类不用创建对象，它里边封装了数学运算相关的属性和方法。

案例演示：

```js
/*固定值*/
console.log("PI = " + Math.PI);
console.log("E  = " + Math.E);
console.log("===============");
/*正数*/
console.log(Math.abs(1));        //可以用来计算一个数的绝对值
console.log(Math.ceil(1.1));     //可以对一个数进行向上取整，小数位只有有值就自动进1
console.log(Math.floor(1.99));   //可以对一个数进行向下取整，小数部分会被舍掉
console.log(Math.round(1.4));    //可以对一个数进行四舍五入取整
console.log("===============");
/*负数*/
console.log(Math.abs(-1));       //可以用来计算一个数的绝对值
console.log(Math.ceil(-1.1));    //可以对一个数进行向上取整，小数部分会被舍掉
console.log(Math.floor(-1.99));  //可以对一个数进行向下取整，小数位只有有值就自动进1
console.log(Math.round(-1.4));   //可以对一个数进行四舍五入取整
console.log("===============");
/*随机数*/
//Math.random()：可以用来生成一个0-1之间的随机数
//生成一个0-x之间的随机数：Math.round(Math.random()*x)
//生成一个x-y之间的随机数：Math.round(Math.random()*(y-x)+x)
console.log(Math.round(Math.random() * 10));            //生成一个0-10之间的随机数
console.log(Math.round(Math.random() * (10 - 1) + 1));  //生成一个1-10之间的随机数
console.log("===============");
/*数学运算*/
console.log(Math.pow(12, 3));   //Math.pow(x,y)：返回x的y次幂
console.log(Math.sqrt(4));      //Math.sqrt(x) ：返回x的平方根
```

![image-20201018132005284](D:\E\学习\Go\笔记\技术栈\前端\assets\0ae2f3c2b632fadb6328eba8f1b499af.png)

## String对象

### 概述

在JS中为我们提供了三个包装类，通过这三个包装类可以将基本数据类型的数据转换为对象

- String()：可以将基本数据类型字符串转换为String对象

- Number()：可以将基本数据类型的数字转换为Number对象
- Boolean()：可以将基本数据类型的布尔值转换为Boolean对象

但是注意：我们在实际应用中不会使用基本数据类型的对象，如果使用基本数据类型的对象，在做一些比较时可能会带来一些不可预期的结果，在这一章节中，我们重点介绍String()对象的属性和方法。

### 字符串属性

#### constructor

返回创建字符串对象的原型函数

```js
var str = "Hello,World!";
console.log(str.constructor);
```

![image-20201018152739858](D:\E\学习\Go\笔记\技术栈\前端\assets\485ae6120999b6c7cb7d9df6702a06b6.png)

#### length

可以用来获取字符串的长度

```js
var str = "Hello,World!";
console.log(str.length);
```

### 字符串方法

#### charAt()方法

该方法可以根据索引获取指定位置的字符

```js
var str = "Hello,World!";
console.log(str.charAt(1));
```

#### charCodeAt()

该方法获取指定位置字符的字符编码（Unicode编码）

```js
var str = "Hello,World!";
console.log(str.charCodeAt(1));
```

#### concat()方法

该方法可以用来连接两个或多个字符串

```js
var str = "Hello,World!";
console.log(str.concat("你好，", "世界！"));
```

#### indexof()方法

该方法可以检索一个字符串中是否含有指定内容，如果字符串中含有该内容，则会返回其第一次出现的索引，如果没有找到指定的内容，则返回-1，可以指定一个第二个参数，指定开始查找的位置

```js
var str = "Hello,World!";
console.log(str.indexOf("o"));
console.log(str.indexOf("o", 5));
```

#### lastIndexOf()方法

该方法的用法和indexOf()一样，不同的是indexOf是从前往后找，而lastIndexOf是从后往前找，也可以指定开始查找的位置

```js
var str = "Hello,World!";
console.log(str.lastIndexOf("o"));
console.log(str.lastIndexOf("o", 5));
```

#### slice()方法

可以从字符串中截取指定的内容，不会影响原字符串，而是将截取到内容返回

参数：

- 第一个参数：开始位置的索引（包括开始位置）
- 第二个参数：结束位置的索引（不包括结束位置），如果省略第二个参数，则会截取到后边所有的

> 注意：也可以传递一个负数作为参数，负数的话将会从后边计算

```js
var str = "Hello,World!";
var result = str.slice(1, 4);
console.log(result);
result = str.slice(1);
console.log(result);
result = str.slice(1, -1);
console.log(result);
```

#### substring()方法

可以用来截取一个字符串，它和slice()类似

参数：

- 第一个参数：开始截取位置的索引（包括开始位置）

- 第二个参数：结束位置的索引（不包括结束位置），如果省略第二个参数，则会截取到后边所有的

> 注意：不同的是这个方法不能接受负值作为参数，如果传递了一个负值，则默认使用0，而且它还自动调整参数的位置，如果第二个参数小于第一个，则自动交换

```js
var str = "Hello,World!";
var result = str.substring(1, 4);
console.log(result);
result = str.substring(1);
console.log(result);
result = str.substring(1, -1);
console.log(result);
```

#### substr()方法

该方法用来截取字符串

参数：

- 第一个参数：截取开始位置的索引

- 第二个参数：截取的长度

```js
var str = "Hello,World!";
var result = str.substr(6, 6);
console.log(result);
```

#### split()方法

该方法可以将一个字符串拆分为一个数组，需要一个字符串作为参数，将会根据该字符串去拆分数组

var str = "Hello,World!";
var result = str.split(",");
console.log(result);

#### toUpperCase()方法

将一个字符串转换为大写并返回

var str = "Hello,World!";
var result = str.toUpperCase();
console.log(result);

#### toLowerCase()方法

将一个字符串转换为小写并返回

var str = "Hello,World!";
var result = str.toLowerCase();
console.log(result);

### RegExp对象

# DOM

## 简介

文档对象模型（Document Object Model，简称 DOM），是 W3C 组织推荐的处理可扩展标记语言（HTML或者XML）的标准编程接口。

W3C 已经定义了一系列的 DOM 接口，通过这些 DOM 接口可以改变网页的内容、结构和样式。

## 文档节点

### 简介

节点Node，是构成我们网页的最基本的组成部分，网页中的每一个部分都可以称为是一个节点。比如：html标签、属性、文本、注释、整个文档等都是一个节点。

常用节点分为四类：

- **文档节点：整个HTML文档**
- **元素节点：HTML文档中的HTML标签**
- **属性节点：元素的属性**
- **文本节点：HTML标签中的文本内容**

### 节点操作

#### 总结

![image-20250407152655558](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250407152655558.png)

![image-20250407152721477](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250407152721477.png)

利用节点操作，我们不仅可以通过DOM提供的方法比如getElementById获取元素还可以通过节点的层级关系来获取元素。

节点至少拥有nodeType（节点类型）、nodeName（节点名称）和nodeValue（节点值）这三个基本属性。

- **元素节点 nodeType 为 1**
- **属性节点 nodeType 为 2**
- **文本节点 nodeType 为 3 （文本节点包含文字、空格、换行等）**

#### 获取节点

##### parentNode

- parentNode 属性可返回某节点的父节点，注意是最近的一个父节点
- 如果指定的节点没有父节点则返回 null

##### childNodes

返回包含指定节点的子节点的集合，该集合为即时更新的集合。

**注意：**返回值里面包含了所有的子节点，包括元素节点，文本节点等。如果只想要获得里面的元素节点，则需要专门处理。 所以我们一般不提倡使用childNodes

##### children

是一个只读属性，返回所有的子元素节点。它只返回子元素节点，其余节点不返

##### firstChild和lastChild

第一个和最后一个子节点

##### firstElementChild和lastElementChild

第一个和最后一个元素节点

**注意：**

实际开发中，firstChild 和 lastChild 包含其他节点，操作不方便，firstElementChild 和lastElementChild 又有兼容性问题，那么我们如何获取第一个子元素节点或最后一个子元素节点呢？

解决方案：

1. **如果想要第一个子元素节点，可以使用 parentNode.chilren[0]**
2. **如果想要最后一个子元素节点，可以使用 parentNode.chilren[parentNode.chilren.length - 1]**

##### nextSibling和previousSibling

返回当前元素的下一个和上一个兄弟节点。

#### 创建节点

document.createElement() 方法创建由 tagName 指定的 HTML 元素。因为这些元素原先不存在，
是根据我们的需求动态生成的，所以我们也称为动态创建元素节点。

#### 添加节点

node.appendChild() 方法将一个节点添加到指定父节点的子节点列表末尾。类似于 CSS 里面的after 伪元素。

node.insertBefore(child, 指定元素) 方法将一个节点添加到父节点的指定子节点前面。类似于 CSS 里面的 before 伪元素。

#### 删除节点

node.removeChild() 方法从 DOM 中删除一个子节点，返回删除的节点。

node.cloneNode() 方法返回调用该方法的节点的一个副本。

#### 复制节点

node.cloneNode(true或false) 方法返回调用该方法的节点的一个副本。

1. 如果括号参数为空或者为 false ，则是浅拷贝，即只克隆复制节点本身，不克隆里面的子节点。
2. 如果括号参数为 true ，则是深度拷贝，会复制节点本身以及里面所有的子节点。

## 文档操作

### 获取元素

![image-20250407151951282](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250407151951282.png)

### 获取元素属性和内容

![image-20250407152126037](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250407152126037.png)

元素.getAttribute一般是用来获取自定义属性的。

### 操作元素

![image-20250407152312308](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250407152312308.png)

innerHTML包含html标签格式，且不忽略空格

通过className修改元素的指定的类名，而不是class

#### 自定义属性

## 事件

HTML事件可以触发浏览器中的行为，比方说当用户点击某个 HTML 元素时启动一段 JavaScript

### 常用事件

#### 窗口事件

由窗口触发该事件 (同样适用于 <body> 标签)：

| 属性      | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| onblur    | 当窗口失去焦点时运行脚本。                                   |
| onfocus   | 当窗口获得焦点时运行脚本。                                   |
| onload    | 当文档加载之后运行脚本。                                     |
| onresize  | 当调整窗口大小时运行脚本。                                   |
| onstorage | 当 Web Storage 区域更新时（存储空间中的数据发生变化时）运行脚本。
16 |

#### 表单事件

表单事件在HTML表单中触发 (适用于所有 HTML 元素，但该HTML元素需在form表单内)：

![image-20250407153415152](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250407153415152.png)

#### 键盘事件

| 属性       | 描述                         |
| ---------- | ---------------------------- |
| onkeydown  | 当按下按键时运行脚本。       |
| onkeyup    | 当松开按键时运行脚本。       |
| onkeypress | 当按下并松开按键时运行脚本。 |

#### 鼠标事件

![image-20250407153500583](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250407153500583.png)

### 事件绑定

```js
eventTarget.addEventListener(type, listener[, useCapture])
```

该方法接收三个参数：

-  type：事件类型字符串，比如 click 、mouseover ，注意这里不要带 on
-  listener：事件处理函数，事件发生时，会调用该监听函数
- useCapture：可选参数，是一个布尔值，默认是 false。学完 DOM 事件流后，我们再进一步学习

```js
eventTarget.attachEvent(eventNameWithOn, callback)
```

- eventNameWithOn：事件类型字符串，比如 onclick 、onmouseover ，这里要带 on
- callback： 事件处理函数，当目标触发事件时回调函数被调用

#### 兼容性

我们以前绑定事件代码只能一个事件绑定一个函数，那我们要是想一个事件对应多个函数，并且不存在兼容性的问题该如何解决呢？

接下来，我会直接提供两个已经编写好的事件绑定和事件解绑的兼容性代码，如下：

```js
/*为元素绑定事件兼容性代码*/
function addEventListener(element, type, fn) {
	if(element.addEventListener) {
		element.addEventListener(type, fn, false);
	} else if(element.attachEvent) {
		element.attachEvent("on" + type, fn);
	} else {
		element["on" + type] = fn;
	}
}

/*为元素解绑事件兼容性代码*/
function removeEventListener(element, type, fnName) {
	if(element.removeEventListener) {
		element.removeEventListener(type, fnName, false);
	} else if(element.detachEvent) {
		element.detachEvent("on" + type, fnName);
	} else {
		element["on" + type] = null;
	}
}
```



### 事件冒泡（重要）

事件的冒泡（Bubble）

所谓的冒泡指的就是事件的向上传导，**当后代元素上的事件被触发时，其祖先元素的相同事件也会被触发**，在开发中大部分情况冒泡都是有用的，如果不希望发生事件冒泡可以通过事件对象来取消冒泡。

### 事件委派

我们希望只绑定一次事件，即可应用到多个的元素上，即使元素是后添加的，**我们可以尝试将其绑定给元素的共同的祖先元素，也就是事件的委派。**

事件的委派，是指**将事件统一绑定给元素的共同的祖先元素，这样当后代元素上的事件触发时，会一直冒泡到祖先元素，从而通过祖先元素的响应函数来处理事件。**

事件委派是利用了事件冒泡，通过委派可以减少事件绑定的次数，提高程序的性能。

```js
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<ul id="u1">
    <li><a href="javascript:;" class="link">超链接一</a></li>
    <li><a href="javascript:;" class="link">超链接二</a></li>
    <li><a href="javascript:;" class="link">超链接三</a></li>
</ul>

<!-- 在这里写JavaScript代码，因为JavaScript是由上到下执行的 -->
<script>
    var u1 = document.getElementById("u1");

    // 为ul绑定一个单击响应函数
    u1.onclick = function (event) {
        event = event || window.event;
        // 如果触发事件的对象是我们期望的元素，则执行，否则不执行
        if (event.target.className == "link") {
            console.log("我是ul的单击响应函数");
        }
    };
</script>
</body>
</html>

```

# BOM

## 简介

浏览器对象模型（BOM）使 JavaScript 有能力与浏览器"对话"。

由于现代浏览器已经（几乎）实现了 JavaScript 交互性方面的相同方法和属性，因此常被认为是BOM的方法和属性。

浏览器对象模型（BOM）可以使我们通过JS来操作浏览器，在BOM中为我们提供了一组对象，用来完成对浏览器的操作，常见的BOM对象如下：

- **Window**：代表的是整个**浏览器的窗口**，同时window也是网页中的全局对象
- **Navigator**：代表的当前**浏览器的信息**，通过该对象可以来识别不同的浏览器
- **Location**：代表当前**浏览器的地址栏信息**，通过Location可以获取地址栏信息，或者操作浏览器跳转页面
- **History**：代表**浏览器的历史记录**，可以通过该对象来操作浏览器的历史记录，由于隐私原因，该对象不能获取到具体的历史记录，只能操作浏览器向前或向后翻页，而且该操作只在当次访问时有效
- **Screen**：代表用户的屏幕的信息，通过该对象可以获取到用户的显示器的相关的信息

这些BOM对象在浏览器中都是作为window对象的属性保存的，可以通过window对象来使用，也可以直接使用。

## Window对象

### 弹出框

JavaScript 有三种类型的弹出框：警告框、确认框和提示框。

#### 警告框

如果要确保信息传递给用户，通常会使用警告框。当警告框弹出时，用户将需要单击“确定”来继续。

```js
window.alert("sometext");
```

**注意：**window.alert() 方法可以不带 window 前缀来写。

#### 确认框

如果您希望用户验证或接受某个东西，则通常使用“确认”框。当确认框弹出时，用户将不得不单击“确定”或“取消”来继续进行。

**如果用户单击“确定”，该框返回 true。如果用户单击“取消”，该框返回 false。**

```js
window.confirm("sometext");
```

注意：window.confirm() 方法可以不带 window 前缀来编写。

#### 提示框

如果您希望用户在进入页面前输入值，通常会使用提示框。

当提示框弹出时，用户将不得不输入值后单击“确定”或点击“取消”来继续进行。

**如果用户单击“确定”，该框返回输入值。如果用户单击“取消”，该框返回 NULL。**

```js
window.prompt("sometext","defaultText");
```

window.prompt() 方法可以不带 window 前缀来编写。

### 定时事件

**JavaScript 可以在时间间隔内执行，这就是所谓的定时事件（ Timing Events）。**window 对象允许以指定的时间间隔执行代码，这些时间间隔称为定时事件。

两个关键的方法

- setTimeout(function, milliseconds) 在等待指定的毫秒数后执行函数。

- setInterval(function, milliseconds) 等同于 setTimeout()，但持续重复执行该函数。setTimeout() 和 setInterval() 都属于 window 对象的方法。


#### 延时器

setTimeout() 方法：延时器

```js
window.setTimeout(function, milliseconds);
```

- 第一个参数是要执行的函数。
- 第二个参数指示执行之前的毫秒数。

#### 定时器

**setInterval() 方法：定时器**

setInterval() 方法在每个给定的时间间隔重复给定的函数。

```javascript
window.setInterval(function, milliseconds);
```

### 常用窗口属性

### 其它窗口方法

- **window.open() ：打开新的窗口**
- **window.close() ：关闭当前窗口**

## Navigator对象

Navigator代表的当前浏览器的信息，通过该对象可以来识别不同的浏览器，由于历史原因，Navigator对象中的大部分属性都已经不能帮助我们识别浏览器了，一般我们只会使用userAgent来判断浏览器的信息，userAgent是一个字符串，这个字符串中包含有用来描述浏览器信息的内容，不同的浏览器会有不同的userAgent，如下代码：

```js
var ua = navigator.userAgent;
console.log(ua);
```

## Location对象

### lacation简介

window 对象给我们提供了一个 location 属性用于获取或设置窗体的 URL，并且可以用于解析 URL 。 因为这个属性返回的是一个对象，所以我们将这个属性也称为 location 对象。

> url格式

```
protocol://host[:port]/path/[?query]#fragment
http://www.itcast.cn/index.html?name=andy&age=18#link
```

![image-20250408105928578](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250408105928578.png)

### 常用属性

```js
console.log(location);          //输出location对象
console.log(location.href);     //输出当前地址的全路径地址
console.log(location.origin);   //输出当前地址的来源
console.log(location.protocol); //输出当前地址的协议
console.log(location.hostname); //输出当前地址的主机名
console.log(location.host);     //输出当前地址的主机
console.log(location.port);     //输出当前地址的端口号
console.log(location.pathname); //输出当前地址的路径部分
console.log(location.search);   //输出当前地址的?后边的参数部分
```

### 常用方法

![image-20250408110520035](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250408110520035.png)

## History对象

History对象可以用来操作浏览器向前或向后翻页

![image-20250408110629288](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250408110629288.png)

# 高级语法

## 解构

数组解构



对象解构

```js
let { name, age } = { name: 'Alice', age: 25 };
console.log(name); // Alice
console.log(age);  // 25
```

```js
let { name: userName, age: userAge } = { name: 'Bob', age: 30 };
console.log(userName); // Bob
console.log(userAge);  // 30
```



# JavaScript新特性

