# citegraph — 引文追踪与关联文献发现 Skill

从给定的参考文献图构建可导航的引文网络：前向/后向/2 跳邻域、连通分量与桥接文献。

## 解决什么问题
"相关文献"无法追溯到来源；同一种子被静默丢弃；同一文献的不同标识被当成两篇。

## 依赖
- Python 3.9+，仅标准库。

## 用法
```bash
python3 scripts/citegraph.py --case /input/case.json --work /app/work
```

输入示例：
```json
{
  "case_id": "demo",
  "depth": 2,
  "seeds": ["10.1000/A"],
  "records": [
    {"id": "10.1000/A", "title": "Attention", "references": ["10.1000/B"]},
    {"id": "10.1000/B", "title": "RNN", "references": ["10.1000/C"]}
  ]
}
```
> 示意示例，不是正式案例结果。

## 输出
| 文件 | 内容 |
|-|-|
| `graph.json` | 节点、边（含来源字段）、统计 |
| `related.json` | 每个种子的邻域（带 hops）、连通分量、桥接者 |
| `result.json` | `case_id` / `status` / counts / uncertainty |

## 关键设计
- 标识符归一化（DOI/标题）；有向图；双向 BFS；边可追溯；种子缺失显式报告；桥接者需触及 ≥2 分量。

## 局限
- 只覆盖给定图，不外推；标识府并是假设，需人工确认。

## 版本
citegraph 1.0.0，纯 Python 标准库。
