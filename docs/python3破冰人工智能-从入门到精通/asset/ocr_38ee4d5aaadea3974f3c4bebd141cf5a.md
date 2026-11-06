```
a=[ 1.  1.  1.  1.]

b=[1 1 1 1]

c=[[ 1.]
    [ 1.]]

d=[[ 1.  1.]
    [ 1.  1.]]

e=[ 1.  1.  1.  1.]
```

（2）依据给定形状和类型，返回一个新的元素全部为 0 的数组。函数形式为：

```
zeros(shape, dtype=None, order='C')
```

参数同 np.ones 参数。

# 【例 3-11】zeros 案例

输入：

```
a=np.ones(5)
b=np.zeros((5,),dtype=np.int)
c=np.zeros((2,1))
S=(2,2)
d=np.zeros(S)
e=np.zeros((2,), dtype=[('x','i4'), ('y','i4')])
x=np.arange(6)
x=x.reshape((2,3))  #[[0, 1, 2],[3, 4, 5]] reshape 重塑数组的形状
f=np.zeros_like(x)
y=np.arange(3,dtype=np.float)  #[0., 1., 2.]
g=np.zeros_like(y)
print("a={}\n b={}\n c={}\n d={}\n e={}\n f={}\n g={}".format(a,b,c,d,e,f,g))
```

输出：

```
a=[ 1.  1.  1.  1.  1.]

b=[0 0 0 0 0]

c=[[ 0.]
    [ 0.]]

d=[[ 0.  0.]
    [ 0.  0.]]

e=[(0, 0) (0, 0)]

f=[[0 0 0]
    [0 0 0]]
```