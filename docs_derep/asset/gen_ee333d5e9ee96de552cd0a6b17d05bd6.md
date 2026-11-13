# 游戏AI转向行为：从避障到决策的代码实战解析

[PRE0]

```python
class ObstacleAvoidance(Seek):
    # 碰撞检测器引用
    detector: CollisionDetector
    
    # 【核心参数】安全缓冲距离
    # 含义：与障碍物保持的最小间距，必须大于角色体积半径
    avoidDistance: float
    
    # 【核心参数】前瞻探测距离
    # 含义：射线向前投射的长度，决定“看多远”
    lookahead: float
    
    # 继承自 Seek 的其他属性...

    function getSteering():
        # --- 阶段一：构建探测射线 ---
        # 以当前速度方向为基准，归一化后乘以前瞻距离
        ray = character.velocity.normalized() * lookahead
        
        # --- 阶段二：碰撞查询 ---
        collision = detector.getCollision(character.position, ray)
        
        # 无障碍物则直接返回空，不产生转向
        if not collision:
            return null
            
        # --- 阶段三：生成规避目标 ---
        # 目标点 = 碰撞触点 + (法线方向 × 安全距离)
        # 利用 Seek 行为朝该点移动即可实现避让
        target = collision.position + collision.normal * avoidDistance
        
        return super.getSteering()  # 委托父类 Seek 执行最终转向计算
```

[PRE1]

```python
class CollisionDetector:
    # 核心查询接口
    # 输入：起始坐标、移动位移向量
    # 输出：碰撞详情（为空表示安全）
    function getCollision(position: Vector, moveAmount: Vector) -> Collision
```

[PRE2]

```python
struct Collision:
    # 碰撞发生的精确世界坐标
    position: Vector
    # 碰撞面朝向（单位法线），指向障碍物外部
    normal: Vector
```

[PRE3]

```python
class BlendedSteering:
    class BehaviorAndWeight:
        behavior: SteeringBehavior
        weight: float  # 权重系数

    behaviors: BehaviorAndWeight[]
    # 全局输出上限
    maxAcceleration: float
    maxRotation: float

    function getSteering() -> SteeringOutput:
        result = new SteeringOutput()
        
        # 线性加权累加所有行为的输出
        for item in behaviors:
            result.linear   += item.weight * item.behavior.getSteering().linear
            result.angular  += item.weight * item.behavior.getSteering().angular
            
        # 最终结果裁剪，防止数值溢出
        result.linear  = clamp(result.linear,  maxAcceleration)
        result.angular = clamp(result.angular, maxRotation)
        
        return result
```

[PRE4]

```python
# 判定“微小动作”的阈值，通常设为极小值（如 0.001）
epsilon: float

class PrioritySteering:
    # 行为组列表，按优先级从高到低排序
    # 每个组内部可为 BlendedSteering（混合模式）
    groups: BlendedSteering[]

    function getSteering() -> SteeringOutput:
        for group in groups:
            steering = group.getSteering()
            
            # 关键判定：只要当前组产生“有效动作”，立即采纳并中断后续
            if steering.linear.magnitude() > epsilon or abs(steering.angular) > epsilon:
                return steering
                
        # 兜底：所有组输出均微乎其微，返回最后一组的微小值
        return steering
```