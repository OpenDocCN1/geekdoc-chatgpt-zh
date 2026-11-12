```
# 更新位置和方向
half_t_sq: float = 0.5 * time * time
position += velocity * time + steering.linear * half_t_sq
orientation += rotation * time + steering.angular * half_t_sq
# 更新速度和旋转
velocity += steering.linear * time
rotation += steering.angular * time
```

上述更新使用了高中物理方程进行运动。如果帧速率很高，那么传递给该函数的更新时间可能非常小。这个时间的平方可能更小，因此加速度对位置和方向的贡献将很小。更常见的是从更新算法中删除这些项目，以提供所谓的牛顿-欧拉-1（Newton-Euler-1）集成更新：

```
class Kinematic:
# ... 和前面一样的成员数据 ...
function update(steering: SteeringOutput, time: float):
# 更新位置和方向
position += velocity * time
orientation += rotation * time
# 更新速度和旋转
velocity += steering.linear * time
rotation += steering.angular * time
```

这是用于游戏的最常见更新。请注意，在两个代码块中，已经假设可以使用向量进行常规数学运算，例如，通过标量进行加法和乘法运算。开发人员可能需要根据自己所使用的语言，使用函数调用替换这些基本运算。

Morgan Kaufmann（摩根考夫曼）出版的交互式 3D 技术系列中的 Game Physics（《游戏物理学》，详见附录参考资料[12]）一书和 Ian 的 Game Physics Engine Development（《游戏物理引擎开发》，详见附录参考资料[40]）一书，对不同的更新方法进行了全面分析，涵盖了完整的物理学方面的游戏工具（以及向量和矩阵运算的详细实现）。

# 3. 可变帧速率

请注意，到目前为止，我们假设速度是以每秒为单位而不是按每帧为单位给出的。早期的游戏通常使用以每帧为单位的速度，但现在这种做法已基本消失。虽然以固定帧速率在单独执行线程中进行处理以隔离图形更新的做法偶尔也会死灰复燃，例如，Unity 就有 FixedUpdate 函数，可取代使用可变帧速率的 Update 函数，但即