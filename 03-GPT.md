# GPT 1 论文介绍
## Abstract
**目前存在的问题**

Although large `unlabeled` text corpora are **abundant**, `labeled` data for learning these specific tasks is **scarce**, making it challenging for discriminatively trained models to perform adequately.

**解决的方案**

These tasks can be realized by `generative pre-training` of a language model on a diverse corpus of **unlabeled text**, followed by `discriminative fine-tuning` on each **specific task**.

## 1 Introduction
**为什么可以利用这种方式？**

The most compelling evidence for this so far has been the extensive use of `pretrained word embeddings` to improve performance on a range of NLP tasks.

**挑战：**

Leveraging more than word-level information from unlabeled text is challenging:
1. It is unclear **what type of optimization objectives are most effective** at learning text representations that are useful for transfer.
2. There is **no consensus** on the most effective way to transfer these learned representations to the target task.

**本文工作**

A `semi-supervised` approach for language understanding tasks using a combination of unsupervised pre-training and supervised fine-tuning:
1. Using a language modeling objective on the unlabeled data to learn the initial parameters of a neural network model.
2. Adapting these parameters to a target task using the corresponding supervised objective.

## 3 Framework
### Unsupervised pre-training
$$
L_1(\mathcal{U})=\sum \log P\left(u_i \mid u_{i-k}, \ldots, u_{i-1} ; \Theta\right)
$$
### Supervised fine-tuning
$$
L_2(\mathcal{C})=\sum_{(x, y)} \log P\left(y \mid x^1, \ldots, x^m\right)
$$

$$
L_3(\mathcal{C})=L_2(\mathcal{C})+\lambda * L_1(\mathcal{C})
$$

$L_1$ an auxiliary objective.
### Task-specific input transformations
![](https://github.com/toby-katakuri/Papers-Notes/blob/main/images/003_001.PNG)

# GPT 2 论文介绍
相关文章：
[The Illustrated GPT-2 (Visualizing Transformer Language Models)](http://jalammar.github.io/illustrated-gpt2/)
## Abstract
- We demonstrate that language models begin to learn these tasks **without any explicit supervision** when trained on a new dataset of millions of webpages called WebText.
- **GPT-2特点**: a `1.5B` parameter Transformer，a `zero-shot` setting.
- **A promising path towards building language processing systems**: learn to perform tasks from their naturally occurring demonstrations.

## 1 Introduction
**该工作的目的是什么？**

Current systems are better characterized as **narrow experts** rather than **competent generalists**. We would like to move towards more general systems which can **perform many tasks** – eventually
**without the need** to manually create and label a training dataset for each one.

**一个Robust系统需要什么？**

Progress towards robust systems with current architectures is likely to **require training and measuring performance** on a wide range of domains and tasks.
- GLUE
- decaNLP

### Multitask learning
This suggests that multitask training **many need just as many effective training pairs** to realize its promise with current approaches. 这表明，多任务训练需要同样多的有效训练配对，才能实现现有方法的承诺。

It will be very **difficult** to continue to **scale** the creation of datasets and the design of objectives to the degree that may be required to brute force our way there with current techniques. 要想继续扩大数据集创建和目标设计的规模，并达到利用现有技术强行实现目标所需的程度，将非常困难。

**本文的工作**

1. We demonstrate language models can perform down-stream tasks in a **zero-shot setting** – **without any parameter or architecture modification**.
2. We demonstrate this approach shows **potential** by highlighting the ability of language models to perform a wide range of tasks in a zero-shot setting.
3. We achieve promising, competitive, and state of the art results depending on the task.

## 2 Approach
**思路：**

Learning to perform **a single task** can be expressed in a probabilistic framework as estimating a conditional distribution $p(\text { output|input })$.

Since a general system should be able to perform **many different tasks**, even for the same input, it should condition not only on the input but also
on the task to be performed. That is, it should model $p(\text { output|input,task })$. 

language provides a flexible way to **specify tasks, inputs, and outputs** all as a sequence of symbols:
- a translation training example can be written as the sequence `(translate to french, english text, french text)`.
- a reading comprehension training example can be written as `(answer the question, document, question, answer)`.

### Training Dataset
Our approach motivates **building as large and diverse a dataset as possible** in order to **collect natural language demonstrations of tasks in as varied of domains and contexts as possible**

**WebText**： contains the text subset of these 45 million links.

### Input Representation
Current **byte-level** LMs are not competitive with **word-level** LMs on large scale datasets such as the One Billion Word Benchmark.

**BPE**

This input representation allows us to combine the empirical benefits of word-level LMs with the generality of byte-level approaches.

### Model

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

**in-context learning**：using the text input of a pretrained language model as **a form of task specification**: the model is conditioned on a natural language **instruction and/or a few demonstrations** of the task
and is then expected to **complete further instances of the task** simply by **predicting what comes next**.

![](https://github.com/toby-katakuri/Papers-Notes/blob/main/images/003_002.PNG)

- **meta-learning** to capture the inner-loop / outer-loop structure of the general method
- **in context-learning** to refer to the inner loop of meta-learning

**GTP-2** 中提到的zero-shot transfer和**GTP-3**中的`zero-shot`是不一样的：
The method is “zero-shot” in the sense (GPT-2) that **no gradient updates** are performed, but it often involves **providing inference-time demonstrations** to the model, so is not truly learning from zero examples.

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
