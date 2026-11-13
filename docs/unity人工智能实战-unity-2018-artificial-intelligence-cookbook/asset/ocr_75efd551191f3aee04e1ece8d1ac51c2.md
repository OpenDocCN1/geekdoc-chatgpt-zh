```
speed);
float c = 4 * Vector3.Dot(delta, delta);
if (4 * a * c > b * b)
{
    return direction;
}
float time0 = Mathf.Sqrt((-b + Mathf.Sqrt(b * b - 4 * a * c)) / (2*a));
float time1 = Mathf.Sqrt((-b - Mathf.Sqrt(b * b - 4 * a * c)) / (2*a));
```

+   3. 如果根据参数可以射中抛射体，就返回一个非 0 的 direction 向量:
+   
+   ```csharp
+   float time;
+   if (time0 < 0.0f)
+   {
+       if (time1 < 0)
+       {
+           return direction;
+       }
+       time = time1;
+   }
+   else
+   {
+       if (time1 < 0)
+           time = time0;
+       else
+           time = Mathf.Min(time0, time1);
+   }
+   direction = 2 * delta - Physics.gravity * (time * time);
+   direction = direction / (2 * speed * time);
+   return direction;
+   ```

# ## 运行原理

根据固定的速度值，解出相应的二次方程式的值，以获取目标方向（当至少存在一个值时），这个方向值不用归一化，因为在初始化抛射体时已经归一化这个向量了。

# ## 延伸阅读

注意当时间为负值时返回了一个空的方向，这意味着速度不够。解决这个问题的方法是定义一个函数，测试不同的速度后再射击这个抛射体。

另一个相关的改进是添加一个额外的 bool 类型参数，用于当有两个有效时间点时（意味着可能有两条弧线），就需要透过障碍物（比如墙体）射击：

```
if (isWall)
time = Mathf.Max(time0, time1);
else
time = Mathf.Min(time0, time1);
```

# ## 1.16 创建跳跃系统

假设正在开发一款很酷的动作类游戏，玩家可以通过悬崖或屋顶逃跑。在这种情况下，敌人要能够追赶玩家，并且要足够聪明地去识别是否跳跃并计算出怎么跳。