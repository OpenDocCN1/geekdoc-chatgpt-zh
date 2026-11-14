```
run fig02_01.py

1 # fig02_01.py
2 """Comparing integers using if statements and comparison operators."""
3
4 print('Enter two integers, and I will tell you you',
5 'the relationships they satisfy.')
6
7 # read first integer
8 number1 = int(input('Enter first integer: '))
9
10 # read second integer
11 number2 = int(input('Enter second integer: '))
12
13 if number1 == number2:
14 print(number1, 'is equal to', number2)
15
16 if number1 != number2:
17 print(number1, 'is not equal to', number2)
18
19 if number1 < number2:
20 print(number1, 'is less than', number2)
21
22 if number1 > number2:
23 print(number1, 'is greater than', number2)
24
25 if number1 <= number2:
26 print(number1, 'is less than or equal to', number2)
27
28 if number1 >= number2:
29 print(number1, 'is greater than or equal to', number2)
```

```
Enter two integers and I will tell you the relationships they satisfy.
Enter first integer: 37
Enter second integer: 42
37 is not equal to 42
37 is less than 42
37 is less than or equal to 42
```

```
Enter two integers and I will tell you the relationships they satisfy.
Enter first integer: 7
Enter second integer: 7
7 is equal to 7
7 is less than or equal to 7
7 is greater than or equal to 7
```

```
Enter two integers and I will tell you the relationships they satisfy.
Enter first integer: 54
Enter second integer: 17
54 is not equal to 17
54 is greater than 17
54 is greater than or equal to 17
```

# 注释

第1行以字符井号（#）开头，表示该行的其余部分是注释：

```
# fig02_01.py
```