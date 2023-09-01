# PET(2021)
本文介绍**Prompt Tuning**方向的**PET**方法，论文标题为：**Exploiting Cloze Questions for Few Shot Text Classification and Natural Language Inference**，来自EACL2021。
## 摘要
随着GPT3的出现，通过一些具有自然语言“任务描述”的预训练语言模型，可以以完全无监督的方式解决一些NLP任务。**PET**是一种半监督的训练方式，它首先将`input examples`转化为`cloze-style phrases`的形式以帮助模型理解给出的任务，之后利用这些`phrases`为一个更大的未标注的数据集分配`soft labels`，最后在该数据集上执行有监督训练。

## 介绍

 PET是一种半监督式训练程序，将任务描述与标准的监督学习相结合它使用自然语言`pattern`将`input examples`转化为`cloze-style phrases`。如下图所示，PET 分三步工作：
1. 针对每种`pattern`，在一个小的训练集 $\mathcal{T}$ 上对一个单独的PLM进行微调。
2. 使用所有PLM模型的集合为一个大的未标注数据集 $\mathcal{D}$ 添加`soft labels`。
3. 在这个`soft-labeled`的数据集上训练标准分类器。

![](https://github.com/toby-katakuri/Papers-Notes/blob/main/images/001_001.PNG)

作者还设计了 **iPET**，这是 PET 的一个迭代的变体，随着训练集大小的增加，这一过程会不断重复。

在多种语言的各种任务中，给定少量到中等数量的标注样本，PET 和 iPET 的表现大大优于无监督方法、有监督训练和强半监督基线。

## PET方法
- $M$ : masked LM
- $V$ : vocabulary
-  $\mathcal{L}$ : a set of labels
-  $\mathbf{x}$ : `input` as a sequence of phrases, $\mathbf{x}=\left(s_1,\ldots, s_k\right)$
- $P$ : ***pattern***，a function, $\mathbf{x}$ 为输入, $P(\mathbf{x}) \in V^*$ 为输出，是一个`phrase`或`sentence`, 其中包含有一个`mask` token。
- $v$：***verbalizer***，an injective function, $\mathcal{L} \rightarrow V$ 将每个标签映射到 $M$ 词汇表中的一个词。

将 $(P, v)$ 称为***pattern-verbalizer pair*** (PVP)。

**为什么要利用PVP的形式作为输入：**
1. (from Introduction) Solving a task from only a few examples becomes much easier **when we also have a task description**,
i.e., a textual explanation that helps us understand what the task is about.
2. The task now **changes from** having to assign a label without inherent meaning **to** answering whether the most likely choice for the masked position in $P(\mathbf{x})$ .
   

**PVP Training and Inference**
- a small training set $\mathcal{T}$.
- a (typically much larger) set of unlabeled examples $\mathcal{D}$.

$$
s_{\mathbf{p}}(l \mid \mathbf{x})=M(v(l) \mid P(\mathbf{x}))
$$

$$
q_{\mathbf{p}}(l \mid \mathbf{x})=\frac{e^{s_{\mathbf{p}}(l \mid \mathbf{x})}}{\sum_{l^{\prime} \in \mathcal{L}} e^{s_{\mathbf{p}}\left(l^{\prime} \mid \mathbf{x}\right)}}
$$

use the cross-entropy between $q_{\mathbf{p}}(l \mid \mathbf{x})$ and the true (one-hot) distribution of training example $(x, l)$ – summed over all $\mathcal{T}$ – as loss for finetuning $M$ for $p$.

***备注：*** 模型输出是一个向量，其与总词汇表中每个词的向量相乘，算出对于每个词的分数（对全部词做softmax可以算出对应每个词的概率），这里的 $M(v(l) \mid P(\mathbf{x}))$ 其实就是 $l$ 对应的词 $v(l)$ 的分数。

**Auxiliary Language Modeling**

$$
L=(1-\alpha) \cdot L_{\mathrm{CE}}+\alpha \cdot L_{\mathrm{MLM}}
$$

**Combining PVPs**
1.  finetune a separate language model Mp for each $p$.
2. annotate examples from $\mathcal{D}$.

$$
s_{\mathcal{M}}(l \mid \mathbf{x})=\frac{1}{Z} \sum_{\mathbf{p} \in \mathcal{P}} w(\mathbf{p}) \cdot s_{\mathbf{p}}(l \mid \mathbf{x})
$$

3.  finetune a PLM $\mathcal{C}$.

## Automatic Verbalizer Search
**$p=(P, v)$ 到底是干嘛的？**

Given an input x, we apply P to obtain an input representation P(x), which is then processed by M to determine the label y ∈ L for which v(y) is the most likely substitute for the mask。 通过 $M$ 处理 $p$, 以确定标签 $y$ , 此时 $v(y)$ 最有可能替代`mask`。


## Iterative PET (iPET)
![](https://github.com/toby-katakuri/Papers-Notes/blob/main/images/001_002.PNG)
PET (1-3) 和 iPET (a-c) 的示意图。
-  (**1**) 初始训练集 $\mathcal{T}$ 用于微调 PLM 集合。 
- (**a**) 对于每个模型，其他模型的随机子集通过标记 $\mathcal{D}$ 中的样本来生成新的训练集。 
- (**b**) 使用生成的更大的特定于模型的数据集来训练一组新的 PET 模型。 
- (**c**) 前两个步骤重复 $k$ 次，每次将生成的训练集的大小增加 $d$ 倍。
-  (**2**) 最终的模型集用于创建`soft-labeled`数据集 $\mathcal{T}_C$。
-  (**3**) 在此数据集上训练分类器 $C$ 。

**只需要少量的调整，iPET就能在zero-shot的情况下使用**

## 实验
**Using 4 English datasets:**
- `Yelp Reviews`: the task is to estimate the rating that a customer gave to a restaurant on a 1- to 5-star scale based on their review’s text.
- `AG’s News`: is a news classification dataset, where given a headline a and text body b, news have to be classified as belonging to one of the categories *World (1)*, *Sports (2)*, *Business (3)* or *Science/Tech (4)*.
- `Yahoo Questions`: Given a question a and an answer b, one of ten possible categories has to be assigned: “Society”, “Science”, “Health”, “Education”, “Computer”, “Sports”, “Business”, “Entertainment”, “Relationship” and “Politics”.
- `MNLI `: consists of text pairs x = (a, b). The task is to find out whether a implies b (0), a and b contradict each other (1) or neither (2).

use `RoBERTa large`

**For other languages:**
- `x-stance`: a multilingual stance detection  dataset with German, French and Italian examples.  Each example x = (a, b) consists of a question  a concerning some political issue and a comment  b; the task is to identify whether the writer of b supports the subject of the question (0) or not (1).

use `XLM-R`

**PET方法主要解决的问题？**

在`few-shot`设置下利用**semi-supervised**的方法训练，**利用尽可能少的被标注的数据样本，提升模型准确率。**

![](https://github.com/toby-katakuri/Papers-Notes/blob/main/images/001_003.PNG)

## 结论
向预训练语言模型提供任务描述可以与标准的监督训练相结合。PET 包括的***pattern-verbalizer pair*** 帮助利用预训练语言模型中包含的知识来完成下游任务。利用PVP对模型进行微调，并使用它们来创建可以训练标准分类器的大型标注数据集。当初始训练数据有限时，PET 比标准监督训练和强大的半监督方法有很大提升。

# LM-BFF(21)
## Abstract
**当前工作现状？**

The recent GPT-3 model achieves remarkable few-shot performance solely by leveraging a **natural-language prompt** and **a few task demonstrations** as input context.

**本文工作研究的东西**

We study `few-shot` learning in a **more practical scenario**, where we use **smaller** language models for which fine-tuning is computationally efficient.

We present **LM-BFF**—better few-shot fine-tuning of language models1—a suite of simple and complementary techniques for finetuning language models on **a small number of annotated examples**.(和PET类似）

**研究内容**
1. **Prompt-based fine-tuning** together with a novel pipeline for **automating prompt generation**;
2. A refined strategy for **dynamically and selectively incorporating demonstrations into each context**.
3. **A systematic evaluation** for analyzing few-shot performance on a range of NLP tasks, including **classification and regression**.

**本文工作的优点**

Our approach makes **minimal assumptions** on task resources and domain expertise, and hence constitutes **a strong task-agnostic method** for `few-shot` learning.



