---
layout: post
title:  "Introduction to Visual Language Models (VLM)"
date:   2023-12-12 
categories: Deep Learning
author: "HK"
---

## Pre-training 

**Dataset**. The dataset normally used to pretrain the dataset are:

||train|val|Annotation|Overlap with <br> downstream task?|
|---|:---:|:---:|:---:|:---:|
|COCO Captions | 533K (106K) | 25K (5K)| human| in-domain|
|VG Dense Captions| 5.06M (101K) | 106K (2.1K) |human|in-domain|
|Conceptual Captions 3M (CC3M)| 3.0M (3.0M) | 14K (14K) | web, noisy|out-of-domain|
|Conceptual Captions 12M (CC12M) | ? |? |web, noisy|out-of-domain|
|SBU  Captions | 990K (990K) | 10K (10K) |web, noisy|out-of-domain|
|LAION400M | ? (?) | ? (?) | ? |out-of-domain|

#### Pretraining details
For BLIP, the pre-training details are as follows:
* System: 2 nodes x 16 GPUs = 32 GPUs
* Batch size: 2880 (ViT-B) and 2400 (ViT-L)
* ViT is pretrained on ImageNet, Text transformer is initialized from  $\text{BERT}_{base}$.
* Scheduling: Warmed up to 0.0003 (ViT-B) or 0.0002 (ViT-L), and decayed linearly with a rate of 0.85
* Weight decay = 0.05
* Number of epochs: 20
* Optimize: AdamW

 > Notes: On one RTX2080TI 12GB, BLIP could only handle a maximum batch size of 2 and completed 1 epoch in 2 hours plus when training the VizWiz dataset (20523 samples). That means that the inference speed is around 700ms per batch.
