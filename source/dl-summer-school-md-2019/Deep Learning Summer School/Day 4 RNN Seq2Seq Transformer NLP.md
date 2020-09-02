---
title:  Day 4 RNN / Seq2Seq → Transformer / NLP
date: 2019-10-07 14:32:46
---



# Recurrent Neural Networks

https://github.com/mila-iqia/ivado-mila-dl-school-2019/blob/master/presentations/day4/1_recurrent_neural_networks.pdf

## Motivation

MLP: Fully Connected

CNN: with convolution

How can we handle sequences of variable size?

MLP is fixed size, thus can't handle sequences of variable size.

Some techniques (bag of words: mapped to fix variables) allow an MLP to handle sequences of variable size, but those techniques ignore the order of the elements in the sequence.

A CNN **can** operate on sequences, but:

- the receptive field is limited by the filter size.
- more layers are needed to capture information from the entire sequence.
- longer sentences can still fall out of the receptive field.

### Example in real world

- Audio:
    - Speech recognition
    - Text to speech
- Video:
    - Caption generation
    - Movement detection
- Text:
    - Email classification
    - Machine translation
- Medical and Biological data:
    - DNA study
    - Electrocardiogram
- Time series (stocks, weather, ...)
- Etc...

### Modeling Sequences

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled.png)

## RNN

### Explain with example

Named Entity Recognition (NER) problem: assign a label that represents an entity class to every word in an input.
In this example, possible labels are: “PER” (person), “LOC” (location), “-” (not a named entity).

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%201.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%201.png)

### RNN - Formalization

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%202.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%202.png)

### RNN - Implementation

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%203.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%203.png)

`g` is softmax, sigmoid, ... (non linear activation function)

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%204.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%204.png)

## Training RNNs

### Training Error

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%205.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%205.png)

Calculate the gradients of global error:

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%206.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%206.png)

With chain rule, we need to take `U` in, however they are used more than once:

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%207.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%207.png)

### Backpropagation through time

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%208.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%208.png)

More going to right, the formula going longer.

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%209.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%209.png)

## Training problems

### Long-Term Dependencies

→ Long chain of gradients: we need to multiply by `W` several times (= # sequence).

→ The result can `explode` (`W` too big) or `vanish` (`W` too small)

### Solving Exploding Gradient

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2010.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2010.png)

### No cure for vanishing gradient

## RNN architecture

### LSTM

[Understanding LSTM Networks](https://colah.github.io/posts/2015-08-Understanding-LSTMs/)

### GRU

Fast computation, since less complicated on architecture.

## Deep RNNs

NLP: 12-22 layers

### More than 1 layer

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2011.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2011.png)

### Bidirectional RNNS

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2012.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2012.png)

---

# Seq2Seq

[](https://github.com/mila-iqia/ivado-mila-dl-school-2019/blob/master/presentations/day4/2_sequence_to_sequence_models.pdf)

## Model

Input size and output size can be unequal. 

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2013.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2013.png)

### Autoregressive RNNs

Decoder will take the output of last decoder.

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2014.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2014.png)

<SOS>: start of sequence

<EOS>: end of sequence

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2015.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2015.png)

## Attention Mechanism

[Attention and Augmented Recurrent Neural Networks](https://distill.pub/2016/augmented-rnns/)

### bottleneck on `h_n`

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2016.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2016.png)

### Selectively look back at the encoded input sequence

The attention mechanism is try to find out which information from encoder is more import than the other.

Attention is a function `A` that given a decoder state `s` and an encoded input sequence `h` that are important at the current decoding time step.

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2017.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2017.png)

The context vector `c` is then fed to decoder.

### Example: Seq2Seq + attention

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2018.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2018.png)

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2019.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2019.png)

### Image caption generation

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2020.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2020.png)

with attention visualized:

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2021.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2021.png)

## Transformer

[](https://papers.nips.cc/paper/7181-attention-is-all-you-need.pdf)

RNNs suffers from:

- not easy to parallelize
- hard to capture **very** long term dependencies even with LSTM

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2022.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2022.png)

- Several key points:
    - Recurrence replaced with self-attention and multi-head attention.
    - Positional encodings: position of work.
    - Residual connections: ResNet.
    - Layer normalization: normalized on the layer dimension (time)
    - Position-wise feed-forward networks.

### Self-attention

The attention mechanism is used to identify the elements in a sequence which are “relevant” to **encode** the current one.

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2023.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2023.png)

### Self-attention → Multiple Heads

different heads are learning different information.

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2024.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2024.png)

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2025.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2025.png)

### Performance & intuition from self-attention

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2026.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2026.png)

### Advantages

- Can be computed in parallel
- no dependencies between time steps
- No chain of computation
- The information does not need to flow over a long chain of elements

---

# NLP

[](https://github.com/mila-iqia/ivado-mila-dl-school-2019/blob/master/presentations/day4/3_natural_language_processing.pdf)

## Natural Language Processing

> “Natural Language Processing (NLP) is a subfield of computer science, information engineering, and artificial intelligence concerned with the interactions between computers and human (natural) languages, in particular how to program computers to process and analyze large amounts of natural language data.”

### Tasks

- Classification (word-level)
    - Named Entity Recognition

    ![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2027.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2027.png)

    - Part of Speech Tagging
    - Extractive Question Answering
- Classification (sentence-level)
    - Sentiment Analysis

    ![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2028.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2028.png)

    - Spam Filters
- Classification (sentence pair-level)
    - Entailment: identify the relationship between two sentences → `entailment`, `contradiction`, `neutral`

    `SEP` = separator indicating the end of the first sentence

    ![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2029.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2029.png)

    - Sentence similarity
- Generative
    - Machine translation requires a sequence-to-sequence (encoder plus decoder)
    model.
        - The encoder parses the input.
        - The decoder produces the output (using an autoregressive approach).
        - Attention (between encoder and decoder) greatly improves results.
    - Abstractive Text Summarization
    - Abstractive Question Answering

## Words and Semantics

We need some way to link words and semantics.

### Distributional Hypothesis

- “Words that occur in the same contexts tend to have similar meanings (Harris, 1954).”
- “You shall know a word by the company it keeps” Firth, J. R. 1957:11
- The distributional hypothesis suggests that **the more semantically similar** two words are, **the more distributionally similar** they will in turn be, and thus the more that they will tend to occur in similar linguistic contexts.

### Principle of Compositionality

> “In mathematics, semantics, and philosophy of language, the principle of compositionality is the principle that the meaning of a complex expression is determined by the meanings of its constituent expressions and the rules used to combine them.”

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2030.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2030.png)

### Linking Words and Semantics

- The distributional hypothesis is a promising way to generate semantics for a
word by looking at the context where the word appears.
- The principle of compositionality allows us to tackle the NLP tasks in a
hierarchical way.
- Both can help us in creating algorithms to solve NLP tasks.

## Classical Approaches

### Tokens

A token is a unit in a text sequence: characters (A-Z, 0-9, etc..), subwords (er, est, ...), words.

### Vocabulary Size

The # of tokens in task: words > subwords > characters

Small vocabulary is better.

### Sentence Length

Smaller the token unit, the longer the representation of a sentence.

Short representation is better.

### Out of Vocabulary

OOV: is a token that does not appear in training.

Subword/character can do better than words.

### Summary of Advantages x Disadvantages

- Subwords are usually a good compromise that avoids the two extreme cases of
having a too big vocabulary and having too long sentences.
- They are also less affected by the OOV problem.

### Token Represetation

1-hot representation: 

- vector of length of vocabulary size.
- Words represented as one hot vectors are all at the same distance of each other.

Bag of word representation → sum of 1-hot vectors → lose the order of the words

## Word Embeddings

1-hot vectors are local representations: 1:1 mapping between a word and a position in a vector.

### Distributed representation

A different approach is to distribute the representation of a word across all available dimensions in a continuous space in a way that leads to “similar” words being close to each other. where:

- Semantically similar words can be close to each other.
- Relationships between words can be captured.

[Get Busy with Word Embeddings - An Introduction](https://www.shanelynn.ie/get-busy-with-word-embeddings-introduction/)

Distributed representations for words are called **word embeddings**.

### Word2Vec

- Word2Vec is an efficient way to compute word embeddings.
- It is inspired from the distributional hypothesis.
- It is based on a very simple linear model.
- There are two versions:
    - Continuous bag-of-words: mask a word in a sequence of text → train a model to predict this word given the words surround it (context)

    ![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2031.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2031.png)

    - Continuous Skip-Gram.

    ![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2032.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2032.png)

### FastText

Instead of only on word, it also done on subword.

[Enriching Word Vectors with Subword Information](https://arxiv.org/abs/1607.04606)

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2033.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2033.png)

### Pre-Training

Leverage pre-trained representation into real task of interest.

## Contextualized Word Embeddings

Word Embeddings help synonymy but not polysemy. The word embedding don't consider the context.

### ELMo

[ELMo](https://allennlp.org/elmo)

- ELMo (Embeddings from Language Models) proposes a solution to two
problems:
    - Generate **contextualized** word embeddings...
    - ... in an unsupervised pre-training phase.
- To pre-train, they select the Language Modeling (LM) task.

### Language Modeling

Given n words (from a sentence), predict the next word (n+1). → unsupervised task → can work 'right-to-left':

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2034.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2034.png)

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2035.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2035.png)

### ELMo

it is a N-layer bidirectional LSTM:

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2036.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2036.png)

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2037.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2037.png)

[Visualizing ELMo Contextual Vectors](https://towardsdatascience.com/visualizing-elmo-contextual-vectors-94168768fdaa)

## BERT

- BERT (Bidirectional Encoder Representations from Transformers) improved results over many NLP tasks by:
    - using a Transformer-based architecture;
    - pre-training on two unsupervised tasks:
        - Masked Language Modeling (MLM) instead of Language Modeling;
        - Next sentence prediction.

### Masked Language Model

Randomly masked, but no left-to-right or right-to-left. the model has access to both the past and the future.

### Next Sentence Prediction

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2038.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2038.png)

### BERT - training phase

![Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2039.png](Day%204%20RNN%20Seq2Seq%20Transformer%20NLP/Untitled%2039.png)

## Summary

- Natural Language Processing includes many types of tasks.
- These tasks share some common problems such as the need to link words to semantics.
- Several algorithms have been developed to address these problems, mainly word embeddings (such as Word2Vec / FastText) and contextualized word embeddings (such as ELMo / BERT).
- BERT is able to address many NLP tasks with a common core architecture (based on the Transformer).
- All these algorithms (Word2Vec / FastText / ELMo / BERT) use the idea of (unsupervised) pre-training to help address scarcity of data for supervised tasks.
- New algorithms are created regularly: e.g., XLNet, ERNIE, RoBERTa...