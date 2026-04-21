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