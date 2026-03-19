---
title: Research and Reflections on Controllable Generation in Multimodal Models
published: 2025-12-24
tags: [Blogging]
category: Experiments
draft: false
---

# Introduction:

Regarding multimodal models, the current mainstream approach by companies like Google involves mapping multimodal information and semantic information into the same space. Specifically in autoregressive generative models, this is known as native multimodal models, which is more commonly manifested as:

```bash
cat(text,image)
```

Here, $text$ and $image$ are the token-wise features obtained after encoding semantic information and image information through the Encoder, respectively. After mapping both into the same space, we perform Self-Attention:

```bash
selfAttetion(cat(text,image))
```

The principle is similarly seq2seq, but with the image encoding features also mapped into the sequence space. What's worth thinking about is that current model results have already demonstrated to us that native multimodality has strong fitting capabilities and also stronger semantic understanding. So where is the capability boundary of native multimodal models? I don't have enough understanding of language models, so I can't explain in detail the extent to which semantic understanding can be achieved, but my previous thought was that native multimodal models can theoretically achieve the sum of all current fine-tuned model capabilities, of course, this is just a theoretical value. The reasons are as follows:

We know that the core principle of fine-tuning methods like LoRA is to use:

$$W = W_0 + \Delta W = W_0 + BA$$

Where $A$ and $B$ are both low-rank matrices. In implementation, for many models, LoRA is used for linear layer injection, injecting the results of low-rank matrices into linear layers and convolutional layers, etc., to change the parameters of corresponding layers when enabled. So, is there theoretically a perfect alternative to LoRA? I think a MoE model with an appropriate gating mechanism + multiple Experts can achieve the same effect, where we take the $Top\text{-}K$ value as $1$.

The specific principle is actually not difficult to understand:

For input $x$, when the $Top\text{-}1$ gating $G(x)$ activates the $i$-th expert $E_i$, its linear layer output can be expressed as:

$$y_{MoE} = \sum_{i=1}^{n} G(x)_i \cdot (W_i x) \xrightarrow{Top\text{-}1} W_i x = (W_0 + B_i A_i)x = y_{LoRA}$$

Where $\Delta W_i = B_i A_i$ is the specific task injection corresponding to that expert. This is not difficult to understand mathematically. As long as there is a sufficiently "intelligent" gating mechanism and Expert models corresponding to the gating, it's equivalent to us using learned gating to implicitly control when to use which Expert, and this Expert and LoRA injection can actually be equivalent. So this huge model becomes equivalent to a huge collection containing all fine-tuned models.

So we just need to find $FFN(K) = A_i B_i$ for each corresponding pair, and $FFN$ can obviously fit this process, so the output set $S$ of $FFN$ should contain all LoRA outputs $\{L(i)\}_{i=0}^{n}$.

That is:
$$\{L(i)\}_{i=0}^{n} \subseteq S$$

Of course, this is just a bit of unrealistic fantasy, because even if it exists theoretically, it doesn't mean we can train such a huge model containing all information. At the same time, I'm still quite pessimistic about learning real-world information through fitting methods, because images are actually a projection of the real world, not information compression in semantic space. Traditional graphics are mostly Physics-based, so I still think that if we want to control models more, we need more physical laws to participate in the intermediate process of generative models, rather than a mixed space feature of semantics and other modalities that is difficult to explain.

Before conducting experiments, I hope to do more thinking. As we all know, current generative models, especially multimodal models, whether generating images or 3D models, mostly generate a "shell," or rather fit the appearance more accurately, rather than substantially generating objects. In other words, humans actually don't understand matter from beginning to end, because our research in quantum physics has been difficult to achieve major breakthroughs, so our understanding of the formation of matter is actually quite limited. Can I understand it this way: if we truly understand matter, we relatively also have the ability to create matter? But at least for now, I don't see this ability. Therefore, whether it's computer graphics or computer vision, the task at the current stage is how to fit this "shell" more accurately. I still believe that before humans make breakthroughs in basic research, it's unrealistic to expect AI models to solve problems that were previously difficult for humans to solve.

I will publish our experimental results and reflections on the experimental results in the following articles.

---
