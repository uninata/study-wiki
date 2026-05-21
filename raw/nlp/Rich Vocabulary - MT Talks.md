---
title: "Rich Vocabulary - MT Talks"
source: "https://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Rich_Vocabulary"
author:
published:
created: 2026-05-03
description:
tags:
  - "clippings"
---
## Examples of Languages with a Rich Vocabulary

### German -- compounding

While German has some degree of inflection, it is the Germans' fondness of complex word compounds that causes the large vocabulary problem for MT. Consider the following compound:

![](https://mttalks.ufal.ms.mff.cuni.cz/images/a/aa/Rindfleish-prezi.png)

### Finnish -- agglutination

Agglutinative languages (such as Finnish, Turkish or Hungarian) often attach many affixes (prefixes or suffixes) to words. These affixes can describe grammatical properties or change the word meaning, as shown in the example:

![](https://mttalks.ufal.ms.mff.cuni.cz/images/b/b7/Finnish-prezi.png)

For Finnish, nouns are said to have over 2000 possible inflections. The number of unique word forms in Finnish can therefore be astronomical.

### Czech -- fusional inflection

Fusional languages differ from agglutinative languages in that they *fuse* multiple properties into a single affix. In Czech, one suffix can describe case, gender and number at the same time. On the other hand, fusional affixes tend to be ambiguous (e.g. an identical suffix can be used for multiple morphological cases).

Morphologically rich languages tend to impose strong agreement constraints on the suffixes (adjetive inflection must agree with its governing noun, subject and objects must agree with the verb inflection). Consider the following example:

![](https://mttalks.ufal.ms.mff.cuni.cz/images/7/7f/Czech-inflection-prezi.png)

## Large Vocabulary Sizes in MT Pipeline

Let us describe how large vocabulary size affects each step in the standard MT pipeline.

### Word Alignment

Word alignment treats different inflection of one word as unrelated units. This prevents the algorithm from sharing statistics and results in sparse observations for inflected forms.

### Phrase Extraction

Rich vocabulary along with Zipf's law imply that we cannot expect to see all possible word inflections in our training data. We can be simply unable to create the correct word form because we have never observed it -- even though we may have sufficient statistics for the word lemma.

### Decoding

Each extra word form creates an additional branching point for the search which the decoder must evaluate. There is an inherent trade-off for decoding between time (or even tractability) and quality: we can increase pruning limits to avoid discarding the potentially correct word forms but this comes at a high computational cost.

In short, when dealing with rich vocabulary, we might not even have the correct form available and when we do, we still might not find due to pruning.

### Evaluation

Rich vocabulary even presents a problem for MT evaluation. Automatic metrics are (mostly) defined in a language-agnostic way and do not look beyond surface forms. Metrics such as [BLEU](https://mttalks.ufal.ms.mff.cuni.cz/index.php/Automatic_MT_Evaluation "Automatic MT Evaluation") therefore can't distinguish a small error in inflection from a serious mistranslation of the given word.

## Possible Solutions

Whenever our task is not to *produce* the rich word forms, we can often use some coarse representation of the words. For compounds, various algorithms for compound splitting have been proposed -- these can easily be used on the source side (when we're translating *from* German). For fusional languages, we can simply use word lemmas instead of the inflected forms (or stems, or even just 4- or 5-letter prefixes); this applies also to word alignment and MT evaluation (even on the target side).

On the target side (when we have to produce the inflected forms), more involved techniques are required.