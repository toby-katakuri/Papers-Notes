# GPT 1




# GPT-3论文介绍
## Abstract
### 最近NLP工作的现状？
首先在大的语料库中进行`pre-training`，之后再特殊的任务上进行`fine-tuning`。
### 这些方法存在的问题？
尽管架构上是`task-agnostic`的,但针对具体任务的微调还是需要成千上万的数据样本。
### GPT-3
- GPT-3是一个`autoregressive`的语言模型，包含了175B的参数，将在`few-shot`的设置下进行测试。
- GPT-3 is applied **without any gradient updates** or **fine-tuning**, with **tasks and few-shot demonstrations** specified purely via **text interaction** with the model.
