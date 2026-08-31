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
| I1 | 置换一致且可定位干预的离散图 Tokenizer | 方法 + 评测 | 候选 | H2/H3/H5；Q2/Q3/Q4/Q10/Q11 | 8/10 | 先复现 GraphTokenizer，再加入语义锚点、Graph-KV mask-only 的 matched equality/intervention 审计 |

## I1：置换一致且可定位干预的离散图 Tokenizer

### 基本信息

- 状态：候选
- Idea 类型：方法 + 评测协议
- 今日研究模式：强化
- 来源假设：H2、H3、H5
- 来源问题：Q2、Q3、Q4、Q10、Q11
- 今日来源论文：Colorful Talks with Graphs: Human-Interpretable Graph Encodings for Large Language Models（doi:10.18653/v1/2026.findings-acl.2049）
- 主要来源论文：Graph Tokenization for Bridging Graphs and Transformers（arXiv:2603.11099）
- 关联历史论文：Lost in Serialization（arXiv:2511.10234）、Detecting Differences Is Not Understanding Structure（arXiv:2606.09484）、CausalGraph2LLM（doi:10.18653/v1/2025.findings-naacl.110）
- 当前可信度：中等；任务条件多视图的必要性有理论和实验支持，但离散接口的实测不变性、因果责任和资源成本仍待核验

### 当前方法及不足

GraphTokenizer 使用 frequency-guided reversible serialization 与 BPE 将 labeled graph 转换为离散 structural tokens，并把 determinism 作为缓解 ordering ambiguity 的接口属性。现有证据支持可逆性、压缩率和标准任务性能，但尚未直接证明随机 node relabeling、edge order、起点、方向、重复 labels 和 disconnected components 下 serialized sequence、BPE token、hidden state 与最终预测保持一致。即使接口可逆，也没有证明关键 topology intervention 会引起方向正确的预测变化，或 LLM 而不是 tokenizer/readout 完成了主要结构计算。

不足类型：

- 论文设计主张：reversibility、determinism 与 almost permutation invariance。
- 实验直接支持：标准任务性能、decode path、BPE compression 和结构词表统计。
- 研究者推断：lexical tie-break、traversal start/direction 和 BPE context 可能重新引入序列顺序依赖；需要实验验证，不能写成既定事实。

补充判断：Lost in Tokenization 说明 adjacency、spectral 和 random-walk 视图存在不同的 finite-depth、lossiness 和 trainability trade-off，因此 I1 需要加入 task-conditioned complementary-view 对照；但“多视图”本身不能作为独立创新主张，最小差异仍必须是 discrete addressability、fixed-text topology intervention 与 GNN/LLM/readout responsibility split 的联合验证。

本轮补充判断：Colorful Talks with Graphs 以 ordered 1-WL 和自然颜色词改善部分任务，并在保持 WL partition 不变时显示 natural colors 明显优于 opaque Hue tokens；这把 semantic-anchor control 提升为 I1 的必要对照。该方法仍是外部 graph-to-prompt descriptor，不提供 reversible/BPE、逐样本 permutation equality、fixed-text topology intervention 或组件责任分离，因此不替代 I1 的核心缺口。

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
| Lost in Tokenization | adjacency、spectral、random-walk 的 finite-depth、lossiness 和 trainability trade-off，以及部分 combined-view 收益 | 不是 Graph-LLM；没有离散 addressable token、最终输出 equality、fixed-text topology intervention 或 responsibility split | I1 不把 multi-view 组合本身当创新，而是在离散可逆接口上把 view choice 与 addressability、因果干预和责任审计联合可证伪 |
| Lost in Serialization | node relabeling、ordering、syntax 与泛化审计 | 研究对象主要是序列化 LLM，没有可逆离散 tokenizer 改进 | 将 invariance audit 迁移到可逆 tokenizer，并形成训练目标 |
| Detecting Differences Is Not Understanding Structure | ordinary task 与 permutation invariance 可脱钩 | 没有 Graph-LLM hybrid、结构 token 或 causal intervention | 在 Graph-LLM 接口上联合测等价稳定性和关键拓扑因果响应 |
| CausalGraph2LLM | encoding/order 与 intervention query 评测 | 没有 tokenizer/LLM/readout 责任分离和 matched topology pair | 增加 fixed-text key/irrelevant intervention 与组件责任拆分 |
| Graph-KV | LLM-side source→target KV block mask、shared PE、chunk-order/long-context robustness | 没有 discrete graph token/addressability、node relabeling/isomorphism、fixed-text key/irrelevant topology intervention 或 GNN/LLM/readout split | 在 matched token/compute budget 下把 Graph-KV-style mask-only 与 reversible discrete/BPE 接口放入同一 equality、intervention、responsibility 和 topology-OOD 协议 |
| Colorful Talks with Graphs | ordered 1-WL labels、natural color tokens、compressed graph-to-prompt encoding；same-WL partition 的 color versus opaque-token ablation | 没有逐样本 relabeling/output equality、fixed-text topology intervention、text shuffle、GNN/LLM/readout split 或 topology-family OOD；WL 和颜色映射在 LLM 外部预计算 | 将 semantic-anchor control 与 reversible/BPE、mask-only、readout-only 放入 matched equality/intervention/responsibility/OOD 协议，不把颜色词收益直接视为 causal topology use |

当前 novelty 判断：候选差异主要是“方法约束 + 联合评测协议”，不是单独增加一个 relabeling 实验。仍需检索 permutation-invariant graph serialization、canonical graph tokenization、TokenGT/Graphormer invariance 和 graph counterfactual training，确认没有高度相似方法。

补充 Novelty Gate 结论：本轮独立检索了 Graph-KV、Lost in Tokenization、GQT、TokenGT、SwapGT、permutation-invariant graph serialization 和 mechanistic graph-token work；未发现把“离散可寻址接口 + fixed-text key/irrelevant topology intervention + GNN/LLM/readout responsibility split + topology-family OOD”作为同一可证伪协议的完全重复工作。Graph-KV 使 LLM-side structural routing 成为明确相邻先例，因此 Novelty 只记 1/2，且不使用“首次”表述；若后续结果只显示 mask、multi-view 或 standard-split 增益，则 I1 应降级为评测复现而非方法创新。

### 最小可证伪实验

- 第一阶段对象：GraphTokenizer 官方代码和一个可在现有资源上运行的小型 Transformer；具体 GPU 需求待复现后确认。
- 数据：先使用 synthetic labeled graphs 与一个官方小型 benchmark；固定节点/边标签分布。
- 等价变换：`10 node relabelings × 4 edge orders × 3 starts/directions`，额外覆盖重复 labels 和 disconnected components。
- 拓扑干预：保持文本、节点特征和输出格式不变，分别修改任务关键边和无关边。
- Baseline：GraphTokenizer Feuler/FCPP+BPE、raw edge list、BFS/DFS、continuous projector、GNN-only、readout-only、LLM/Transformer-only、shuffled structural tokens。
- 指标：decode fidelity、serialized equality、BPE token equality、token count、hidden distance、prediction equality、output KL、flip rate、key-intervention direction accuracy、irrelevant-intervention stability、size/density/topology-family OOD。
- 视图对照：local edge-covering、global spectral、random-walk/diffusion、单一 reversible view 与 complementary-view 组合；固定 Transformer 深度、宽度和 token budget，区分 view 信息量、finite-depth 可达性与训练稳定性。
- 关键消融：无 consistency loss、无 addressable tokens、无 intervention loss、randomized BPE、不同 tie-break、去掉各个 view、固定文本与非固定文本两种干预。

### 支持与失败条件

- 支持条件：decode fidelity 保持 100%；等价变换下表示和输出显著更稳定；关键拓扑干预方向正确、无关干预稳定；完整模型在 direction accuracy 上超过 readout-only/GNN-only，并在 held-out topology family 保留优势。
- 失败条件：改进仍依赖原 node index 或 traversal tie-break；relabeling 造成明显 token/prediction flip；BPE 无法稳定 decode；GNN-only/readout-only 复现完整模型；或改进只提升 standard split、在 topology-OOD 上失效。

### 评分

| 维度 | 分数 | 理由 |
|---|---:|---|
| Novelty | 1/2 | 多视图和离散 token 已有相邻工作，当前差异主要是联合可证伪约束与责任审计 |
| Importance | 2/2 | 直接回答结构接口是否置换一致且被因果利用 |
| Falsifiability | 2/2 | 有明确 equality、direction accuracy 和失败条件 |
| Feasibility | 1/2 | GraphTokenizer 官方代码可作为入口；今日论文无明确官方代码，spectral view 还引入预处理和数值稳定性成本 |
| Thesis Fit | 2/2 | 可形成问题、方法、评测、消融和负面结论闭环 |
| 总分 | 8/10 | 通过候选门槛，尚未进入准备原型状态 |

### 7 天内第一步

只复现 GraphTokenizer 的 serialization/decode 路径，不训练大模型：选择 100--1000 个小图，运行 node relabeling、edge order、起点/方向、重复 labels 和 disconnected components 变换，记录 serialized equality、BPE token equality 与 decode isomorphism；随后在同一 token budget 下比较 single-view 与 complementary-view。该结果决定 I1 是进入“待实验”，还是因接口已完全稳定而调整研究重点。

### 更新记录

- 2026-08-30：根据第 12 次自动调研的 GraphTokenizer 证据建立 I1；状态为“候选”，novelty 和资源成本待进一步核验。
- 2026-08-30：强化。根据 Lost in Tokenization 的 finite-depth、lossiness 和 complementary-view 证据，补充 task-conditioned view ablation；明确多视图本身不构成创新，I1 的差异必须落在 discrete addressability、fixed-text intervention 和 responsibility split 的联合验证上，状态保持“候选”，评分保持 8/10。
- 2026-08-30：强化。Graph-KV 提供 LLM-side source→target KV mask/shared-PE 的相邻基线，说明 structural routing 已有直接 LLM-side prior art；I1 不新增 I2，也不把 mask、multi-view 或 relabeling 单项作为创新，加入 Graph-KV-style mask-only、random/irrelevant-edge、mask shuffle 和 matched token/compute controls，状态保持“候选”，评分保持 8/10。
- 2026-08-30：强化。Colorful Talks with Graphs 将 ordered 1-WL 的自然颜色词与同一 WL partition 的 opaque tokens 对照，直接暴露 semantic-anchor shortcut；I1 加入 natural color、opaque Hue 和 random-label controls，仍不新增 I2，状态保持“候选”，评分保持 8/10。
