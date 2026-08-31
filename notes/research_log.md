# 每日科研日志

本文件只允许在末尾追加，不得覆盖、删除或重写历史记录。首次正式运行从“第 1 次自动调研”开始。

## 2026-08-15 第 1 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q1：现有代表性 GNN-LLM 方法分别提供了哪一层结构证据？Q2：哪些工作真正使用了 permutation、counterfactual intervention 或 topology OOD 测试？
- 今日检索目标：建立 GraphGPT、LLaGA 等代表性 GTokenLLM 的统一结构证据审计基线，并优先寻找能直接检验 graph-token 是否被理解的新评测工作。

### 检索与筛选

- 检索范围与关键词：`GraphGPT`、`LLaGA`、`GraphToken`、`Graph-LLM permutation invariance`、`graph reasoning counterfactual intervention`、`topology OOD generalization`；重点查看 arXiv 与 OpenReview 的论文页、HTML/PDF 和公开代码入口。
- 主要来源：[arXiv:2605.03514](https://arxiv.org/abs/2605.03514)、[论文 HTML](https://arxiv.org/html/2605.03514)、[论文 PDF](https://arxiv.org/pdf/2605.03514)、GraphGPT/LLaGA 等论文的 arXiv 页面及候选 OpenReview 页面。
- 初筛候选数：6 个主要候选方向/论文入口。
- 去重结果：`papers/index.md` 初始为空；以 `arXiv:2605.03514`、规范化标题和 DOI 进行三重去重后，今日新增 1 条，无重复。
- 选择该论文精读的理由：它不是又一个单一接口方法，而是直接复测 6 个代表性 GTokenLLM，提出 GTEval 并同时覆盖 instruction robustness、结构攻击、representation probe 和 attention，最能推进 H1/Q1；同时明确暴露 Q2 仍缺少 permutation 与 topology-OOD 证据。

### 今日精读

- 论文：Revisiting Graph-Tokenizing Large Language Models: A Systematic Evaluation of Graph Token Understanding
- Paper ID：arXiv:2605.03514
- 阅读级别：全文精读
- 笔记：`notes/2605.03514.md`
- PDF：`papers/2026_Zhang_Revisiting_Graph_Tokenizing_LLMs.pdf`，已下载并验证 `%PDF-1.7` 文件头、908648 bytes，arXiv 页面确认 13 页 PDF。

### 核心发现

1. GTEval 将 graph-token understanding 拆为 format-level Rephrasing 和 content-level Relabeling、Reversing、Randomizing；6 个模型在原始固定模板上的准确率不能代表对同一 graph token 的稳定理解，改写后整体下降可达约 50%，content-level 结果多数低于 50% 甚至低于 10%。
2. 论文的 stage-wise 审计显示：GT 可以保留任务相关的文本与结构信息，LLaGA 的 GT/GE 能支持 GCN link prediction 且优于随机特征；但 PRBCD 结构攻击下 GraphGPT/TEA-GLM 基本不变，提示它们可能主要依赖中心节点文本，attention 也只能证明被关注而不能证明因果使用。
3. 增加 instruction tuning 对见过的 Rephrasing/Relabeling 变体有效，但对未见的 Reversing/Randomizing 不能稳定泛化；论文没有直接测试 node permutation、isomorphism consistency、语义保持的 topology counterfactual 或 held-out topology OOD。

### 对研究命题的影响

- 新增证据：H1 从“待验证”更新为“部分支持”；新增待验证 H4，要求把 instruction invariance、结构反事实敏感性和 shortcut controls 联合起来，才讨论因果有效的 graph-token understanding。
- 与历史结论一致或冲突之处：与纲领中“结构被输入、可由 probe 解码、attention 或指标提升都不足以证明结构被因果使用”一致；论文同时显示 GT 确实保留可探测结构信息，因此不能把失败解释为结构信息完全没有进入表示。
- 当前仍不能得出的结论：不能证明 LLM 本身完成了主要结构推理；不能证明置换一致、同构一致、反事实因果利用或跨图规模/密度/拓扑族泛化。

### Idea 与最小实验

- 新增或更新的假设：H4：只有同时通过 instruction invariance、graph counterfactual sensitivity 和 text/label shortcut controls，才能将 graph-token 能力称为因果有效的图结构理解；H1 记录为“部分支持”。
- 最小可证伪实验：对同一模型和同一节点批次做 `instruction variants × node permutation × same-text topology counterfactual × text shuffle`；同时报告原始、text-only、graph-only、GNN-only 与 shortcut baseline，并检查同语义变换稳定、关键结构干预方向正确、无关干预稳定。
- 暂不建议的方向：暂不把 structural probe、attention 可视化或单一原任务 accuracy 单独当作“LLM 已理解图结构”的证据，也暂不把跨数据集测试当作 topology-OOD 结论。

### 下一次研究任务

- 未解决问题：Q2/Q5：寻找直接测量 Graph-LLM 的 node permutation/isomorphic consistency，并与 text shuffle、语义保持的 topology counterfactual 和 topology-OOD 组合的评测工作。
- 建议检索词或目标论文：`Graph-LLM permutation invariance node reordering`、`GTokenLLM isomorphic graph`、`same text different topology counterfactual graph reasoning`、`graph language model topology OOD`；优先精读直接提供这些控制实验的论文。

### 参考来源

- [arXiv abstract page](https://arxiv.org/abs/2605.03514)：论文元数据、摘要、作者结论和版本信息。
- [论文 HTML](https://arxiv.org/html/2605.03514)：统一 GTokenLLM 框架、GTEval 定义、主结果表、PRBCD 结构攻击、probe、attention、消融和附录实验设置。
- [论文 PDF](https://arxiv.org/pdf/2605.03514)：公开 PDF 下载与全文核验来源。

## 2026-08-16 第 2 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q2/Q5：寻找直接测量 node permutation 或 isomorphic consistency 的工作，并区分等价序列化鲁棒性、结构被因果使用和 topology-OOD 泛化。
- 今日检索目标：在 GTEval 的 6 个 GTokenLLM 基线之后，优先寻找包含 node relabeling、edge ordering、syntax control 或未见结构任务的公开评测；同时确认这些证据是否仍缺少 GNN-LLM hybrid 的 same-text topology counterfactual。

### 检索与筛选

- 检索范围与关键词：`Graph-LLM permutation invariance node reordering`、`GTokenLLM isomorphic graph`、`same text different topology counterfactual graph reasoning`、`graph language model topology OOD`；重点查看 arXiv HTML/PDF、arXiv 元数据页和 OpenReview 论文页。
- 主要来源：[Lost in Serialization arXiv 页面](https://arxiv.org/abs/2511.10234)、[论文 HTML](https://arxiv.org/html/2511.10234v3)、[论文 PDF](https://arxiv.org/pdf/2511.10234)、[GraphMind OpenReview 页面](https://openreview.net/forum?id=1D2A6fD6JW)、[Graph Linearization Methods OpenReview 页面](https://openreview.net/forum?id=RpBnG6f4Re)。
- 初筛候选数：6 个主要候选论文/方向入口。
- 去重结果：与 `papers/index.md` 的 `arXiv:2605.03514` 及规范化标题比较后，`arXiv:2511.10234` 为唯一新增条目；未发现重复 Paper ID、DOI 或规范化标题。
- 选择该论文精读的理由：它直接对同一图采样 10 次 node relabeling，进一步分离 computational structure 与 syntax，并新增 spectral tasks；因此可为 H2/Q5 提供比单一 accuracy 更强的行为级证据。它是纯 textual-serialization LLM graph reasoner，不是 GNN-LLM hybrid，这一适用边界本身成为下一轮缺口。

### 今日精读

- 论文：Lost in Serialization: Invariance and Generalization of LLM Graph Reasoners
- Paper ID：arXiv:2511.10234
- 阅读级别：全文精读
- 笔记：`notes/2511.10234.md`
- PDF：`papers/2026_Herbst_Lost_in_Serialization.pdf`；已下载并验证 `%PDF-1.7` 文件头、5,008,193 bytes、32 页、元数据标题和首页文本可提取。

### 核心发现

1. 论文把 serialization 分解成 node labeling、computational structure 和 syntax，并对同一图的 10 次随机重编号报告 accuracy shift 与 normalized output span。G1 在部分任务上对重编号更脆弱，`bipartite_maximum_matching` 暴露了原始编号的连续二分位置捷径；因此 post-training 降低 relabeling sensitivity 不能直接解释为真正的 permutation invariance。
2. edge-list 的邻居 locality、edge ordering、adjacency-list/matrix 结构和 JSON/NetworkX/PyG syntax 都会改变结果；G1-3B/7B 的跨 syntax 平均标准差为 9.69%/10.51%，而 gpt-oss-20B/120B 为 5.41%/3.45%。这说明“模型看到了图”与“模型对等价表示稳定”是不同证据层次。
3. 新增的 12 个 spectral tasks 只显示有限的未见任务泛化：G1-7B 在 12 个任务中仅 7 个胜过 Qwen-7B，整体仅约一半任务的 RelMAE 优于 mean baseline；论文没有 same-text topology counterfactual、text-only/graph-only 或 held-out topology-family split，故不能证明因果结构利用或跨拓扑泛化。

### 对研究命题的影响

- 新增证据：H2 从“待验证”更新为“部分支持”；H1 获得跨纯序列化 LLM 的补充证据；新增 H5：permutation robustness 与 causal topology use 是可分离能力。
- 与历史结论一致或冲突之处：与 GTEval 对“固定模板 accuracy、probe 或 attention 不足以证明结构理解”的结论一致；本论文把缺口具体化为 node position、edge locality、syntax 和 post-training 数据分布捷径。没有证据冲突，但其对象不是 GNN-LLM，不能直接外推到 GraphGPT/LLaGA。
- 当前仍不能得出的结论：不能证明任何模型形成了 representation-level permutation-invariant graph representation；不能证明关键拓扑反事实会引起方向正确的输出变化；不能证明 held-out topology、图规模或密度泛化。

### Idea 与最小实验

- 新增或更新的假设：H5：同一模型可以在 10-way node relabeling 上稳定，却在保持节点文本与任务语义的 topology counterfactual 上不响应或响应方向错误；因此 permutation consistency 不是 causal topology use 的充分条件。
- 最小可证伪实验：对同一模型、同一批图和同一目标节点交叉执行 `10 relabelings × edge-order/syntax variants × same-text topology counterfactual`，记录 output span、accuracy shift、关键结构干预方向正确率和 held-out topology-family accuracy。
- 暂不建议的方向：暂不把 canonical serialization、单一 permutation test、spectral task 分数或更大模型的格式稳定性单独当作 causal structure understanding；下一轮应优先寻找 GNN-LLM/GTokenLLM 上的同类控制。

### 下一次研究任务

- 未解决问题：Q2/Q5/Q6：GNN-LLM/GTokenLLM 是否也对 node relabeling、edge order 和 syntax 敏感？如果通过 permutation test，是否仍能在 same-text topology counterfactual 与 held-out topology 上方向正确地使用结构？
- 建议检索词或目标论文：`GTokenLLM permutation invariance node relabeling`、`GraphGPT LLaGA isomorphic graph consistency`、`GraphMind node edge order robustness`；优先精读直接报告 GNN-LLM permutation/isomorphism 或结构反事实控制的论文。

### 参考来源

- [arXiv abstract page](https://arxiv.org/abs/2511.10234)：论文作者、版本、发表状态和摘要。
- [论文 HTML](https://arxiv.org/html/2511.10234v3)：serialization 分解、模型/数据设置、node relabeling、edge/syntax ablations、spectral benchmark、附录与 limitations。
- [论文 PDF](https://arxiv.org/pdf/2511.10234)：公开 PDF 下载、全文阅读和本地文件核验来源。
- [GraphMind OpenReview 页面](https://openreview.net/forum?id=1D2A6fD6JW)：初筛候选的 graph-structure interaction 与 order robustness 方向入口。
- [Graph Linearization Methods OpenReview 页面](https://openreview.net/forum?id=RpBnG6f4Re)：初筛候选的 serialization/ordering 方法入口。

## 2026-08-17 第 3 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q2/Q5/Q6：GNN-LLM/GTokenLLM 是否也对 node relabeling、edge order 和 syntax 敏感？如果采用 causal/OOD 方法，是否仍能在 same-text topology counterfactual 与 held-out topology 上方向正确地使用结构？
- 今日检索目标：沿上一轮的 GNN-LLM 控制实验队列，寻找同时涉及 causal intervention、confounding control 或 topology OOD 的公开工作，并判断 latent-space intervention 是否足以作为 graph-structure causal-use 证据。

### 检索与筛选

- 检索范围与关键词：`GTokenLLM permutation invariance node relabeling`、`GraphGPT LLaGA isomorphic graph consistency`、`GraphMind node edge order robustness`、`GNN LLM graph counterfactual intervention topology OOD`、`Interventional Prompting for Graph-LLMs`；重点查看 Springer、OpenReview、ACL Anthology 和 arXiv 正式页面。
- 主要来源：[IP-GLLM Springer 正式页面](https://link.springer.com/chapter/10.1007/978-981-92-3520-9_19)、[IP-GLLM PDF URL](https://link.springer.com/content/pdf/10.1007/978-981-92-3520-9_19.pdf?pdf=inline%20link)、[GraphMind OpenReview 页面](https://openreview.net/forum?id=1D2A6fD6JW)、[GraphDO ACL 2025 页面](https://aclanthology.org/2025.acl-long.321/)、[BadGraph arXiv 页面](https://arxiv.org/abs/2603.21155)、[LLM-GOOD arXiv 页面](https://arxiv.org/abs/2503.22097)。
- 初筛候选数：5 个主要论文/方向入口。
- 去重结果：与 `papers/index.md` 中的 `arXiv:2605.03514`、`arXiv:2511.10234` 及规范化标题比较后，`doi:10.1007/978-981-92-3520-9_19` 是唯一新增且直接覆盖 Graph-LLM causal/OOD 的论文；GraphMind、GraphDO、BadGraph 和 LLM-GOOD 暂保留为后续候选，未新增重复行。
- 选择该论文精读的理由：它是当前队列中少见的直接 Graph-LLM hybrid 工作，明确把局部 GNN tokenizer、causal filtering、interventional prompt 和 OOD/transfer 组合起来，能检验“因果方法增益是否已经接近结构因果利用”；同时它的缺失项能精确推进 H4/H5/Q2/Q5，而不应被单纯的指标提升掩盖。

### 今日精读

- 论文：Interventional Prompting for Graph-LLMs: Approximating Atomic Causality at Scale
- Paper ID：doi:10.1007/978-981-92-3520-9_19
- 阅读级别：全文精读（Springer 官方 HTML 全文）
- 笔记：`notes/10.1007_978-981-92-3520-9_19.md`
- PDF：公开 Springer PDF URL 可访问，但本机下载被 Springer browser/authentication challenge 阻断；已尝试官方 PDF 端点的 HEAD、curl、PowerShell 和 Python 请求，均未得到合法 PDF 文件，因此没有创建伪造本地 PDF。后续获得合法文件后需补做 PDF header、page count、Poppler 文本与表格复核。

### 核心发现

1. IP-GLLM 的结构路径是 `Graph → 2-layer GIN tokenizer → MLP projector → REC-Context token mask → K 个 interventional prompts → frozen LLM + LoRA → graph classification`。它把 intervention 的位置从 graph-level 改为 embedding-space，作者给出 conditional KL bound，并报告额外 cross-attention 为 `O(KNd)`。
2. 作者在 RWG-Molecular、RWG-Citation、SPMotif、GOOD-HIV、GOOD-SST2、DrugOOD、ExplaGraphs 和 OGB-Arxiv 上比较 10 个 causal GNN/Graph Transformer/Graph-LLM baseline；报告 confounding 10%–90% 下更稳定、8 个 benchmark 的增益、prompt removal 平均下降 4.6 points，以及从 RWG-Molecular 到未见 SPMotif 的 zero-shot 63.6%（比 LLaGA 高 3.4 points）。这些是结构相关的 OOD/transfer 间接证据，不是 graph-level causal proof。
3. 论文没有 node permutation/isomorphism、same-text topology counterfactual、text shuffle、GNN-only 或 LLM-only 控制。2-layer GIN 和 REC-Context 可能已完成主要局部结构编码与 causal-subgraph 选择；attention visualization 只能说明 prompt 关注了 ring、避开了 star，不能证明最终 LLM 对结构做了因果推理。

### 对研究命题的影响

- 新增证据：H1 获得补充支持；即使显式引入 causal tokenizer、REC-Context 和 intervention-style prompt，论文主要仍以 OOD/transfer、ablation 和 attention 支持方法有效，不能直接把增益归因于 LLM 的结构因果使用。H4 获得可复用的 causal alignment/confounding-sweep 方法，但状态仍为“待验证”。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization 对“accuracy、probe、attention 或单一 robustness 不能单独证明结构理解”的判断一致；IP-GLLM 的新贡献是把 causal/OOD 控制放进 Graph-LLM 接口，而不是提供已完成的 permutation/counterfactual 闭环。没有证据冲突。
- 当前仍不能得出的结论：不能证明 latent-space prompt 等价于 graph-level `do(·)`；不能证明 LLM 而非 GNN/tokenizer 完成主要结构计算；不能证明 node permutation/isomorphism 一致、同文本 topology 干预方向正确或 held-out topology-family 泛化。

### Idea 与最小实验

- 新增或更新的假设：H6：latent-space soft intervention 的 OOD 增益与 LLM 对 causal topology 的 graph-level 因果利用是可分离能力；前者成立不蕴含后者成立。状态：待验证。
- 最小可证伪实验：固定节点文本/特征和标签语义，构造 causal ring 与 star confounder 的 matched graph pairs；对关键 causal edge/path、无关 edge、10 次 node relabeling 和 edge-order/syntax 变体做干预，比较 base Graph-LLM、IP-GLLM、GNN-only、LLM-only、text-only，并在 held-out topology family 上报告 output direction accuracy、invariant variance 和 OOD accuracy。
- 暂不建议的方向：暂不把 causal prompt attention、confounding-sweep gain、单一 OOD 分数或更高的 graph-classification accuracy 直接表述为 LLM 已完成 causal topology reasoning；也暂不把 IP-GLLM 的 local GIN tokenizer 视为已验证的“结构瓶颈”。

### 下一次研究任务

- 未解决问题：Q2/Q5/Q6/H6：IP-GLLM 的 latent-space intervention 能否在保持文本和任务语义不变时，对关键拓扑做方向正确的输出干预，并与 GNN-only/readout-only 分离？
- 建议检索词或目标论文：`Graph-LLM graph-level counterfactual intervention`、`GNN-LLM causal topology intervention`、`Graph-LLM node permutation isomorphism consistency`、`IP-GLLM code benchmark`；若没有直接联合评测，下一次先找能提供 matched graph pair 与 LLM/GNN ablation 的工作。

### 参考来源

- [Springer 正式论文页面](https://link.springer.com/chapter/10.1007/978-981-92-3520-9_19)：元数据、摘要、方法、理论、实验、消融、限制与结论的官方全文。
- [Springer 公开 PDF URL](https://link.springer.com/content/pdf/10.1007/978-981-92-3520-9_19.pdf?pdf=inline%20link)：公开 PDF 下载入口；本轮本机请求被 browser/authentication challenge 阻断，未形成合法本地归档。
- [GraphMind OpenReview](https://openreview.net/forum?id=1D2A6fD6JW)：初筛的 node/edge description order robustness 候选。
- [GraphDO ACL 2025](https://aclanthology.org/2025.acl-long.321/)：初筛的 edge-list description order 控制候选。

## 2026-08-18 第 4 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q2/Q5/Q6/H6：现有工作是否能把 node permutation robustness 与 topology counterfactual、LLM/GNN 责任分离和 topology-OOD 放进同一条证据链？如果不能，专用结构 encoder 是否可以单独制造稳定性？
- 今日检索目标：沿上一轮“Graph-LLM node permutation/isomorphism consistency”队列，寻找包含 10-way node relabeling、结构化 encoder/readout、graph-level baseline 或 graph counterfactual 的公开论文；优先精读能提供合法全文和可复用控制协议的工作。

### 检索与筛选

- 检索范围与关键词：`Graph-LLM permutation invariance node relabeling`、`GraphGPT LLaGA isomorphic graph consistency`、`graph-level counterfactual intervention LLM GNN`、`graph descriptive order`、`small language models graph classifiers permutation robustness`；重点查看 arXiv HTML/PDF、ACL Anthology、OpenReview 和作者论文页。
- 主要来源：[HLM-G arXiv 页面](https://arxiv.org/abs/2410.22372)、[HLM-G HTML 全文](https://arxiv.org/html/2410.22372)、[GraphDO ACL 2025 页面](https://aclanthology.org/2025.acl-long.321/)、[LLM-GCE ACL Findings 2024 页面](https://aclanthology.org/2024.findings-emnlp.415/)、[Small Language Models permutation robustness 页面](https://www.preprints.org/manuscript/202602.0946)、[Graph Linearization Methods OpenReview 页面](https://openreview.net/forum?id=RpBnG6f4Re)。
- 初筛候选数：5 个主要候选论文/方向入口：HLM-G、GraphDO、LLM-GCE、Small Language Models as Graph Classifiers、Graph Linearization Methods。
- 去重结果：与 `papers/index.md` 中已有的 `arXiv:2605.03514`、`arXiv:2511.10234` 和 `doi:10.1007/978-981-92-3520-9_19` 以及规范化标题比较后，`arXiv:2410.22372` 是唯一新增且能同时提供 permutation protocol、结构化 encoder、GraphToken/GNN baseline 和公开完整 PDF 的精读对象；其余候选保留为后续队列，未新增重复行。
- 选择该论文精读的理由：HLM-G 直接对同一图执行 10 次 `A_t = P A_{t-1} P^T`，同时公开 local/global 结构瓶颈、结构/特征 pooling、解释器与 prompt/token OOD 消融；它可以检验“置换稳定性是否可能由 encoder/readout 架构提供，而不是由 LLM 因果推理提供”。

### 今日精读

- 论文：A Hierarchical Language Model For Interpretable Graph Reasoning
- Paper ID：arXiv:2410.22372
- 阅读级别：全文精读
- 笔记：`notes/2410.22372.md`
- PDF：`papers/2024_Khurana_Hierarchical_Language_Model_For_Interpretable_Graph_Reasoning.pdf`；已从官方 arXiv PDF 下载并验证 `%PDF-1.5` 文件头、2,677,793 bytes、34 页、未加密；Poppler `pdfinfo` 可读，`pdftoppm` 成功渲染首​​页。

### 核心发现

1. HLM-G 的结构路径为 `Graph(A,X,E) → 每节点 feature/1-hop structure annotation → local block 的 intra-node attention → structure/feature pooling → global block 的 inter-node attention → query readout → MLP`。主 graph reasoning 实验不是 `GNN → frozen LLM`，而是专用的 hierarchical encoder/classifier；生成附录才通过 prefix tuning 接入 frozen Llama-2 7B。
2. 论文在 7 个合成 graph reasoning 任务上报告高 accuracy，并对每个图做 10 次 node relabeling；HLM-G accuracy drop 为 Node Degree 0.0、Edge Existence 0.0、Shortest Distance 6.1、Reachable 0.8、Cycle 0.1、Edge Count 3.0、Components 10.2，明显低于 BERT、Llama-3 和 GraphWiz。该结果是较强的 permutation robustness 证据，但只报告 accuracy drop，不是逐样本 prediction equality、representation equivariance 或完整 isomorphism consistency。
3. 结构/特征 pooling、local embedding 的 hop/common-neighbor similarity、Recall@k、attention 和 cross-prompt/node-token OOD 共同说明结构被编码并能支持任务；但论文没有 fixed-node-text topology counterfactual、text shuffle、GNN-LLM permutation 对照、held-out topology family split 或关键/无关干预方向。因此它支持“架构级结构编码和置换鲁棒性”而不支持“LLM 对拓扑进行因果使用”。

### 对研究命题的影响

- 新增证据：H1 维持“部分支持”；HLM-G 是结构化 hierarchical encoder 的正面边界案例，证明低 permutation drop 可以出现，但仍没有 LLM causal-use 证据。H2 维持“部分支持”；HLM-G 的 local/global bottleneck 缓解 node-index shortcut，但普通 BERT/Llama-3/GraphWiz 仍敏感，且不能直接外推到 GNN-LLM。H5 维持“待验证”，但得到正面设计证据：permutation robustness 与 topology counterfactual 必须分开测量。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization、IP-GLLM 对“accuracy、probe、attention、OOD gain 或单一 robustness 不能单独证明结构因果使用”的结论一致；HLM-G 的新价值是提供了一个结构化 encoder 能稳定置换的反例，没有推翻历史结论，也不能将纯/专用 encoder 的结果直接迁移到 GraphGPT、LLaGA 或 IP-GLLM。
- 当前仍不能得出的结论：不能证明 HLM-G 的 global readout 或附录 Llama-2 decoder 对关键拓扑做了因果推理；不能证明其 80/10/10 random split 泛化到未见 topology family、图规模外推或结构组合；不能证明 GraphToken/IP-GLLM 在相同 permutation protocol 下也有相同稳定性。

### Idea 与最小实验

- 新增或更新的假设：H7：架构级 permutation robustness 可以由局部结构 encoder、pooling 和全局 readout 提供，而不代表后续 LLM 形成了因果拓扑表征。状态：待验证。证据来源为 HLM-G 的 10-way relabeling、local embedding similarity、pooling ablation 与 cross-prompt/node-token OOD。
- 最小可证伪实验：在相同节点文本/特征、任务语义和标签的 matched graph pairs 上构造 ring/star、connected/disconnected、short-path/long-path topology counterfactual；对 HLM-G、GraphToken、IP-GLLM 执行 10 次 node relabeling、edge-order/syntax 变体、关键边/无关边干预和 held-out topology-family 测试，记录 permutation flip rate、invariant variance、intervention direction accuracy 与 OOD accuracy。
- 对照组：HLM-G local-only、global readout-only、α=0 feature-only、α=1 structure-only、GNN-only、LLM-only、text-only，以及直接把结构摘要写入文本的 shortcut baseline。
- 支持条件：置换和边顺序变化下输出/表示稳定；关键拓扑干预使输出按任务语义方向变化；无关边干预稳定；未见 topology family 上仍成立，且 LLM-only/readout-only 不能复现全部结果。
- 失败条件：模型虽保持 permutation stability，但对关键 topology intervention 不敏感或方向错误；GNN-only/readout-only 已达到相同 direction accuracy；text-only 或 feature-only 与完整模型相当；或优势只存在于训练过的 graph generator/topology family。
- 暂不建议的方向：暂不把 HLM-G 的低 permutation drop、Recall@k、attention、α ablation 或高合成任务 accuracy 写成“LLM 已理解并因果使用图结构”；也不把 cross-prompt/node-token OOD 写成 topology-OOD。

### 下一次研究任务

- 未解决问题：优先核验 LLM-GCE 是否提供真正可复用的 graph-level counterfactual control；它用 LLM 生成 counterfactual text pairs、autoencoder 生成 topology edits 并翻转 GNN 预测，但对象是 LLM-assisted GNN explanation 而非 Graph-LLM。随后继续 IP-GLLM 的 causal-ring/star-confounder matched pairs，并加入 HLM-G/GraphToken encoder/readout-only 对照。
- 建议检索词或目标论文：`LLM-GCE counterfactual graph topology text pairs`、`Graph-LLM graph-level counterfactual intervention`、`GNN-LLM causal topology intervention`、`IP-GLLM code benchmark`、`GraphToken permutation robustness`。

### 参考来源

- [HLM-G arXiv abstract page](https://arxiv.org/abs/2410.22372)：论文作者、版本、摘要和发表状态。
- [HLM-G HTML 全文](https://arxiv.org/html/2410.22372)：local/global architecture、permutation experiment、real-world datasets、interpretability、OOD/prompt ablation、limitations 和 appendix。
- [HLM-G 官方 PDF](https://arxiv.org/pdf/2410.22372)：34 页公开全文；本地 PDF 已完成 header、page count、未加密和渲染验证。
- [GraphDO ACL 2025](https://aclanthology.org/2025.acl-long.321/)：初筛的 graph description edge-order 控制候选。
- [LLM-GCE ACL Findings 2024](https://aclanthology.org/2024.findings-emnlp.415/)：下一轮 graph counterfactual explanation 候选及公开 PDF 入口。

## 2026-08-19 第 5 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q2/Q5/Q6/H6：graph-level counterfactual 是否能在固定节点文本/特征下证明 Graph-LLM 的结构因果利用，并分离 LLM、GNN oracle 与 graph decoder 的责任？
- 今日检索目标：沿上一轮队列优先核验 LLM-GCE 的 LLM-generated counterfactual text pairs、autoencoder topology edits 和 GT-GNN prediction flip；明确它能复用为 Graph-LLM causal control 的部分，以及它作为 LLM-assisted GNN explanation 的边界。

### 检索与筛选

- 检索范围与关键词：`LLM-GCE counterfactual graph topology text pairs`、`Graph-LLM graph-level counterfactual intervention`、`GNN-LLM causal topology intervention`、`Graph-LLM node permutation isomorphism consistency`、`graph descriptive order LLM`；重点查看 ACL Anthology、arXiv 和可访问的论文 PDF。
- 主要来源：[LLM-GCE ACL 正式页面](https://aclanthology.org/2024.findings-emnlp.415/)、[LLM-GCE ACL 官方 PDF](https://aclanthology.org/2024.findings-emnlp.415.pdf)、[LLM-GCE arXiv 页面](https://arxiv.org/abs/2410.15165)、[GraphDO ACL 2025 页面](https://aclanthology.org/2025.acl-long.321/)、Small Language Models permutation robustness 的预印本页面和 Graph Linearization Methods/OpenReview 候选入口。
- 初筛候选数：5 个主要论文/方向入口：LLM-GCE、GraphDO、Small Language Models as Graph Classifiers、Graph Linearization Methods、GraphMind。
- 去重结果：与 `papers/index.md` 中已有的四条 Paper ID、DOI 和规范化标题比较后，`doi:10.18653/v1/2024.findings-emnlp.415` 是唯一新增且能提供 graph-level counterfactual、公开完整 PDF 和 GNN oracle 细节的精读对象；GraphDO、Small Language Models、Graph Linearization Methods 和 GraphMind 暂保留为后续候选。OpenReview 的部分候选页面触发 browser verification，本轮没有把它们升级为精读论文。
- 选择该论文精读的理由：它直接定义并生成最小距离、目标预测翻转的 graph counterfactual，且把 LLM 语义指令、graph decoder、GNN prediction feedback 和化学 feasibility 放进一条可复用协议；同时它能精确暴露“反事实成功由谁完成”的责任分离缺口，推进 H1/H6/H8/Q7。

### 今日精读

- 论文：Explaining Graph Neural Networks with Large Language Models: A Counterfactual Perspective on Molecule Graphs
- Paper ID：doi:10.18653/v1/2024.findings-emnlp.415（arXiv:2410.15165）
- 阅读级别：全文精读
- 笔记：`notes/10.18653_v1_2024.findings-emnlp.415.md`
- PDF：`papers/2024_He_Explaining_Graph_Neural_Networks_with_Large_Language_Models.pdf`；从 ACL 官方 PDF 下载，已验证 `%PDF-1.5`、745815 bytes、18 页、未加密；Poppler `pdfinfo` 可读且 `pdftoppm` 成功渲染首页。

### 核心发现

1. LLM-GCE 的结构路径是 `G=(X,A,E) → 2-layer edge-aware GCN (GT-GNN) → q_G`，并行地 `SMILES/TP/CTP → GPT-4/GPT-3.5 → BERT encoder → z_G`，再通过 `z_G ⊕ q_G → MLP graph decoder → (A_hat,X_hat,E_hat)` 生成 counterfactual；GT-GNN 再用 `L_pred` 判定目标类别并提供 dynamic feedback。
2. 论文在 AIDS、Mutagenicity、BBBP、ClinTox、Tox21 上比较 GNNExplainer、CF-GNNExplainer、CLEAR、RegExplainer；带 RDKit feasibility check 时，LLM-GCE 在几乎所有数据集上取得最高或接近最高的 feasible validity 和最低 feasible proximity。这个结果证明了 GCE 工程协议的有效性，不证明 LLM 最终因果使用 topology。
3. 反事实干预证据仍有明确边界：没有 node permutation/isomorphism、edge-order、text shuffle、same-text topology pair、q_G removal/shuffle、LLM-only/GNN-only responsibility split 或 held-out topology-family；direct GPT-4 生成五个数据集的 valid counterfactual validity 全为 0，说明 CA/GNN 负责主要图执行。论文自身也承认只评估 molecular property prediction、依赖预训练知识、计算成本高且可能 hallucinate。

### 对研究命题的影响

- 新增证据：H1 维持“部分支持”；LLM-GCE 的 LLM-guided GCE 增益仍由 GT-GNN prediction loss、graph decoder 和 RDKit feasibility 共同定义。H6 维持“待验证”，但从 latent-space prompt intervention 扩展为 graph-level counterfactual explainer 的边界证据；新增 H8“LLM 语义反事实指令可提升 GNN explanation，但不蕴含 LLM topology causal use”，状态为“待验证”。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization、IP-GLLM 和 HLM-G 对“accuracy、probe、attention、OOD 或单一 robustness 不能单独证明结构因果使用”的结论一致；它提供了可复用的 graph-level prediction flip，但没有推翻“permutation consistency 与 causal topology use 必须分开”的 H5。
- 当前仍不能得出的结论：不能证明 LLM 自己编码并执行了关键拓扑干预；不能证明 text/CTP 不含标签或显著子图泄漏；不能证明对 node relabeling、等价 SMILES、固定文本不同 topology 或未见拓扑族稳定。

### Idea 与最小实验

- 新增或更新的假设：H8：LLM-generated semantic CTP 能提升 GNN counterfactual 的 feasible validity/proximity，但这种增益不蕴含 LLM 对 topology 的因果利用；若移除或打乱 `q_G`，性能和 direction accuracy 应显著下降，而 LLM-only 不能复现结构干预方向。
- 最小可证伪实验：固定节点文本/属性、标签语义和输出格式，构造 ring/star、connected/disconnected 及关键/无关 edge/path 的 matched topology pairs；对 LLM-GCE 原始、q_G removal/shuffle、text shuffle、CTP random、direct LLM、GNN-only、CA/readout-only 与 Graph-LLM 执行 `10 relabelings × edge-order/syntax × same-text topology counterfactual × held-out topology family`，报告 feasible validity、proximity、direction accuracy、permutation flip rate 和 invariant variance。
- 暂不建议的方向：暂不把 LLM-GCE 的 feasible validity/proximity、dynamic feedback 或 chemical case study 写成 LLM 已理解图结构；也不把 graph-level prediction flip 自动写成因果干预，除非同时证明 fixed-text、关键/无关方向、等价变换稳定和 GNN/LLM responsibility separation。

### 下一次研究任务

- 未解决问题：Q7：能否把 LLM-GCE 的 `GT-GNN oracle + graph distance + prediction flip + feasibility` 协议迁移到固定节点文本的 Graph-LLM matched pairs，并用 q_G/text/decoder ablation 分离结构计算责任？
- 建议检索词或目标论文：`LLM-GCE q_G ablation counterfactual`、`Graph-LLM fixed text topology intervention`、`GNN oracle versus LLM graph reasoning`、`Graph-LLM node permutation isomorphism consistency`；若没有现成联合评测，下一轮先做 IP-GLLM causal-ring/star-confounder pairs，并把 HLM-G、GraphToken 的 encoder/readout-only 对照加入统一 protocol。

### 参考来源

- [ACL Anthology 正式页面](https://aclanthology.org/2024.findings-emnlp.415/)：作者、DOI、发表信息、摘要和公开 PDF 入口。
- [ACL 官方 PDF](https://aclanthology.org/2024.findings-emnlp.415.pdf)：全文的 GCE 定义、数据构造、方法、GT-GNN/CA 路径、表格、消融、direct-LLM 失败、limitations 和 appendix。
- [arXiv:2410.15165](https://arxiv.org/abs/2410.15165)：预印本 ID、标题、作者、发表关联和代码入口。
- [GraphDO ACL 2025](https://aclanthology.org/2025.acl-long.321/)：初筛的 graph description order 控制候选。

## 2026-08-22 第 6 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q7：能否把 graph-level causal intervention protocol 迁移到 fixed-node-text Graph-LLM matched pairs，并分离 graph encoder、readout/decoder 与 LLM 的责任；同时推进 Q5/Q6 对 encoding/order shortcut 与 topology intervention 的联合评测。
- 今日检索目标：沿上一轮 `LLM-GCE q_G ablation counterfactual`、`Graph-LLM fixed text topology intervention`、`GNN oracle versus LLM graph reasoning` 和 `Graph-LLM node permutation isomorphism consistency` 队列，寻找可公开核验、包含 causal query/intervention、encoding/order control 或责任分离协议的新工作。

### 检索与筛选

- 检索范围与关键词：`LLM-GCE q_G ablation counterfactual`、`Graph-LLM fixed text topology intervention counterfactual`、`GNN oracle LLM graph reasoning counterfactual`、`Graph-LLM node permutation isomorphism consistency`、`causal graph encoding intervention LLM`；重点查看 ACL Anthology、arXiv、OpenReview 和作者公开代码仓库。
- 主要来源：[CausalGraph2LLM ACL 页面](https://aclanthology.org/2025.findings-naacl.110/)、[CausalGraph2LLM 官方 PDF](https://aclanthology.org/2025.findings-naacl.110.pdf)、[CausalGraph2LLM 官方代码](https://github.com/ivaxi0s/CausalGraph2LLM)、[CoEvoT arXiv 页面](https://arxiv.org/abs/2607.14114)、[Graph-R1 ACL 页面](https://aclanthology.org/2025.emnlp-main.1220/)、[LIBERTy ACL 页面](https://aclanthology.org/2026.findings-acl.399/)、[LLM-GCE ACL 页面](https://aclanthology.org/2024.findings-emnlp.415/)。
- 初筛候选数：5 个主要候选方向/论文入口：CausalGraph2LLM、CoEvoT、Graph-R1、LIBERTy、LLM-GCE。
- 去重结果：与 `papers/index.md` 中已有的 5 条 Paper ID、DOI 和规范化标题比较后，`doi:10.18653/v1/2025.findings-naacl.110` 为唯一新增且能同时提供 causal graph encoding、downstream intervention、order/semantic shortcut control 和合法公开全文的精读对象；LLM-GCE 为历史重复，CoEvoT、Graph-R1、LIBERTy 暂保留为后续候选，未新增重复行。
- 选择该论文精读的理由：它把同一 DAG 的 7 种 serialization、graph-level/node-level query、random-vs-semantic label、BFS/BFS-R ordering 和 `do(X=x)` downstream task 放入统一 benchmark，能为 H4/H5/Q5/Q6 提供比单一 Graph-LLM accuracy 更明确的诊断轴；同时其纯 LLM 对象边界可作为 Graph-LLM transfer 的负面对照。

### 今日精读

- 论文：CausalGraph2LLM: Evaluating LLMs for Causal Queries
- Paper ID：doi:10.18653/v1/2025.findings-naacl.110（arXiv:2410.15939）
- 阅读级别：全文精读
- 笔记：`notes/10.18653_v1_2025.findings-naacl.110.md`
- PDF：`papers/2025_Sheth_CausalGraph2LLM.pdf`；ACL 官方 PDF 下载成功，已验证 `%PDF-1.5`、786789 bytes、23 页、未加密、A4；Poppler `pdfinfo` 可读且 `pdftoppm` 成功渲染首页。

### 核心发现

1. CausalGraph2LLM 将同一有向无环图分别编码为 JSON、edge-list adjacency、adjacency matrix、GraphML、Graphviz、single-node 和 multi-node 文本，并评估 source、sink、parent、child、mediator、confounder 六类 graph-level/node-level 查询；作者报告不同 encoding 间最高约 60% 的性能变化，adjacency-matrix 在 graph-level 通常较弱，JSON 对 parent query 存在格式对齐优势。
2. benchmark 的 shortcut controls 很有价值：Alarm/Insurance 在 meaningful variable names 下通常优于 random IDs，但会出现 pretraining-driven false positive；anti-commonsense DAG 方向翻转会降低性能；BFS 与 BFS-R 节点遍历顺序改变 source/sink 结果。node-level 查询普遍比要求全局汇总的 graph-level 查询容易，且不同模型呈现 causal overestimation 或 underestimation 偏置。
3. 论文把 `do(X=x)` 定义为固定 X 并移除其 incoming edges，加入 downstream intervention task；该任务同样对 encoding 敏感，adjacency-matrix 在 GPT-4 上接近 0.50 random baseline。这个结果提供了“结构查询正确率”和“干预任务敏感性”必须分开测量的证据，但不是 fixed-text topology counterfactual，也没有 GNN/LLM/readout responsibility split。

### 对研究命题的影响

- 新增证据：H1/H2 获得纯序列化 causal-graph LLM 的补充支持；H4/H5 仍为“待验证”，但得到可复用的 encoding、ordering、semantic-label 与 intervention 交叉控制；新增 H9“encoding/order robustness 与 topology intervention sensitivity 是两个近似正交轴”，状态为“待验证”。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization、IP-GLLM、HLM-G 和 LLM-GCE 对“accuracy、probe、attention、OOD 或单一 intervention 不能单独证明 causal topology use”的判断一致；CausalGraph2LLM 的新增价值是把 semantic/ordering shortcut 直接接到 downstream intervention 上，没有证据冲突。
- 当前仍不能得出的结论：不能证明任何 Graph-LLM 具备 node permutation/isomorphism consistency；不能证明 `do(X=x)` query 等价于对模型图输入做 topology intervention；不能证明 LLM 而非 encoder/readout 完成主要结构计算，也不能推出 held-out topology-family 泛化。

### Idea 与最小实验

- 新增或更新的假设：H9：Graph-LLM 的 encoding/order robustness 与 topology intervention sensitivity 是两个近似正交轴；通过一个轴不能推出通过另一个轴。
- 最小可证伪实验：在固定节点文本/特征、标签语义和输出格式的 matched graph pairs 上执行 `10 relabelings × 7 serialization/edge-order variants × key/irrelevant topology intervention × text shuffle`，复现 CausalGraph2LLM 的 graph/node/intervention query，并报告 prediction equality、permutation flip rate、intervention direction accuracy 和 invariant variance。
- 对照组与失败条件：加入 base Graph-LLM、GNN-only、LLM-only、encoder/readout-only、text-only、random-label/text-shuffle、q_G removal/shuffle；若无关拓扑干预不稳定、关键干预方向错误、readout/text-only 达到完整模型，或性能只在 semantic label/known DAG 上成立，则否定 H9。暂不把纯 LLM causal-query accuracy 或 adjacency-matrix/JSON 的单点优势写成 Graph-LLM causal-use 证据。

### 下一次研究任务

- 未解决问题：Q7/H9：能否把 CausalGraph2LLM 的 7-way encoding/order、random-vs-semantic label 和 `do(X=x)` direction task 与 GraphToken、IP-GLLM、HLM-G 的 fixed-text topology pairs 联合，分离 permutation robustness、causal topology use 和 graph encoder/readout responsibility？
- 建议检索词或目标论文：`Graph-LLM causal query encoding intervention`、`fixed text topology counterfactual Graph-LLM`、`GraphToken permutation encoding robustness`、`CausalGraph2LLM Graph-LLM transfer`；若没有直接论文，继续审计 IP-GLLM、GraphToken、HLM-G 的 encoder/readout-only 与 LLM-only 对照，并优先构造 causal-ring/star-confounder matched pairs。

### 参考来源

- [ACL Anthology 正式页面](https://aclanthology.org/2025.findings-naacl.110/)：作者、DOI、发表信息、摘要和官方 PDF 入口。
- [ACL 官方 PDF](https://aclanthology.org/2025.findings-naacl.110.pdf)：全文的 7 种 encoding、graph/node-level queries、`do(X=x)` intervention、context/ordering/fine-tuning/visual controls、结果表、limitations 和 appendix。
- [官方代码仓库](https://github.com/ivaxi0s/CausalGraph2LLM)：benchmark 的 prompt、任务、模型和 intervention 运行入口。
- [CoEvoT arXiv](https://arxiv.org/abs/2607.14114)、[Graph-R1 ACL](https://aclanthology.org/2025.emnlp-main.1220/)、[LIBERTy ACL](https://aclanthology.org/2026.findings-acl.399/)：初筛的 graph reasoning、structural counterfactual 和 Graph-LLM 候选入口。

## 2026-08-23 第 7 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q7/H9：能否把 encoding/order、fixed-text topology intervention、跨拓扑泛化和 encoder/readout/LLM responsibility split 放入同一条证据链；如果没有直接 Graph-LLM 论文，哪些 OOD 轴可先迁移？
- 今日检索目标：沿上一轮 `Graph-LLM causal query encoding intervention`、`fixed text topology counterfactual Graph-LLM`、`GraphToken permutation encoding robustness` 和 `CausalGraph2LLM Graph-LLM transfer` 队列，寻找能补充 topology/generalization 证据的公开工作，并区分纯文本 LLM benchmark 与 GNN-LLM hybrid 证据边界。

### 检索与筛选

- 检索范围与关键词：`Graph-LLM causal query encoding intervention`、`fixed text topology counterfactual Graph-LLM`、`GraphToken permutation encoding robustness`、`GNN-LLM synthetic to real graph reasoning`、`Graph-LLM topology OOD graph size extrapolation`；重点查看 ACL Anthology、arXiv 和作者公开代码仓库。
- 主要来源：[NLGift ACL 正式页面](https://aclanthology.org/2024.findings-emnlp.127/)、[NLGift 官方 PDF](https://aclanthology.org/2024.findings-emnlp.127.pdf)、[NLGift arXiv 页面](https://arxiv.org/abs/2406.15992)、[NLGift 官方代码](https://github.com/MatthewYZhang/NLGift)、[CoEvoT arXiv](https://arxiv.org/abs/2607.14114)、[Graph-R1 ACL 页面](https://aclanthology.org/2025.emnlp-main.1220/)、[LIBERTy ACL 页面](https://aclanthology.org/2026.findings-acl.399/)、[GraphTMI ACL 页面](https://aclanthology.org/2024.findings-naacl.34/)。
- 初筛候选数：5 个主要论文/方向入口：NLGift、CoEvoT、Graph-R1、LIBERTy、GraphTMI。
- 去重结果：与 `papers/index.md` 中现有 6 条 Paper ID、DOI 和规范化标题比较后，5 个候选均未命中已有条目；NLGift 与 GraphTMI 都是结构泛化/编码评测候选，CoEvoT 是较新的 Graph-LLM 结构反馈方法，Graph-R1 是显式 reasoning 的 GNN-free 对照，LIBERTy 是结构反事实的通用 LLM explainability 方法。本轮选择 NLGift，其他候选保留后续队列，不新增重复行。
- 选择该论文精读的理由：它在同一 benchmark 中拆分 semantic、numeric、graph-size/generator/transitivity、cross-task 和 synthetic-to-real 五个迁移轴，并报告 graph size、reasoning transfer 与 real-world transfer 的明显缺口；这比上一轮单一 encoding/intervention 结果更适合推进 H10/Q6，同时能作为 Graph-LLM transfer 的负面对照。它不是 GNN-LLM hybrid，正是需要在笔记中明确的适用边界。

### 今日精读

- 论文：Can LLM Graph Reasoning Generalize beyond Pattern Memorization?
- Paper ID：doi:10.18653/v1/2024.findings-emnlp.127（arXiv:2406.15992）
- 阅读级别：全文精读
- 笔记：`notes/10.18653_v1_2024.findings-emnlp.127.md`
- PDF：`papers/2024_Zhang_Can_LLM_Graph_Reasoning_Generalize_Beyond_Pattern_Memorization.pdf`；从 ACL 官方 PDF 下载，已验证 HTTP 200、`application/pdf`、`%PDF-1.5`、390891 bytes、17 页、未加密，pypdf 可解析；本机无 Poppler，使用 PyMuPDF 渲染第 1、9、17 页并目检版面可读。

### 核心发现

1. NLGift 将 4 个 graph reasoning task（connectivity、shortest path、topological sort、maximum flow）与 5 类 pattern transfer 组合成 37,000 问题 benchmark。semantic/numeric 变化有一定迁移，但 reasoning pattern 只有 8/24 设置达到 Significant Transfer、没有 Strong Recovery；这说明跨 task 的 graph reasoning 不能由同 task accuracy 推出。
2. structural pattern 中 graph size 是最难的 OOD 轴：只有 2/8 设置达到 Strong Recovery，small-to-large graph 平均 PGR 低于 60%；ER↔BA generator 与 low/high transitivity 的迁移相对更好。该结果把 size、generator、transitivity 分成可单独报告的 topology/generalization 轴，但仍混入序列长度和任务难度。
3. synthetic graph instruction tuning 对 StrategyQA、Knowledge-Crosswords、ExplaGraphs 和 ProScript 几乎没有普遍收益，ProScript 上两种模型调优后平均下降约 12.5%；直接在 real-world task 上调优明显更好。keyword frequency、semantic template、task mixture 与 in-context 分析进一步暴露文本和任务组成捷径。

### 对研究命题的影响

- 新增证据：H1 维持“部分支持”；NLGift 补充了从表面 semantic/numeric transfer 到 size、cross-task、synthetic-to-real transfer 的分层证据。H5/H6 维持“待验证”，但获得可迁移的 `PGR/Strong Recovery` 与多轴 OOD protocol。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization、IP-GLLM、HLM-G、LLM-GCE 和 CausalGraph2LLM 对“accuracy、probe、attention、单一 robustness 或单一 intervention 不能证明 causal topology use”的判断一致；NLGift 没有推翻已有结论，也没有提供 Graph-LLM 的 fixed-text causal evidence。
- 当前仍不能得出的结论：不能证明 node permutation/isomorphism consistency；不能证明关键拓扑反事实对最终输出有方向正确的 causal effect；不能证明 GNN encoder/readout 与 LLM 的责任比例；不能把 real-world implicit graph task 的负迁移单独归因于 topology 表征缺失。

### Idea 与最小实验

- 新增或更新的假设：H10：Graph-LLM 的 OOD 能力应沿 semantic、graph-size、generator/transitivity、cross-task 和 real-world 五个轴分解；同 task/同 size 稳定性不能称为 topology-generalization。状态：待验证。证据来源为 NLGift 的五类 pattern、PGR、graph-size/generator/transitivity 结果、cross-task transfer、synthetic-to-real gap 与 keyword/task shortcut analysis。
- 最小可证伪实验：在 GraphToken、IP-GLLM、HLM-G 或同一 Graph-LLM 上固定节点文本、节点特征、标签语义和输出格式，交叉执行 `10 relabelings × size/generator/transitivity/topology-family × cross-task query × key/irrelevant topology intervention`，并报告 PGR、prediction equality、permutation flip rate、intervention direction accuracy、text-only/GNN-only/readout-only 对照。
- 暂不建议的方向：暂不把 NLGift 的 synthetic-to-real 负迁移直接写成 LLM 不会使用 topology，也不把 graph-size 的 Strong Recovery 失败单独解释为 node-index shortcut；必须补充 explicit graph context、fixed-text matched pairs 和责任分离。

### 下一次研究任务

- 未解决问题：Q7/H9/H10：NLGift 的多轴 OOD protocol 能否迁移到 GraphToken/IP-GLLM/HLM-G，并与 node relabeling、encoding/order、key/irrelevant topology intervention 和 encoder/readout-only 分离同时成立？
- 建议检索词或目标论文：`Graph-LLM topology OOD graph size extrapolation`、`GraphToken cross-task generalization`、`GNN-LLM synthetic to real graph reasoning`、`fixed text topology counterfactual Graph-LLM`；若仍没有直接联合评测，优先构造 fixed-node-text matched pairs，并把 NLGift 的 PGR/Strong Recovery 接入统一 protocol。

### 参考来源

- [ACL Anthology 正式页面](https://aclanthology.org/2024.findings-emnlp.127/)：作者、DOI、EMNLP 2024 Findings 元数据和摘要。
- [ACL 官方 PDF](https://aclanthology.org/2024.findings-emnlp.127.pdf)：全文的 NLGift benchmark、五类 pattern、PGR/Strong Recovery、结果表、shortcut analysis、改进策略、limitations 和 appendix。
- [arXiv:2406.15992](https://arxiv.org/abs/2406.15992)：预印本 ID、版本历史和公开全文入口。
- [NLGift 官方代码与数据](https://github.com/MatthewYZhang/NLGift)：benchmark 数据、训练/评估代码和实验日志入口。
- [CoEvoT arXiv](https://arxiv.org/abs/2607.14114)、[Graph-R1 ACL](https://aclanthology.org/2025.emnlp-main.1220/)、[LIBERTy ACL](https://aclanthology.org/2026.findings-acl.399/)、[GraphTMI ACL](https://aclanthology.org/2024.findings-naacl.34/)：本轮初筛候选及后续评测/方法方向。

## 2026-08-25 第 8 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q7/H9/H10：能否把 fixed-text topology intervention、node permutation、cross-topology OOD 和 encoder/readout/LLM responsibility split 放入同一条 Graph-LLM 证据链；如果不能，如何先把 OOD 轴迁移到实际 molecular Graph-LLM？
- 今日检索目标：沿上一轮 `Graph-LLM topology OOD graph size extrapolation`、`GraphToken cross-task generalization`、`GNN-LLM synthetic to real graph reasoning` 和 `fixed text topology counterfactual Graph-LLM` 队列，筛选能直接补充 Graph-LLM OOD 证据且全文公开的工作。

### 检索与筛选

- 检索范围与关键词：`Graph-LLM topology OOD graph size extrapolation`、`GraphToken cross-task generalization`、`GNN-LLM synthetic to real graph reasoning`、`fixed text topology counterfactual Graph-LLM`；重点查看 arXiv、ACL Anthology、OpenReview 和官方代码仓库。
- 主要来源：[OOD-GraphLLM arXiv 正式页面](https://arxiv.org/abs/2605.30247)、[OOD-GraphLLM 官方 PDF](https://arxiv.org/pdf/2605.30247)、[OOD-GraphLLM 官方代码](https://github.com/EkkoXiao/Bio-GraphLLM)、[CoEvoT arXiv](https://arxiv.org/abs/2607.14114)、[GraphDO ACL PDF](https://aclanthology.org/2025.acl-long.321.pdf)、[LIBERTy ACL 页面](https://aclanthology.org/2026.findings-acl.399/)。
- 初筛候选数：5 个主要论文/方向入口：OOD-GraphLLM、CoEvoT、GraphDO、LIBERTy、GraphTMI/相关 Graph-LLM OOD 方向。
- 去重结果：与 `papers/index.md` 的 arXiv ID、DOI 和规范化标题比较后，`arXiv:2605.30247` 是唯一新增条目；CoEvoT、GraphDO、LIBERTy 和 GraphTMI 未命中已有条目，但保留为后续队列，不重复新增。
- 选择该论文精读的理由：OOD-GraphLLM 是当前队列中直接研究 Graph-LLM scaffold/size OOD 的 hybrid 工作，且公开 arXiv PDF、代码和模型入口；它可把 H10 从纯文本 NLGift 的边界对照推进到实际 Graph-LLM，同时用其多组件 ablation 检验 H11 的责任分离缺口。

### 今日精读

- 论文：OOD-GraphLLM: Graph Large Language Model for Out-of-Distribution Generalized Drug Synergy Prediction
- Paper ID：arXiv:2605.30247
- 阅读级别：全文精读
- 笔记：`notes/2605.30247.md`
- PDF：`papers/2026_Wang_OOD_GraphLLM.pdf`；来自 arXiv 官方 PDF，已核验本地文件头 `%PDF-1.7`、3,660,119 bytes、`%%EOF`；官方 PDF 解析显示 12 页、`application/pdf`。本机未安装 `pdfinfo`/`pdftoppm`，因此未完成本地 PNG 视觉复核；没有把 HTML 或登录页误存为 PDF。

### 核心发现

1. OOD-GraphLLM 将 molecular graph 经 heterogeneous bond-aware GNN、target-adaptive disentanglement、pairwise attentive graph architecture search 和 BERT-based projector 后，与 SMILES、cell-line text/embedding 和 DrugSyn-LLM 结合；LLM 生成 synergy label 与 score，图结构不是直接以普通文本 edge list 进入，而是通过 graph-level continuous representations 与 SMILES 双路径进入。
2. 在 DrugComb 的 Bliss/HSA/Loewe/ZIP 四种 score 上，scaffold-based 与 molecular-size OOD split 都显示完整模型优于 DNN、GNN 和其他 LLM baseline；scaffold split 的 `ACC` 为 `77.66/79.97/96.17/76.56`，size split 的 `ACC` 为 `77.27/80.98/93.55/76.98`。这补充了 Graph-LLM 的 chemical-distribution OOD 证据，但不能等价为 held-out topology-family 泛化。
3. `w/o NAS`、`w/o Ctx`、`w/o R-IT`、`w/o Attn`、`w/o Sep` 和 `w/o Decor` 均有性能下降，且 `w/o Ctx`/`w/o NAS` 最明显；但这些是系统组件 ablation，没有 GNN-only、LLM-only、readout-only、text shuffle、node relabeling、same-text topology intervention 或 key/irrelevant edge control，因此不能证明 LLM 单独因果使用 topology。

### 对研究命题的影响

- 新增证据：H1 维持“部分支持”；H10 获得实际 Graph-LLM 在 scaffold/size OOD 上的补充证据；新增 H11“多组件 Graph-LLM 的 OOD gain 在责任分离前不能归因于 LLM 的 topology causal use”，状态为“待验证”。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization、IP-GLLM、HLM-G、LLM-GCE、CausalGraph2LLM 和 NLGift 对“指标、OOD、attention 或组件消融不等于 LLM 因果结构使用”的判断一致；没有证据冲突。
- 当前仍不能得出的结论：不能证明 node permutation/isomorphism consistency；不能证明固定节点文本/特征下关键 topology intervention 方向正确、无关 topology 稳定；不能证明 GNN、RAG、cell-context、readout 与 LLM 的责任比例，也不能把 scaffold/size shift 直接称为 topology-family generalization。

### Idea 与最小实验

- 新增或更新的假设：H11：Graph-LLM 在 scaffold/size OOD 上的收益可能主要来自 GNN encoder、target-aware readout、retrieved biomedical semantics 或 cell-context alignment；只有完成责任分离和 fixed-text topology intervention 后，才可归因于 LLM 的 topology causal use。状态：待验证。
- 最小可证伪实验：构造 molecular-like matched pairs，固定 atom/node features、cell text、task prompt 和 output format，只改变关键或无关 edges；执行 `10 relabelings × key/irrelevant topology intervention × no-RAG/component ablation × scaffold/size holdout`，比较 full OOD-GraphLLM、GNN-only、LLM-only、readout-only、text/SMILES-only 和 shuffled graph embedding，报告 prediction equality、permutation flip rate、direction accuracy、AUC/MAE 与 PGR。
- 暂不建议的方向：暂不把 scaffold/size split、target/SMILES attention、case study 或 `w/o NAS/w/o Ctx` 的下降写成 LLM 已理解或因果使用 topology；先完成 Q8 的 molecular-like matched-pair 和责任分离协议。

### 下一次研究任务

- 未解决问题：Q8/H10/H11：在 OOD-GraphLLM 或同类 molecular Graph-LLM 上移除 RAG、固定 node features/text，并比较 GNN-only/readout-only 后，scaffold/size OOD 是否仍保留且由 LLM 贡献？
- 建议检索词或目标论文：`OOD-GraphLLM GNN-only ablation`、`molecular Graph-LLM fixed text topology counterfactual`、`Graph-LLM scaffold split responsibility`、`Graph-LLM readout-only topology intervention`；同时复核 [OOD-GraphLLM 官方代码](https://github.com/EkkoXiao/Bio-GraphLLM) 的 split 与组件实现。

### 参考来源

- [arXiv 正式页面](https://arxiv.org/abs/2605.30247)：作者、提交日期、摘要、12 页元数据、代码和模型链接。
- [arXiv 官方 PDF](https://arxiv.org/pdf/2605.30247)：全文 method、dataset/split、Table 2、ablation、hyperparameter、case study、conclusion 和 appendix。
- [官方代码仓库](https://github.com/EkkoXiao/Bio-GraphLLM)：实现与模型资源入口。
- [官方模型/演示页面](https://mn.cs.tsinghua.edu.cn/bio-graphllm/)：论文列出的 release/demo 入口。

## 2026-08-26 第 9 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q8/H10/H11：在 molecular Graph-LLM 中，scaffold/size OOD 增益在固定 atom/node features、移除 RAG 并比较 GNN-only/readout-only 后是否仍由 LLM 贡献？同时推进 Q9：dynamic graph tokens 是否受等价 SMILES 和 node relabeling 影响？
- 今日检索目标：沿上一轮 `EDT-Former`、`OOD-GraphLLM GNN-only ablation`、`molecular Graph-LLM fixed text topology counterfactual`、`Graph-LLM scaffold split responsibility` 和 `Graph-LLM readout-only topology intervention` 队列，寻找同时提供结构接口、OOD 或责任分离证据的新工作。

### 检索与筛选

- 检索范围与关键词：`OOD-GraphLLM GNN-only ablation`、`molecular Graph-LLM fixed text topology counterfactual intervention`、`Graph-LLM scaffold split responsibility`、`Graph-LLM readout-only topology intervention`、`topology-aware graph LLM dynamic tokens`；重点查看 arXiv HTML/PDF、OpenReview、ACL Anthology、Zenodo 和作者公开仓库。
- 主要来源：[EDT-Former arXiv 页面](https://arxiv.org/abs/2602.02742)、[EDT-Former HTML 全文](https://arxiv.org/html/2602.02742)、[EDT-Former 官方 PDF](https://arxiv.org/pdf/2602.02742v3)、[EDT-Former ICLR OpenReview PDF](https://openreview.net/pdf?id=yzwSzhqLpH)、[TopoOp-SLM preprint](https://www.preprints.org/manuscript/202606.1835)、[GSPELL ACL 2026 页面](https://aclanthology.org/2026.acl-long.1944/)、[T2T-LA Zenodo 记录](https://zenodo.org/records/20178090)。
- 初筛候选数：4 个主要新增论文/方向入口：EDT-Former、Beyond Graph Serialization: Topology-Aware Low-Rank Operators for Small Language Models（TopoOp-SLM）、From Nodes to Narratives: Explaining Graph Neural Networks with LLMs and Graph Context（GSPELL/LOGIC）、T2T-LA。
- 去重结果：与 `papers/index.md` 中已有的 arXiv ID、DOI 和规范化标题比较后，`arXiv:2602.02742` 是唯一与当前 molecular Graph-LLM OOD/结构接口主线直接匹配、且能取得官方公开全文并完成全文核验的新增论文；TopoOp-SLM、GSPELL/LOGIC 和 T2T-LA 保留为后续候选，未新增重复行。OpenReview PDF 触发浏览器验证，但 arXiv 官方 HTML/PDF 足以完成核验。
- 选择该论文精读的理由：EDT-Former 同时提供 frozen graph encoder/LLM、dynamic substructure token、Text/Graph/3D modality ladder、dynamic-token removal/randomization、graph encoder 替换、data contamination check、Bemis–Murcko scaffold split 和 macrocycle evaluation，能把上一轮 OOD-GraphLLM 的系统级 OOD 证据推进到更细的接口和 shortcut 审计。

### 今日精读

- 论文：Entropy-Guided Dynamic Tokens for Graph-LLM Alignment in Molecular Understanding
- Paper ID：arXiv:2602.02742（arXiv v3；Published as a conference paper at ICLR 2026）
- 阅读级别：全文精读
- 笔记：`notes/2602.02742.md`
- PDF：`papers/2026_Jing_Entropy_Guided_Dynamic_Tokens_Graph_LLM.pdf`；从官方 arXiv `export.arxiv.org/pdf/2602.02742` 合法下载，已验证文件头 `%PDF-1.7`、`3,887,061 bytes`、`%%EOF`、SHA256 `81639123b28f8ad2fd1b112be70668767d681a61975c67530068093775691d37`；临时安装 `pypdf`/`PyMuPDF` 后验证 44 页、未加密、44/44 页面可解析，并渲染目检第 1、22、44 页，未发现截断、重叠或不可读版面。

### 核心发现

1. EDT-Former 的结构路径为 `Graph/3D → frozen graph encoder → node embeddings X → canonical SMILES NAP surprisal peaks → graph-node pooling → dynamic tokens + modality anchors → Dynamic Query Transformer → projector → frozen LLM`。它不是把 edge list 直接交给 LLM，而是先由 graph encoder 和 connector 完成结构压缩。
2. 结构路径确实是系统功能部分：MoleculeQA 的 Text/Graph/3D 总准确率为 `68.34`，Text-only 为 `58.77`；推理时去掉 dynamic tokens，PAMPA/BBBP 从 `82.34/72.48` 降为 `54.97/51.29`，替换为 random dynamic tokens 仍低于完整模型；去 entropy patching 的 MoleculeQA 平均下降约 `11.5%`。这比单纯 attention 或总 accuracy 提供了更强的组件必要性证据，但仍是系统级 ablation。
3. 论文给出比 OOD-GraphLLM 更完整的 chemical OOD/shortcut controls：MoleculeQA Bemis–Murcko scaffold split 的总 accuracy 为 `68.65`，official split 为 `68.34`；clean-set 去除与训练语料重复分子后平均 accuracy 反而 `+0.59%`；entropy patch 与 BRICS/RECAP 的 mean NMI 约 `0.484/0.401`，不同 NAP 规模间 NMI `0.86–0.94`。但没有 node permutation/isomorphism、equivalent-SMILES、edge-order、fixed-text topology counterfactual、text shuffle、GNN-only/LLM-only/readout-only 或 key/irrelevant topology intervention。

### 对研究命题的影响

- 新增证据：H1 维持“部分支持”；EDT-Former 证明 dynamic graph–LLM interface 能带来任务增益和较弱的 chemical OOD/contamination robustness，但不能证明 LLM 单独因果使用 topology。H10 获得 scaffold split 和 macrocycle 证据，但仍属于 chemical-distribution OOD。H11 获得双向证据：dynamic-token removal 和 Text-only gap 说明 interface 重要，而 stronger graph encoder 的 minor gain 削弱“全部收益来自 encoder”的简单解释；责任仍未分离。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization、IP-GLLM、HLM-G、LLM-GCE、CausalGraph2LLM、NLGift 和 OOD-GraphLLM 对“accuracy、attention、单一 ablation 或 chemical OOD 不等于 LLM causal topology use”的判断一致，没有冲突证据。
- 当前仍不能得出的结论：不能证明 canonical SMILES 以外的等价序列化会得到相同 patch/token 表征；不能证明关键 topology intervention 会按任务语义改变输出、无关 topology 会保持稳定；不能证明 LLM 而不是 graph encoder/connector 完成主要图计算，也不能把 scaffold split 称为 held-out topology-family generalization。

### Idea 与最小实验

- 新增或更新的假设：H12：基于 SMILES surprisal 的 dynamic token 能保留局部化学结构，但其 patch/token 路径可能依赖序列遍历而不是 graph invariant；状态为“待验证”。证据来自 entropy patch 的 sequence-based 定义、BRICS/RECAP NMI、dynamic-token ablation 和缺失的 equivalent-SMILES/node-permutation control。
- 最小可证伪实验：对同一 molecular graph 固定 atom features、node text、cell/task prompt 和输出格式，生成 `10 relabelings × 3+ valid SMILES`；比较 patch boundary NMI、token count、connector representation、LLM logits/prediction equality。再构造固定文本的关键/无关 edge matched pairs，报告 key-intervention direction accuracy 与 irrelevant-intervention stability。
- 对照组：canonical SMILES、random valid SMILES、BRICS patch、random patch、no dynamic token、random dynamic token、text-only、graph encoder/readout-only、GNN-only 和 full EDT-Former；同时对 OOD-GraphLLM 加入 no-RAG、GNN-only、LLM-only 和 readout-only。
- 支持条件：等价 SMILES/节点置换下 patch 与预测稳定；关键 topology 干预方向正确、无关干预稳定；full 模型在 direction accuracy 上显著超过 text-only/readout-only/GNN-only；scaffold/size PGR 在责任分离后仍保留。
- 失败条件：等价 SMILES 造成明显 token/prediction flip；关键干预方向错误或无响应；GNN-only/readout-only 复现 full direction accuracy；random/BRICS patch 与 entropy patch 相当；或 scaffold/size OOD 在移除 graph/text shortcut 后消失。
- 暂不建议的方向：暂不把 scaffold split、attention visualization、NMI、hallucination rate 或 dynamic-token removal 写成 LLM 已理解并因果使用 topology；先完成 Q8/Q9 的统一 protocol。

### 下一次研究任务

- 未解决问题：Q8/Q9/H10/H11/H12：EDT-Former 和 OOD-GraphLLM 的 chemical OOD 增益，在 equivalent-SMILES/node permutation、fixed-text key/irrelevant topology intervention、no-RAG 和 GNN/LLM/readout responsibility split 后是否仍存在？
- 建议检索词或目标论文：`EDT-Former code permutation SMILES`、`molecular Graph-LLM equivalent SMILES robustness`、`Graph-LLM fixed atom features topology intervention`、`OOD-GraphLLM readout-only ablation`；同时复核 EDT-Former anonymous code 与 OOD-GraphLLM 官方代码的 split、canonicalization 和组件实现。

### 参考来源

- [EDT-Former arXiv 正式页面](https://arxiv.org/abs/2602.02742)：作者、版本、发表状态和摘要。
- [EDT-Former HTML 全文](https://arxiv.org/html/2602.02742)：方法、主实验、scaffold split、data contamination、extended ablation、limitations 和 appendix。
- [EDT-Former 官方 PDF](https://arxiv.org/pdf/2602.02742v3)：44 页公开全文；本地文件已完成 header、EOF、SHA256、解析、页数、未加密和渲染验证。
- [EDT-Former ICLR OpenReview PDF](https://openreview.net/pdf?id=yzwSzhqLpH)：ICLR 2026 conference-paper 入口；本轮页面触发验证，未绕过访问控制。
- [TopoOp-SLM preprint](https://www.preprints.org/manuscript/202606.1835)、[GSPELL ACL 2026](https://aclanthology.org/2026.acl-long.1944/)、[T2T-LA Zenodo](https://zenodo.org/records/20178090)：初筛候选及后续结构接口/责任分离方向。

## 2026-08-27 第 10 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q8/Q9/H10/H11/H12：molecular Graph-LLM 的 chemical OOD 与 dynamic-token 增益，在 equivalent-SMILES、node relabeling、fixed-text topology intervention、no-RAG 和 GNN/LLM/readout responsibility split 后是否仍成立？
- 今日检索目标：沿 EDT-Former 的 canonical-SMILES patch shortcut 队列，寻找能直接测量 identity-preserving molecular representations、hidden representation consistency 和 downstream representation OOD 的工作，并判断 AMORE 是否能迁移到 Graph-LLM 接口。

### 检索与筛选

- 检索范围与关键词：`molecular Graph-LLM equivalent SMILES robustness`、`EDT-Former code permutation SMILES`、`Graph-LLM fixed text topology intervention counterfactual`、`Graph-LLM readout-only ablation topology causal responsibility`；重点查看 Springer/PMC、arXiv、ACL Anthology、OpenReview 和作者公开代码仓库。
- 主要来源：[AMORE Springer 正式页面](https://link.springer.com/article/10.1186/s13321-025-01079-0)、[AMORE PMC 全文](https://pmc.ncbi.nlm.nih.gov/articles/PMC12574305/)、[AMORE 作者代码与数据](https://github.com/ChemistryLLMs/AMORE)、[MolJSON arXiv 页面](https://arxiv.org/abs/2605.01822)、[Inconsistency of LLMs in molecular representations](https://pubs.rsc.org/en/content/articlehtml/2025/dd/d5dd00176e)。
- 初筛候选数：5 个主要论文/方向入口：AMORE、MolJSON、EDT-Former code/model audit、Inconsistency of LLMs in molecular representations、Graph2SMILES permutation-invariant graph-to-sequence encoder。
- 去重结果：与 `papers/index.md` 的 arXiv ID、DOI 和规范化标题比较后，AMORE 的期刊 DOI `10.1186/s13321-025-01079-0` 为唯一新增精读条目；其 EMNLP 2024 conference predecessor “Lost in Translation” 未在索引中单列，本轮按 version-of-record DOI 建立一条记录，并在论文笔记中保留 lineage 说明；其余候选保留后续队列，不新增重复。
- 选择该论文精读的理由：AMORE 直接把 canonical、explicit-hydrogen、Kekul、cycle renumbering 和 random atom-order 作为保持分子身份不变的变换，在 hidden embedding 上做 Acc@1/Acc@5/MRR retrieval，并连接 layer-wise 与 MoleculeNet augmented-test；它比上一轮只有 canonical SMILES 的 EDT-Former 证据更直接地推进 Q9/H12，同时明确暴露纯 sequence ChemLM 与 Graph-LLM causal-use 之间的证据边界。

### 今日精读

- 论文：Measuring Chemical LLM robustness to molecular representations: a SMILES variation-based framework
- Paper ID：doi:10.1186/s13321-025-01079-0
- 阅读级别：全文精读
- 笔记：`notes/10.1186_s13321-025-01079-0.md`
- PDF：公开 [Springer PDF URL](https://link.springer.com/content/pdf/10.1186/s13321-025-01079-0.pdf)；本轮 Springer 返回 browser/authentication challenge，PMC PDF 端点返回 “Preparing to download” challenge。已核验 Springer/PMC HTML 全文并完成方法、表格、附录结果、讨论、结论和 limitations 阅读；没有把 HTML challenge 页面保存为 PDF，也没有绕过访问控制，因此本地无 PDF 文件。

### 核心发现

1. AMORE 的核心是 `identity-preserving SMILES transformation → Transformer hidden mean pooling → FAISS nearest-neighbor retrieval`；五类变换都保持底层分子不变，但模型对不同变换的鲁棒性差异很大。ChEBI-20 上 Text+Chem T5-standard 的 Acc@1 为 Canon/Hydro/Kekul/Cycle=`63.03/5.46/76.76/96.70%`，Random=`46.94%`；在 C9H12N2O isomers 上 explicit-hydrogen 的 Acc@1 几乎为 0。
2. 该现象同时出现在最终任务和中间层：MoleculeNet augmented test 的性能通常下降，例如 ESOL RMSE 从 `0.87` 增至 `7.93`，PubChemDeBERTa 的一个 binary accuracy 从 `0.80` 降至 `0.38`；layer-wise retrieval 在不同 ChemLM 上有局部稳定/失稳层，但没有一个 intermediate layer 对所有 augmentation 都稳定。AMORE Acc@1 与 captioning 指标差分的 Spearman 相关被报告为大于 `0.7`，`p=0.003`。
3. AMORE 把序列表示的 shortcut 具体化为 atom traversal、显式氢 token、芳香性写法、ring identifier、canonical training distribution 和 token/长度 shift，但它没有 key/irrelevant topology intervention、final-output causal mediation、Graph-LLM connector 或 GNN/LLM/readout responsibility split。因此它支持“equivalent-representation robustness 是必要审计轴”，不支持“ChemLM/Graph-LLM 已经因果使用 topology”或“失败完全来自 LLM”。

### 对研究命题的影响

- 新增证据：H2 维持“部分支持”，从一般 node relabeling/serialization sensitivity 扩展到 molecular identity-preserving SMILES variations；H5/H9 维持“待验证”，但获得了 embedding-level representation consistency 与 downstream prediction shift 的直接分离证据；H12 维持“待验证”并强化 canonical-SMILES dynamic-token 路径存在 traversal shortcut 的风险。H1 维持“部分支持”，H10 维持“待验证”。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization、CausalGraph2LLM、NLGift、OOD-GraphLLM 和 EDT-Former 对“accuracy、probe、attention、单一 ablation 或 OOD gain 不等于结构因果使用”的判断一致；AMORE 的新增价值是提供了可迁移的 equivalent-SMILES hidden-retrieval protocol，没有证据冲突。
- 当前仍不能得出的结论：不能证明 EDT-Former 的 dynamic tokens 在合法等价 SMILES 或 node relabeling 下不一致；不能证明 Graph-LLM 对关键 topology 干预方向正确；不能区分 graph encoder、patch/connector、readout 和 LLM 的贡献；不能把 augmented MoleculeNet 视为 topology-family OOD。

### Idea 与最小实验

- 新增或更新的假设：强化 H12：dynamic-token Graph-LLM 必须先通过 identity-preserving representation consistency，才能把 patch/token 路径解释为图结构接口；即使通过，该条件仍不充分，必须再通过 fixed-text key/irrelevant topology intervention 和责任分离。H2/H5/H9 的状态不变。
- 最小可证伪实验：对同一 molecular graph 固定 atom features、node text、cell/task prompt 和 output format，生成 `10 relabelings × 3+ valid SMILES`，并加入 canonical、random atom order、Kekul、cycle 和 explicit-hydrogen variants；在 graph encoder、patch boundary、connector hidden state、LLM hidden state 和 final output 五层计算 `Acc@1/MRR`、patch NMI、token count、prediction equality 与 invariant variance。再构造固定文本的 key-edge/irrelevant-edge matched pairs，报告 direction accuracy。
- 对照组：canonical/random valid SMILES、AMORE augmentations、BRICS/RECAP/random patch、no dynamic token、random dynamic token、text-only、graph encoder/readout-only、GNN-only、LLM-only、full EDT-Former/OOD-GraphLLM，并加入 no-RAG 与 shuffled graph embedding。
- 支持条件：equivalent SMILES 与 node relabeling 下 patch、connector 表示和输出稳定；key topology 干预方向正确、irrelevant topology 稳定；full model 的 direction accuracy 超过 text-only/readout-only/GNN-only；scaffold/size PGR 在责任分离后仍保留。
- 失败条件：合法等价 SMILES 导致明显 token/prediction flip；key intervention 无响应或方向错误；GNN-only/readout-only 复现 full direction accuracy；random/BRICS patch 与 entropy patch 相当；或 OOD gain 在去除 graph/text/RAG shortcut 后消失。
- 暂不建议的方向：暂不把 AMORE 的 Acc@1、hidden-layer retrieval、MoleculeNet augmented-test 下降或 captioning correlation 写成 LLM causal topology use，也不把 explicit-hydrogen 的极端下降单独解释为拓扑理解缺失；先做 token/length/pretraining-distribution matched controls。

### 下一次研究任务

- 未解决问题：Q8/Q9/H10/H11/H12：AMORE 的 identity-preserving transformations 在 EDT-Former/OOD-GraphLLM 上是否会改变 dynamic patch、connector representation 和最终预测；若会，变化由 SMILES traversal、graph encoder、connector 还是 LLM 产生？
- 建议检索词或目标论文：`EDT-Former AMORE equivalent SMILES`、`molecular Graph-LLM SMILES augmentation hidden representation`、`Graph-LLM identity transformation responsibility split`、`fixed atom features topology intervention`；优先复核 EDT-Former/OOD-GraphLLM 公开代码的 canonicalization、split 和 component ablation，再运行上述统一 protocol。

### 参考来源

- [Springer Nature 正式页面](https://link.springer.com/article/10.1186/s13321-025-01079-0)：2025 期刊版本的元数据、全文方法、实验、讨论和 limitations。
- [PMC 全文](https://pmc.ncbi.nlm.nih.gov/articles/PMC12574305/)：可访问的完整 HTML、AMORE 定义、augmentation、表格、layer-wise 与 MoleculeNet 结果。
- [AMORE 作者代码与数据](https://github.com/ChemistryLLMs/AMORE)：RDKit augmentation、ChEBI-20/QM9 数据、FAISS hidden-state evaluation 和复现实验入口。
- [PubMed 记录](https://pubmed.ncbi.nlm.nih.gov/41168806/)：作者、DOI、期刊、发表日期与 PMCID 元数据。
- [Springer PDF URL](https://link.springer.com/content/pdf/10.1186/s13321-025-01079-0.pdf)：公开 PDF 入口；本轮返回下载 challenge，未形成合法本地归档。

## 2026-08-29 第 11 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q2/Q5/Q6 与 Q8/Q9：如何把 node permutation、encoding/order、fixed-text topology intervention 和 GNN/LLM/readout responsibility split 放入同一条 Graph-LLM 证据链；同时收束此前已下载但未归档的 graph-isomorphism 论文。
- 今日检索目标：沿 `Graph-LLM graph isomorphism permutation invariance`、`GTokenLLM prediction equality node relabeling`、`fixed text topology intervention Graph-LLM` 和 `EDT-Former equivalent SMILES code` 检索约 5 个候选，优先补齐直接测试同拓扑随机重标记的中断论文。

### 检索与筛选

- 检索范围与关键词：`Graph-LLM graph isomorphism permutation invariance`、`GTokenLLM prediction equality node relabeling`、`fixed text topology intervention Graph-LLM`、`EDT-Former equivalent SMILES code`、`Graph-LLM topology OOD graph size extrapolation`；重点查看 arXiv、ACL Anthology 和正式论文 HTML/PDF 页面。
- 主要来源：[arXiv:2606.09484](https://arxiv.org/abs/2606.09484)、[论文 HTML](https://arxiv.org/html/2606.09484)、[GraphDO ACL 页面](https://aclanthology.org/2025.acl-long.321/)、[CoEvoT arXiv 页面](https://arxiv.org/abs/2607.14114)、[GraphInsight ACL 页面](https://aclanthology.org/2025.acl-long.591/)、[GDL4LLM ACL 页面](https://aclanthology.org/2025.findings-acl.902/)。
- 初筛候选数：5 个主要候选论文/方向入口：arXiv:2606.09484、GraphDO、CoEvoT、GraphInsight、GDL4LLM。
- 去重结果：`arXiv:2606.09484` 尚未出现在 `papers/index.md`，但仓库中已有一份此前中断下载的同论文 PDF；与索引中的 arXiv ID、DOI 和规范化标题比较后，本轮只新增 1 条索引记录并复用该 PDF，没有重复下载或重复建档。GraphDO、CoEvoT、GraphInsight 和 GDL4LLM 均保留为候选，未新增重复条目。
- 选择该论文精读的理由：它直接把 ordinary graph-isomorphism detection 与同拓扑随机 node relabeling 分成两个实验；普通检测接近满分而重标记任务接近随机，是当前队列中最直接的 permutation shortcut 证据，也正好完成历史中断的四项状态更新。它虽不是 GNN-LLM，但能为 Graph-LLM protocol 提供清晰的纯序列化边界对照。

### 今日精读

- 论文：Detecting Differences Is Not Understanding Structure: Large Language Models Fail at Graph Isomorphism
- Paper ID：arXiv:2606.09484
- 阅读级别：全文精读
- 笔记：`notes/2606.09484.md`
- PDF：`papers/2026_Thushalika_Detecting_Differences_Graph_Isomorphism.pdf`；复用仓库中已存在的官方 arXiv PDF，核验 `%PDF-1.7`、176057 bytes、SHA256 `C4CB8BB1A8D8D34311EFE554152BA9BE911D5D718F1112B9EBA52E676644F253`、`%%EOF`、6 页、未加密；使用 bundled Poppler 渲染并目检首页、主结果页、置换结果页和末页。全文通过官方 arXiv HTML 阅读 abstract、introduction、related work、experimental setup、两组实验、limitations/conclusion 和 references。

### 核心发现

1. 论文在 400 个非同构图对上测试 GPT-4o-mini、Gemini 2.5 Flash 和 Llama 3.3 70B Instruct，输入为 edge list、edge index 或 adjacency matrix；三种模型普通 isomorphism detection 总体接近满分，连 1-WL indistinguishable 的类别也保持很高准确率。
2. 在另一个 400 个随机连通图及其随机节点重标记同构副本的实验中，OpenAI 的准确率为 0-18%，Gemini 为 0-0.2%，Llama 为 2-39.2%；同一模型在三种 serialization 之间也明显波动，且 instructed prompt 不能稳定修复问题。模型常把同拓扑重标记造成的序列差异判断为 non-isomorphic。
3. 该结果直接说明 ordinary task accuracy 与 permutation-invariance robustness 可以脱钩，但没有 hidden-state probe、fixed-text key/irrelevant topology intervention、GNN/LLM/readout responsibility split 或 held-out topology-family OOD；因此它是强 shortcut/robustness 诊断，不是 Graph-LLM 的 causal topology-use 证明。

### 对研究命题的影响

- 新增证据：H2 维持“部分支持”并得到更直接的纯序列化证据；H5 从“待验证”调整为“部分支持”，因为同一评测中 ordinary isomorphism accuracy 近满分而 permutation-invariance 只有 0-39.2%，支持“任务高分与不变量稳定性是可分离轴”。H1 获得边界补充，但仍不外推为 GNN-LLM 结论。
- 与历史结论一致或冲突之处：与 GTEval、Lost in Serialization、CausalGraph2LLM、AMORE、EDT-Former 和 OOD-GraphLLM 对“accuracy、attention、单一 ablation 或 chemical/OOD gain 不等于结构因果使用”的判断一致；它进一步把 node relabeling 作为可复现的直接 failure test，没有冲突证据。
- 当前仍不能得出的结论：不能证明所有 LLM 都不具备结构推理，不能定位失败究竟来自 tokenization、节点编号、attention、推理策略还是输出解析；更不能证明 GraphToken、IP-GLLM、HLM-G、EDT-Former 或 OOD-GraphLLM 的 LLM 本身未使用 topology。

### Idea 与最小实验

- 新增或更新的假设：更新 H5 为“部分支持”：对 Graph-LLM，ordinary graph-task accuracy 与同拓扑 node relabeling 的 prediction equality 必须分别报告；即使原始编号上高分，若 relabeling flip rate 高，则不能称为稳定图结构表征。H5 的 causal-use 部分仍待验证。
- 最小可证伪实验：在 GraphToken、IP-GLLM、HLM-G、EDT-Former 和 OOD-GraphLLM 上固定节点文本/特征、任务语义与输出格式，执行 `10 relabelings × encoding/order variants`，记录逐样本 prediction equality、output KL、hidden-state distance 和最终答案 flip rate；随后在同一批样本中只改变关键边或无关边，测 direction accuracy 与 irrelevant-intervention stability。
- 对照组：base Graph-LLM、GNN-only、LLM-only、encoder/readout-only、text-only、shuffled graph embedding、random-label/text shuffle，以及 canonical/random serialization；molecular 分支额外加入 3+ valid SMILES、no-RAG 和 dynamic-token/connector ablation。
- 支持条件：等价重标记和序列化下 prediction equality 高；关键拓扑干预方向正确、无关干预稳定；完整模型在 direction accuracy 上超过 readout-only/text-only，并在 held-out topology family 保持。
- 失败条件：普通任务高分但 relabeling flip rate 接近随机；关键干预无响应或方向错误；readout-only/GNN-only 复现完整模型；或变化主要由 tokenization、SMILES traversal、文本模板和 RAG 解释。
- 暂不建议的方向：不把该论文的 permutation failure 直接写成 Graph-LLM failure，也不把通过单一 permutation test 写成 causal topology use；必须与 fixed-text intervention、责任分离和 topology-OOD 同时报告。

### 下一次研究任务

- 未解决问题：Q2/Q5/Q6/Q8/Q9/H5：Graph-LLM 的 graph encoder 是否能消除纯序列化模型的 permutation shortcut？若能，connector、LLM hidden state 和最终输出是否仍保持等价；若不能，失败发生在哪一层？
- 建议检索词或目标论文：`Graph-LLM graph isomorphism permutation invariance`、`GTokenLLM prediction equality node relabeling`、`Graph-LLM fixed text topology intervention`、`EDT-Former equivalent SMILES code`、`OOD-GraphLLM readout-only ablation`；优先把 2606.09484 的 protocol 与 AMORE 的 `Acc@1/MRR`、EDT-Former 的 patch NMI 和 OOD-GraphLLM 的 no-RAG/component split 组合。

### 参考来源

- [arXiv abstract page](https://arxiv.org/abs/2606.09484)：论文元数据、作者、版本和摘要。
- [arXiv HTML 全文](https://arxiv.org/html/2606.09484)：三种模型、两种 prompt、三种 serialization、400+400 图对、表 1-3、观察、解释与 limitations。
- [arXiv PDF](https://arxiv.org/pdf/2606.09484)：公开 PDF 下载入口；本地文件完成 header、size、SHA256、EOF、page count、encryption 和渲染检查。
- [GraphDO ACL 2025](https://aclanthology.org/2025.acl-long.321/)、[CoEvoT arXiv](https://arxiv.org/abs/2607.14114)、[GraphInsight ACL 2025](https://aclanthology.org/2025.acl-long.591/)、[GDL4LLM ACL 2025](https://aclanthology.org/2025.findings-acl.902/)：今日初筛候选及未选原因的正式来源。

## 2026-08-29 第 12 次自动调研

### 今日承接的问题

- 来源于 `ideas/research_map.md` 的问题：Q2/Q5/Q6/Q10 与 H2/H3/H5：Graph-LLM 的 graph encoder 是否能消除纯序列化模型的 permutation shortcut？一个 reversible/deterministic discrete graph token 接口能否同时保持等价序列化稳定、结构可解码和最终输出稳定？
- 今日检索目标：沿 `Graph-LLM graph isomorphism permutation invariance`、`GTokenLLM prediction equality node relabeling`、`fixed text topology intervention Graph-LLM`、`EDT-Former equivalent SMILES code` 队列，寻找能提供结构 token、deterministic serialization 或直接 permutation evidence 的新论文，并优先选择公开全文。

### 检索与筛选

- 检索范围与关键词：`Graph-LLM permutation invariance node relabeling`、`Graph Tokenization for Bridging Graphs and Transformers`、`molecular Graph-LLM equivalent SMILES robustness`、`fixed text topology intervention Graph-LLM`、`Graph-LLM topology OOD graph size extrapolation`；重点查看 arXiv HTML/PDF、ACL Anthology、OpenReview 和官方代码仓库。
- 主要来源：[Graph Tokenization arXiv 页面](https://arxiv.org/abs/2603.11099)、[Graph Tokenization HTML 全文](https://arxiv.org/html/2603.11099)、[GraphTokenizer 官方代码](https://github.com/BUPT-GAMMA/Graph-Tokenization-for-Bridging-Graphs-and-Transformers)、[MolBasic arXiv 页面](https://arxiv.org/abs/2607.03007)、[TANS ACL 页面](https://aclanthology.org/2025.naacl-long.65/)、[CoEvoT arXiv 页面](https://arxiv.org/abs/2607.14114)、[GraphDO ACL 页面](https://aclanthology.org/2025.acl-long.321/)。
- 初筛候选数：5 个主要论文/方向入口：Graph Tokenization、MolBasic、TANS、CoEvoT、GraphDO；另参考 GSPELL、GraphInsight 和 GDL4LLM 的正式页面确认相邻方向。
- 去重结果：与 `papers/index.md` 的 arXiv ID、DOI 和规范化标题比较后，`arXiv:2603.11099` 是唯一新增精读条目；MolBasic、TANS、CoEvoT 和 GraphDO 均未命中现有索引，但分别属于 molecular structure comprehension、topology-to-text、co-evolving Graph-LLM prompting 和 serialization-order 方向，保留为后续候选，不重复新增。
- 选择该论文精读的理由：GraphTokenizer 直接形式化 `reversibility` 与 `determinism`，用 Frequency-Guided Eulerian/CPP 加 BPE 构造离散 structural token，并公开 14 个 graph-level benchmark、GraphGPT/LLAGA 纯结构适配对照和官方代码。它为 H3 提供了可操作的结构瓶颈方案，也能把“接口级 invariance 设计”与“最终 causal topology use”严格分开。

### 今日精读

- 论文：Graph Tokenization for Bridging Graphs and Transformers
- Paper ID：arXiv:2603.11099
- 阅读级别：全文精读
- 笔记：`notes/2603.11099.md`
- PDF：`papers/2026_Guo_Graph_Tokenization_Bridging_Graphs_Transformers.pdf`；通过官方 arXiv `export.arxiv.org/pdf/2603.11099` 合法下载，已验证 `%PDF-1.7`、2,103,854 bytes、SHA256 `B280A1EDD68F2C923BD533D77A31D417E872FEF764F87B48904D51C685B10D4B`、`%%EOF`、30 页、未加密、30/30 页可解析；Poppler `pdfinfo` 通过，并渲染目检第 1、7、10、17、25、30 页。

### 核心发现

1. GraphTokenizer 将 labeled graph 转成 edge-covering 的 node-edge-node sequence，再以训练集 labeled-edge pattern frequency 指导 Feuler/FCPP 的 traversal，最后用 BPE 合并相邻 symbol。该接口可通过逆 BPE 与逆 serialization 恢复 topology up to isomorphism，结构处理在 Transformer 之前完成。
2. 论文把 determinism 作为解决 graph ordering ambiguity 的关键属性，表 1 将 Feuler/FCPP 标为 reversible、deterministic，并称方法“almost invariant to graph permutation”；但没有对同一 graph 做 node relabeling、起点/方向、edge order、重复 labels 的逐样本 token/prediction equality 测试，因此这仍是形式化设计主张而非 empirical permutation 证据。
3. 在 14 个跨 molecular、biomedical、social、academic 和 synthetic 的 graph-level benchmark 上，GT+GTE 在主表中取得 MOLHIV AUC `87.4`、COIL-DEL ACC `89.6`、Peptide-func AP `73.1`、ZINC MAE `0.131`；ZINC 上 BPE 将序列压缩到约原始长度的 10%。在 textualized pure-structure COIL-DEL 对照中，GraphGPT/LLAGA 为 `5.6/12.5` ACC，而 GraphTokenizer 为 `89.6`，说明显式离散结构接口有实用价值，但仍不能证明 LLM causal topology use。
4. 关键消融显示 serialization method 与 BPE 都影响结果：含 BPE 的 MOLHIV AUC 中 BFS/DFS/TOPO/Eulerian/Feuler/CPP/FCPP 为 `72.3/76.0/73.2/84.5/87.4/86.9/86.4`；ZINC 上 `K=2000` 达到 `10.84x` compression ratio 和 `0.131` MAE。BPE vocabulary 中 4--6 node token 占 `41.5%`，但这只是 token 结构语义的间接证据。

### 对研究命题的影响

- 新增证据：H1 维持“部分支持”；GraphTokenizer 的 high score 来自显式 reversible serialization、BPE 和标准 Transformer，进一步说明结构输入/系统增益不能直接归因于 LLM causal use。H2 获得针对 ordering ambiguity 的正面接口设计证据；H5 维持“部分支持”，determinism/reversibility 与 final-output causal intervention 被清楚分开。H3 从“构想”调整为“待验证”。
- 与历史结论一致或冲突之处：与 Lost in Serialization、CausalGraph2LLM、AMORE、EDT-Former、OOD-GraphLLM 和 2606.09484 一致，即 encoding/order robustness、representation decode、OOD gain、attention 或 ablation 不能单独推出 causal topology use；未发现冲突证据。
- 当前仍不能得出的结论：不能证明 arbitrary isomorphic relabeling 下最终 Graph-LLM 输出一致；不能证明 key/irrelevant topology intervention 的方向正确/无关稳定；不能区分 serialization、BPE、Transformer readout 和 LLM 的责任；不能把多域 standard split 称为 topology-family OOD。

### Idea 与最小实验

- 新增或更新的假设：H3：显式、可逆、可干预的 discrete structural bottleneck 可能比单一 continuous projector 更适合建立可验证的 Graph-LLM topology evidence；状态：待验证。证据来源为 GraphTokenizer 的 reversible/deterministic interface、BPE structural vocabulary、decode path、compression 和 pure-structure benchmark 对照。
- 最小可证伪实验：在相同 Transformer 和 matched labeled graphs 上比较 Feuler+BPE、raw edge-list/BFS/DFS、continuous graph projector 和 GNN encoder；执行 `10 relabelings × 4 edge orders × 3 starts/directions`，记录 serialized/BPE token equality、decode isomorphism、token count、hidden distance、prediction equality、output KL，再加入 fixed-text key/irrelevant topology intervention 和 held-out topology-family。
- 对照组：GraphTokenizer+BERT/GTE、raw serialization、GNN-only、readout-only、text-only、random-label/text shuffle、shuffled graph embedding，以及 GraphToken/IP-GLLM/HLM-G/EDT-Former 的 continuous connector；molecular branch 额外加入 AMORE 的 `3+ valid SMILES`、no-RAG 和 dynamic-token ablation。
- 支持条件：decode fidelity 为 100%；等价变换下 token、hidden state 和 output 稳定；关键 edge 干预方向正确、无关 edge 稳定；discrete bottleneck 在 held-out topology-family/PGR 上超过 readout-only/text-only，且不随 serialization order 大幅波动。
- 失败条件：重复 label 的 tie-break 依赖原 node index；relabeling 造成 token/prediction flip；BPE 无法稳定 decode；GNN-only/readout-only 复现 full direction accuracy；或离散接口只改善 standard split、在 size/density/topology-family holdout 上失效。
- 暂不建议的方向：不把“deterministic”标签、词表可视化、COIL-DEL 高分或 MNIST graph generation proof-of-concept 写成已证明的 LLM causal topology use；先完成 empirical isomorphism、fixed-text intervention 和 responsibility split。

### 下一次研究任务

- 未解决问题：Q10/H2/H3/H5：GraphTokenizer 的 frequency-guided traversal 在重复 labels、起点/方向、edge order 和 disconnected components 下是否真正 invariant；BPE 是否重新引入 serialization-order shortcut；该离散接口接入 LLM 后是否仍保持 hidden/output 等价？
- 建议检索词或目标论文：`GraphTokenizer Feuler permutation code audit`、`Graph Tokenization BPE isomorphism consistency`、`reversible graph serialization repeated labels`、`Graph-LLM discrete token causal intervention`；优先运行官方代码的 10-way relabeling/decode audit，再将结果迁移到 GraphToken/IP-GLLM/HLM-G。

### 参考来源

- [arXiv 正式页面](https://arxiv.org/abs/2603.11099)：作者、Paper ID、arXiv v1、ICLR 2026 poster 状态和公开 PDF 入口。
- [arXiv HTML 全文](https://arxiv.org/html/2603.11099)：形式化定义、Feuler/FCPP、BPE、实验表格、消融、局限和附录。
- [GraphTokenizer 官方代码](https://github.com/BUPT-GAMMA/Graph-Tokenization-for-Bridging-Graphs-and-Transformers)：实现、配置、release/dev 分支和复现入口。
- [MolBasic arXiv](https://arxiv.org/abs/2607.03007)、[TANS ACL](https://aclanthology.org/2025.naacl-long.65/)、[CoEvoT arXiv](https://arxiv.org/abs/2607.14114)、[GraphDO ACL](https://aclanthology.org/2025.acl-long.321/)：本轮初筛候选及后续结构接口/顺序鲁棒性方向。

## 2026-08-30 第 13 次自动调研

### 研究问题与模式

- 连续主问题：如何让 Graph-LLM 的图结构表示同时满足 structural decodability、permutation consistency、causal topology use 和 cross-topology generalization，而不是把标准任务分数、attention 或 representation probe 当作结构因果证据。
- 今日聚焦：Q10/H2/H3/H5，并新增 Q11：在 reversible discrete interface 中，task-conditioned complementary views 能否在有限深度和 token budget 下同时改善 local/global topology 任务，并保持等价序列化稳定与可定位因果干预？
- 今日模式：强化已有 I1“置换一致且可定位干预的离散图 Tokenizer”，不新增 Idea。

### 检索、去重与选文

- 检索词：`graph tokenization permutation invariant reversible serialization`、`permutation invariant graph serialization`、`Graph-LLM node relabeling`、`graph tokenization finite depth spectral random walk`、`graph tokenizer causal intervention`。
- 初筛方向：Lost in Tokenization、GQT、TokenGT、SwapGT、Discovering Mechanisms in Tokenized Graph Transformers；另复核 GraphTokenizer、Lost in Serialization、2606.09484 与 CausalGraph2LLM 的已归档结论。
- 论文去重：今日唯一新增归档论文为 arXiv:2605.22471；GraphTokenizer 已在索引中，GQT/TokenGT/SwapGT 作为相邻工作用于 Novelty Gate，不重复写入论文归档。
- Idea 去重：不新增 I2；今日结果用于强化 I1 的 view choice、addressability、fixed-text intervention 和 responsibility split 约束。
- 选文理由：该论文直接给出不同 graph tokenization 在 finite-depth、information loss 和 optimization stability 上的理论边界与受控比较，能检验 I1 是否应从单一序列化扩展为 complementary-view 对照。

### 精读论文与验证

- 论文：[Lost in Tokenization: Fundamental Trade-offs in Graph Tokenization for Transformers](https://arxiv.org/abs/2605.22471)，Maya Bechler-Speicher 等，arXiv:2605.22471，2026。
- 阅读级别：全文精读，包括主文、理论结果、实验设置、结论与附录 A.1--A.7；本轮只精读这一篇论文。
- PDF：`papers/2026_Bechler-Speicher_Lost_in_Tokenization.pdf`；笔记：`notes/2605.22471.md`。
- PDF 验证：官方公开 export.arxiv.org PDF，HTTP 200，`Content-Type: application/pdf`，文件 851833 bytes；PDF 1.7、22 页、未加密；SHA-256 为 `4AFE3DA25F13EBE0E96A4110A7217EE4FAA13FFFBB335C1710610FC78A431113`；pypdf 读取 22/22 页文本，Poppler 渲染并目检第 1、8、11、22 页。

### 关键发现与证据边界

- 论文将 adjacency、spectral 和 random-walk 视为互补输入：full spectral tokenization 可无损但可能 ill-conditioned；random-walk return probabilities 在任意长度仍有 lossy 极限；adjacency 可无损但依赖 dataset node order，未自然满足 permutation equivariance。证据类型：形式化定义与理论分析。
- Theorem 1--4 给出有限深度/带宽/复杂度限制：adjacency 的 connectivity 需要至少 logarithmic depth（在复杂度假设下），random-walk 不能区分某些 planar/non-planar GM-switching 图，截断 spectral/adjacency 不能稳定恢复部分全局性质。证据类型：定理、构造和下界证明。
- 受控实验中，single view 没有统一赢家；combined view 在部分任务提升，但提升同时受信息充分性、Transformer depth、token budget 和优化条件影响。论文报告同一 Transformer、多个 depth/width 和 3 seeds 的 synthetic/real-world 比较；证据类型：实验表格与合成恢复实验。
- 最重要不足：论文没有 Graph-LLM 最终输出上的 fixed-text key/irrelevant topology intervention，也没有 GNN/LLM/readout responsibility split、随机 relabeling 的离散 token equality 或 topology-family OOD。因此它能证明 tokenization 的 finite-depth/lossiness/training trade-off，不能单独证明 LLM causal topology use。证据类型：实验设计缺口与研究者归因边界；相关局限已在 `notes/2605.22471.md` 分层记录。

### 对研究地图与假设的更新

- H1：维持“部分支持”。tokenization 选择会改变有限深度下可表达/可优化的结构信息，但这仍不是 Graph-LLM 的最终因果证据。
- H2：维持“部分支持”。spectral/random-walk 的等变输入与 adjacency 的顺序依赖形成明确对照，但 GraphTokenizer 的实测 relabeling equality 仍待运行。
- H3：维持“待验证”。今日论文强化了 complementary-view 的动机，但 discrete structural bottleneck 是否带来可定位、方向正确的 topology intervention 尚无证据。
- H5：维持“部分支持”。需区分 information sufficiency、permutation equivariance、finite-depth reachability、optimization stability 与 causal use；不能把 combined-view standard-split 增益直接归因于 LLM。
- H10：维持“待验证”。今日实验不是 topology-family OOD，仍需 size/density/topology-family holdout。
- 研究地图新增 Q11，并将 I1 的下一步从单一 serialization audit 扩展为 single-view 与 complementary-view 的 matched ablation。

### Idea 与 Novelty Gate

- 候选 Idea：I1“置换一致且可定位干预的离散图 Tokenizer”，模式为强化，状态保持“候选”，评分保持 8/10。
- 结合依据：今日 Lost in Tokenization；历史 GraphTokenizer、Lost in Serialization、Detecting Differences Is Not Understanding Structure、CausalGraph2LLM；当前 H2/H3/H5 与 Q2/Q3/Q4/Q10/Q11。
- 最近工作与最小差异：Lost in Tokenization 研究 tokenization trade-off 但没有 Graph-LLM causal protocol；GraphTokenizer 提供 reversible/deterministic serialization+BPE 但未做系统 equality/intervention/responsibility audit；GQT 使用 GNN+RVQ 离散 token 但未回答 fixed-text topology causality；TokenGT 提供 permutation-equivariant token design 但未做最终输出 causal split；SwapGT 生成多样 token sequence 但不等同于 I1 的可定位干预。I1 的最小实质差异是把 discrete addressability、等价变换稳定性、fixed-text key/irrelevant intervention、模块责任拆分和 topology-family OOD 作为一个联合可证伪协议，不把 multi-view 本身称为创新。
- Novelty Gate 结论：未发现上述联合协议的完全重复工作；但相邻的 invariant/canonical/discrete token 方法使 Novelty 仅 1/2，且不使用“首次”表述。若后续只有 standard-split 或 multi-view 增益，I1 应降级为评测复现。

### 今日唯一推荐 Idea 与最小实验

- 方法：以 GraphTokenizer reversible serialization 为接口，比较 local edge-covering、global spectral、random-walk/diffusion、single reversible view 与 complementary-view；加入 token/representation/prediction consistency、可定位 structural token、fixed-text key/irrelevant intervention 和 GNN/LLM/readout responsibility split。
- 核心预测：若离散接口确实承载可验证 topology evidence，等价变换下 serialized/BPE token、hidden state 和输出应稳定，关键边干预应方向正确，无关边干预应稳定，且完整模型在 topology-family OOD 上不能被 readout-only/GNN-only 完全复现。
- Baseline 与对照：GraphTokenizer Feuler/FCPP+BPE、raw edge-list/BFS/DFS、continuous projector、GNN-only、readout-only、LLM/Transformer-only、shuffled structural token；matched fixed-text 与非固定文本干预、random label/text shuffle。
- 关键消融：去 consistency loss、addressable token、intervention loss、各个 view、randomized BPE、tie-break、不同 depth/width/token budget；报告 decode fidelity、token equality、hidden distance、output KL、direction accuracy、irrelevant stability 与 topology-OOD。
- 支持条件：decode fidelity 100%，等价变换稳定，关键/无关干预分别通过，完整模型相对责任拆分基线在 held-out topology family 保持优势。
- 失败条件：重复 label 或原 node index 触发 tie-break shortcut；relabeling 导致 token/prediction flip；BPE 无法稳定 decode；GNN-only/readout-only 复现完整模型；或只改善 standard split、在 topology-OOD 失效。
- 资源与 7 天第一步：先不训练大模型，运行 GraphTokenizer 官方 serialization/decode 路径；使用 100--1000 个小图，执行 `10 relabelings × 4 edge orders × 3 starts/directions`，覆盖 repeated labels 和 disconnected components，随后在相同 Transformer/token budget 下做 single/complementary-view 小型对照。算力需求待该审计后确认。
- 评分：Novelty 1/2、Importance 2/2、Falsifiability 2/2、Feasibility 1/2、Thesis Fit 2/2，总分 8/10。

### 下一次研究问题

- 先回答：GraphTokenizer 的 frequency-guided traversal 在 repeated labels、edge order、起点/方向和 disconnected components 下是否真的保持 sequence/BPE/decode equality？若保持，complementary-view 是否仍能在 matched token budget 下改善 causal intervention 与 topology-family OOD？
- 优先动作：运行官方代码小审计；只有通过接口稳定性检查后，才进入 task-conditioned view ablation 和 responsibility split。

### 参考来源

- [arXiv 正式页面](https://arxiv.org/abs/2605.22471)、[HTML 全文](https://arxiv.org/html/2605.22471)：论文元数据、理论结果、实验与附录。
- [GraphTokenizer](https://arxiv.org/abs/2603.11099)、[GQT](https://arxiv.org/abs/2410.13798)、[TokenGT](https://neurips.cc/virtual/2022/poster/54611)、[SwapGT](https://openreview.net/forum?id=ofBqR4l0TD)：相邻工作与 Novelty Gate 对照。
- [GraphTokenizer 官方代码](https://github.com/BUPT-GAMMA/Graph-Tokenization-for-Bridging-Graphs-and-Transformers)：下一次 serialization/decode audit 的复现入口。

## 2026-08-30 第 15 次自动调研

### 研究问题与模式

- 连续主问题：如何让 Graph-LLM 的图结构接口同时满足 structural decodability、permutation consistency、causal topology use 和 cross-topology generalization，而不是把结构提示、标准任务增益或语义词汇效果误判为 LLM 的结构因果理解。
- 今日聚焦：承接 Q10/Q11 与 H1/H2/H3/H5/H10，追问 ordered-WL/color graph-to-prompt encoding 是否提供了可审计的离散结构瓶颈，还是主要依赖外部 WL 计算和自然语言 semantic anchor。
- 今日模式：强化已有 I1“置换一致且可定位干预的离散图 Tokenizer”，不新增 Idea。Colorful Talks with Graphs 作为 ordered-WL、语义颜色词和 lexical-control 的相邻基线。

### 检索、去重与选文

- 检索词：Graph-LLM graph encoding Weisfeiler Lehman color tokens、human interpretable graph encodings LLM、graph tokenizer permutation invariance、same partition opaque token graph、fixed text topology intervention graph LLM、mechanistic tokenized graph transformer。
- 初筛方向：Colorful Talks with Graphs、GraphTokenizer、GQT、TokenGT、Discovering Mechanisms in Tokenized Graph Transformers、Graph-KV、Lost in Serialization；GraphTokenizer、GQT、TokenGT、Graph-KV 和 mechanistic graph-token work 仅用于 Novelty Gate，不重复写入今日索引。
- 论文去重：今日唯一新增归档论文为 doi:10.18653/v1/2026.findings-acl.2049；此前 papers/index.md 无该 DOI、论文标题或本地笔记。
- Idea 去重：不新增 I2；把 same-WL partition 的 natural-color versus opaque-token ablation 加入 I1 的 semantic-anchor shortcut controls，并保留 GraphTokenizer、Graph-KV mask-only、GNN-only/readout-only 和 topology-OOD 对照。
- 选文理由：该论文同时提供 ordered 1-WL 离散 descriptor、自然颜色词、压缩 prompt 和同一 WL partition 的 lexical ablation，既能补充 H3 的结构瓶颈证据，也能直接检验“结构 descriptor 增益不等于 LLM causal topology use”的边界。

### 精读论文与验证

- 论文：[Colorful Talks with Graphs: Human-Interpretable Graph Encodings for Large Language Models](https://aclanthology.org/2026.findings-acl.2049/)，Zangari、Baghershahi、Medya，doi:10.18653/v1/2026.findings-acl.2049，ACL Findings 2026。
- 阅读级别：全文精读，包括主文、方法、synthetic graph tasks、Cora/Citeseer/PubMed/OGBN-ArXiv node classification、compressed prompt、color ablation、size scalability、limitations 和 reproducibility appendix；本轮只精读这一篇论文。
- PDF：papers/2026_Zangari_Colorful_Talks_with_Graphs.pdf；笔记：notes/10.18653_v1_2026.findings-acl.2049.md。
- PDF 下载：PowerShell Invoke-WebRequest 因 TLS Authentication failed 未采用；改用 Python urllib.request 从 ACL 官方 PDF 下载，HTTP 200，Content-Type 为 application/pdf，大小 520828 bytes。
- PDF 验证：文件头为 %PDF-1.7，尾部含 %%EOF，SHA-256 为 FEE55376F15DC2BF775C967F7623D7BC2116F973459F112F62DDA0777099D0F8；pdfinfo 报告 22 页、A4、未加密；Poppler 成功提取文本并渲染，目检第 1、11、22 页，标题、表格、参考文献和末页附录版面可读。

### 关键发现与证据边界

- 方法路径是 Graph -> adjacency list -> ordered 1-WL refinement -> WL labels/colors -> text prompt -> GPT-4o/GPT-3.5 -> answer。外部算法完成邻居标签排序、全局 message 编号和颜色映射，LLM 不负责从原始图中学习该结构摘要。
- GPT-4o cycle check 的 Acc 为 TLG-A 89.5、TLG-F 91.5、L-OWL 92.0、C-OWL 92.5、CL-OWL 93.0；shortest path 为 87.80、83.74、91.87、88.62、86.99，说明收益依赖任务。50-node Cora compressed setting 中 TLG-A 的 1/2/3-hop 为 30/40/45%，C-OWL 为 75%；外部 Cora 表中 C-OWL 为 68.9%、CL-OWL 为 66.4%。
- 同一 WL partition 的 color ablation 中 TLG-A、C-OWL、L-OWL、CL-OWL 为 49.0/68.9/63.6/66.4%，opaque Hue i 和普通颜色名只有 15.2/6.3%；该结果是 semantic-anchor/lexical shortcut 的直接证据，不是 topology causal-use 的直接证据。
- 图规模和任务存在明显边界：graph-level triangle counting 的 CL-OWL accuracy 约 14.5%，局部 triangle membership 约 68.3%；ER/path maximum-flow 的 C-OWL 从 n=50 的 81.8% 变为 n=100 的 75%，TLG-A 从 45% 变为 0%。作者还说明每个 configuration 只运行一次，没有标准差。
- 结构证据审计：结构被输入为“是”；descriptor-level 可读性为“部分”；permutation/invariance 只有设计主张、无逐样本 relabeling/output equality；没有 fixed-text key/irrelevant topology intervention、text shuffle、GNN/LLM/readout responsibility split 或 held-out topology-family OOD。最重要不足的证据类型是实验设计缺口、外部预计算组件混杂和 lexical shortcut 对照不足。

### 对研究地图与假设的更新

- H1：维持“部分支持”。任务收益和可读性不等于 LLM causal topology use；ordered-WL 和颜色映射的外部预计算使责任边界更清楚。
- H2：维持“部分支持”。论文把 permutation-invariant 作为设计目标，却没有 node relabeling、edge order、起点/方向或 isomorphism equality 的最终输出测试。
- H3：维持“待验证”并强化。离散 WL descriptor 值得作为可审计 bottleneck，但 same-WL 的 natural-color/opaque-token 差异要求 I1 加入 semantic-anchor control。
- H5：维持“部分支持”。representation robustness、语义捷径与 causal topology use 在本论文中仍是分离轴；自然颜色词影响结果不能替代 fixed-text topology intervention。
- H10：维持“待验证”。BA、ER、Path、长距离图和多个 citation-network 数据集是规模/图类型压力测试，不是 held-out topology-family 或 generator/transitivity OOD。

### Idea 与 Novelty Gate

- 候选 Idea：I1“置换一致且可定位干预的离散图 Tokenizer”，模式为强化，状态保持“候选”，评分保持 8/10；不新增 I2。
- 结合依据：今日 Colorful Talks with Graphs；历史 GraphTokenizer、Lost in Tokenization、Graph-KV、Lost in Serialization；当前 H1/H2/H3/H5/H10 与 Q2/Q3/Q4/Q10/Q11。
- 最近工作与最小差异：Colorful Talks 解决 ordered-WL/color graph-to-prompt encoding 和 lexical color ablation；GraphTokenizer 解决 reversible/deterministic serialization+BPE；GQT 解决 learned RVQ hierarchical discrete tokens；Discovering Mechanisms 解决 permutation-augmented tokenized Transformer 的 activation/circuit analysis；Graph-KV 解决 LLM-side KV structural routing。I1 的最小实质差异不是 color、multi-view、relabeling 或 mask 中任一单项，而是把 discrete addressability、等价变换 stability、fixed-text key/irrelevant intervention、GNN/LLM/readout responsibility split 和 topology-family OOD 作为同一联合可证伪协议。
- Novelty Gate 结论：未发现上述工作把该联合协议作为完整重复方法；Colorful Talks 的 semantic-anchor 结果使 Novelty 仍只记 1/2，不使用“首次”表述。若 I1 只提升 standard split、prompt length 或自然语言颜色词，必须降级为评测复现。

### 今日唯一推荐 Idea 与最小实验

- 方法：以 GraphTokenizer reversible serialization/BPE 为主接口，加入 ordered-WL natural colors、opaque same-partition tokens、raw serialization、Graph-KV mask-only、continuous projector、GNN-only、readout-only 和 shuffled structural tokens。
- 核心预测：在 10 relabelings、4 edge orders、3 starts/directions 下，真正稳定的离散接口应同时保持 serialized/BPE equality、decode isomorphism、hidden/output consistency；固定节点文本的 key-edge 应有方向正确响应，irrelevant edge 应保持 prediction equality；去除颜色语义后仍应超过 readout-only。
- 关键对照：natural color versus opaque Hue/name、random-label/text shuffle、mask shuffle、retriever-randomized Graph-KV、fixed-text versus non-fixed-text、GNN-only/LLM-only/readout-only，以及 topology-family holdout。
- 支持条件：decode fidelity 100%；等价变换下 token/hidden/output 稳定；key-edge direction accuracy 高、irrelevant intervention 稳定；完整模型在 topology-family OOD 上超过 single-view、semantic-anchor、Graph-KV mask-only 和 GNN/readout-only。
- 失败条件：natural colors 或 Graph-KV mask-only 复现全部增益；opaque same-partition token 退化揭示收益主要来自词汇先验；relabeling、edge order、BPE 或 node address 造成 output flip；关键 topology 无方向响应；或优势只存在于 standard split、prompt 长度和顺序摆放。
- 资源与 7 天第一步：先运行 GraphTokenizer 官方 serialization/decode 小审计，不训练大模型；使用 100--1000 个小图覆盖 repeated labels 和 disconnected components，记录 serialized/BPE equality、decode isomorphism，再在 matched token/compute budget 下加入 ordered-WL/color 与 Graph-KV mask-only comparator。完整 Graph-KV 复现的 Llama-3.1-8B、多 GPU 与长上下文成本暂不作为第一步。

### 下一次研究问题

GraphTokenizer 的 frequency-guided traversal 在 repeated labels、edge order、起点/方向和 disconnected components 下，是否同时保持 sequence/BPE/decode equality；加入 semantic-anchor control 后，离散接口是否仍能在 fixed-text topology intervention 和 topology-family OOD 上超过 Graph-KV mask-only 与 readout-only？

### 参考来源

- [ACL Anthology paper page](https://aclanthology.org/2026.findings-acl.2049/)：元数据与摘要。
- [ACL 官方 PDF](https://aclanthology.org/2026.findings-acl.2049.pdf)：全文、方法、实验、附录和 limitations。
- [GraphTokenizer](https://arxiv.org/abs/2603.11099)、[Lost in Tokenization](https://arxiv.org/abs/2605.22471)、[Graph-KV](https://arxiv.org/abs/2506.07334)、[GQT](https://arxiv.org/abs/2410.13798)、[Discovering Mechanisms in Tokenized Graph Transformers](https://openreview.net/pdf?id=0WEuCiokZq)、[Lost in Serialization](https://arxiv.org/abs/2511.10234)：Novelty Gate 相邻工作。

## 2026-08-30 第 14 次自动调研

### 研究问题与模式

- 连续主问题：如何让 Graph-LLM 的图结构接口同时满足 structural decodability、permutation consistency、causal topology use 和 cross-topology generalization，而不是把顺序稳健、标准任务分数或 attention 当作结构因果证据。
- 今日聚焦：承接 Q10/Q11 与 H2/H3/H5/H6/H10；具体追问 Graph-KV-style LLM-side graph mask 是否只是更强的 routing baseline，还是能在固定文本下证明拓扑被因果使用。
- 今日模式：强化已有 I1“置换一致且可定位干预的离散图 Tokenizer”，不新增 Idea。Graph-KV 只作为 mask/shared-PE 的相邻对照和责任拆分入口。

### 检索、去重与选文

- 检索词：`Graph-LLM structural bias KV cache`、`graph mask large language model topology`、`Graph-KV permutation invariance`、`discrete graph token causal intervention`、`Graph-LLM responsibility split topology OOD`。
- 初筛方向：Graph-KV、Discovering Mechanisms in Tokenized Graph Transformers、GQT、TokenGT、GraSAME、Graph Language Models；另复核 GraphTokenizer、Lost in Tokenization、Lost in Serialization、Detecting Differences Is Not Understanding Structure 与 CausalGraph2LLM。
- 论文去重：今日唯一新增归档论文为 arXiv:2506.07334；GraphTokenizer、GQT、TokenGT、GraSAME、Graph Language Models 和 Discovering Mechanisms 作为 Novelty Gate 相邻工作，不重复写入索引。OpenReview 受挑战页面限制的论文不作为今日精读对象。
- Idea 去重：不新增 I2；Graph-KV 用于强化 I1 的 mask-only baseline、retriever/position confound 控制和 matched responsibility audit。
- 选文理由：Graph-KV 直接把图邻接写入 LLM 的 KV-cache attention mask，并报告 RAG、topic classification、chunk-placement/distractor 与大邻居压力测试，最适合检验现有 I1 是否必须与 LLM-side structural routing 做同预算对照。

### 精读论文与验证

- 论文：[Graph-KV: Breaking Sequence via Injecting Structural Biases into Large Language Models](https://arxiv.org/abs/2506.07334)，Wang 等，arXiv:2506.07334，2025；NeurIPS 2025 poster。
- 阅读级别：全文精读，包括主文、方法、RAG/Arxiv-QA/topic classification/stress test、结论、局限、NeurIPS checklist 与附录 A.1--A.2；本轮只精读这一篇论文。
- PDF：`papers/2025_Wang_Graph-KV_Breaking_Sequence_Structural_Biases.pdf`；笔记：`notes/2506.07334.md`。
- PDF 验证：官方 arXiv PDF，HTTP 200，`Content-Type: application/pdf`，文件 3331011 bytes；PDF 1.7、30 页、未加密；SHA-256 为 `a112bd50c4246175c8431b74465b714ef3d7f68692031e3262b397f0bd34ac7a`；bundled pypdf 读取 30/30 页文本，Poppler 渲染并目检第 1、10、16、22、30 页，版面、表格和附录可读。

### 关键发现与证据边界

- Graph-KV 将 source/target 文本块先独立 prefill 为 KV-cache，再按 source→target 图邻接限制 attention，目标节点更新只读取其 source 邻居的 K/V；共享 positional embedding 将 source、target 和 query/answer 放入同一上下文坐标范围。该机制的复杂度为初始 `O(|V|L^2)`、edge update `O(|E|L^2)`、生成阶段 `O(|V|L)`，证据类型为方法定义与复杂度分析。
- 在 top-3 RAG、Arxiv-QA、Cora/Pubmed topic classification 和 synthetic star-neighbor stress test 中，Graph-KV 相对 Sequential/Block-RAG 有任务级或扩展性收益；chunk placement、distractor 与邻居数量实验说明显式 routing 可缓解部分顺序/长上下文问题。证据类型为受控实验与资源测量，但 RAG 图边由 semantic retriever 启发式构造。
- 论文主实验使用 one-hop propagation、Llama-3.1-8B 家族和 post-trained `llama-3.1-8B-block-ft`，没有直接对 Graph-KV 继续 fine-tune；appendix 的 2-hop/3-hop 结果支持进一步传播的可行性，但不能替代 held-out topology-family 泛化。
- 最重要不足：没有逐样本 node-relabeling/isomorphism equality、fixed-text key/irrelevant topology intervention、text shuffle、GNN-only/LLM-only/readout-only responsibility split 或 topology-family OOD；因此 Graph-KV 能证明 LLM-side structural routing 和上下文稳健性，不能单独证明 discrete graph token 的必要性或 LLM 的 causal topology use。证据类型：实验设计缺口、组件混杂和研究者归因边界。

### 对研究地图与假设的更新

- H1：维持“部分支持”。Graph-KV 让结构进入 LLM-side KV attention 并产生任务收益，但 mask、shared PE、retriever 和 post-trained backbone 共同承担效果，仍不能归因于 LLM 的独立 causal topology use。
- H2：维持“部分支持”。chunk placement/distractor 稳健性是顺序路由正面证据，但不等价于 node relabeling 或 graph-isomorphism consistency。
- H5：维持“部分支持”。Graph-KV 的 order/position robustness 与 permutation equality、fixed-text topology sensitivity 不是同一指标，支持继续拆分这些轴。
- H6：维持“待验证”。显式结构 mask 比 latent soft intervention 更接近 routing-level intervention，但缺少 key/irrelevant direction accuracy 和责任分离。
- H10：维持“待验证”。Arxiv-QA、RAG 和 star-neighbor stress test 不是 held-out topology-family OOD。
- 研究地图新增 Graph-KV evidence-ledger 行和状态更新；I1 加入 Graph-KV-style mask-only、retriever randomization、mask shuffle 与 matched token/compute 对照。

### Idea 与 Novelty Gate

- 候选 Idea：I1“置换一致且可定位干预的离散图 Tokenizer”，模式为强化，状态保持“候选”，评分保持 8/10；不新增 I2。
- 结合依据：今日 Graph-KV；历史 GraphTokenizer、Lost in Tokenization、Lost in Serialization、Detecting Differences Is Not Understanding Structure、CausalGraph2LLM；当前 H2/H3/H5/H6/H10 与 Q2/Q3/Q4/Q10/Q11。
- 最近工作与最小差异：Graph-KV 已解决 LLM-side source→target KV mask、shared PE 和 chunk-order/long-context routing；GraphTokenizer 已解决 reversible/deterministic serialization+BPE；GQT 提供 GNN+RVQ hierarchical discrete token；TokenGT 提供 node/edge token 与 permutation-equivariant 理论；GraSAME/Graph Language Models 提供 graph-guided PLM/LM attention bias；Discovering Mechanisms 提供 permutation-augmented TokenGT-like Transformer 的 activation patching/circuit analysis。I1 的最小实质差异仍是把 discrete addressability、等价变换稳定性、fixed-text key/irrelevant intervention、GNN/LLM/readout responsibility split 和 topology-family OOD 作为一个联合可证伪协议，并在 matched token/compute budget 下加入 Graph-KV mask-only 对照。
- Novelty Gate 结论：未发现上述联合协议的完全重复工作；Graph-KV 使 LLM-side structural routing 成为明确 prior art，因此 Novelty 只记 1/2，且不使用“首次”表述。若结果只显示 mask、multi-view 或 standard-split 增益，I1 应降级为评测复现。

### 今日唯一推荐 Idea 与最小实验

- 方法：以 GraphTokenizer reversible serialization/BPE 为离散接口，加入 Graph-KV-style mask-only、continuous projector、raw serialization、GNN-only、readout-only、LLM/Transformer-only 和 shuffled structural token 对照；在相同 backbone、hidden size、token/compute budget 下联合测等价变换与 topology intervention。
- 核心预测：若离散可寻址接口确实提供独立结构证据，`10 relabelings × 4 edge orders × 3 starts/directions` 下 serialized/BPE token、hidden state 和输出应稳定；fixed-text key-edge 要求 direction accuracy 高，irrelevant-edge 要求 prediction equality 高；完整模型还应在 held-out topology family 超过 Graph-KV mask-only、GNN-only 和 readout-only。
- 关键对照：retriever semantic-edge 与 random/irrelevant-edge、mask shuffle、shared-PE removal、one-hop/2-hop、fixed-text 与非固定文本、text shuffle，以及去掉 consistency loss/addressable token/intervention loss。
- 支持条件：decode fidelity 100%；等价变换 prediction equality 高且 output KL/hidden distance 低；关键干预方向正确、无关干预稳定；完整模型在 topology-family OOD 有独立优势且不能被 mask-only/readout-only 复现。
- 失败条件：Graph-KV mask-only 或 readout-only 复现全部增益；random/irrelevant edge 与 semantic retriever 表现相同；relabeling/edge order 触发 token 或输出翻转；关键 topology 无方向响应；或优势只存在于顺序摆放、长上下文和 standard split。
- 资源与 7 天第一步：先运行 GraphTokenizer 官方 serialization/decode 小审计，不训练大模型；使用 100--1000 个小图，执行 `10 relabelings × 4 edge orders × 3 starts/directions`，覆盖 repeated labels 与 disconnected components，记录 serialized/BPE equality、decode isomorphism，再在小型 Transformer 上加入 Graph-KV mask-only matched comparator。完整 Graph-KV 复现需要 Llama-3.1-8B、FlashAttention-2 和长上下文多 GPU，暂不作为第一步。
- 评分：Novelty 1/2、Importance 2/2、Falsifiability 2/2、Feasibility 1/2、Thesis Fit 2/2，总分 8/10。

### 下一次研究问题

- 先回答：GraphTokenizer 的 frequency-guided traversal 在 repeated labels、edge order、起点/方向和 disconnected components 下是否保持 sequence/BPE/decode equality；Graph-KV-style mask-only 是否在 matched budget 下复现 Graph-LLM 的顺序稳健和 topology intervention 结果？
- 优先动作：运行 GraphTokenizer 官方代码小审计；随后只在 mask-only、retriever-randomized 与 discrete/BPE 对照均可复现的情况下进入 fixed-text topology intervention 和 responsibility split。

### 参考来源

- [arXiv 正式页面](https://arxiv.org/abs/2506.07334)、[HTML 全文](https://arxiv.org/html/2506.07334)、[NeurIPS 2025 poster](https://neurips.cc/virtual/2025/poster/118749)：论文元数据、方法、实验、附录与会议状态。
- [Graph-KV 官方代码](https://github.com/Graph-COM/GraphKV)：公开实现、Arxiv-QA 数据与运行环境入口。
- [GraphTokenizer](https://arxiv.org/abs/2603.11099)、[GQT](https://arxiv.org/abs/2410.13798)、[TokenGT](https://arxiv.org/abs/2207.02505)、[GraSAME](https://aclanthology.org/2024.findings-naacl.58/)、[Graph Language Models](https://aclanthology.org/2024.acl-long.245/) 和 [Discovering Mechanisms in Tokenized Graph Transformers](https://www.lgresearch.ai/publication/view?seq=172)：Novelty Gate 相邻工作。
