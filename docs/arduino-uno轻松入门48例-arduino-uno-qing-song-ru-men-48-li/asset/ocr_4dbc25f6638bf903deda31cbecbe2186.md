表示密码输入正确。此方法适用于编写独立按键控制下的密码锁程序。

```
if(digitalRead(1)==0| 语句 1;

if(digitalRead(3)==0| 语句 2;

if(digitalRead(5)==0| 语句 3;

if(digitalRead(2)==0| 语句 4;

if(digitalRead(4)==0| 语句 5;

if(digitalRead(6)==0| 语句 6;

}
}
}
}
}
}
```

该语句表示如果满足外层判断条件，那么执行下一层语句，如果同时满足外层和下一层判断条件，那么更进一步执行再下一层语句，