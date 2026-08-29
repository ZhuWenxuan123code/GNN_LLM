# 候选论文 Idea Backlog

本文件管理可能形成论文贡献的研究 Idea。`ideas/research_map.md` 负责维护假设、证据和未解决问题；本文件负责维护 Idea 的去重、优先级、实验准备和淘汰过程。每日任务不得把 Idea 数量增长当作科研进展，应优先强化、反驳、合并或执行已有 Idea。

## 使用规则

- 每个 Idea 使用稳定编号，如 `I1`、`I2`，措辞变化不得重复创建。
- 状态只能使用：`候选`、`强化中`、`待实验`、`已合并`、`已否定`、`准备原型`。
- 每次最多新增 1 个通过 novelty gate 的 Idea；未通过门槛的候选只记录在当日日志，不进入 backlog。
- 每个 Idea 必须结合至少 1 篇当日论文、2 篇历史论文或正式相邻工作，以及 `research_map.md` 中至少 1 个假设或问题。
- “论文没有做”不等于“领域没有做”；新颖性判断必须包含至少 3 个最近工作，并保留反对证据。
- 五项评分均为 `0--2`：Novelty、Importance、Falsifiability、Feasibility、Thesis Fit。总分低于 `7/10` 不得标记为候选论文 Idea。
- 评分表示当前研究优先级，不代表论文录用概率。

## Idea 总览

| ID | 名称 | 类型 | 状态 | 关联假设/问题 | 当前评分 | 下一步 |
|---|---|---|---|---|---:|---|
| I1 | 置换一致且可定位干预的离散图 Tokenizer | 方法 + 评测 | 候选 | H2/H3/H5；Q2/Q3/Q4/Q10 | 8/10 | 复现 GraphTokenizer，并完成 serialization/token equality 小规模审计 |

## I1：置换一致且可定位干预的离散图 Tokenizer

### 基本信息

- 状态：候选
- Idea 类型：方法 + 评测协议
- 来源假设：H2、H3、H5
- 来源问题：Q2、Q3、Q4、Q10
- 主要来源论文：Graph Tokenization for Bridging Graphs and Transformers（arXiv:2603.11099）
- 历史证据：Lost in Serialization（arXiv:2511.10234）、Detecting Differences Is Not Understanding Structure（arXiv:2606.09484）、CausalGraph2LLM（doi:10.18653/v1/2025.findings-naacl.110）
- 当前可信度：中等；方法缺口明确，但新颖性仍需扩大检索并审计官方代码

### 当前方法及不足

GraphTokenizer 使用 frequency-guided reversible serialization 与 BPE 将 labeled graph 转换为离散 structural tokens，并把 determinism 作为缓解 ordering ambiguity 的接口属性。现有证据支持可逆性、压缩率和标准任务性能，但尚未直接证明随机 node relabeling、edge order、起点、方向、重复 labels 和 disconnected components 下 serialized sequence、BPE token、hidden state 与最终预测保持一致。即使接口可逆，也没有证明关键 topology intervention 会引起方向正确的预测变化，或 LLM 而不是 tokenizer/readout 完成了主要结构计算。

不足类型：

- 论文设计主张：reversibility、determinism 与 almost permutation invariance。
- 实验直接支持：标准任务性能、decode path、BPE compression 和结构词表统计。
- 研究者推断：lexical tie-break、traversal start/direction 和 BPE context 可能重新引入序列顺序依赖；需要实验验证，不能写成既定事实。

### 核心假设

仅依赖 reversible serialization 不能保证 Graph-LLM 的 permutation consistency 和 causal topology use。若在离散图 tokenizer 中加入多视图置换一致约束、可定位的结构 token 和关键/无关拓扑干预目标，则模型应同时提高等价变换稳定性、关键干预方向正确率和跨拓扑泛化；若改进只提升标准任务分数或 token 压缩率，则假设不成立。

### 方法草图

1. 对同一图生成多种 node relabeling、edge order、起点和方向视图。
2. 保留可逆 decode 约束，并对等价图视图加入 token/representation/prediction consistency。
3. 为目标节点、关键边或关键路径建立可定位 structural token，避免只有 graph-level 压缩而无法实施精确干预。
4. 构造 fixed-text key-edge 与 irrelevant-edge matched pairs：关键干预要求方向正确，无关干预要求预测稳定。
5. 通过 GNN-only、readout-only、LLM-only 和 shuffled structural token 分离 tokenizer、Transformer/LLM 与 readout 的责任。

### 最接近工作与最小差异

| 工作 | 已解决 | 尚未解决 | I1 的最小差异 |
|---|---|---|---|
| GraphTokenizer | reversible/deterministic serialization、BPE structural tokens、标准图任务 | 缺少系统 node-relabeling equality、fixed-text intervention 和责任分离 | 在可逆离散接口上联合加入 empirical invariance、可定位干预和模块责任审计 |
| Lost in Serialization | node relabeling、ordering、syntax 与泛化审计 | 研究对象主要是序列化 LLM，没有可逆离散 tokenizer 改进 | 将 invariance audit 迁移到可逆 tokenizer，并形成训练目标 |
| Detecting Differences Is Not Understanding Structure | ordinary task 与 permutation invariance 可脱钩 | 没有 Graph-LLM hybrid、结构 token 或 causal intervention | 在 Graph-LLM 接口上联合测等价稳定性和关键拓扑因果响应 |
| CausalGraph2LLM | encoding/order 与 intervention query 评测 | 没有 tokenizer/LLM/readout 责任分离和 matched topology pair | 增加 fixed-text key/irrelevant intervention 与组件责任拆分 |

当前 novelty 判断：候选差异主要是“方法约束 + 联合评测协议”，不是单独增加一个 relabeling 实验。仍需检索 permutation-invariant graph serialization、canonical graph tokenization、TokenGT/Graphormer invariance 和 graph counterfactual training，确认没有高度相似方法。

### 最小可证伪实验

- 第一阶段对象：GraphTokenizer 官方代码和一个可在现有资源上运行的小型 Transformer；具体 GPU 需求待复现后确认。
- 数据：先使用 synthetic labeled graphs 与一个官方小型 benchmark；固定节点/边标签分布。
- 等价变换：`10 node relabelings × 4 edge orders × 3 starts/directions`，额外覆盖重复 labels 和 disconnected components。
- 拓扑干预：保持文本、节点特征和输出格式不变，分别修改任务关键边和无关边。
- Baseline：GraphTokenizer Feuler/FCPP+BPE、raw edge list、BFS/DFS、continuous projector、GNN-only、readout-only、LLM/Transformer-only、shuffled structural tokens。
- 指标：decode fidelity、serialized equality、BPE token equality、token count、hidden distance、prediction equality、output KL、flip rate、key-intervention direction accuracy、irrelevant-intervention stability、size/density/topology-family OOD。
- 关键消融：无 consistency loss、无 addressable tokens、无 intervention loss、randomized BPE、不同 tie-break。

### 支持与失败条件

- 支持条件：decode fidelity 保持 100%；等价变换下表示和输出显著更稳定；关键拓扑干预方向正确、无关干预稳定；完整模型在 direction accuracy 上超过 readout-only/GNN-only，并在 held-out topology family 保留优势。
- 失败条件：改进仍依赖原 node index 或 traversal tie-break；relabeling 造成明显 token/prediction flip；BPE 无法稳定 decode；GNN-only/readout-only 复现完整模型；或改进只提升 standard split、在 topology-OOD 上失效。

### 评分

| 维度 | 分数 | 理由 |
|---|---:|---|
| Novelty | 1/2 | 联合缺口明确，但 canonical/invariant tokenizer 相邻工作尚未完成系统检索 |
| Importance | 2/2 | 直接回答结构接口是否置换一致且被因果利用 |
| Falsifiability | 2/2 | 有明确 equality、direction accuracy 和失败条件 |
| Feasibility | 1/2 | 官方代码可作为入口，但复现成本和算力尚待核验 |
| Thesis Fit | 2/2 | 可形成问题、方法、评测、消融和负面结论闭环 |
| 总分 | 8/10 | 通过候选门槛，尚未进入准备原型状态 |

### 7 天内第一步

只复现 GraphTokenizer 的 serialization/decode 路径，不训练大模型：选择 100--1000 个小图，运行 node relabeling、edge order、起点/方向、重复 labels 和 disconnected components 变换，记录 serialized equality、BPE token equality 与 decode isomorphism。该结果决定 I1 是进入“待实验”，还是因接口已完全稳定而调整研究重点。

### 更新记录

- 2026-08-30：根据第 12 次自动调研的 GraphTokenizer 证据建立 I1；状态为“候选”，novelty 和资源成本待进一步核验。
