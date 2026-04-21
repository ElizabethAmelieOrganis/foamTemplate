# foamTemplate - OpenFOAM 空白模板
适用于：simpleFoam 稳态、层流、2D 管道流动

## 文件夹结构
0/           - 初始场（速度 U、压力 p）
constant/    - 网格 + 流体属性
system/      - 求解器设置 + 网格图纸

## 必须文件清单（最小可运行算例）
1. 0/U
2. 0/p
3. constant/transportProperties
4. constant/turbulenceProperties
5. system/controlDict
6. system/blockMeshDict
7. system/fvSchemes
8. system/fvSolution

## 数据流向
```plain
blockMeshDict ──→ 生成网格 ──→ constant/polyMesh/
     ↑                                      ↓
     └──────── 边界名字必须对应 ────────────┘
                                             
0/U, 0/p ──────→ 定义边界条件 ──→ 求解器读取
     ↑                                      ↓
     └────── 名字必须与 polyMesh/boundary 一致 ─┘
```

## 运行命令
# 1. 启动OpenFoam
```bash
source /usr/lib/openfoam/openfoam2406/etc/bashrc
run
```

# 1. 生成网格
```bash
blockMesh
```

# 2. 开始计算
```bash
simpleFoam
```

## 可修改的地方
1. system/blockMeshDict → 修改几何形状、网格大小
2. 0/U → 修改入口速度
3. constant/transportProperties → 修改流体粘度
4. system/controlDict → 修改计算步数

## 边界名称（必须对应）
inlet     入口
outlet    出口
walls     壁面

# 3.Q&A

## 如果提示找不到 bashrc，尝试：
```bash
 source /opt/openfoam12/etc/bashrc   # 基金会版本
 source /usr/lib/openfoam/openfoam2406/etc/bashrc  # ESI 版本
```
## 结果解读：高速公路比喻

> 如果你还没学流体力学，用这段比喻来理解你的计算结果。

### 场景设定

想象你的管道是一条**高速公路**，流体是上面的车流：

| CFD 概念 | 高速公路类比 |
|---------|------------|
| 管道入口 | 收费站，所有车并排，速度一样 |
| 管道壁面 | 公路护栏，车必须贴边减速 |
| 管道中心 | 快车道，不受摩擦，速度最快 |
| 流动发展 | 车从收费站出来后，逐渐找到自己的速度 |

### 理想状态（充分发展）

如果公路**足够长**，最终所有车都会形成稳定的"队形"：
- 护栏边：速度 ≈ 0（被摩擦拖住了）
- 越往中间：速度越快
- 正中间：速度是平均的 **1.5 倍**
- 整体形状像一座**拱桥**（数学上叫抛物线）

这就是教科书上的**泊肃叶流（Poiseuille flow）**。

### 你的结果为什么"差一口气"

因为你的公路**太短了**（只有 1 米）：
- 车刚出收费站，还没跑顺就到出口了
- 护栏边的车只降到 0.1（理论上应该是 0）
- 中间的车只涨到 1.25（理论上应该是 1.5）
- 形状像**鼓了一半的包**

### 壁面速度为什么不是 0

CFD 画的是**车道中心**的速度，不是护栏表面的速度。

就像你站在护栏旁边 5 厘米处测车速——不是 0，但已经很慢了。网格越密（车道越多），测量点离护栏越近，速度越接近 0。

### 怎么看到完美抛物线

把公路加长到 10 米（改 `blockMeshDict` 的 x 方向），再跑一遍。出口处的速度剖面就会接近教科书上的完美形状。

### 一句话总结

> **你的算例是对的，只是路太短，车还没跑顺。**
> 
> 这就是 CFD 的魅力：它逼着你理解"流动需要空间来发展"这个物理直觉。

---

### 进阶验证（可选）

| 你想观察什么 | 改哪里 |
|------------|--------|
| 看流动怎么从均匀变抛物线 | 加长管道，多切几个 x 位置的剖面 |
| 让壁面速度更接近 0 | y 方向加密到 40 个单元 |
| 验证压降公式 | 对比理论 `dp/dx = -12·nu·U/H²`