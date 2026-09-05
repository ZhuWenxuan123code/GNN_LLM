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
| I1 | 置换一致且可定位干预的离散图 Tokenizer | 方法 + 评测 | 候选 | H1/H2/H3/H5/H10/H11/H13/H14/H15；Q2/Q3/Q4/Q10/Q11/Q12/Q13/Q14/Q15 | 8/10 | 先做 semantic-sufficiency/structure-essential gate，再复现 GraphTokenizer，并加入 parser/oracle、sink-aware、语义锚点、Graph-KV mask-only 和 EstGraph access-aware OOD 的 matched equality/intervention 审计 |

## I1：置换一致且可定位干预的离散图 Tokenizer

### 基本信息

- 状态：候选
- Idea 类型：方法 + 评测协议
- 今日研究模式：强化
- 来源假设：H1、H3、H5、H10、H11、H13、H14、H15
- 来源问题：Q2、Q3、Q4、Q10、Q11、Q12、Q13、Q14、Q15
- 今日来源论文：Evaluating LLMs on Large-Scale Graph Property Estimation via Random Walks（doi:10.18653/v1/2026.acl-long.1846；arXiv:2605.01484）
- 主要来源论文：Graph Tokenization for Bridging Graphs and Transformers（arXiv:2603.11099）
- 关联历史论文：Lost in Serialization（arXiv:2511.10234）、Detecting Differences Is Not Understanding Structure（arXiv:2606.09484）、CausalGraph2LLM（doi:10.18653/v1/2025.findings-naacl.110）
- 当前可信度：中等；任务条件多视图和 partial-access/scale OOD 的必要性有边界证据，但离散接口的实测不变性、因果责任、sampling bias 和资源成本仍待核验

### 当前方法及不足

GraphTokenizer 使用 frequency-guided reversible serialization 与 BPE 将 labeled graph 转换为离散 structural tokens，并把 determinism 作为缓解 ordering ambiguity 的接口属性。现有证据支持可逆性、压缩率和标准任务性能，但尚未直接证明随机 node relabeling、edge order、起点、方向、重复 labels 和 disconnected components 下 serialized sequence、BPE token、hidden state 与最终预测保持一致。即使接口可逆，也没有证明关键 topology intervention 会引起方向正确的预测变化，或 LLM 而不是 tokenizer/readout 完成了主要结构计算。

不足类型：

- 论文设计主张：reversibility、determinism 与 almost permutation invariance。
- 实验直接支持：标准任务性能、decode path、BPE compression 和结构词表统计。
- 研究者推断：lexical tie-break、traversal start/direction 和 BPE context 可能重新引入序列顺序依赖；需要实验验证，不能写成既定事实。

补充判断：Lost in Tokenization 说明 adjacency、spectral 和 random-walk 视图存在不同的 finite-depth、lossiness 和 trainability trade-off，因此 I1 需要加入 task-conditioned complementary-view 对照；但“多视图”本身不能作为独立创新主张，最小差异仍必须是 discrete addressability、fixed-text topology intervention 与 GNN/LLM/readout responsibility split 的联合验证。

本轮补充判断：Colorful Talks with Graphs 以 ordered 1-WL 和自然颜色词改善部分任务，并在保持 WL partition 不变时显示 natural colors 明显优于 opaque Hue tokens；这把 semantic-anchor control 提升为 I1 的必要对照。该方法仍是外部 graph-to-prompt descriptor，不提供 reversible/BPE、逐样本 permutation equality、fixed-text topology intervention 或组件责任分离，因此不替代 I1 的核心缺口。

本轮补充判断：GRAIN 在多种命名/叙事 views 上用 invariance reward 约束显式 graph IR recovery，并由 external graph-tool solver 执行主要拓扑算法；这为 I1 增加了 parser/oracle 与 LLM-answer-head 的责任边界基线。GRAIN 的 known-mapping canonical-edge Jaccard、surface-form OOD 和 connected-undirected ER 图仍不等于 arbitrary node-relabelling equality、fixed-text topology intervention 或 hidden/output causal topology use，因此不替代 I1 的核心缺口，也不把 graph IR、invariance reward 或 executable solver 单独写成新颖性。

本轮补充判断：When Graph Tokens Sink 发现 activation sink、attention 和 token pruning utility 可能脱钩；LLaGA 的 top-2 sink 常为 `[PAD]`，而随机 non-sink pruning 在 node task 上反而更伤性能。该结果为 I1 增加 sink-aware pre-intervention diagnostic 和 padding/位置/预训练域 shortcut control，但 token-level pruning/swap/reposition 仍不是 graph-level topology counterfactual；因此 saliency 不能替代可定位 topology intervention 或 responsibility split。

本轮反驳判断：When Structure Doesn't Help 在语义丰富 TAG、分子和 DTI 任务上发现 semantic-only、中心节点、HN-1 或 MLP 常与 ND/GNN 相当或更好，说明“结构普遍有益”不是可接受的默认前提。该论文的 HN-1 仍由图连接选择邻居，且主要使用 8:2 random split，因此它不能直接证明 LLM 不使用拓扑；I1 需要把 semantic-sufficiency/structure-essential matched task gate 放在所有离散 tokenizer、等价性和因果干预之前。

本轮强化判断：EstGraph 将 LLM 图评测从 full-visibility 小图扩展到 partial-access large graph，使用 MH/SRW random-walk statistics 估计节点/边规模、community、graph type 和 centrality。它为 I1 增加 access-aware OOD 轴，但 degree distribution、visit frequency、coverage、seed、Chapman estimator 和 prompt summarization 可能解释主要结果；因此 EstGraph 只应作为 I1 的 sampled-access comparator，不替代 fixed-text topology intervention、等价输出和责任拆分。

### 核心假设

仅依赖 reversible serialization 不能保证 Graph-LLM 的 permutation consistency 和 causal topology use；同时，结构增强只有在 topology 是任务必要信息时才可被识别。若先通过 semantic-sufficiency/structure-essential gate，再在离散图 tokenizer 中加入多视图置换一致约束、可定位的结构 token 和关键/无关拓扑干预目标，并在 full graph 与 MH/SRW sampled access、不同 coverage/seed 和 held-out scale/topology family 下保持同样趋势，则模型应在 structure-essential 子集上同时提高等价变换稳定性、关键干预方向正确率和跨拓扑泛化；若 semantic-only、HN-1、MLP、degree-only、walk-stat-only 或 readout-only 已复现完整模型，或改进只提升标准任务分数/压缩率/coverage，则假设不成立。

### 方法草图

0. 先构造同长度、同文本、同 prompt 的 semantic-sufficient 与 structure-essential matched tasks，确认结构收益不是文本—标签相关性或图驱动邻居采样造成的不可识别差异。
1. 对同一图生成多种 node relabeling、edge order、起点和方向视图。
2. 保留可逆 decode 约束，并对等价图视图加入 token/representation/prediction consistency。
3. 为目标节点、关键边或关键路径建立可定位 structural token，避免只有 graph-level 压缩而无法实施精确干预。
4. 构造 fixed-text key-edge 与 irrelevant-edge matched pairs：关键干预要求方向正确，无关干预要求预测稳定。
5. 通过 GNN-only、readout-only、LLM-only 和 shuffled structural token 分离 tokenizer、Transformer/LLM 与 readout 的责任。

### 最接近工作与最小差异

| 工作 | 已解决 | 尚未解决 | I1 的最小差异 |
|---|---|---|---|
| GraphTokenizer | reversible/deterministic serialization、BPE structural tokens、标准图任务 | 缺少系统 node-relabeling equality、fixed-text intervention 和责任分离 | 在可逆离散接口上联合加入 empirical invariance、可定位干预和模块责任审计 |
| When Structure Doesn't Help | semantic-only、HN-1/CO、MLP/GNN 和多数据集结构必要性对照；指出 GDC 的序列中心化局部收益 | 主要为 8:2 random split；HN-1 仍用图选择邻居；没有 structure-essential fixed-text topology pair、最终输出 equality 或完整 responsibility split | I1 增加先验的 semantic-sufficiency gate，再在结构必需子集联合测离散等价性、topology direction、责任拆分和 topology-OOD |
| Lost in Tokenization | adjacency、spectral、random-walk 的 finite-depth、lossiness 和 trainability trade-off，以及部分 combined-view 收益 | 不是 Graph-LLM；没有离散 addressable token、最终输出 equality、fixed-text topology intervention 或 responsibility split | I1 不把 multi-view 组合本身当创新，而是在离散可逆接口上把 view choice 与 addressability、因果干预和责任审计联合可证伪 |
| Lost in Serialization | node relabeling、ordering、syntax 与泛化审计 | 研究对象主要是序列化 LLM，没有可逆离散 tokenizer 改进 | 将 invariance audit 迁移到可逆 tokenizer，并形成训练目标 |
| Detecting Differences Is Not Understanding Structure | ordinary task 与 permutation invariance 可脱钩 | 没有 Graph-LLM hybrid、结构 token 或 causal intervention | 在 Graph-LLM 接口上联合测等价稳定性和关键拓扑因果响应 |
| CausalGraph2LLM | encoding/order 与 intervention query 评测 | 没有 tokenizer/LLM/readout 责任分离和 matched topology pair | 增加 fixed-text key/irrelevant intervention 与组件责任拆分 |
| Graph-KV | LLM-side source→target KV block mask、shared PE、chunk-order/long-context robustness | 没有 discrete graph token/addressability、node relabeling/isomorphism、fixed-text key/irrelevant topology intervention 或 GNN/LLM/readout split | 在 matched token/compute budget 下把 Graph-KV-style mask-only 与 reversible discrete/BPE 接口放入同一 equality、intervention、responsibility 和 topology-OOD 协议 |
| Colorful Talks with Graphs | ordered 1-WL labels、natural color tokens、compressed graph-to-prompt encoding；same-WL partition 的 color versus opaque-token ablation | 没有逐样本 relabeling/output equality、fixed-text topology intervention、text shuffle、GNN/LLM/readout split 或 topology-family OOD；WL 和颜色映射在 LLM 外部预计算 | 将 semantic-anchor control 与 reversible/BPE、mask-only、readout-only 放入 matched equality/intervention/responsibility/OOD 协议，不把颜色词收益直接视为 causal topology use |
| GRAIN | naming/narrative invariance reward、显式 graph IR recovery、external deterministic graph solver、表面和规模/OOD 测试 | 没有 arbitrary node-relabeling equality、fixed-text key/irrelevant intervention、text shuffle、GNN/LLM/readout split 或 held-out topology family；known-mapping Jaccard 不是 graph-isomorphism equality | 将 parser/oracle、solver-disabled、exact recovery 与 I1 的离散 addressability、等价输出和拓扑因果干预放入同一 responsibility/OOD 协议；不把 invariance reward 或 graph IR 单独当作方法创新 |
| When Graph Tokens Sink | sink dimension 检测、attention/position 分布、top-2 sink vs random non-sink pruning、swap/reposition、logit lens | 只有 token-level activation/position intervention；没有 graph-level topology counterfactual、permutation equality、组件责任分离或 topology-family OOD，且只覆盖两个架构 | 将 sink score、attention share 和 pruning delta 作为诊断变量，再与 I1 的 fixed-text key/irrelevant topology intervention、decode equality 和 GNN/LLM/readout split 联合；不把显著 activation 当作 topology causal proof |
| SOG: One LLM Token for Explicit Graph Structural Understanding | topology-aware tokenizer、GNN、hierarchical traversal、virtual global node、VQ structural token 与 hybrid structure QA | 没有同构/重编号/edge-order equality、fixed-text topology intervention、text shuffle、组件责任分离或 topology-family OOD；one-token 设计已构成直接相邻 prior art | I1 的最小差异不是再提出一个 structural token，而是要求离散 token 的 empirical equality、可定位 topology intervention、parser/GNN/LLM/readout responsibility 和 OOD 联合通过 |
| EstGraph | partial-access random-walk statistics、100--100,000-node synthetic 与最高 2,388,953-node real-world graph、四类 global estimation task | 没有 discrete tokenizer、同图 relabeling/output equality、fixed-text topology intervention、degree-matched control 或组件责任分离 | I1 将其作为 access-aware scale/OOD slice，并要求与 discrete addressability、等价输出、关键/无关 topology direction 和 GNN/LLM/readout split 联合验证，不把大图估计本身当作 tokenizer 创新 |

当前 novelty 判断：候选差异主要是“方法约束 + 联合评测协议”，不是单独增加一个 relabeling 实验。仍需检索 permutation-invariant graph serialization、canonical graph tokenization、TokenGT/Graphormer invariance 和 graph counterfactual training，确认没有高度相似方法。

补充 Novelty Gate 结论：本轮独立检索了 Graph-KV、Lost in Tokenization、GQT、TokenGT、SwapGT、permutation-invariant graph serialization、mechanistic graph-token work 和 SOG；未发现把“离散可寻址接口 + fixed-text key/irrelevant topology intervention + GNN/LLM/readout responsibility split + topology-family OOD”作为同一可证伪协议的完全重复工作。SOG 使 one-structural-token 方法主张成为明确相邻 prior art，When Graph Tokens Sink 又说明 saliency/attention/pruning 不能替代 topology causality，因此 I1 的 Novelty 只记 1/2，且不使用“首次”表述；若后续结果只显示 mask、multi-view、sink diagnostic 或 standard-split 增益，则 I1 应降级为评测复现而非方法创新。

2026-08-31 补充 Novelty Gate 结论：独立比较 GRAIN、Structural Chain-of-Thought、Agentic Graph Token Reasoning、GraphTokenizer 与 Lost in Serialization 后，GRAIN 明确把 surface-form invariance、显式 graph IR 和 external solver 作为既有边界；Structural CoT 的 executable graph 与 intervention reward、Agentic Graph Token Reasoning 的 trajectory-dependent graph tokens 也分别覆盖相邻子问题。I1 不得声称 graph IR、invariance reward、executable graph 或 agentic token 单独新颖；当前最小差异仍是“离散可寻址接口 + empirical equality + fixed-text key/irrelevant topology intervention + parser/GNN/LLM/readout responsibility split + topology-family OOD”的联合、可证伪协议。Novelty 维持 1/2，状态维持“候选”。

### 最小可证伪实验

- 第一阶段对象：GraphTokenizer 官方代码、GRAIN-style parser/oracle exact-recovery baseline 和一个可在现有资源上运行的小型 Transformer；具体 GPU 需求待复现后确认。
- 数据：先使用 synthetic labeled graphs 构造两类 matched tasks：semantic-sufficient（中心文本可预测标签）与 structure-essential（文本、prompt、长度相同但标签由 shortest-path/connectivity/cycle 等 topology 决定）；再接一个官方小型 benchmark，固定节点/边标签分布。
- 等价变换：`10 node relabelings × 4 edge orders × 3 starts/directions`，额外覆盖重复 labels 和 disconnected components。
- 拓扑干预：保持文本、节点特征和输出格式不变，分别修改任务关键边和无关边。
- Baseline：GraphTokenizer Feuler/FCPP+BPE、GRAIN-style parser/oracle、solver-disabled、raw edge list、BFS/DFS、continuous projector、GNN-only、readout-only、LLM/Transformer-only、shuffled structural tokens。
- 指标：decode fidelity、serialized equality、BPE token equality、token count、hidden distance、prediction equality、output KL、flip rate、key-intervention direction accuracy、irrelevant-intervention stability、size/density/topology-family OOD。
- 视图对照：local edge-covering、global spectral、random-walk/diffusion、单一 reversible view 与 complementary-view 组合；固定 Transformer 深度、宽度和 token budget，区分 view 信息量、finite-depth 可达性与训练稳定性。
- 关键消融：无 consistency loss、无 addressable tokens、无 intervention loss、randomized BPE、不同 tie-break、去掉各个 view、solver-disabled、parser/oracle-only、固定文本与非固定文本两种干预。

### 支持与失败条件

- 支持条件：semantic-sufficient 子集允许 text-only/HN-1/MLP 接近完整模型；在 structure-essential 子集中 decode fidelity 保持 100%，等价变换下表示和输出稳定，关键拓扑干预方向正确、无关干预稳定；完整模型在 direction accuracy 上超过 parser/oracle、readout-only/GNN-only，并在 held-out topology family 保留优势。
- 失败条件：semantic-sufficient 与 structure-essential 没有可重复差异；HN-1、MLP、readout-only 或 parser/oracle 复现结构必需子集的全部效果；改进仍依赖原 node index 或 traversal tie-break；relabeling 造成 token/prediction flip；BPE 无法稳定 decode；或只提升 standard split、在 topology-OOD 失效。

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

先完成 semantic-sufficiency gate 的 CPU 小实验，不训练大模型：构造 semantic-sufficient/structure-essential matched graph pairs，验证 text-only、HN-1、MLP 与 GNN/readout-only 的差异；若后者有独立 topology signal，再在隔离环境运行 GraphTokenizer serialization/decode 路径，选择 100--1000 个小图，执行 node relabeling、edge order、起点/方向、重复 labels 和 disconnected components 变换，记录 serialized equality、BPE token equality 与 decode isomorphism；随后用 EstGraph 风格的 MH/SRW、coverage、burn-in、seed diversity 和 degree-only/walk-stat-only 做 access-aware OOD 小切片。本地仍缺少 `torch/dgl/networkx/rdkit/transformers`，完整运行审计待补；随后加入 GRAIN-style parser/oracle、solver-disabled、surface naming/form 和 sink-aware controls。该结果决定 I1 进入“待实验”还是降级为评测协议。

### 更新记录

- 2026-08-30：根据第 12 次自动调研的 GraphTokenizer 证据建立 I1；状态为“候选”，novelty 和资源成本待进一步核验。
- 2026-08-30：强化。根据 Lost in Tokenization 的 finite-depth、lossiness 和 complementary-view 证据，补充 task-conditioned view ablation；明确多视图本身不构成创新，I1 的差异必须落在 discrete addressability、fixed-text intervention 和 responsibility split 的联合验证上，状态保持“候选”，评分保持 8/10。
- 2026-08-30：强化。Graph-KV 提供 LLM-side source→target KV mask/shared-PE 的相邻基线，说明 structural routing 已有直接 LLM-side prior art；I1 不新增 I2，也不把 mask、multi-view 或 relabeling 单项作为创新，加入 Graph-KV-style mask-only、random/irrelevant-edge、mask shuffle 和 matched token/compute controls，状态保持“候选”，评分保持 8/10。
- 2026-08-30：强化。Colorful Talks with Graphs 将 ordered 1-WL 的自然颜色词与同一 WL partition 的 opaque tokens 对照，直接暴露 semantic-anchor shortcut；I1 加入 natural color、opaque Hue 和 random-label controls，仍不新增 I2，状态保持“候选”，评分保持 8/10。
- 2026-08-31：强化。GRAIN 将 naming/narrative invariance、exact graph IR recovery 与 external solver responsibility 纳入相邻基线；I1 加入 parser/oracle、solver-disabled 和 surface-form controls，补充 H10/H11/Q12，仍不新增 I2，状态保持“候选”，评分保持 8/10。GraphTokenizer 官方 release 仅完成静态 source audit，运行审计因本地缺少依赖待补。
- 2026-09-01：强化。When Graph Tokens Sink 将 graph-token sink activation、attention、pruning/reposition/swap 与 logit lens 纳入 token-level mechanistic baseline，支持 H1/H5 的证据边界并提出 H13；I1 加入 sink-aware diagnostic、`[PAD]`/位置/预训练域 shortcut controls，明确它们不能替代 fixed-text topology intervention 和 responsibility split。独立 Novelty Gate 同时复核 SOG 的 one-structural-token prior art，单一 structural-token 方法主张不再视为新颖；不新增 I2，状态保持“候选”，评分保持 8/10。
- 2026-09-02：反驳。When Structure Doesn't Help 在语义丰富 TAG、分子和 DTI 任务上显示 semantic-only/中心节点/HN-1/MLP 可匹配或超过结构-aware ND/GNN，削弱“结构普遍有益”的前提；由于 HN-1 仍使用图驱动邻居选择且主要为 8:2 random split，I1 不被直接否定，而是加入 semantic-sufficiency/structure-essential gate、MLP/HN-1/readout-only 失败条件和结构必要子集的 fixed-text topology 审计。Novelty 保持 1/2，状态保持“候选”，评分保持 8/10，不新增 I2。
- 2026-09-03：强化。EstGraph 将 partial-access random-walk statistics、coverage/seed/sampling bias 和百万级 graph-scale estimation 纳入 H10/H15；I1 增加 access-aware OOD slice、degree-only/walk-stat-only、MH/SRW 与 robust aggregation 对照，但不新增 I2。官方 GraphTokenizer release 本轮仍因 bundled Python 缺少 `torch`、`dgl`、`networkx`、`rdkit`、`transformers` 只完成静态 source audit；状态保持“候选”，Novelty 保持 1/2，评分保持 8/10。

### 2026-09-04 强化记录（GenNIE）

- 今日论文：GenNIE 将 relation-aware GNN、MoE projector、structure-guided cross-attention、global soft prompt 和 label-aware supervised contrastive learning 组合到知识图谱 node importance estimation；当前结果是强系统级正例，不构成新的离散 tokenizer 方法。
- 对 I1 的实质强化：增加 label-aware structural objective 的 label-leakage audit；加入 score-shuffle/random-label structural embedding、text-shuffle/random-text、degree/centrality-only、capacity-matched readout、target-label-unseen 和 popularity/name semantic controls。NMI/ARI 只能作为 label-aligned separability，不能作为 topology decoder 结论。
- 因果边界：`w/o Topological Injection` 同时移除 GNN 与 MoE，text-only/structure-only 也未控制参数量和固定文本反事实；因此只能支持“联合输入路径对 NIE 有用”，不能定位 LLM、GNN、projector 或 readout 的独立拓扑责任。
- Novelty Gate：与 GenNIE、GraphTokenizer、Graph-KV、NT-LLM 比较后，I1 的最小差异保持为“discrete addressability + empirical equality + fixed-text key/irrelevant intervention + parser/GNN/LLM/readout responsibility split + topology-family/access OOD”的联合协议；不把 MoE、global prompt、contrastive loss、reversible/BPE、mask 或单项 relabeling 作为新颖方法。
- 状态与评分：不新增 I2；I1 继续为“候选”，Novelty 1/2、Importance 2/2、Falsifiability 2/2、Feasibility 1/2、Thesis Fit 2/2，总分 8/10。
- 下一步：先完成 semantic-sufficiency/structure-essential CPU gate；若 structure-essential 任务存在独立 topology signal，再补 GraphTokenizer runtime equality，并在同一 matched graph pairs 上执行 fixed-text topology intervention 与 component responsibility split。
