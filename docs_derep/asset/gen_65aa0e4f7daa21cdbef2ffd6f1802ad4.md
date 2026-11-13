### 8.3 基于裁剪的轮廓偏置实战

前文介绍了两种基础偏置思路——线段平移法与角平分线法，但均未落地实现。原因有二：一是自研多边形偏置算法工程量极大、易出错；二是计算几何领域已有成熟开源库（如 CGAL、Clipper）可直接调用，没必要重复造轮子。

CGAL 功能全面但 C++ 模板代码晦涩，上手门槛高；相比之下，**Clipper** 专注平面多边形布尔运算，轻量、高效、鲁棒性强，且已移植至 Python、C# 等主流语言。本章将基于 Python 版 Clipper 实现二维轮廓偏置。

---

#### 8.3.1 Clipper 核心概念速览

Clipper 由 Angus Johnson 基于 Vatti 1992 年论文实现，支持交、并、差、异或及偏置运算。

**1. 基础数据结构：Path**  
所有输入输出均为 `Path` 对象，本质是二维多段线（类似前文 `Polyline` 但仅 2D）。可开合、自相交、单点或多点。为叙述方便，下文仍称其为“轮廓”。

**2. 填充规则——布尔运算的基石**  
Clipper 定义四种填充规则，决定平面区域哪些部分被视为“内部”：

| 规则 | 判定逻辑 | 适用场景 |
|------|----------|----------|
| **奇偶填充** | 从区域外发射射线，穿过奇数条边为内部，偶数条为外部 | 简单无自交轮廓组 |
| **非零填充** | 计算绕数，非零即内部 | 复杂自交/嵌套轮廓 |
| **正填充** | 绕数 > 0 为内部 | 同上 |
| **负填充** | 绕数 < 0 为内部 | 同上 |

> **绕数计算**：轮廓从右向左传射线 ➕1，左向右传 ➖1。

**3. 两类轮廓角色**  
- `subject`：被操作对象  
- `clip`：裁剪窗口/操作数  

四大布尔运算中的角色对应关系：  
- **交**：`clip` 为窗口，保留 `subject` 落在窗口内的部分（闭合轮廓时双方可互换）  
- **并**：双方等价，输出覆盖区域并集  
- **差**：`subject - clip`，从被减数扣除减数  
- **异或**：双方等价，输出并集扣除交集后的剩余区域  

**4. 整型坐标设计**  
Clipper 强制使用**整数坐标**（C++ `IntPoint` / Python `Point`），而非浮点。这是有意为之：规避浮点截断误差导致的几何判断翻车（如相交性、包含性测试），显著提升鲁棒性。

---

#### 8.3.2 Python 环境下的极简调用

**安装推荐**：  
```bash
pip install pyclipper   # 自动拉取预编译轮子，版本紧跟上游
```
导入方式：`import pyclipper`。

**核心类 `Pyclipper` 仅四个关键接口**：  
- `AddPath(path, type, closed)` / `AddPaths(paths, type, closed)`：注册轮廓，`type` 为 `PT_SUBJECT` 或 `PT_CLIP`  
- `Execute(op, subj_fill=PFT_EVENODD, clip_fill=PFT_EVENODD)` / `Execute2(...)`：执行运算，`op` 取 `CT_UNION`、`CT_INTERSECTION`、`CT_DIFFERENCE`、`CT_XOR`；填充规则默认奇偶  

**最小化 Demo**：
```python
from pyclipper import *
from VtkAdaptor import *          # 书中配套可视化模块

def to_polyline(tuples):
    poly = Polyline()
    for x, y in tuples:
        poly.addPoint(Point3D(x, y, 0))
    poly.addPoint(poly.startPoint())  # 闭合
    return poly

if __name__ == '__main__':
    subject = [(0,0), (100,0), (100,70), (0,70)]      # 正方形
    clip    = [(30,50), (70,50), (70,100), (30,100)]  # 长方形

    clipper = Pyclipper()
    clipper.AddPath(subject, PT_SUBJECT, True)
    clipper.AddPath(clip,    PT_CLIP,    True)

    result = clipper.Execute(CT_UNION)   # 并运算

    va = VtkAdaptor()
    for pts in result:
        va.drawPolyline(to_polyline(pts)).GetProperty().SetColor(0,0,0)
    va.display()
```
寥寥数行即可完成布尔运算，得益于 Angus Johnson 的极致封装。将 `CT_UNION` 换为其他 `CT_*` 即可切换运算类型；`Execute` 第二、三参数可显式指定 `PFT_NONZERO` 等填充规则。

---

[PRE0]  
[PRE1]  
[PRE2]  
[PRE3]  
[PRE4]