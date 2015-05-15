---
layout: post
title:  "javaScript继承"
date:   2015-05-07 8:16:33
categories: 文档
tags: js
---
<!--more-->
###一、new prototype 实例化对象 原型链
所有实例对象需要共享的属性和方法，都放在prototype对象对象里面；那些不需要共享的本地属性和方法，就放在构造函数里面。

实例对象一旦创建，将自动引用prototype对象的属性和方法。也就是说，实例对象的属性和方法，分成两种，一种是本地的，另一种是引用的

实例：姓名和年龄是私有的 而性别是共享的 实例化对象的性别总是一样

注意：prototype是可以重写



	function People( name, age ) {
        this.name = name;
        this.age = age;
    }
    People.prototype = {
        sex:"male"
    };
    People.prototype.sex="female";
    var people1 = new People( "zodiac", "20" ),
            people2 = new People( "xl", "21" );



###二、apply call 对象冒充实现继承 只能继承构造函数 不能继承原型prototype



	function People( name, age ) {
        this.name = name;
        this.age = age;
    }
    People.prototype = {
        sayName : function () {
            alert( this.name );
        }
    };

    function Dog( name, age ) {

        this.constructor = People;
        this.constructor( name+"狗", age);
        delete this.constructor;

        People.apply( this, [name+"狗", age] );
        People.call(this, name+"狗",age);
    }

    var dog = new Dog( "单身", "22" );

	 alert(dog.name);//单身狗
    dog.sayName();//error

    //下面3种方法一样
    this.constructor = People;
    this.constructor( name, age);
    delete this.constructor;

    People.apply( this, [name, age] );

    People.call(this, name,age);





###三、原型链继承
原型方式将<strong>继承者Dog</strong>的<strong>prototype</strong>属性设置成<strong>被继承者People</strong>的实例。

因为想要获得People的所有属性和方法（不包括构造函数）将People的实例赋予Dog的prototype属性是最好的方法了



	function People(age) {
       this.age=age;
    }

    People.prototype.name = "zodiac";
    People.prototype.sayName = function () {
        alert(this.name);
    };
    People.prototype.sayAge = function () {
        alert(this.age);
    };

    function Dog() {

    }

    Dog.prototype = new People();
    var dog=new Dog();
    dog.sayName();// zodiac
    dog.sayAge();// undefined


instanceof 可以判断一个实例是否属于某种类型 在继承关系中用来判断一个实例是否属于它的父类型

在原型链中 对于继承者Dog的所有实例，instanceof 为 People 和 Dog 都返回 true

	alert(dog instanceof Dog);	//输出 "true"
    alert(dog instanceof People);	//输出 "true"

###四、混合方式
* apply call对象冒充

	优点：可以继承构造函数 构造函数可以带参数
	缺点：不能继承原型
* 原型链继承

	优点：可以继承原型
	缺点：不可以继承构造函数

为了即能构造函数带参数又能继承原型我们可以混合使用

* 用对象冒充继承构造函数的属性
* 用原型链继承 prototype 对象的方法



 	function People( name, age ) {
        this.name = name;
        this.age = age;
    }
    People.prototype = {
        sayName : function () {
            alert( this.name );
        }
    };
    function Dog( name, age ) {
        People.call(this, name+"狗",age);
    }

    Dog.prototype = new People();
    var dog=new Dog("单身","22");
    dog.sayName();//单身狗
