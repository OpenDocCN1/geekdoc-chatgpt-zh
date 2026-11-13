[PRE0]

function calculateFiringSolution(origin: Vector,
    target: Vector,
    speed: float,
    gravity: Vector) -> Vector:

    # 反向向量：从目标指向发射点
    offset: Vector = origin - target

    # 构建四次方程系数 a, b, c
    a = gravity.sqrMagnitude
    b = -4 * (Vector.Dot(gravity, offset) + speed * speed)
    c = 4 * offset.sqrMagnitude

    # 判别式检查：无实根则无解
    discriminant = b * b - 4 * a * c
    if discriminant < 0:
        return null

    # 计算两个候选飞行时间
    sqrtD = sqrt(discriminant)
    t0 = sqrt((-b + sqrtD) / (2 * a))
    t1 = sqrt((-b - sqrtD) / (2 * a))

    # 筛选合法的最小正时间
    var flightTime: float
    if t0 < 0:
        flightTime = (t1 < 0) ? -1 : t1
    else:
        flightTime = (t1 < 0) ? t0 : min(t0, t1)

    if flightTime < 0: return null

    # 推导初速度向量
    return (offset * 2 - gravity * flightTime * flightTime) / (2 * speed * flightTime)

[PRE1]

阻力模型：$D = -k\mathbf{v} - c\mathbf{v}^2$

[PRE2]

含阻力运动方程：
$\ddot{\mathbf{p}}_t = \mathbf{g} - k\dot{\mathbf{p}}_t - c\dot{\mathbf{p}}_t |\dot{\mathbf{p}}_t|$

[PRE3]

线性阻力简化模型 (3.6)：
$\ddot{\mathbf{p}}_t = \mathbf{g} - k\dot{\mathbf{p}}_t$

[PRE4]

function refineTargeting(origin: Vector,
    target: Vector,
    muzzleV: float,
    gravity: Vector,
    tolerance: float) -> Vector:

    # --- 内部辅助：按角度试算 ---
    function checkAngle(pitch: float):
        toTarget = target - origin
        dir = angleToVector(toTarget, pitch)
        err = simulateError(dir, origin, target, muzzleV)
        return dir, err

    # 1. 先用无阻力解做初始猜测
    guessDir = calculateFiringSolution(origin, target, muzzleV, gravity)
    err = simulateError(guessDir, origin, target, muzzleV)

    # 命中判定
    if -tolerance < err < tolerance:
        return guessDir

    # 2. 二分法修正角度
    # 当前俯仰角
    currentPitch = asin(guessDir.y / guessDir.magnitude)

    var lo, hi: float

    if err > 0:  # 打远了 -> 角度过大，当前为上界
        hi = currentPitch
        lo = -PI / 2
        # 检查下界是否刚好命中
        dirLo, errLo = checkAngle(lo)
        if -tolerance < errLo < tolerance: return dirLo
    else:        # 打近了 -> 角度过小，当前为下界
        lo = currentPitch
        hi = PI / 4  # 45度为理论最大射程上限
    # 后续二分循环省略...