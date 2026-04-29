# Research Pipeline V4 — Rules-First Manifesto

> **驱动 (用户 2026-04-29)**: V2/V3 的 paper-anchored 全是红海。V4 必须 rules-first, 验证为目的。
> **核心断言**: 不再 "看论文找 idea",而是 **"先定规则,找哪儿能验证它"**。

## 哲学反转 (paper-first → rules-first)

```
旧链路 (V2/V3 — red ocean):
  papers → axioms → reframe → experiment
   ↑ same paper context = same idea, 同一假设空间内变种

新链路 (V4 — rules-first):
  rule (用户/Gemini 第一性原理拆解)
    ↓
  寻找 "在哪个领域 / 哪个 dataset 能验证这条 rule"
    ↓
  feasibility + novelty audit
    ↓
  experiment plan + execute + verify
   ↑ rule space 远比 paper space 小 + 跨领域可迁移
```

## 模型分工 (固定)

| 阶段 | 模型 | 职责 |
|---|---|---|
| **规则拆解** | Gemini 3 Pro | 第一性原理把 rule 拆成 testable predictions |
| **领域定位** | Claude Opus 4.7 | 调 Scholar Search 找哪个 dataset/domain 能验证 rule |
| **可行性 + 原创性** | Codex GPT-5.4 | feasibility audit + novelty audit (是否被人做过) |
| **执行** | Claude Sonnet 4.6 | 写 train.py + 跑实验 + 写 results.tsv |
| **验证** | Claude Opus 4.7 | 验证 rule 是否被实验证实/证伪 |

## 文件结构 (简化, 替代 rgmare-lite)

```
~/research/v4/
├── MANIFESTO.md                  ← 你正在读
├── rules/
│   ├── R-001-<short-name>.yaml   ← 1 rule = 1 yaml
│   ├── R-002-<short-name>.yaml
│   └── ...
├── experiments/
│   └── R-001/
│       ├── plan.md               ← /v4-experiment-plan 输出
│       ├── decompose.yaml        ← Gemini 第一性原理拆解
│       ├── locate.yaml           ← Opus + Scholar 定位的 dataset
│       ├── feasibility.yaml      ← Codex 可行性 + 原创性
│       ├── train.py              ← Sonnet 写的训练代码
│       ├── results.tsv           ← 实验数据
│       └── verify.md             ← Opus 最终验证报告
├── scoring/
│   └── SCORING-V4.md             ← 4 axes only (vs V2 的 7 axes)
└── contracts/
    └── V4-RULE-SCHEMA.yaml       ← 1 rule yaml 的标准格式
```

## 1 Rule YAML 格式 (rules/R-001-<name>.yaml)

```yaml
---
id: R-001
name: <short-snake-case>
type: first-principle | constitution-clause | module-design | optimization-axiom
status: draft | locating | feasibility-checked | planning | executing | verified | falsified
created_at: 2026-04-29
created_by: user | gemini-decompose | opus-derive

statement: |
  <2-3 sentences — what this rule claims>
  Example: "Recurrent depth in language models can be expanded WITHOUT retraining
            if hidden state has a Lipschitz fixed-point structure."

origin:
  source: user-prompt | first-principles | module-redesign | constitution-migration
  trigger_question: "<the question that gave birth to this rule>"

falsifier:
  testable_prediction: "<if we measure X under condition Y, we should see Z>"
  five_min_kill: "<single-GPU 5-min experiment>"

scope:
  applicable_domains: [LLM, vision, speech, code, ...]
  excluded_domains: [...]

provenance:
  derived_from: <citekey | another rule id | first-principles>
  conflicts_with: <list of citekeys or rule ids>

# Filled by /v4-rule-decompose (Gemini)
first_principles:
  - <atom 1>
  - <atom 2>
  ...

# Filled by /v4-rule-locate (Opus + Scholar)
candidate_datasets:
  - name: <e.g. MMLU, ImageNet, GSM8K>
    why_fits: "<one line>"
    why_not: "<one line, if has caveat>"
    scholar_evidence: "<Scholar URL of paper using this dataset>"

# Filled by /v4-rule-feasibility (Codex)
feasibility:
  hardware_required: "<1×4090 | 8×H100 | ...>"
  data_size: "<rough estimate>"
  time_estimate: "<5min | 1h | 1day>"
  novelty_status: confirmed-novel | near-match | published | cannot-verify
  near_matches: [<citekey>, ...]
  status: GO | NO-GO | DEFER
```

## 命令链路 (Superpowers-style 单 Loop)

```
/v4-rule-add        → 用户输入 rule, 创建 rules/R-XXX.yaml
        ↓
/v4-rule-decompose  → Gemini 拆 first_principles
        ↓
/v4-rule-locate     → Opus + Scholar 找 candidate_datasets
        ↓
/v4-rule-feasibility → Codex 跑 feasibility + novelty
        ↓ (if GO)
/v4-experiment-plan → 生成 experiments/R-XXX/plan.md
        ↓
/v4-experiment-run  → Sonnet 写 + 跑代码 (superpowers verification-before-completion)
        ↓
/v4-experiment-verify → Opus 验证 rule 是否被证实
        ↓
verified | falsified
```

## V4 与旧链路对照

| 维度 | V2/V3 | V4 |
|---|---|---|
| 起点 | 14 papers/topics | 1 rule (用户给或 Gemini 拆) |
| 红海风险 | 高 (papers 已被人挖) | 低 (rule space 跨域可迁移) |
| 阶段数 | 6 (init/survey/axiom/propose/dispatch/loop) | 4 (decompose/locate/feasibility/run) |
| 并行度 | 9 niches share context | 1 rule 1 实验, 多 rule 自然并行 |
| 文件结构 | rgmare-lite/ledgers/<sprint>/<14 dirs> | v4/rules/ + v4/experiments/ |
| 上下文隔离 | v3.5 才加 (friction partition) | 天然隔离 (1 rule 1 yaml) |
| 验证为目的 | 后置 (loop 在末) | 前置 (rule 自带 falsifier) |

## rgmare-lite Deprecation

**不删,但停用**:
- `~/research/rgmare-lite/` 保留为 v2/v3 历史归档
- 不再写新 sprint 到 ledgers/
- 已有 friction.yaml (来自 cron) 可作为 rule 的输入源——但 v4 不强依赖

## 当前还保留的基础设施

- ✅ Friction Extract cron (Kimi K2.6) — paper → friction.yaml,可选输入到 v4 rules
- ✅ scholar MCP server — Opus 调用找 dataset
- ✅ Hermes 12 cron jobs — 不动
- ❌ /research-init / survey / axiom / propose / dispatch — 转 deprecated (替代为 /v4-*)
- ❌ /research-ideate / ideate-audit — 转 deprecated

## 用户决策点 (V4 启动前)

1. **rgmare-lite 处理**: 完全删除 vs 保留为只读归档?
2. **第一个 rule**: 你来写第一条 rule (作为 sample) 还是我从 friction.yaml 抽 1 条?
3. **/v4-experiment-run 自动化程度**: 全自动 (Sonnet 自己写 train.py 跑) vs 半自动 (生成 plan, 用户 review 后跑)?
4. **scoring v4 axes**: 简化到 4 (novelty + feasibility + falsifier_strength + scale-fit) 还是保留 v2 的 7?
