# 微信小程序开发实战

switch-case 结构的最后还可以添加一个 default 代码块，当所有的 case 都匹配失败后，会执行 default 代码块中的代码，示例代码如下：

```
var res = "优秀1";
switch (res) {
    case "优秀":
    {
        console.log("分数大于或等于 85 分");
    }
    break;
    case "良好":
    {
        console.log("分数大于或等于 70 分且小于 85 分");
    }
    break;
    case "及格":
    {
        console.log("分数大于或等于 60 分且小于 70 分");
    }
    break;
    case "不及格":
    {
        console.log("分数小于 60 分");
    }
    break;
    default:
    {
        console.log("错误的成绩");
    }
    break;
}
```

# 2.1.7 循环结构

分支结构的作用是让程序更加灵活，可以根据输入做出不同的逻辑，循环结构则让程序大量重复执行某段代码。计算机非常善于执行大量重复的计算，并且速度非常快。JavaScript 中常用的循环结构有 3 种，分别为 while 循环、do-while 循环和 for 循环。

while 循环是最简单的循环结构，其首先会进行循环条件的判定，如果条件成立，则继续执行循环体中的代码，执行完后会再次进行循环条件的判定，循环执行循环体中的代码，直到循环条件不成立为止，示例代码如下：

```
var count = 0;
var res = 0;
while(count<=100) {
    res+=count;
    count++;
}
console.log(res);
```