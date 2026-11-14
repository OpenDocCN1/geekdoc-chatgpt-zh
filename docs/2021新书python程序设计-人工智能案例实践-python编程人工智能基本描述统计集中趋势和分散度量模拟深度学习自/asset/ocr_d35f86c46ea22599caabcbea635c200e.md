趣的操作。例如，可以通过继承将相关的对象放入列表中，然后遍历列表并将每个元素视为基类对象进行处理。这就使得我们能够以一般化的方式处理各种对象。在这里，我们把CommissionEmployee对象和SalariedCommissionEmployee对象放在一个列表中，然后对列表中的每个元素显示其对应的字符串表示和收入：

```
In [21]: employees = [c, s]

In [22]: for employee in employees:
    ....:     print(employee)
    ....:     print(f'employee.earnings():.{2f}\n')
    ....: 
CommissionEmployee: Sue Jones
social security number: 333-33-3333
gross sales: 20000.00
commission rate: 0.10
2,000.00

SalariedCommissionEmployee: Bob Lewis
social security number: 444-44-4444
gross sales: 10000.00
commission rate: 0.05
base salary: 1000.00
1,500.00
```

可见，对于每名员工，都显示了正确的字符串表示和收入。这就是所谓的多态，多态是面向对象编程的一项重要特性。

# 10.8.4 关于基于对象和面向对象编程的说明

支持方法重写的继承，是基于已存在的相似组件、根据应用程序的独特需求自定义软件组件的一种有效方式。在Python开源世界中，有大量良好设计的类库。基于这些类库，通常采用的编程方式是：

+   - 掌握有哪些库可以使用；
+   - 掌握有哪些类可以使用；
+   - 根据已有类创建对象；
+   - 向已创建的对象发送消息（即调用它们的方法）。

这种编程方式被称为基于对象的编程（Object-Based Programming，OBP）。当基于已知类的对象进行组合时，所进行的仍然是基于对象的编程。一旦增加了继承功能，则相应的编程方式被称为面向对象编程（Object-Oriented Programming，OOP）。在继承中，可以通过重写方法来满足应用程序的独特需求，也可以以多态方式处理对象。如果基于派生的子类的对象进行组合，则所进行的也是面向对象编程。