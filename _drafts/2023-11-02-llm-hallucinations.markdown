---
layout: post
title:  "Reducing Hallucinations in LLM"
date:   2023-11-02 08:00:00 +0800
categories: LLM
author: HK
---
References [^1] [^2] [^3]

## Hallucination

Sometimes, even when the model is wrong, it is still over confident. For example, when asked if SVB collapsed, the model answered confidently but wrongly (SVB was closed for good on March 2023)[^1].

```
Q: Did SVB collapse?

A: No, SVB (Silicon Valley Bank) did not collapse. It is still operating as a 
successful financial services company.
``` 

### References
[^1]: T. Stadelmann (deepset.ai). "LLM Hallucinations in RAG QA." <https://www.youtube.com/watch?v=lsZCVmCBRlc>{:target="_blank"} (accessed Nov. 02, 2023).

[^2]: Prolego. "Ep 6. Conquer LLM Hallucinations with an Evaluation Framework." <https://www.youtube.com/watch?v=mW3_vn1zGfE>{:target="_blank"} (accessed Nov. 02, 2023).

[^3]: James Briggs. "Fixing LLM Hallucinations with Retrieval Augmentation in LangChain #6." <https://www.youtube.com/watch?v=kvdVduIJsc8>{:target="_blank"} (accessed Nov. 02, 2023).
