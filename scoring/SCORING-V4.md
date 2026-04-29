# Scoring V4 — 4 axes only (rules-first edition)

> **简化逻辑**: V2 用 7 axes 评分 reframe; V4 评分对象是 rule (不是 reframe), 4 axes 足够。

## 4 axes

| # | Axis | What it measures | Range | Weight |
|---|---|---|---|---|
| 1 | **first_principle_strength** | 这条 rule 是否真的来自 first-principle 推导, 还是 paper-heuristic? | 0-5 | 1.5 |
| 2 | **falsifier_concreteness** | 5-min kill experiment 是否具体到能直接 run? | 0-5 | 1.5 |
| 3 | **dataset_availability** | candidate_datasets 是否真存在 + 单卡可跑? | 0-5 | 1.0 |
| 4 | **novelty_after_scholar_audit** | Codex Scholar audit 后, status = confirmed-novel? | 0-5 | 1.0 |

**Total range**: 0-25 (= 5×1.5 + 5×1.5 + 5×1.0 + 5×1.0)

## Verdict

```
total ≥ 18  → GO (执行 /v4-experiment-run)
14-17       → NEEDS REWORK (返回 /v4-rule-decompose 重新拆)
< 14        → DEFER (rule 进 backlog)

axis-2 = 0  → DROP (没 falsifier = 不可证伪)
axis-4 ≤ 1  → DROP (已被人做过)
```

## 跟 V2 SCORING 的区别

- V2: 评分对象 = reframe (从 paper 衍生), 7 axes (含 system_tier_baseline)
- V4: 评分对象 = rule (从 first-principle 衍生), 4 axes
- V4 删掉的 V2 axes: mechanism_strength, compression, prior_art_overlap_risk
  原因: 这些已经被 rule 自身的 type + statement + falsifier 覆盖
- V4 强化的: first_principle_strength (新), falsifier_concreteness (从 V2 的 ax-6 升级为 ax-2)

## 用途

- /v4-rule-feasibility 输出 SCORING-V4 评分
- 评分 ≥ 18 才进 /v4-experiment-plan
- 落盘到 experiments/R-XXX/feasibility.yaml 的 score 字段
