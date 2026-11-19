---
name: citegraph
description: "Trace citations and discover related literature from a supplied reference graph: normalize identifiers, build the directed citation graph, compute forward/backward/2-hop neighbourhoods per seed, find connected components and bridgers, and keep every edge traceable to its source record. Trigger on: '引文追踪', '关联文献发现', 'citation tracing', 'related literature', 'reference graph', 'find papers citing'. Not for: live database search, writing, or figure drawing."
---

# citegraph — Citation Tracing and Related-literature Discovery

> **脚本路径**：本技能安装于 `/skills/weekly-skill/`，脚本绝对路径为 `/skills/weekly-skill/scripts/`（工作目录是 `/app/work`，不要用相对路径）。

Turn a retained reference graph into a navigable citation network: who cites whom, what sits one or two hops away from a seed, which papers bridge clusters. The failure mode this skill exists to prevent: "related papers" with no traceable link, seeds silently dropped, and identifiers that should be the same paper treated as different.

**Method in one line:** normalize ids (DOI/title) → build directed graph → BFS both directions up to N hops → components + bridgers → report with per-edge provenance.

## When to use

- Input: a case file (default `/input/case.json`) with `records[]` (each with an id and a `references`/`cites` list), `seeds[]`, `depth` (default 2), `top_k`.
- Output: under `/app/output` — `graph.json`, `related.json`, `result.json`.

## Pipeline

```bash
python3 /skills/weekly-skill/scripts/citegraph.py --case /input/case.json --work /app/work
```

1. **Normalize** DOI/title into one key space (lowercase, URL prefix stripped, non-alphanumerics to spaces).
2. **Graph** — nodes are records; edges come from `references`/`refs`/`cited`/`cites`/`bibliography`/`reference_list`. Each edge keeps `source`, `target_key`, `target`, `raw`, `field`.
3. **Neighbourhood** — BFS over both directions from each seed up to `depth` hops; neighbours recorded with `hops`.
4. **Components** — connected components of the undirected graph.
5. **Bridgers** — seeds whose neighbourhood touches more than one component.
6. **Report** — graph stats, per-seed neighbours, components, bridgers.

## Rules that must hold

1. **Every edge is traceable** to the record and field it came from.
2. **Seeds are never silently dropped** — a missing seed is reported `found: false`.
3. **No outside literature** — the graph is limited to the supplied records.
4. **Identifier merging is flagged** as a normalization assumption in `uncertainty`.
5. **Bridgers are reported, not assumed** — a seed must touch ≥2 components.

## Exception handling

| Situation | Behaviour |
|-|-|
| No records | `status: partial`, empty graph |
| Seed not in graph | `found: false` with note |
| Reference target not in records | edge kept with `target: null` (unresolved) |
| Duplicate ids | first wins; later duplicates map to the same key |

## Self-check

```bash
python3 -c "import json;g=json.load(open('/app/work/graph.json'));print(g['stats']);print(all('field' in e for e in g['edges']))"
python3 -c "import json;r=json.load(open('/app/work/result.json'));print(r['case_id'], r['status'], r['counts'])"
```