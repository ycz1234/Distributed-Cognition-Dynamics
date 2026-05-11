# 异质智能体网络的理论研究 — Framework v2

> **文档定位**: 研究计划框架的内部 reference, 用于 (1) 记录已 settled 的判断与理由 (2) 标识仍 open 的 framework-level 问题 (3) 列出可独立推进的下一步思路。
>
> **当前阶段**: framework v2。从 v1 升级的核心: 加入 operational procedure (4 步), 加入 task formulation 与 task structure 的分离, 加入 active communication 与 persistent state 维度, 显式 mark 时间维度 deferred。
>
> **未在本文档处理**: 6 个月具体路径、第一篇 paper 选题、与 advisor 沟通的具体 framing。

---

## 一、Thesis Statement

> **异质智能体网络的合适分析方式是 problem-indexed abstraction: 每个研究问题决定其所需的抽象层级; 合适的抽象是能捕获该问题本质特征的最小抽象。本论文阐明此原则, 识别若干 canonical 问题, 对每个问题构造其 minimal abstraction, 并证明 representative theorem。**

这个 statement 的两个关键 claim:

1. *Abstraction 不应预先选定后通用*。Single abstraction 通用于所有问题是常见但错误的假设——不同问题的本质特征不同, 单一抽象要么 over-engineer (在简单问题上过重), 要么 under-engineer (在复杂问题上漏关键 dimension)。
2. *Problem 与 abstraction 之间存在 minimal-faithful matching*。给定一个研究问题, 存在 (一个或几个) "刚好够用"的抽象——既不丢失本质特征, 也不引入无关复杂度。识别这种 matching 本身是研究内容。

---

## 二、定位与方法学承诺

### 2.1 Privileged instantiation: agent 网络, 不绑定 LLM

LLM 是 agent 概念在当前技术阶段的最自然实现, 但 LLM 技术 stack 仍在快速演化, 绑定 LLM 会让 dissertation 在 1-2 年内 dated。Framework 承诺的对象是 "agent 概念本身的数学抽象", LLM 系统是 demonstration target, 不是定义对象。

### 2.2 方法学承诺

- *给已有 empirical 系统铺 theoretical underpinning*。不与 application layer 工作 (AutoGen, AgentNet, Memini, Panini 等) 在架构 / 系统竞赛上竞争; 提供这些系统未触及的理论支柱
- *Minimal model + analytical theorem + 经验验证*。模型尽可能简单, 定理尽可能清晰, 经验验证作为 theorem 预测的检查
- *数学语言依 abstraction 选择切换*。不同 chapter 可使用不同数学 community 的 vocabulary (control theory / 演化博弈 / 算子理论 / 信息论 等), 但 motivation 和 implication 部分必须 cross-community 可读

### 2.3 与四条相关 lineage 的关系

我们识别了四条 substantial 的现有 abstraction lineage:

1. **Open games / Compositional Game Theory** (Hedges, Ghani 等): agent = symmetric monoidal category 中的 morphism。强 composition, 弱内部 dynamics
2. **Active Inference / Free Energy Principle** (Friston lineage; de Vries 2026 综述): agent = variational free energy 最小化器。已有针对 multi-LLM 的扩展
3. **Information-Theoretic Bounded Rationality** (Braun, Ortega, Genewein, Tsiotras): agent = capacity-limited channel; abstraction 与 hierarchy 自然 emerge 自 information 约束
4. **Strategic Player on Networks** (Nowak / Plotkin lineage): agent = graph 节点上的 strategy + payoff; clean theorem 风格

**关键观察**: 这四条 lineage 之间几乎不互相引用。Problem-indexed abstraction 原则允许在不同问题上 import 不同 lineage 的工具, 这本身可能是 framework 的 cross-community contribution 之一。

---

## 三、两个 Seed 的关系

```
Seed 1: 网络组织与效能 (trunk)
                   │
    ┌──────────────┼──────────────┐
    │              │              │
 组织/assignment  稳定性      Seed 2: 演化/跟踪
 (Q-A)           (Q-B)        (Q-C, branch)
```

- **Seed 1 是 trunk**: 异质智能体网络的组织与效能, 涵盖结构选择 (拓扑、assignment、约束分配) 到系统行为的可证明 mapping
- **Seed 2 是 branch**: 固定结构下知识状态演化, 是 trunk 下一个 specific question
- **关系是 design-operation dual**, 类比控制理论的 controller design vs closed-loop dynamics, 不是集合包含

Seed 2 在历史 framing 上比 Seed 1 理论密度高, 但作为研究 programme 的可持续性低于 Seed 1。因此 Seed 1 主推, Seed 2 作为 trunk 下的一个 canonical question 处理。

---

## 四、Problem-Indexed Abstraction 原则与 Operational Procedure

### 4.1 原则陈述

Abstraction 不应预先选定后通用; problem 与 abstraction 之间存在 minimal-faithful matching; 识别这种 matching 本身是研究内容。

### 4.2 Operational Procedure (新增 in v2)

给定一个 research question, 通过以下 4 步推 abstraction stack:

#### Step 1: Identify research question

- *Concrete enough to anchor 数学*: 不能完全抽象到 "agent 协作" 这种泛化层
- *Abstract enough to not be application-specific*: 不要锁到 "5 个 LLaMA-3 instance summarize PDF" 这种过窄 deployment
- 1-2 句描述: 系统在做什么 + 关心什么 metric

#### Step 2: 抽象 agent 节点

- 选 candidate abstraction (light processor / strategic player / operator on state / noisy operator / 等)
- Selection criterion: 能 capture 该 question essential agent feature 的 minimal 抽象
- 同时 specify *information allocation*: agent 看到什么 input。Ensemble setting (所有 agent 看 full input) vs distributed setting (agent 看 partition) 是不同 abstraction

#### Step 3: 抽象交互方式

Interaction 分两类组件, 选 question essential 的:

**(3a) Transient message passing**:
- 通信内容 (raw input / processed output / message)
- 通信成本 (无成本 / capacity bound / latency)
- 通信拓扑 (static graph / dynamic / mean-field)
- 同步性 (同步 round / 异步 / streaming)
- 信息可见性 (全局 visible / 私有)
- **Message type** (新增 in v2): information transfer / action request / query / negotiation。不同 type 数学结构不同, 因为 message 不只 carry content 也 induce receiver action

**(3b) Persistent shared state**:
- 节点与外部数据结构 (e.g. shared knowledge graph) 的交互
- 状态跨 round 累积, 需 dynamical system + operator semigroup 描述
- Q-C 必须 model 这个; Q-A 可能不需要; Q-B 视具体 setup

每个 question 在 (3a) 和 (3b) 上的 emphasis 不同。Minimal: 该 question 不 require 的 dimension 不 specify。

#### Step 4: 回到目标 (verification + closure)

- 用 (Step 2) + (Step 3) 的抽象描述系统目标
- Check 跟 (Step 1) 的 problem 描述一致
- 一致 → abstraction stack 成立, 可以 derive theorem
- 不一致 → 回到 Step 2 或 Step 3 调整

**Exit condition**: Procedure 在 Step 4 verification 通过 *且* abstraction stack 对应一个 well-posed 数学问题 (有明确 unknown 待求 + 明确 constraint) 时 terminate。

### 4.3 时间维度 deferred (新增 in v2)

Static abstraction 默认假设 single-round 或 stationary。时间显式 modeling 留给后续 framework 版本, 除了 Q-C (跟踪) 这种 question 本身 time-dependent 的例外。

理由: 显式 time 立刻引入 dynamical systems 复杂度。第一阶段 static abstraction 让 theorem form 更 clean, time extension 作为 future work。

### 4.4 与 "spectrum of abstractions" 的区别

我们 considered 但 reject 了 "spectrum" framing (按复杂度排列 abstraction)。原因: spectrum 隐含 abstraction 之间存在线性顺序, 这是不必要的承诺。Problem-indexed 没有这个承诺——abstractions 由 problem 决定, 互相之间不必有 reduction 关系。

### 4.5 Dissertation unity 来源

不来自 abstractions 互相 reduce, 而来自:
- *方法学一致*: 都是 minimal-faithful matching
- *theorem form 共享* (aspirational): 多个定理可能呈现共同 algebraic structure (e.g. *异质性指标 × 谱量* 的形式)。如果成立, 是强 unity; 如果不成立, dissertation 仍由方法学一致性 unify

---

## 五、Task Formulation 与 Task Structure (新增 in v2)

V1 没显式处理"网络目标"如何抽象。V2 将这件事拆成两个 orthogonal axes。

### 5.1 Task formulation: 系统在做什么类型的事

4 种 candidate, 每种数学结构不同:

#### (F1) Function approximation

- 系统计算 target function $F^*: \mathcal{X} \to \mathcal{Y}$
- Network output = aggregated estimate $\hat{F}$
- Objective: minimize $\mathbb{E}_x \|F^*(x) - \hat{F}(x)\|^2$
- 数学: distributed function approximation, ensemble methods, communication complexity (后两者依 information allocation 而定)

#### (F2) Distributed inference / belief aggregation

- 系统对 latent $Z$ 做 posterior inference
- Agent 是 noisy channel, network = Bayesian aggregator
- Objective: minimize KL to true posterior
- 数学: 信息论 + Bayesian network

#### (F3) Cooperative game / collective utility

- 系统 collectively 实现 utility $U(s_1, ..., s_n)$
- 每个 agent 选 strategy $s_i$, with capability + topology constraints
- Objective: $\max U$
- 数学: 合作博弈 + 网络博弈 + 演化博弈

#### (F4) Dynamic prediction tracking

- 环境 $w(t)$ non-stationary, 系统 track best estimate
- Objective: minimize tracking error
- 数学: TTSA + tracking analysis

### 5.2 Task structure: task 内部如何 decomposed

跟 task formulation 正交:
- *Sequential*: $\tau = \tau_1 \circ \tau_2 \circ ... \circ \tau_k$, agent 之间 chain
- *Parallel*: 各 agent 独立处理, output 聚合
- *Hierarchical*: 多层 decomposition, agent 在不同层
- *Adaptive*: structure 依 input 而定

任何 task formulation 都可以配任何 task structure。

### 5.3 Question 与 (formulation, structure) 的 mapping

| Question | Likely formulation | Likely structure |
|----------|-------------------|------------------|
| Q-A 组织 | F1 或 F2 (取决具体设定) | parallel 或 sequential |
| Q-B 稳定性 | F1 或 F3 | sequential (错误传播 chain) |
| Q-C 演化跟踪 | F4 | adaptive |

这只是 likely mapping, 实际选择由 problem 决定。

### 5.4 Open question

Task formulation 是否也 problem-indexed (每个 canonical question 用不同 formulation), 还是应该有一个 unified formulation 使所有 canonical questions 在它下都能 expressed?

倾向 **problem-indexed 同样适用于 task formulation**——这跟 framework 整体精神一致。但 final 答案要做完几个 worked example 后才能判断。

---

## 六、Canonical Questions (按 4 步 template re-organize)

### Q-A: 组织 / Assignment 问题

**Step 1 (research question)**: 给定异质 agent profile 与 task structure, 最优 communication topology 是什么?

**Step 2 (agent abstraction)**: light processor + capacity 约束。Agent $i$ 是 $f_i: \mathcal{X} \to \mathcal{Y}$ + mutual information bound $I(X; Y) \leq C_i$。Information allocation: distributed (每 agent 看 input partition)。

**Step 3 (interaction abstraction)**:
- Transient message passing dominant (3a)
- Communication cost: capacity-bounded
- Topology: variable, 是优化对象
- Message type: information transfer (不涉及 active request)
- Persistent state: 不需要

**Step 4 (target verification)**:
Objective = $\min_G \mathbb{E}_x \|F^*(x) - \hat{F}_G(x)\|^2$ subject to $\sum_{(i,j) \in G} c_{ij} \leq B$。
跟 Step 1 一致。Abstraction stack closed。Well-posed math problem: 给定 $\{C_i\}, F^*, B$, 求 $G^*$。

**核心数学**: 信息论 + 组合优化 + (可能) communication complexity

**周边工作**: AgentsNet, Guided Topology Diffusion, GTD 等 (empirical/algorithmic), 几乎无 closed-form theorem。

### Q-B: 稳定性问题

**Step 1 (research question)**: 节点不可靠时, 系统级错误率的 bound 是什么?

**Step 2 (agent abstraction)**: hierarchical noisy operator。
- Top-level $T_i: \mathcal{X} \to \Delta(\mathcal{Y})$ (输入到输出分布的映射)
- Sub-structure: agent 由 sub-agents 组成 redundancy module (e.g. 两个 sub-LLM 互检)
- Reliability profile $\rho_i$ 概括输出分布与 ground truth 的偏差
- 子结构 explicit: 不 fold 进 effective $\rho_i$, 因为 fold 会丢失 sub-agent vs network-level redundancy 的 trade-off
- Information allocation: ensemble (所有 agent 看 full input) 或 distributed, 依具体 question

**Step 3 (interaction abstraction)**:
- 两 levels:
  - Sub-agent level: cross-checking / voting (transient, parallel)
  - Network level: error 在 task chain 上传播 (transient, sequential)
- Message type: information transfer + 可能 query (sub-agent 互检涉及 query type)
- Persistent state: 不需要

**Step 4 (target verification)**:
Objective = bound $E(G, \boldsymbol{\rho})$ given task structure。
跟 Step 1 一致。Well-posed: given $G, \boldsymbol{\rho}$ + task structure, find tight bound on $E$。

**核心数学**: 概率算子 + 谱图论 + 借鉴 (但不直接 import) Byzantine Fault Tolerance。LLM 错误是 distributional 不是 adversarial, 这给 BFT 之外的 *更乐观的 bound* 留空间。

**Theorem candidate (conjecture)**:
$$E(G, \boldsymbol{\rho}) \leq f(\boldsymbol{\rho}) \cdot g(\lambda_2(L_G))$$
即: 高连通性可以补偿节点不可靠性, 补偿率由 Fiedler value 决定。

**周边工作**: Multi-agent LLM 的 cross-checking 工作 (engineering 风格), BFT 文献 (假设 adversarial)。把 distributional error 作为 fundamental primitive 证稳定性定理是 underexplored 的位置。

### Q-C: 演化 / 跟踪问题 (Seed 2 主体)

**Step 1 (research question)**: 固定算子下知识状态如何 track 非平稳目标?

**Step 2 (agent abstraction)**: operator on knowledge state (双时间尺度)。
- 知识状态 $\mathcal{K}$ = 动态语义网络 $G = (V, E, w)$, 边权 $w$ 是慢变量
- Agent 是固定算子对 $(T_i^{\text{fast}}, T_i^{\text{slow}})$, 分别作用于活动状态和知识状态
- 网络动力学是双时间尺度 stochastic system
- Information allocation: agent 通过 shared knowledge state 间接共享信息

**Step 3 (interaction abstraction)**:
- Persistent shared state dominant (3b): 知识图是核心
- Transient message passing 次要
- Message type: 主要是 state update 而非 message
- 必须 model 时间 (Q-C 是 time-dependent question)

**Step 4 (target verification)**:
Objective = bound $\sup_t \|w(t) - w^*(u(t))\|$ given driver $u(t)$。
跟 Step 1 一致。Well-posed: 给 driver 性质 + agent 算子 + 拓扑, find tracking error bound。

**核心数学**: TTSA (Two-Timescale Stochastic Approximation, Borkar 路线 + Chandak 2025 non-expansive 推广) + 算子理论 + 谱图论。

**Theorem candidate**: 给定非平稳 driver $u(t)$, 跟踪误差 $\sup_t \|w(t) - w^*(u(t))\|$ 由 *agent 异质性 × 拉普拉斯谱 gap* 控制。

**周边工作**: Memini (2026 May, Benna-Fusi synaptic consolidation on knowledge graph), Panini (2026 Feb, structured semantic memory), Buehler 的 Agentic Deep Graph Reasoning (2025 Feb)。这些是 architecture-level 工作, theoretical 工作几乎空白。

### Q-D 候选 (尚未 develop)

可能加: privacy-constrained communication / decision authority allocation / generativity beyond stability / active negotiation。

每个 candidate 走 4 步 procedure 后再决定是否纳入 canonical 列表。

---

## 七、Theorem Form 共享的 Conjecture

注意 Q-B 和 Q-C 的 theorem form 都呈现 *异质性指标 × 谱量* 的结构。这不是巧合——稳定性和跟踪在数学上是同源问题, 跟踪是稳定性的 dynamic 版本。

**Conjecture (aspirational)**: 多个 (或所有) canonical questions 的 representative theorems 都可以写成

$$\text{效能上界} \sim h(\text{异质性}) \cdot s(\text{图谱量})$$

的形式, 且 $h$ 和 $s$ 在不同 abstraction 下有 explicit relationship。

如果成立 → dissertation unity 的强证据。
如果不成立 → 各 question 的 theorem 仍独立有价值, dissertation 由方法学一致性统一。

---

## 八、Dissertation Arc Candidate

*Aspirational, 不是 promise*。

| Chapter | 内容 | 状态 |
|---------|------|------|
| 1 | 阐明 problem-indexed abstraction 原则 + operational procedure | 已有 framework |
| 2 | Q-A: 组织/assignment 的 representative theorem | 待 develop |
| 3 | Q-B: 稳定性的 representative theorem | 待 develop |
| 4 | Q-C: 演化/跟踪的 representative theorem | 待 develop |
| 5 | Cross-theorem unifying conjecture 探索 | aspirational, 可能 reject |

**Fall-back**: 如果第 5 章 unifying conjecture 做不出, 前 4 章 4 篇独立 paper 仍 stand。Thesis proposal 写作时这件事必须 explicit。

**风险评估**:
- 三个 canonical questions 都做需要数学功底覆盖较广 (信息论 + 概率算子 + TTSA)
- 第 5 章是最 ambitious 的部分, 也是最 risky 的部分
- 整个 arc 是 4 papers + 1 synthesis 的结构, 标准 PhD timing 下可行但 tight

---

## 九、关键决策与理由 (Decision Log)

### D1: Seed 1 不搁置, 与 Seed 2 同 logic 推进 (只是 deprioritize)

- *Alternative*: 搁置 Seed 1 十二到十八个月, 集中 Seed 2
- *Why rejected*: "给已有 empirical 系统铺 theoretical underpinning"这个 reframe 对 Seed 1 同样成立。AutoGen / AgentNet 占据 application layer, theory layer sparse, gap 与 Seed 2 的 Memini 情形对称
- *Decision date*: framework v1

### D2: Problem-indexed abstraction 原则, 不是 spectrum

- *Alternative*: 按复杂度排列 abstractions 成 spectrum (light → heavy)
- *Why rejected*: spectrum 隐含 abstractions 间线性顺序, 是不必要承诺。Problem-indexed 更深, 因为它把 abstraction 选择本身作为研究内容
- *Decision date*: framework v1, 用户提出修订

### D3: Stability 问题用 hierarchical, 不 fold 进 effective $\rho_i$

- *Alternative*: 把 sub-agent redundancy fold 进单一 effective reliability $\rho_i$
- *Why rejected*: hierarchy 是 LLM agent 的真实结构; fold 会丢失 sub-agent vs network-level redundancy 的 trade-off; recursive 结构有 mathematical payoff (self-similar, 连到 multi-level selection)
- *Decision date*: framework v1

### D4: Privileged instantiation 是 agent, 不是 LLM

- *Alternative*: 绑定 LLM 作为 main object
- *Why rejected*: LLM 技术 stack 演化太快, 绑定会让 dissertation dated; "agent 概念的数学抽象"作为更稳定的研究对象
- *Decision date*: framework v1

### D5: Theory layer 而非 application layer 作为 contribution 位置

- *Alternative*: 做 application 层的 system 工作
- *Why rejected*: application 层拥挤; theory 层 sparse 是真正的 gap; theoretical work 抗 obsolescence
- *Decision date*: framework v1

### D6: Operational Procedure 4 步 + exit condition (新增 v2)

- *Alternative*: 只给 abstract 原则, 不给 procedure
- *Why rejected*: 没 procedure, 原则停在口号层。Procedure 让 framework 从 description 变成 actionable
- *Decision date*: framework v2

### D7: Task formulation 与 task structure 分离 (新增 v2)

- *Alternative*: 把 compositional decomposition 当 task formulation 之一
- *Why rejected*: decomposition 是 task structure 不是 formulation; 两者 orthogonal, 混在一起 dilute analysis
- *Decision date*: framework v2

### D8: Interaction 分 transient + persistent (新增 v2)

- *Alternative*: 只 model transient message passing
- *Why rejected*: Q-C 必须 persistent state, 漏掉这个维度 framework 不完整
- *Decision date*: framework v2

### D9: 时间维度 deferred (除 Q-C, 新增 v2)

- *Alternative*: 全部 question 都 model 时间
- *Why rejected*: 显式 time 立刻引入 dynamical systems 复杂度。Static abstraction 第一阶段 cleaner。Q-C 例外因为 question 本身 time-dependent
- *Decision date*: framework v2

---

## 十、Open at Framework Level

- **(O-1)** Hierarchical 抽象的 well-defined 性: Stability 问题用 recursive agent, 递归层数和不动点性质需要检查
- **(O-2)** 异质性的 unified 度量: 不同 abstraction 给不同的"异质性"指标, 是否能找到 abstraction-independent 的统一度量?
- **(O-3)** 第 4/5 个 canonical question 选哪个 (privacy / authority / generativity / negotiation 中)
- **(O-4)** Empirical anchor 深度: theorem + simulation 够还是需要在真实 LLM agent 系统 demonstrate?
- **(O-5)** Theorem form 共享 conjecture 的 specific form
- **(O-6)** (新增 v2) Active communication (request / query / negotiation) 数学 framework: 在哪些 canonical questions 下需要 explicit model, 怎么 model
- **(O-7)** (新增 v2) Time extension: 哪些 canonical questions 在 v3 应该升级到 dynamic abstraction
- **(O-8)** (新增 v2) Task formulation 是否也 problem-indexed (5.4 提到的问题)

---

## 十一、下一步思路 (不依赖外部因素)

### 可立即推进 (independent of advisor / collaborator)

1. **TTSA 数学功底自检**: 读 Borkar *Stochastic Approximation: A Dynamical Systems Viewpoint* 前 3 章 + Chandak 2025 (non-expansive slow-scale)
2. **直接邻居精读**: Memini, Panini, AgentNet, Buehler agentic deep graph reasoning 各自抽象成 formal dynamical system 描述
3. **稳定性问题 toy theorem 探索**: Q-B 的 minimal setup 下尝试证小定理
4. **Open games 文献摸底**: Hedges 2016, Bolt-Hedges-Winschel 2018
5. **(新增 v2) Worked example**: 选一个 canonical question, 跑完整 4 步 procedure, output 一个 well-posed 数学 problem (准 theorem 候选)

### 暂不推进 (待外部 input)

- 第一篇 paper 选 Q-A / Q-B / Q-C 哪个
- 6 个月具体路径
- 是否需要 import 具体 collaborator
- Dissertation arc 是否需要 revise

### 优先级 suggestion (revised in v2)

短期最高 value: **Worked example** (选 Q-A/B/C 之一) + TTSA 自检 + 邻居精读

Worked example 的产出是给 advisor 见面用的最 substantive material——不是 framework 全景, 是一个具体 well-posed problem。

---

## 附录 A: 周边研究 inventory

### Multi-agent LLM 系统 (application layer, 拥挤)

- AutoGen, MetaGPT, CAMEL, AgentVerse, ChatDev — 成熟 frameworks
- AgentsNet (Schoendube et al.), Guided Topology Diffusion, AMAS, AgentNet, HieraMAS, TopoDIM, Graph-GRPO — 12 个月内的 dynamic topology 工作
- 整体: 经验/算法工作密集, theoretical bound 工作 sparse

### 知识图动力学与 frozen-operator 持续学习 (近期密集)

- Memini (2026 May): Benna-Fusi synaptic consolidation on directed knowledge graph + frozen LLM
- Panini (2026 Feb): structured external semantic memory for non-parametric continual learning
- Buehler "Agentic Deep Graph Reasoning" (2025 Feb): self-organizing knowledge networks
- TRAIL: joint inference + knowledge graph refinement
- 整体: architectural/empirical 密集, theoretical 工作几乎空白

### 数学工具 lineage

- Borkar TTSA + Bhatnagar / Kwon-Chen-Xie 2024 / Chandak 2025: 双时间尺度 stochastic approximation
- Hedges / Ghani open games: compositional game theory
- Friston / de Vries 2026: active inference / FEP, 含 multi-LLM 扩展 (Prakki 2024)
- Braun / Ortega / Genewein / Tsiotras: 信息论 bounded rationality
- Nowak / Plotkin / 苏奇: evolutionary dynamics on networks

---

## 附录 B: Stability 问题 abstraction 详细 sketch

### 设置

- $T_i: \mathcal{X} \to \Delta(\mathcal{Y})$ — 噪声算子 (输入到输出分布的映射)
- $\rho_i$ — reliability profile, 概括输出分布与 ground truth 的偏差。具体形式由错误模型决定:
  - i.i.d. 噪声: $\rho_i$ = 噪声方差 (最简, 第一篇 paper 推荐)
  - Systematic bias: $\rho_i$ = bias vector + variance (中等)
  - Input-dependent: $\rho_i$ = measure on input space (最难)
- Hierarchical: 节点本身是 sub-agents 组成的 redundancy module。Top-level $T_i$ 由 sub-agents $\{T_i^{(1)}, T_i^{(2)}, \ldots\}$ 通过 cross-check / voting 合成

### 网络层

- Composition 规则: 串联 = stochastic operator 复合; 并联+投票 = stochastic operator 在 message space 上的 majority vote
- Topology $G$ 决定 composition 在哪些 node 上发生

### Theorem candidate

$$E(G, \boldsymbol{\rho}) \leq f(\boldsymbol{\rho}) \cdot g(\lambda_2(L_G))$$

意义: 高连通性补偿节点不可靠性, 补偿率由 Fiedler value 控制。

### 与 BFT 的精确 differentiation

- BFT: adversarial errors, 需要 $\geq 2/3$ honest, worst-case bound
- 本 setup: distributional errors, 期望误差不需要 honest majority, 可获得更乐观 average-case bound
- Paper 第一段必须 spell out 这件事, 否则 reviewer 会问 "BFT 不已经解决了吗"

---

## 附录 C: Active Communication 初步思考 (新增 v2, 留给 O-6 future develop)

### 问题

普通 channel theory 假设 message 是 passive information payload。但 agent 网络里, message 经常是:
- Request: "请做 X"
- Query: "请告诉我 Y"
- Instruction: "执行 Z"
- Negotiation: "我提议这样"

这些**不是 passive information**, 而是 *action-inducing communication*。

### 数学 implication

- Plain channel theory: $p(\text{output}_B | \text{input}_B, \text{message from A})$ 是 fixed mapping
- Active communication: $\text{action}_B(t+1)$ 依赖于 $\text{message}_A(t)$ 的 *speech act type*, 不只 *content*

### 相邻文献

- J.L. Austin speech act theory
- Game theory cheap talk literature
- Multi-agent system 的 ACL (Agent Communication Language)

### Status

留给 framework v3 处理。当前 v2 在 (3a) interaction abstraction 中加了 message type 维度作为 placeholder, 但具体数学 framework 待 develop。

---

## 文档版本

- v1 — 初版 framework
- v2 — 加入 operational procedure (4 步 + exit condition), task formulation/structure 分离, transient/persistent interaction 区分, 时间维度 deferred 显式标记, 加 D6-D9 decision log, 加 O-6/O-7/O-8 open questions, 加附录 C active communication
