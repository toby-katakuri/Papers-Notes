# GPT 1 论文介绍

# GPT 2 论文介绍
- **GPT-2特点**: a `1.5B` parameter Transformer.
- **A promising path towards building language processing systems**: learn to perform tasks from their naturally occurring demonstrations.


# GPT-3 论文介绍
## Abstract
### 最近NLP工作的现状？
通过首先在大的语料库中进行`pre-training`，之后再特殊的任务上进行`fine-tuning`的这种方式，许多NLP的任务取得了一定的成功。
### 这些方法存在的问题？
尽管架构上是`task-agnostic`的,但针对具体任务的微调还是需要成千上万的数据样本。
### GPT-3特点
- GPT-3是一个`autoregressive`的语言模型，包含了175B的参数，将在`few-shot`的设置下进行测试。
- GPT-3 is applied **without any gradient updates** or **fine-tuning**, with **tasks and few-shot demonstrations** specified purely via **text interaction** with the model.

## 1 Introduction

### 移除目前方法的限制（即尽管架构上是`task-agnostic`的,但针对具体任务的微调还是需要成千上万的数据样本）是可取的,以下有三个原因：
1. For many of these tasks it is difficult to collect a **large supervised** training dataset, especially when the process must **be repeated** for every new task
2. **Generalization** achieved under this paradigm can be **poor** because the model is **overly specific to the training distribution** and **does not generalize well** outside it. Thus, the performance of fine-tuned models on specific benchmarks, even when it is **nominally at
human-level**, may **exaggerate actual performance** on the underlying task.
3. A **brief directive** in natural language or at most a tiny number of **demonstrations** is often sufficient to enable a human to perform a new task to at least a reasonable degree of competence.
   - **brief directive**：e.g. “please tell me if this sentence describes something happy or something sad”
   - **demonstrations**：e.g. “here are two examples of people acting brave; please give a third example of bravery”)

### 解决方案：
**meta-learning**：During unsupervised pre-training, a language model develops a broad set of skills and pattern recognition abilities. It then uses these abilities at inference time to rapidly adapt to or recognize the desired task

最近的GPT-2将这种学习称为**in-context learning**：using the text input of a pretrained language model as **a form of task specification**: the model is conditioned on a natural language **instruction and/or a few demonstrations** of the task
and is then expected to **complete further instances of the task** simply by **predicting what comes next**.

还有一个研究趋势，Since in-context learning involves absorbing many skills and tasks within the **parameters** of the model, it is plausible that in-context learning abilities might **show similarly strong gains with scale**.

### 实验评估
**数据集**：two dozen NLP datasets, as well as several novel tasks designed to test rapid adaptation to tasks unlikely to be directly contained in the training set.

**Evaluate GPT-3 under 3 conditions**:
- `few-shot learning`, or in-context learning where we allow as many demonstrations as will fit into the model’s context window (typically 10 to 100).
- `one-shot learning`, where we allow only one demonstration.
- `zero-shot learning`, where no demonstrations are allowed and only an instruction in natural language is given to the model.

### Study of “data contamination”

## 2 Approach
- Our basic pre-training approach, including `model`, `data`, and `training`, is **similar to the process** described in GPT-2, with relatively straightforward **scaling up** of the `model size`, `dataset size` and `diversity`, and `length of training`.
- Our use of in-context learning is also **similar to GPT-2**, but in this work we systematically explore **different settings** for learning within the context.
