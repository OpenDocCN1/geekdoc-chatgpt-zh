```
class People {
    sayHi(){
        console.log("Hi");
    }
}
class Teacher extends People {
    constructor(name,age,subject){
        super();
        this.name = name;
        this.age = age;
        this.subject = subject;
    }
    teaching(){
        console.log(this.name+"老师正在教"+this.subject);
    }
}
var t1 = new Teacher("珺少",27,"JavaScript");
t1.teaching();    //珺少老师正在教JavaScript
t1.sayHi();       //Hi
```

# 2.1.13 解构赋值

解构赋值也是 ECMAScript6 的一种新特性，通常会使用点语法获取对象中属性的值，示例代码如下：

```
var obj = {
    name:"珺少",
    age:27
}
var name = obj.name;
var age = obj.age;
console.log(name,age);    //珺少 27
```

如果对象中属性很多，这种方法会非常麻烦，并且增加许多逻辑类型的冗余代码，使用解构赋值可以一次性解析对象进行属性取值，示例代码如下：

```
var {name,age} = obj
console.log(name,age);    //珺少 27
```

在进行对象的解构赋值时，左侧大括号中声明的变量名字只要和对象的属性名一致，即可直接完成解析与赋值。需要注意的是，如果声明的变量名在对象中并没有属性可以对应，则会被赋值为 undefined，示例代码如下：

```
var obj = {
    name:"珺少",
    age:27
}
var {name,age,unKnow} = obj
console.log(name,age,unKnow);    //珺少 27 undefined
```

如果变量名与对象中的属性名不一致，也可以额外指定变量名，示例代码如下：