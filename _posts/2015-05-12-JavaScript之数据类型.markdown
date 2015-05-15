---
layout: post
title:  "JavaScript之数据类型"
date:   2015-05-12 20:16:33
categories: 文档
tags: js
---
<!--more-->
###数据类型
JavaScript有5种简单数据类型（也称为基本数据类型）

* Undefined
* Null
* Boolean
* Number
* String

1种复杂数据类型（也称为对象类型）

* Object 本质是由一组无序的名值对组成

###typeof操作符
JavaScript是松散类型，不需要提前指定数据类型，因此需要一个方法来检测给定变量的数据类型

对一个值使用typeof操作符可能返回下列字符串：

* "undefiend"-----值未定义message
* "boolean"----布尔值
* "string"----字符串
* "number"----数值
* "object"----对象(null也返回"object" 可以用来表示对象不存在，但本身属于NULL类型，并不不是object类型，是一个历史遗留问题)
* "function"----函数

缺点：typeof会把所有的数组类型以及用户自定义类型判断为object

优化：使用Object.prototype.toString()匹配出具体的数据类型

<code>Object.prototype.toString()</code>有一个妙用，如果我们以某个特别的对象为上下文来调用该函数，它会返回正确的类型。我们需要做的就是手动处理其返回的字符串，最终便能获得typeof应该返回的正确字符串。

可以用来区分：<code>Boolean</code>, <code>Number</code>, <code>String</code>, <code>Function</code>, <code>Array</code>, <code>Date</code>, <code>RegExp</code>, <code>Object</code>, <code>Error</code>等等。

<code>jQuery.type()</code>就是这样实现的。以下代码从jQuery源码中抽取出来，可以直接用。




	var class2type = {};
    "Boolean Number String Function Array Date RegExp Object Error".split( " " ).forEach( function ( e, i ) {
        class2type["[object " + e + "]"] = e.toLowerCase();
    } );
    function _typeof( obj ) {
        if ( obj == null ) {
            return String( obj );
        }
        return typeof obj === "object" || typeof obj === "function" ?
        class2type[class2type.toString.call( obj )] || "object" :
                typeof obj;
    }



###construct 获取构造函数判断数据类型
因为typeof会把所有的数组类型以及用户自定义类型判断为object，从而无法知道更确切的信息。而constructor却可以解决这个问题。


typeof可以检查到变量是否有定义，而construct是检查构造函数所以只能检查已定义变量的类型。



	var i;
	alert(typeof(i)); //"undefined"
	alert(i.constructor);//报错 i is not defined



已定义变量调用construct属性返回的是一个构造函数



	var i=1;
	alert(typeof(i)); //"number"
	alert(i.constructor);//function Number() { [native code] }




这样我们通过把construcor返回的构造函数转化为字符串，通过寻找匹配字符串（function名）就可以确定具体类型啦


用基于constructor的方法去检查一些基本类型，如

Object / Array / Function / String / Number / Boolean

在你的正则表达式中，一定要将这些单词的首字母大写！！而如果该类型是自定义类型，则根据你定义的时候标识符的写法确定。如：



	function user() {};
	var i = new user();
	alert(/user/.test(i.constructor));//true



缺点：因为是通过构造函数判断，所以通过原型链继承的情况，constuctor并不能准确判断


###instanceof 获取实例判断数据类型
通常来讲，使用 instanceof 就是判断一个实例是否属于某种类型



	function People(){}
	var dog = new People();
	console.log(dog instanceof People)//true



另外，更重的一点是 instanceof 可以在继承关系中用来判断一个实例是否属于它的父类型。例如：



	 // 判断 dog 是否是 Foo 类的实例 , 并且是否是其父类型的实例
	 function People(){}
	 function Dog(){}
	 Dog.prototype = new People();//JavaScript 原型继承

	 var dog = new Dog();
	 console.log(dog instanceof Dog)//true
	 console.log(dog instanceof People)//true



instanceof疑惑：



	 console.log(Object instanceof Object);//true
	 console.log(Object instanceof Function);//true
	 console.log(Function instanceof Object);//true
	 console.log(Function instanceof Function);//true
	 console.log(Number instanceof Number);//false
	 console.log(String instanceof String);//false

	 console.log(Function instanceof Object);//true

	 console.log(Foo instanceof Function);//true
	 console.log(Foo instanceof Foo);//false



 先看看 JavaScript instanceof 运算符思想：

 左边L的隐式原型或者L的更父级的隐式原型 和 右边R的显示原型和相等才返回true

 在原型链总说过JavaScript中一切皆为对象 所有数据类型包括function的最终父类都是Object

而prototype属性是只有Object和Function及Function的实例才有的一个属性且均为Object {}；所以只有Object和Function及Function的实例instanceof才返回true



	function instance_of(L, R) {//L 表示左表达式，R 表示右表达式
	    var O = R.prototype;// 取 R 的显示原型
	  	 L = L.__proto__;// 取 L 的隐式原型
	    while (true) {
		    if (L === null)
		      return false;
		    if (O === L)// 这里重点：当 O 严格等于 L 时，返回 true
		      return true;
		    L = L.__proto__;
	  	}
 	}



###Undefined类型
Undefined类型只有一个值，即特殊的undefined。在使用var声明了变量但未对其初始化时，这个变量的值就是undefined



	var message;
    alert(message == undefined) //true



###Null类型
Null类型也只有一特殊的值是：null。null值表示一个空对象指针，所以typeof操作符检测null时会返回"object"



	var message = null;
    alert(typeof message); // "object"



如果定义的变量准备在将来用于保存对象，那么最好将该变量初始化为null而不是其他值。这样一来，只要直接检测null值就可以知道相应的变量是否已经保存了一个对象的引用了，例如：



 	if(message != null)
    {
        //执行某些操作
    }



实际上，undefined值是派生自null值的，因此ECMA-262规定对它们的相等性测试返回true。

	alert(undefined == null); //true

尽管null和undefined有这样的关系，但它们的用途完全不同。无论在什么情况下都没有必要把一个变量的值显式地设置为undefined，可是同样的规则对null却不适用。

* undefined 如果变量是未定义 往往是没有数据或数据结构中没有此字段 基本可以认为是程序的不完善或错误 应尽量避免
* null 一般是人为设置 表示以后会用到这个变量 但是还未定义具体数据 一般用来占坑备用

###Boolean类型
该类型只有两个字面值：true和false。

虽然Boolean类型的字面值只有两个，但JavaScript中所有类型的值都有与这两个Boolean值等价的值。要将一个值转换为其对应的Boolean值，可以调用类型转换函数Boolean()



	var message = 'Hello World';
    var messageAsBoolean = Boolean(message);



 各种数据类型及其对象的转换规则：

 数据类型	|	转换为true的值	|	转换为false的值
 ----		|	-------------	|	------
 Boolean	|	true			|	false
 String	|	任何非空字符串(包括"0")	|""(空字符串)
 Number	|	任何非零数值（包括无穷大）	|0或NaN（NaN 与所有值都不相等，包括它自己）
 Object	|	任何非null对象		|	null
 Undefined|	n/a（不适用）		|	undefined




###Number类型
Number类型用来表示整数和浮点数值

还有一种特殊的数值，即NaN（非数值 Not a Number）。

用于表示一个本来要返回数值的操作数未返回数值的情况（这样就不会抛出错误了）。

例如:

在其他编程语言中，任何数值除以或者余0都会导致错误，从而停止代码执行。但在JavaScript中，会溢出或者返回NaN，因此不会影响其他代码的执行。

* 任何数字余0都返回NaN
* 0除以0返回NaN 其他数字除以0要么上溢出(Infinity)要么下溢出(-Infinity)

NaN的特点

 * 任何涉及NaN的操作（例如NaN/10）都会返回NaN
 * NaN 与所有值都不相等，包括它自己
 * NaN 属性用于引用特殊的非数字值。
 * 无法使用 for/in 循环来枚举 NaN 属性，也不能用 delete 运算符来删除它。


JavaScript中有一个isNaN()函数，这个函数接受一个参数，该参数可以使任何类型，而函数会帮我们确定这个参数是否“不是数值”。isNaN()在接收一个值之后，会尝试将这个值转换为数值。某些不是数值的值会直接转换为数值。而任何不能被转换为数值的值都会导致这个函数返回true。例如：



	alert(isNaN(NaN));    //true
    alert(isNaN(10));    //false(10是一个数值)
    alert(isNaN("10"));    //false(可能被转换为数值10)
    alert(isNaN("blue"));    //true(不能被转换为数值)
    alert(isNaN(true));    //false(可能被转换为数值1)



有3个函数可以把非数值转换为数值：Number()、parseInt()和parseFloat()。第一个函数，即转型函数Number()可以用于任何数据类型，而另外两个函数则专门用于把字符串转换成数值。这3个函数对于同样的输入会返回不同的结果。

Number()函数的转换规则如下：

* 如果是Boolean值，true和false将分别被替换为1和0
* 如果是数字值，只是简单的传入和返回
* 如果是null值，返回0
* 如果是undefined，返回NaN
* 如果是字符串，遵循下列规则：
	* 如果字符串中只包含数字，则将其转换为十进制数值，即”1“会变成1，”123“会变成123，而”011“会变成11（前导的0被忽略）
	* 如果字符串中只包含有效的浮点格式，如”1.1“，则将其转换为对应的浮点数（同样，也会忽略前导0）
	* 如果字符串中只包含有效的十六进制格式，例如”0xf“，则将其转换为相同大小的十进制整数值
	* 如果字符串是空的，则将其转换为0
	* 如果字符串中包含除了上述格式之外的字符，则将其转换为NaN

* 如果是对象，则调用对象的valueOf()方法,然后依照前面的规则转换返回的值。如果转换的结果是NaN，则调用对象的toString()方法，然后再依次按照前面的规则转换返回的字符串值。

由于Number()函数在转换字符串时比较复杂而且不够合理，因此在处理整数的时候更常用的parseInt()函数。parseInt()函数在转换字符串时，更多的是看其是否符合数值模式。它会忽略字符串前面的空格，直至找到第一个非空格字符。如果第一个字符串不是数字字符或者负号，parseInt()会返回NaN；也就是说，用parseInt()转换空字符串会返回NaN。如果第一个字符是数字字符，praseInt()会继续解析第二个字符，知道解析完所有后续字符或者遇到了一个非数字字符。例如，"1234blue"会被转换为1234，”22.5“会被转换为22，因为小数点并不是有效的数字字符。

如果字符串中的第一个字符是数字字符，parseInt()也能够识别出各种整数格式（即十进制、八进制、十六进制）



	var num1 = parseInt("1234blue");    //1234
    var num2 = parseInt("");            //NaN
    var num3 = parseInt("0xA");            //10（十六进制）
    var num4 = parseInt("22.5");        //22
    var num5 = parseInt("070");            //56（八进制）
    var num6 = parseInt("70");            //70

    var num7 = parseInt("10",2);        //2（按二进制解析）
    var num8 = parseInt("10",8);        //8(按八进制解析)
    var num9 = parseInt("10",10);        //10（按十进制解析）
    var num10 = parseInt("10",16);        //16（按十六进制解析）
    var num11 = parseInt("70",8);            //56（按八进制）
    var num12 = parseInt("AF",16);        //175（按十六进制解析）




与parseInt()函数类似，parseFloat()也是从第一个字符（位置0）开始解析每个字符。而且也是一直解析到字符串末尾，或者解析到遇见一个无效的浮点数字字符为止。也就是说，字符串中的第一个小数点是有效的，而第二个小数点就是无效的了，因此它后面的字符串将被忽略。例如，”22.34.5“将会被转换成22.34。

parseFloat()和parseInt()的第二个区别在于它始终都会忽略前导的零。所以parseFloat()只能解析十进制值，它没有用第二个参数指定基数的用法。


	var num1 = parseFloat("1234blue");    //1234
    var num2 = parseFloat("0xA");        //0
    var num3 = parseFloat("22.5");        //22.5
    var num4 = parseFloat("22.34.5");    //22.34
    var num5 = parseFloat("0908.5");    //908.5




###String类型
String类型用于表示由零或多个16位Unicode字符组成的字符序列，即字符串。字符串可以由单引号('')或双引号("")表示。
　

toString()方法：

数值、布尔值、对象和字符串值都有toString()方法。但null和undefined值没有这个方法。

toString()方法可以指定基数，返回指定进制的字符串



	var num = 10;
    alert(num.toString());      //"10"
    alert(num.toString(2));     //"1010"
    alert(num.toString(8));     //"12"
    alert(num.toString(10));    //"10"
    alert(num.toString(16));    //"a"



在不知道要转换的值是不是null或undefined的情况下，还可以使用转型函数String()，这个函数能够将任何类型的值转换为字符串。String()函数遵循下列转换规则：

* 如果值有toString()方法，则调用该方法（没有参数）并返回相应的结果
* 如果值是null，则返回"null"
* 如果值是undefined，则返回”undefined“


###Object类型
对象其实就是一组数据和功能的集合。对象可以通过执行new操作符后跟要创建的对象类型的名称或直接指定{}来创建。而创建Object类型的实例并为其添加属性和（或）方法，就可以创建自定义对象



	var o = new Object();
	var oo = {};



Object的每个实例都具有下列属性和方法：

* constructor——保存着用于创建当前对象的函数
* hasOwnProperty(propertyName)——用于检查给定的属性在当前对象实例中（而不是在实例的原型中）是否存在。其中，作为参数的属性名(propertyName)必须以字符串形式指定（例如：o.hasOwnProperty("name")）
* isPrototypeOf(object)——用于检查传入的对象是否是一个对象的原型
* propertyIsEnumerable(propertyName)——用于检查给定的属性是否能够使用for-in语句来枚举
* toString()——返回对象的字符串表示
* valueOf()——返回对象的字符串、数值或布尔值表示。通常与toString()方法的返回值相同。















 











































