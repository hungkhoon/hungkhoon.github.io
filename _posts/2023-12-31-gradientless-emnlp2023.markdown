---
layout: post
title:  "Parameter-efficient Tuning for Large Language Model without Calculating Its Gradients"
date:   2023-01-01
categories: ["deeplearning", "finetuning"]
permalink: /:categories/:year/:month/:day/:title:output_ext
author: "HK"
---

---
Jin, F., Zhang, J. and Zong, C., 2023, December. [Parameter-efficient Tuning for Large Language Model without Calculating Its Gradients](https://aclanthology.org/2023.emnlp-main.22/){:target="_blank"}. In Proceedings of the 2023 Conference on Empirical Methods in Natural Language Processing (pp. 321-330).

## I. Introduction

The sheer number of parameters in LLM, e.g., GPT3, GPT4, T5-XXL and LLaMA makes fine-tuning slow and memory intensive. To solve this problem, parameter-efficient tuning methods adds a small number of parameters to the language model and only fine-tunes the added subset of parameters, while achieving comparable performance to full fine-tuning. Some famous efficient fine-tuning methods include:
1. *Adapter Tuning*[^1]: inserts small task-specific modules within layers of the transformer.
2. *Prefix Tuning*[^2]: prepends task-specific trainable prompt tokens to the hidden states within each intermediate Transformer layer.
3. *LoRA*[^3]: merges the low-rank and trainable matrices with the frozen weights at each layer of the Transformer.

However, these efficient finetuning method can only save around  30% of the GPU memory requirements because they rely on gradient-based optimization (Figure 1a). Parameter-efficient tuning on small language model (SLMs) and large language models (LLMs) shows high similarity between the two parameter-efficient modules (Figure 1b). E.g., the cosine similarity can reach 75% between the base and the SL model when both are finetuned with LoRA on specific downstrem task. 

<div style="margin:auto; text-align:left; width:600px">
<img src="/assets/images/2024/2024-01-01_Gradientlesss_motivation.png" alt="image" style="width:600px;height:auto;">
<figcaption> Figure 1: (a) Comparison of GPU memory usage. (b) Cosine similarity of efficiently tuned modules for smaller SLM (base or medium) vs LLM (XL). 
</figcaption>
</div>
<br>
To do this, the authors proposes to use a perform efficient fine-tuning on a SLM and then transfer the learnt task specific characteristics to LLM directly without performing gradient-based optimization on the LLM. Hence, the reseach question addressed in this work is:

> How to use a SLM to efficiently fine-tune LLM for a downstream task?

The author proposed the following strategies:
* Perform efficient fine-tuning on a *small language model (SLM)*.
* *Transfer* the task-specific characteristics from the fine-tuned SLM to a pre-trained large language model (LLM).
* Use a minituarized version of the LLM called the *bridge model* to fine-tune the parameter-efficient model to work together with the LLM.

The contributions of this work are as follows:
* A gradient-free method to adapt parameter-efficient modules learned in SLM to a LLM.
* Saves up to 5.7x memory reduction compared to parameter-efficient modeule
* Performance is on par with fine-tuning and parameter-efficient tuning on LLMs.

<br>

## II. System Overview

The proposed method consists of two parts:

<div style="margin:auto; text-align:left; width:600px">
<img src="/assets/images/2024/2024-01-01_Gradientless_SystemOverview.png" alt="image" style="width:600px;height:auto;">
<figcaption> Figure 2: (a) During training, use a SLM to efficiently learn task-specific characteristics and then fine-tune the bridge model with the acquired parameter-efficient modules to adapt to the LLM for the downstream task. (b) During inference, drop the bridge model and plug the parameter-efficient modules into the LLM for efficient representation.
</figcaption>
</div>
<br>

### Phase 1: Finetuning the model

The authors proposed to learn efficiently on SLM and then transfer the parameter-efficient modules to the LLM. To do that, the method needs to overcome the mismatch in the dimension and number of layers between SLM and LLM.

* First, existing parameter-efficient tuning method, e.g., Adapter[^1], LoRA[^2] or Prefix[^3], is used to fine-tune on a *small language model* (SLM) to learn task-specific characteristics. Then, transfer the task-specific characteristic to large language model (LLM) *without* gradient-based optimization.

* To address dimension mismatch between SLM and LLM, *linear projection module* $W_{plug}$ is employed to align the dimensions of the parameter-efficient modules with the LLM

* To address depth difference between SLM and LLM, the layers of parameter-efficient modules is *duplicated*.

* To allow interaction between the parameter-efficient modules with the LLM, a *bridge model* is used to tune the modules to compliment the LLM for the downstream task. The bridge model bridges the parameter-efficient module and LLM where it retains the the knowledge of LLM while interacting with the parameter-efficient modules. 

**Getting the bridge model**

The bridge model is a minituarized version of the LLM which are constructed by pruning the LLM using the [Ladder-side-tuning](https://proceedings.neurips.cc/paper_files/paper/2022/file/54801e196796134a2b0ae5e8adef502f-Paper-Conference.pdf){:target="_blank"} (LST) algorithm. Given the $W^{[l]} \in  \mathbb{R}^{d_b \times d_l}$, the weight of layer $l$ of the backbone network, the *fisher information* is used to compute the importance of each parameter:

$$ H^{[l]} = \frac{1}{|D|}\sum_{i=1}^{|D|}(\nabla_{W^{[l]}} \log p(y_i|x_i))^2$$

where $(x_i, y_i)$ are samples from the data. Only the top $\frac{d_b}{r}$ rows and $\frac{d_l}{r}$ columns in $H^{[l]}$ are retained with reduction factor $r = {8, 16, 32, 64, ...}$ This results in a smaller weight $W_B \in \mathbb{R}^{\frac{d_b}{r} \times \frac{d_l}{r}}$ which is then used to initilize the bridge model.

**Fine-tuning with Bridge Model**

To allow the parameter-efficient modules interact with the bridge model, a linear pojection module $W_{down}$ is applied on the parameter-efficient model. The parameter-efficient modules and the bridge model are finetuned together. This fine-tuning process enriches the parameter-efficient modules to be enriched with knowledge from the 

### Phase 2: Inference

During the inference phase, the bridge model is discarded and the acquired parameter-efficient module is plugged into the LLM directly for inference. 

<br>
Finally, the complete algorithm is given as follows:

---
**Algorithm 1** Adaptation of Parameter-efficient Modules for Large Language Model

---
**Require**: Large language model *LLM*, a small language model *SLM*, the training set $D = \{(x_1, y_1), ..., (x_n, y_n)\}$, linear projection modules $W_{plug}$ and $W_{down}$, and parameter-efficient tuning methods (*PEFT*) (e.g., Adapter tuning, Prefix tuning, and LoRA)

1. Apply the *PEFT* on *SLM* and fine-tuning *SLM* on $D$ to obtain parameter-efficient modules
2. Employ the $W_{plug}$ to align the dimensions of the parameter-efficient modules with *LLM*
3. Prune the parameters of the *LLM* and get a bridge model $g$
4. Apply the linear projection module Wdown on parameter-efficient modules
5. for each instance $(x_1, y_1)$ in $D$ do
6.    &emsp;Fine-tune the parameter-efficient modules together with the Bridge model $g$
7. end for
8. Plug the parameter-efficient modules and the linear projection modules $W_{plug}$ into the *LLM*

---

<br>
## III Experiments

### A. Experimental setup
* System:
    * NVIDIA A100 GPU (80GB memory)
* Evaluated on 8 NLP task from `SuperGLUE`:
    1. BoolQ
    2. CB
    3. COPA
    4. MultiRC
    5. RTE
    6. WiC
    7. WSC
    8. ReCoRD
* Performance metric:
    1. Accuracy
    2. F1-score
* Evaluated Models:

    |Size of model| Models|
    |:---:|:---:|
    | small | GPT2-base <br> T5-base <br> T5-large | 
    | big | GPT2-XL <br> T5-4B <br> T5-XXL|

* Number of parameters:

    | Small Language Models <br>(SLMs) | Large Language Models <br> (LLMs)<br> (x Size ratio to SLM)|  Bridge Models <br> (BMs)|
    |:---:|:---:|:---:|
    | GPT2-base <br> (117M) | GPT2-XL <br> (1542M, 13x) | 96M |
    | T5-base <br>(220M) | T5-3B <br>(2800M, 12.7x) | 185M |
    | T5-large <br>(770M) | T5-XXL <br>(11000M, 14.3x) | 688M |

* Evaluated methods:

    | Baseline  |Description |
    |:---:|:---|
    | Fine-tuning | The vanilla transformer fine-tuning | 
    
    | Efficient fine-tuning on LLM |Description |
    |:---:|:---|    
    | Adapter tuning[^1] | Insert a small task-specific module betweent he self-attention module and the subsequent residual connecation at eacht arnsformer layer. |
    |Prefix tuning[^2] | Add trainable continuous prompt vectors to the Key and Value components of the attention layer at each layer of the Transformer model. |
    |LoRA[^3] | Merging the low-rank and trainable matrices with the frozen weights at each layer of the Transformer. |

    | Proposed |Description |
    |:---:|:---|
    | Adapter tuning plug-in | Apply the Adapter tuning method to SLM and transfer to LLM. |
    | Prefix tuning plug-in | Apply the Prefix tuning method to SLM and transfer to LLM. |
    | LoRA plug-in | Apply LoRA method to SLM and transfer to LLM. |

### B. Main Results

#### GPT-2 Series of Models
<div style="margin:auto; text-align:left; width:800px">
<img src="/assets/images/2024/2024-01-01_Gradientless_table1.png" alt="image" style="width:800px;height:auto;">
</div>
<br>

Setup:
*  In this experiment, efficient finetuning is performed on GPT2-base model (SLM), and then adapted to the GPT2-XL model (LLM).

Observations:
* For LLM, efficient fine-tuning methods (Adapter, Prefix tuning and LoRA) are not as good but approaching the performance of full fine-tuning. 
* For SLM, efficient fine-tuning methods are comparable to or even outperforms FT for some tasks.
* The proposed method (adapter plug-in, prefix tuning plug-in and LoRA plug-in) achieves comparable results compared to efficient fine-tuning on LLM.

<br>
#### T5 Series of Models
<div style="margin:auto; text-align:left; width:800px">
<img src="/assets/images/2024/2024-01-01_Gradientless_table3.png" alt="image" style="width:800px;height:auto;">
</div>
<br>

* Setup:
    * Use T5-base as SLM and T5-3B as LLM.
* Observations:
    * The proposed methods (adapter plug-in-prefix-tuning plug-in and LoRA plug-in) achieve comparable results on all eight SuperGLUE tasks without any fine-tuning.

<div style="margin:auto; text-align:left; width:800px">
<img src="/assets/images/2024/2024-01-01_Gradientless_table4.png" alt="image" style="width:800px;height:auto;">
</div>
<br>

* Setup:
    * Use T5-large as SLM and T5-XXL as LLM. 
    * Compared to the previous experiments, T5-large (SLM) is 3.5x bigger than T5-base, and similarly for LLM, T5-XXL is 3.9x bigger than T5-3B.
* Observations:
    * Since the models are bigger compared to the previous experiments, the performance is around 2-3 points higher.
    * The proposed methods (adapter plug-in-prefix-tuning plug-in and LoRA plug-in) achieve comparable results on all eight SuperGLUE tasks without any fine-tuning.

### C. Importance of Reduction Factor $r$
<div style="margin:auto; text-align:left; width:800px">
<img src="/assets/images/2024/2024-01-01_Gradientless_table5.png" alt="image" style="width:400px;height:auto;">
</div>
<br>

* Setup:
    * This experiment is used to evaluate the impact of the reduction $r$ on the amount of knowledge retained in the Bridge model. 
* Observation:
    * The size of the bridge model should be comparable to that of the small language model. If it is smaller, there is a noticable performance decline.
    * A good balance between model performance and Bridge parameter is $r=16$.

### D. Memory Usage
<div style="margin:auto; text-align:left; width:800px">
<img src="/assets/images/2024/2024-01-01_Gradientless_table6.png" alt="image" style="width:400px;height:auto;">
</div>

* Setup:
    * batch size = 8, sequence length = 512
* Obervation:
    * At least 6x memory savings compared to full fine-tuning.

<div style="margin:auto; text-align:left; width:800px">
<img src="/assets/images/2024/2024-01-01_Gradientless_table7.png" alt="image" style="width:400px;height:auto;">
</div>

* Setup:
    * batch size = 1, sequence length = 512
* Observations:
    * At least 5x memory savings compared to full fine-tuning.
    * For smaller models, the savings is smaller.

### E. Utilize Bridge Model Directly?

<div style="margin:auto; text-align:left; width:800px">
<img src="/assets/images/2024/2024-01-01_Gradientless_table8.png" alt="image" style="width:400px;height:auto;">
</div>
<br>
* Motivation:
    * Can we use the Bridge model alone without transfering from a finetuned SLM, i.e.,  learn the parameter-efficient modules directly on the Bridge model?
* Observation:
    * Directly using the bridge model consistently performs worse than using the SLM enriched by a bridge model across all tasks.
    


<br>
## IV. References
[^1]: Houlsby, N., Giurgiu, A., Jastrzebski, S., Morrone, B., De Laroussilhe, Q., Gesmundo, A., Attariyan, M. and Gelly, S., 2019, May. [Parameter-efficient transfer learning for NLP](https://proceedings.mlr.press/v97/houlsby19a.html){:target="_blank"}. In International Conference on Machine Learning (pp. 2790-2799). PMLR.
[^2]: Hu, E.J., Shen, Y., Wallis, P., Allen-Zhu, Z., Li, Y., Wang, S., Wang, L. and Chen, W., 2021. [Lora: Low-rank adaptation of large language models](https://arxiv.org/pdf/2106.09685.pdf){:target="_blank"}. In The Tenth International Conference on Learning Representation. ICLR.
[^3]: Li XL, Liang P. [Prefix-tuning: Optimizing continuous prompts for generation](https://arxiv.org/pdf/2101.00190.pdf){:target="_blank"}. In The 11th International Joint Conference on Natural Language Processing. ACL-IJCNLP.