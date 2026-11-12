```
19 distance = distanceToTarget(direction, source, target, muzzleV)
20 if -margin < distance < margin:
21     return direction
22
23 # 否则进行二分搜索
24 # 但必须确认最小边界和最大边界
25 angle: float = asin(direction.y / direction.length())
26 if distance > 0:
27     # 已经找到最大边界
28     # 使用尽可能短的射程作为最小边界
29     maxBound = angle
30     minBound = - pi / 2
31     direction, distance = checkAngle(minBound)
32     if -margin < distance < margin:
33         return direction
34
35 # 否则需要找到一个最大边界
36 # 使用 45° 角 (= pi / 4) 发射可以达到最大距离
37 else:
38     minBound = angle
39     maxBound = pi / 4
40     direction, distance = checkAngle(maxBound)
41     if -margin < distance < margin:
42         return direction
43
44 # 检查最长距离的射击是否能够到达目标
45 if distance < 0:
46     return null
47
48 # 现在已经有了最小边界和最大边界，可以使用二分搜索
49 distance = infinity
50 while abs(distance) >= margin:
51     angle = (maxBound - minBound) / 2
52     direction, distance = checkAngle(angle)
53
54 # 改变合适的边界
55 if distance < 0:
56     minBound = angle
57 else:
58     maxBound = angle
59
60 return direction
```