算新的目标方向。

以下将通过举例说明给出三维中的朝向行为的实现。由于这是对本章前文给出的算法的修改，因此这里不再深入讨论该算法（有关更多信息，请参阅朝向行为的前一版本）。

```
class Face3D extends Align3D:
    # 用于计算朝向的基本方向
    baseOrientation: Quaternion

    # 覆盖目标
    target: Kinematic3D

    # ... 其他数据从超类中派生 ...

    # 计算给定向量的朝向
    function calculateOrientation(vector):
        # 通过按基本方向转转 z 轴获取基本向量
        # 仅需为每个基本方向执行一次
        # 所以可在每次方法调用之间创建缓存
        zVector = new Vector(0, 0, 1)
        baseZVector = zVector * baseOrientation

        # 如果基本向量和目标是相同的，则返回基本四元数
        # 如果刚好相反，则返回基本四元数的负数
        if baseZVector == vector:
            return baseOrientation
        elif baseZVector == -vector:
            return -baseOrientation

        # 否则从目标的基本方向找到最小旋转
        axis = crossProduct(baseZVector, vector)
        angle = asin(axis.length())
        axis.normalize()

        # 将这些值打包到四元数中并返回它
        sinAngle = sin(angle / 2)
        return new Quaternion(
            cos(angle / 2),
            sinAngle * axis.x,
            sinAngle * axis.y,
            sinAngle * axis.z)

    # 像追逐行为一样实现它
```