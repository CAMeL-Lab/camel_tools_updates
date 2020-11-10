---
layout: post
title: "CAMeL Tools Release v1.0.0"
---

## Overview

This update marks the first official release of CAMeL Tools.

CAMeL Tools is primarily a collection of Python APIs for Arabic Natural Language
Processing (NLP).
These APIs are meant to be building blocks for NLP applications.

We also provide a collection of command-line utilities to perform some common
Arabic NLP tasks directly in the command line.

In this update, we provide a quick tour of all the features that come in
CAMeL Tools v1.0.0.

The results reported below are extracted from
[the paper by Obeid et al. 2020](https://www.aclweb.org/anthology/2020.lrec-1.868/).

## APIs

CAMeL Tools is primarily a set of APIs for Arabic NLP. Below is a description
of the top-level submodules that are provided:

### camel_tools.utils

A collection of utilities for performing common preprocessing tasks in Arabic
NLP. This includes, cleaning, conversions between various transliteration
schemes, normalization, and de-diacritization.

### camel_tools.morphology

Contains the CAMeL Tools morphological analyzer, generator, and reinflector.

We provide two morphological databases as part of the data download for
CAMeL Tools.

- **calima-msa-r13:** An Modern Standard Arabic database based on the publicly
  available almor-msa-r13 database that ships with MADAMIRA.  
- **calima-egy-r13:** An Egyptian database based on the publicly available
  almor-cra07 database that ships with MADAMIRA.  
  
We also provide access to two additional databases that require access to the
LDC's [Standard Arabic Morphological Analyzer](https://catalog.ldc.upenn.edu/LDC2010L01).

The Analyzer class uses a given morphological database to generate possible
analyses for a given word.

The Generator produces analyses that fit a certain set of pre-defined
morphological features.

The Reinflector combines analysis and generation to allow producing word forms
by reinflecting input words given input features.

### camel_tools.disambig

Disambiguation is the process of finding the most likely analysis of a word.

In the paper, we discuss two disambiguator implementations:
one using maximum likelihood estimation (MLE) and one using multitask learning.

We provide the Diambiguator base class that all diambiguators implement as
well as the MLEDisambiguator which implements the MLE model discussed in the
paper. Our multitask implementation will be provided through a separate
repository.

Comparison of the performance of the CAMeLTools Multitask learning and MLE
systems on MSA to MADAMIRA.

We report on the [LDC](https://catalog.ldc.upenn.edu/) Penn Arabic Treebank
and Egyptian Treebank following the
[Diab et al., 2013](https://arxiv.org/abs/1309.5652) suggested data splits.
See [Obeid et al. 2020](https://www.aclweb.org/anthology/2020.lrec-1.868/)
for more details.

| MSA         |  MADAMIRA | CAMeL Tools<br>Multitask | CAMeL Tools<br>MLE |
|-------------|:---------:|:------------------------:|:------------------:|
| **DIAC**    |   87.7%   |         **90.9%**        |        78.4%       |
| **LEX**     | **96.4%** |           95.4%          |        95.7%       |
| **POS**     |   97.1%   |         **97.2%**        |        95.5%       |
| **FULL**    |   85.6%   |         **89.0%**        |        70.0%       |
| **ATB TOK** |   99.0%   |         **99.4%**        |        99.0%       |

Comparison of the performance of the CAMeLTools MLE system on EGY to MADAMIRA.

| EGY         |  MADAMIRA | CAMeL Tools<br>MLE |
|-------------|:---------:|:------------------:|
| **DIAC**    | **82.8%** |        78.9%       |
| **LEX**     |   86.6%   |      **87.8%**     |
| **POS**     |   91.7%   |      **91.8%**     |
| **FULL**    | **76.4%** |        73.0%       |
| **ATB TOK** | **93.5%** |        92.8%       |

### camel_tools.taggers

This module provides the Tagger base class and the DefaultTagger class, the
default Tagger implementation in CAMeL Tools.
A tagger is just a one-to-one mapping from a token in a sentence to some tag
type (generally a string).
This is a generalization for tagging tasks (POS tagging, diacritization,
lemmatization, etc).

The DefaultTagger utilizes a Disambiguator to perform first disambiguate words
in a sentence and then outputs a specified tag for each word, providing
reasonable defaults when no analyses are returned.

### camel_tools.tokenizers

CAMeL Tools provides utilities for two types of tokenization tasks:

- **Word Tokenization:** Splitting words by whitespace and punctuation.
- **Morphological Tokenization:** Splitting individual words into morphemes.

For word tokenization, we provide a very simple punctuation and whitespace
splitter. For morphological tokenization, we provide the MorphologicalTokenizer
class that wraps a disambiguator to output one of the tokenization features from
the disambiguated analysis. In this sense, the MorphologicalTokenizer acts like
a Tagger except it is a one-to-many map.

### camel_tools.dialectid

We provide a dialect identification system based on the system described by
[Salamah, Bouamor and Habash (2018)](https://www.aclweb.org/anthology/C18-1113/).
It classifies a sentence into one of 25 Arabic city dialects and Modern Standard
Arabic.
Our system is the only publicly available implementation of their work.

We also provide utility functions to sum the output probability scores in order
to output a country dialect probability score and a region dialect probability
score.

Below is a comparison of our dialect identification system with the top two
systems from the
[MADAR Shared Task](https://www.aclweb.org/anthology/W19-4622/).
Our results differ only slightly from Salameh et al (2018)'s results due to
minor implementation variance.

| Metric           |   CAMeL Tools   | (Salameh et al 2018) | ArbDialectID |
|------------------|:---------------:|:--------------------:|:------------:|
| **F1**           |    **67.85%**   |       (67.89%)       |    67.32%    |
| **Precision**    |    **68.36%**   |       (68.41%)       |    67.60%    |
| **Recall**       |    **67.71%**   |       (67.75%)       |    67.29%    |
| **Acc. City**    |    **67.71%**   |       (67.75%)       |    67.29%    |
| **Acc. Country** |    **76.42%**   |       (76.44%)       |    75.23%    |
| **Acc. Region**  |    **85.92%**   |       (85.96%)       |    84.42%    |

An online demo of this tool can be found
[here](https://adida.abudhabi.nyu.edu/#/).

### camel_tools.sentiment

CAMeL Tools sentiment analyzer performance using AraBERT and mBERT compared to
Mazajak over three benchmark datasets.
The results are reported in terms of macro F1 score over the positive and
negative classes.
Please contact the authors for details about the data sets and splits.

|             | CAMeL Tools<br>AraBERT | CAMeL Tools<br>mBERT | Mazajak |
|-------------|:----------------------:|:--------------------:|:-------:|
| **ArSAS**   |         **92%**        |          89%         |   90%   |
| **ASTD**    |         **73%**        |          66%         |   72%   |
| **SemEval** |         **69%**        |          60%         |   63%   |

### camel-tools.ner

The results of the proposed system when trained and tested on ANERcorp
dataset vs. CRF-based system (Benajiba and Rosso, 2008).  The exact
data and splits are provided [here](https://camel.abudhabi.nyu.edu/anercorp/).

| CAMeL Tools           | Precision |  Recall |    F1   |
|-----------------------|:---------:|:-------:|:-------:|
| **LOC**               |    88%    | **92%** | **90%** |
| **MISC**              |    68%    | **58%** | **63%** |
| **ORG**               |    77%    | **70%** | **73%** |
| **PERS**              |  **89%**  | **85%** | **87%** |
| **Overall**           |    84%    | **81%** | **83%** |

| Benajiba & Rosso 2008 | Precision | Recall |    F1   |
|-----------------------|:---------:|:------:|:-------:|
| **LOC**               |  **93%**  |   87%  | **90%** |
| **MISC**              |  **71%**  |   54%  |   61%   |
| **ORG**               |  **84%**  |   54%  |   66%   |
| **PERS**              |    80%    |   67%  |   73%   |
| **Overall**           |  **87%**  |   73%  |   79%   |

## Command-line Tools

We also provide a collection of command-line tools for performing various tasks
directly from the terminal without having to write a script. These are just
wrappers over the APIs provided.

Below is a list of the available command-line tools:

- **camel_transliterate** Convert Arabic text between different transliteration
  schemes.
- **camel_arclean** Clean Arabic text.
- **camel\_word\_tokenize** Tokenize Arabic text in various tokenization
  schemes.
- **camel_dediac** Dediacritize Arabic text.
- **camel_morphology** Provides access to the morphological analyzer, generator,
  and reinflector.
- **camel_diac** Diacritize Arabic text.
