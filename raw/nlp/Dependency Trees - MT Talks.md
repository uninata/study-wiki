---
title: "Dependency Trees - MT Talks"
source: "https://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Dependency_Trees"
author:
published:
created: 2026-05-03
description:
tags:
  - "clippings"
---
## Dependency Tree

A dependency tree is a rooted directed tree with a defined linear ordering of its nodes. The nodes correspond to words in a sentence (in a [constituency tree](https://mttalks.ufal.ms.mff.cuni.cz/index.php/Constituency_Trees "Constituency Trees"), they could be either nonterminals or words) and the directed edges mark *dependency relations* between nodes.

In most linguistic theories, verbs control their arguments. Therefore, e.g. the subject of a sentence will typically depend on the predicate. Modifiers of the subject (such as adjectives) will depend on the subject node and so on.

The following is a (Prague-style) dependency parse of the sentence *Jim gave his brother a new toy yesterday.*

![](https://mttalks.ufal.ms.mff.cuni.cz/images/5/5e/Deptree.png)

## Non-Projectivity

In some cases, dependency trees can be non-projective. Intuitively, we can recognize a non-projective tree by drawing downward lines from the nodes ("projecting" them). If any line crosses a dependency edge, the tree is non-projective, such as the following (the non-projective edge is drawn in red):

![](https://mttalks.ufal.ms.mff.cuni.cz/images/6/6b/Nonprojective.png)

A more formal definition involves the notions of linear and dependency ordering and can be found e.g. [here](http://ufal.mff.cuni.cz/~lopatkova/2015/docs/1-intro-trees.pdf). Note that alternative definitions are sometimes used which may and may not be always equivalent.

In the data, roughly one quarter of Czech sentences contain a non-projective edge, while only about 8 percent of English sentences do.

Fortunately, even for Czech, most non-projective sentences contain at most one "gap". Mildly context-sensitive grammars, such as the tree-adjoining grammar (TAG), can be used to analyze such sentences.