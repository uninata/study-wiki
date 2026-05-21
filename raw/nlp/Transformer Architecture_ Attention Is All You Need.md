---
title: "Transformer Architecture: Attention Is All You Need"
source: "https://medium.com/@adityathiruvengadam/transformer-architecture-attention-is-all-you-need-aeccd9f50d09"
author:
  - "[[Aditya Thiruvengadam]]"
published: 2018-10-09
created: 2026-05-03
description: "Aditya Thiruvengadam highlighted"
tags:
  - "clippings"
---
In this post, we are going to explore the concept of attention and look at how it powers the “Transformer Architecture” which thus demonstrates why “Attention Is All You Need!”

**Introduction:**

Whenever long-term dependencies (natural language processing problems) are involved, we know that RNNs (even with using hacks like bi-directional, multi-layer, memory-based gates — LSTMs/GRUs) suffer from vanishing gradient problem. Also, they handle the sequence of inputs 1 by 1, word by word this resulting in an obstacle towards parallelization of the process.

Especially when it comes to seq2seq models, is one hidden state really enough to capture global information pertaining to the translation?

**The problem with this approach was (as famously said at the ACL 2014 workshop):**

```c
“You can’t cram the meaning of a whole %&!$# sentence into a single $&!#* vector!”
```

Here’s where attention comes in!

**Attention, in general, can be thought of as follows:**

The **idea here is to learn a context vector** (say *U*), which gives us global level information on all the inputs and tells us about the most important information (this could be done by taking a **cosine similarity** of this context vector U w.r.t the input hidden states from the fully connected layer. We do this for each input *x\_i* and thus obtain a *theta\_i* (attention weights).

i.e.: theta\_i = cosine\_similarity(U, x\_i)

For each of the input hidden states *x\_1 … x\_k*, we learn a set of weights *theta\_1* to *theta\_k* which measures how much of the inputs answer the query and this generates an output

![](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*nw42W_hmp7Kw8oRwfzjUPA.png)

The weights for the inputs of attention are learned to understand which inputs it should attend to

**For an encoder-decoder architecture:**

For every single target decoder output ( say, *t\_j* ), all hidden state source inputs (say s\_i’s) are taken into account to compute the cosine similarity with the source inputs *s\_i*, to generate the *theta\_i’s* (attention weights) for every *s\_i*.

i.e. theta\_i = cosine\_similarity(t\_j, s\_i)

The context vector (*out* — refer to the above equation) is now computed for every source input *s\_i* and *theta\_i* (generated for the corresponding target decoder word t\_j). The context vector (out) and target word (t\_j) are used to predict the output in the decoder architecture, which is then daisy chained and continued from here on in the above manner using attention.

Attention mechanism solves this problem by allowing the decoder to “look-back” at the encoder’s hidden states based on its current state. This allows the decoder to extract only relevant information about the input tokens at each decoding, thus learning more complicated dependencies between the input and the output.

**This allows the decoder to capture global information rather than to rely solely based on one hidden state!**

Here, we see that the dependencies are learned between the inputs and outputs.

**But, in the Transformer architecture this idea is extended to learn intra-input and intra-output dependencies as well (we’ll get to that soon!)**

**Attention Definition according to the Transformer paper:**

![](https://miro.medium.com/v2/resize:fit:1238/format:webp/1*Bco4VRvdeeNthFrZkYx5_g.png)

Attention as explained by the Transformer Paper

An attention function can be described as mapping a query (Q) and a set of key-value pairs (K, V) to an output, where the query, keys, values, and output are all vectors. The output is computed as a weighted sum of the values, where the weight assigned to each value is computed by a compatibility function of the query with the corresponding key.

**According to our original definition:**

The attention weights are the relevance scores of the input encoder hidden states (values), in processing the decoder state (query). This is calculated using the encoder hidden states (keys) and the decoder hidden state (query).

In the case of Neural Machine Translation, both keys and values are encoder hidden states (leading to Self-Attention — we’ll get to that soon!)

**But, Why do we need the Transformer?**

**RNN**:

- **Advantages**: are popular and successful for variable-length representations such as sequences (e.g. languages), images, etc. RNNs are considered the core of seq2seq (with attention). The gating models such as LSTM or GRU are for long-range error propagation.
- **Problems**: The sequentiality prohibits parallelization within instances. Long-range dependencies still tricky, despite gating. Sequence-aligned states in RNN are wasteful. Hard to model hierarchical-alike domains such as languages.

**CNN**:

- **Advantages**: Trivial to parallelize (per layer) and fit intuition that most dependencies are local.
- **Problems**: Path length between positions can be logarithmic when using dilated convolutions, left-padding for text. (autoregressive CNNs WaveNet, ByteNET)

**Objective or goal for the architecture:**

- **Parallelization of Seq2Seq:** RNN/CNN handle sequences word-by-word sequentially which is an obstacle to parallelize. Transformer achieves parallelization by replacing recurrence with attention and encoding the symbol position in the sequence. This, in turn, leads to significantly shorter training time.
- **Reduce sequential computation:** Constant O(1) number of operations to learn dependency between two symbols independently of their position distance in sequence.

One solution to this is Hierarchical Convolution Seq2Seq architecture ([https://arxiv.org/abs/1705.03122](https://arxiv.org/abs/1705.03122))

The intuition here is that close input elements interact in the lower layers, while long-term dependencies are captured at the higher layers.

However, in those CNN-based approaches, the number of calculations in the parallel computation of the hidden representation, for input → output position in the sequence, grows with the distance between those positions (architecture grows in height). The complexity of O(n) for ConvS2S and O(nlogn) for ByteNet makes it harder to learn dependencies on distant positions.

As an alternative to convolutions, a new approach is presented by the **Transformer**. It proposes to encode each position and applying the attention mechanism, to relate two distant words of both the inputs and outputs w.r.t itself, which then can be parallelized, thus accelerating the training.

The Transformer reduces the number of sequential operations to relate two symbols from input/output sequences to a constant O(1) number of operations. Transformer achieves this with the multi-head attention mechanism that allows to model dependencies regardless of their distance in input or output sentence.

**Now, Let’s Breakdown the Transformer’s Encoder and Decoder Architecture:**

The encoder and decoder both are both composed of a stack of identical layers, whose main components are as follows:

1. Multi-Head Self-Attention Mechanism
2. Position-Encoding and Position-Wise Feed Forward NNs

In contrast to the CNN based approach, which tackles the sequence problem and the position problem in one go by encoding absolute positional information along with the embeddings, here the Transformer uses 2 different NNs to capture this information (as described below).

The way this attention is integrated makes this architecture special!

**Let’s look at the Multi-Head Attention and Positional Encoding which forms the basis of this Architecture:**

**1.Multi-Head Self-Attention Attention:**

![](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*5h3HHJh7kgezyOdTcRZc0A.png)

The Scaled Dot Product Multi-Head Self-Attention Architecture

The transformer adopts the **Scaled Dot-Product Attention**:

The output is a weighted sum of the values, where the weight assigned to each value is determined by the dot-product of the query with all the keys:

![](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*lH5NKkkZjsGvjQmlis3_uw.png)

Scaled Dot-Product Attention Equation

![](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*NlQPdpNY4d26l8Vu92a0Wg.png)

The Internal Architecture of Scaled Dot-Product Attention

Unlike the commonly used additive based attention function (first definition above), this architecture uses the multiplicative attention function.

**Even though both have the same theoretical complexity, the Scaled Dot-Product is chosen due to it being much faster and space-efficient, as it uses an optimized matrix multiplication code.**

## Get Aditya Thiruvengadam’s stories in your inbox

Join Medium for free to get updates from this writer.

**The intuition behind self-attention is as follows:**

- In a regular encoder-decoder architecture, we fact the problem of long-term dependencies (whether it be LSTM/GRUs or CNNs)
- To eliminate this, for every input word’s representation we learn the attention distribution with every other word (as pairs) and use said distribution with every pair of words as weights of a linear layer and compute a newer representation for each input representation
- This way, not only at the connection between the encoder and the decoder (the end of the sequence) but even at the starting, each input representation has global level information on every other token in the said sequence

Rather than computing single attention (weighted sum of values), the “Multi-Head” Attention computes multiple attention weighted sums, hence the name.

Each of these “Multiple-Heads” is a linear transformation of the input representation. This is done so that different parts of the input representations could interact with different parts of the other representation to which it is compared to in the vector space.

This provides the model to capture various different aspects of the input and improve its expressive ability.

==**Essentially, the Multi-Head Attention is just several attention layers stacked in parallel, with different linear transformations of the same input.**==

**2.Position-Encoding and Position-Wise Feed Forward NN:**

With no recurrence or convolution present, for the model to make use of the order of the sequence, we must inject some information about the relative or absolute position of the tokens in the sequence to the embeddings.

The positional encodings have the same dimensions of the embeddings (say, d), so that they can be summed up. Here, 2 sinusoids (sine, cosine functions) of different frequencies are used:

```c
PE(pos,2i) = sin(pos/100002i/d)PE(pos,2i+1) = cos(pos/100002i/d)
```

Where *pos* is the position of the token and *i* is the dimension.

The wavelengths form a geometric progression from 2π to 10000⋅2π. We chose this function because they hypothesized it would allow the model to easily learn to attend by relative positions, since, for any fixed offset k, PE(pos+k) can be represented as a linear function of PE(pos).

Instead of fixing said positional encodings, a learned set of representation is also providing the same result as the above.

The most important part here is the “Residual Connections” around the layers. This is very important in retaining the position related information which we are adding to the input representation/embedding across the network. The network displayed catastrophic results on removing the Residual Connections.

The authors have also discussed concatenation of the positional embeddings instead of adding them (ref: Allen NLP podcast). They were in the process of doing said experiments, but their initial results seem to say that the residual connections there can be mainly applied to the concatenated positional encoding section to propagate it through.

**Let’s now look at the architecture:**

![](https://miro.medium.com/v2/resize:fit:1252/format:webp/1*JuGZaZcRtmrtCEPY8qfsUw.png)

The Transformer Architecture

**Encoder:**

- Encoder Input is created by adding the Input Embedding and the Positional Encodings
- ’N’ layers of Multi-Head Attention and Position-Wise Feed Forward with Residual Connections employed around each of the 2 sub-layers followed by a layer of Normalization
- Dropouts are also added to the output of each of the above sublayers before it is normalized.

In the encoder phase, the Transformer first generates Initial Inputs (Input Embedding + Position Encoding) for each word in the input sentence.

For each word, self-attention aggregates information from all other words (pairwise) in the context of the sentence, thus creating a new representation for each word — which is an attended representation of all other words in the sequence.

This is repeated for each word in a sentence successively building newer representations on top of previous ones multiple times.

**Decoder:**

- Decoder Input is the Output Embedding + Positional Encoding, which is offset by 1 position to ensure the prediction for position *i* depends only on the positions before *i*
- N layers of Masked Multi-Head Attention, Multi-Head Attention and Position-Wise Feed Forward Network with Residual Connections around them followed by a Layer of Normalization
- Masked Multi-Head Attention to prevent future words to be part of the attention (at inference time, the decoder would not know about the future outputs)
- This is followed by Position-Wise Feed Forward NN

The decoder generates one word at a time from left to right. The first word is based on the final representation of the encoder (offset by 1 position)

Every word predicted subsequently attends to the previously generated words of the decoder at that layer and the final representation of the encoder (Multi-Head Attention) — similar to a typical encoder-decoder architecture.

**Best way to understand the flow:**

![](https://miro.medium.com/v2/resize:fit:1280/format:webp/1*sbfNVjf3yERRD9Rg4OLIBw.gif)

Google’s Visualization describes it all!

It is worth noting how this self-attention strategy tackles the issue of **co-reference resolution** where e.g. word “it” in the sentence “The animal didn’t cross the street because it was too tired.” can refer to different noun (animal or street) of the sentence depending on context.

- Since the model can visualize other parts of a sentence the network attends to when processing or translating a given word, it gains insights into how information travels through the network.
- Visualizing what words the encoder attended to when computing the final representation for the word “it” sheds some light on how the network made the decision. In one of its steps, the Transformer clearly identified the two nouns “it” could refer to and the respective amount of attention reflects its choice in different contexts.
![](https://miro.medium.com/v2/resize:fit:1310/format:webp/1*sV28bBFHMP81J11zAppnOA.png)

**The Transformer uses Multi-Head Attention in three different ways:**

1. The encoder internally contains self-attention layers. In a self-attention layer, all of the keys, values and the queries come from the same place, in this case — the output of the previous layer of the encoder. The input to the multi-head self-attention Is the input sequence itself (the keys, values and also the queries in various linear transformed heads)
2. In the encoder-decoder attention layers, the queries come from the previous decoder layer, and the keys and values come from the output of the encoder. This allows every position in the decoder to attend over all the positions in the input sequence (similar to the typical encoder-decoder architecture)
3. Similarly, self-attention layers in the decoder will allow each position in the decoder to attend to all positions in the decoder up to and including that position.
4. To prevent the leftward information flow in the decoder, masking support is implemented inside of the scaled dot-product attention by masking out all values in the input of the softmax of the multi-head attention which corresponds to illegal connections (masking of future/subsequent words).

**Types of problems the algorithm well suited?**

- sequence transduction (language translation)
- classic language analysis task of syntactic constituency parsing
- different inputs and outputs modalities, such as images and video
- co-reference resolution

**Future Work:**

- Application of the same to images and videos
- Trying different methods of positional encoding schemes (adding vs concatenation with the word ==embeddings==, learned vs preset positional encoding etc.)

**Resources to check out — References:**

1. [https://arxiv.org/abs/1706.03762](https://arxiv.org/abs/1706.03762)
2. [https://ai.googleblog.com/2017/08/transformer-novel-neural-network.html](https://ai.googleblog.com/2017/08/transformer-novel-neural-network.html)
3. [https://ai.googleblog.com/2017/06/accelerating-deep-learning-research.html](https://ai.googleblog.com/2017/06/accelerating-deep-learning-research.html)
4. [http://nlp.seas.harvard.edu/2018/04/03/attention.html](http://nlp.seas.harvard.edu/2018/04/03/attention.html)
5. [https://mchromiak.github.io/articles/2017/Sep/12/Transformer-Attention-is-all-you-need/#.W6EvHRMza-p](https://mchromiak.github.io/articles/2017/Sep/12/Transformer-Attention-is-all-you-need/#.W6EvHRMza-p)
6. [https://www.cloudsek.com/announcements/blog/hierarchical-attention-text-classification/](https://www.cloudsek.com/announcements/blog/hierarchical-attention-text-classification/)
7. [https://medium.com/syncedreview/a-brief-overview-of-attention-mechanism-13c578ba9129](https://medium.com/syncedreview/a-brief-overview-of-attention-mechanism-13c578ba9129)
8. [http://mlexplained.com/2017/12/29/attention-is-all-you-need-explained/](http://mlexplained.com/2017/12/29/attention-is-all-you-need-explained/)