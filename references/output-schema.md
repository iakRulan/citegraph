# 输出字段字典

## `graph.json`
- `case_id`
- `nodes[]`：`id` / `title` / `key` / `year`
- `edges[]`：`source` / `target_key` / `target`（可空）/ `raw` / `field`
- `stats`：`nodes` / `edges` / `resolved_edges` / `components`

## `related.json`
- `seeds[]`：`seed` / `found` / `neighbours[]{id,title,hops}` / `counts{1-hop,2-hop}` / `note`
- `components[]`：`size` / `members[]`
- `bridgers[]`：`seed` / `components[]`

## `result.json`
`case_id` / `task` / `status` / `deliverables` / `counts{records,nodes,edges,resolved_edges,components,seeds,bridgers}` / `uncertainty` / `sources` / `changes`
