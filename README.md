# AxiomKit

> **Give your AI agent unbreakable rules and a memory that never leaks.**
>
> 为 AI 智能体提供不可篡改的行为规则，以及永不出域的记忆共享。

[English](#english) | [中文](#中文)

---

## English

### Why AxiomKit?

Autonomous AI agents fail in two predictable ways: they **rewrite their own constraints** under pressure, and they **leak raw data** when they share knowledge. AxiomKit addresses both with two orthogonal primitives:

1. **Invariants** — formally verified rules (Z3 SMT) that an agent can propose but never approve alone. Proposed rules go through a synthesis → verification → arbitration pipeline; the ground rule set is append-only and hash-locked.
2. **Federated memory** — knowledge is shared between tenants only as *purified semantic fragments*, never as raw documents. A quality grade (A–D) gates what may circulate.

Plus two batteries-included utilities:

- **Gateway** — a minimal HMAC-authenticated HTTP API to query an agent's memory index by anchor conditions (no full scans).
- **Recall** — zero-dependency TF-IDF semantic recall with multi-dimensional routing, for offline agents.
- **Evolution** — guarded recursive self-improvement: the agent proposes changes to its own tunable parameters, a shadow trial measures them, and a grayscale gate promotes or rolls back automatically. The axiom core is frozen; every step is hash-chained for audit.

### Install

```bash
pip install axiomkit            # core, zero hard dependencies
pip install "axiomkit[z3]"      # + formal verification
pip install "axiomkit[http]"    # + memory gateway HTTP API
```

### 60-second tour

```python
from axiomkit.invariants import InvariantPipeline

pipe = InvariantPipeline()

# 1. Propose a rule as a candidate invariant
cand = pipe.propose(
    layer="L3",
    category="circuit-breaker",
    statement="energy_draw(t2) <= energy_draw(t1) + quota for t2 > t1",
)

# 2. Verify it formally (requires z3-solver; falls back to symbolic check without it)
report = pipe.verify(cand)
print(report.status)        # "verified" | "refuted" | "manual-review"

# 3. Arbitrate: only approved invariants enter the locked rule set
pipe.arbitrate(cand.id, decision="approve", arbiter="human-admin")

# 4. The agent can now be checked against the locked set at runtime
print(pipe.locked_snapshot())
```

```python
from axiomkit.fedmem import FederationPool

pool = FederationPool(min_k_tenants=2)

pool.submit(tenant="alpha", unit_id="u1", text="Rate limits must apply per API key.")
pool.submit(tenant="beta",  unit_id="u1", text="Per-key rate limits are mandatory.")

# u1 is corroborated by 2 independent tenants -> becomes publishable fragment
frag = pool.publishable()
print(frag)   # purified fragment; raw tenant text never leaves the pool
```

### Modules

| Module | Import | What you get |
|---|---|---|
| `axiomkit.invariants` | rule synthesis + Z3 verification + append-only ledger | guard-rails agents can't rewrite |
| `axiomkit.fedmem` | cross-tenant semantic fragment pool with K-tenant corroboration | knowledge sharing without data leakage |
| `axiomkit.gateway` | HMAC-signed HTTP API over a memory index (optional `fastapi`) | fast anchored lookups |
| `axiomkit.recall` | TF-IDF + multi-dim routing, stdlib only | offline semantic recall |
| `axiomkit.evolution` | guarded RSI: whitelist → shadow trial → grayscale promote/rollback, frozen core, hash-chained audit | agents that improve themselves without breaking their own rules |

### Origins

AxiomKit is the distilled, general-purpose toolkit extracted from the [ZONGYUAN-ROOT](https://atomgit.com/zongyuangen/ZONGYUAN-ROOT) autonomous governance kernel — its formal invariant pipeline (GASA), federated memory pool (SFMP), dual-axis verification and local semantic recall, rewritten as an open, dependency-light library. See the parent project for the full theoretical framework.

### Status

Alpha. APIs may change. Security review welcome — please open issues.

## 中文

### 为什么需要 AxiomKit?

自治 AI 智能体有两种可预见的失败方式:在压力下**改写自己的约束**,以及在共享知识时**泄露原始数据**。AxiomKit 用两个正交原语解决:

1. **不变量(Invariants)** —— 经 Z3 SMT 形式化验证的规则;智能体可以提议,但不能单独批准。候选规则走 合成 → 验证 → 裁定 流水线;基础规则集只追加、哈希锁档。
2. **联邦记忆(Federated memory)** —— 跨租户只共享*提纯后的语义碎片*,绝不共享原始文档。质量分级(A–D)决定哪些片段可流通。

另附两个开箱即用的工具:

- **Gateway** —— 极简 HMAC 鉴权 HTTP API,按锚定条件定点查询记忆索引(不做全量扫描)。
- **Recall** —— 零依赖 TF-IDF 语义召回 + 多维路由,适合离线智能体。
- **Evolution** —— 受控递归自改进(RSI):智能体可提议修改自身可调参数,影子试验度量效果,灰度门控自动晋级或回滚;公理核心冻结不可改,每步哈希链留痕可审计。

### 安装

```bash
pip install axiomkit            # 核心,零硬依赖
pip install "axiomkit[z3]"      # + 形式化验证
pip install "axiomkit[http]"    # + 记忆网关 HTTP API
```

### 60 秒上手

见上方英文示例——API 完全一致。

### 模块

| 模块 | 导入 | 能力 |
|---|---|---|
| `axiomkit.invariants` | 规则合成 + Z3 验证 + 只追加账本 | 智能体无法改写的护栏 |
| `axiomkit.fedmem` | 跨租户语义碎片池,K 租户交叉印证 | 不泄露数据的知识共享 |
| `axiomkit.gateway` | 记忆索引上的 HMAC 签名 HTTP API(可选 `fastapi`) | 锚定条件定点查询 |
| `axiomkit.recall` | TF-IDF + 多维路由,仅标准库 | 离线语义召回 |
| `axiomkit.evolution` | 受控 RSI:白名单 → 影子试验 → 灰度晋级/回滚,冻结内核,哈希链审计 | 会自我进化而不破坏自身规则的智能体 |

### 来源

AxiomKit 是从 [ZONGYUAN-ROOT](https://atomgit.com/zongyuangen/ZONGYUAN-ROOT) 自治治理内核中提炼的通用工具包——其形式化不变量流水线(GASA)、联邦记忆池(SFMP)、双轴校验与本地语义召回,以开源、轻依赖库的形式重写。完整理论框架见主项目。

### 状态

Alpha。API 可能变动。欢迎安全审计——请提 Issue。

## License

Apache-2.0
