---
layout: default
title: Attention Is All You Need review
---

## Abstract

기존의 좋은 성능을 갖는 sequence 변환 모델들은 RNN이나 CNN encoder-decoder framework를 기반으로 하고 있다. 하지만 이 논문에서는 RNN이나 CNN을 사용하지 않은 간단한 network 구조인 Transformer를 소개한다. 

## Introduction

RNN, LSTM, GRU 등은 machine translation과 language modeling 같은 sequence modeling과 변환에서 state of the art 접근 방식들이다. 많은 노력들이 recurrent language model과 encoder-decoder 구조를 계속해서 발전시키고 있다.

RNN 모델들은 이전의 hidden state를 받아서 다음 time step의 hidden state를 생성하는데, 이러한 sequential한 특징이 전달되면서 학습 데이터에서 parallelization이 힘들어져 긴 sequence 에서는 memory constraints limit으로 인한 문제가 생기게 된다. 최근 다양한 연구(factorization trick, conditional computation) 들로 인하여 computational efficiency 가 좋아졌지만, 기본적으로 sequential computation 한계는 아직도 남아있다.

Attention mechanism 들은 input과 output sequence에서 그들의 거리와 관계 없이 dependency를 modeling 할 수 있다는 점에서 강력한 sequence modeling이나 변환 model 같은 다양한 task에서 필수적인 부분이 되어가고 있다. 

이 논문에서는 recurrence를 사용하지 않고, input과 output 사이의 global dependencies를 뽑아내기 위한 attention mechanism 에만 의존하는 model 구조인 **Transformer**를 제안한다.



## Background

Intra-attention이라고도 불리는 self-attention은 하나의 sequence의 representation을 계산하기 위해 sequence내의 다른 위치에 있는 것들과 계산하는 것이다. Self-attention은 reading comprehension, abstractive summarization, textual entailment 등과 같은 task에서 좋은 성능을 얻어내고 있다.

Transformer는 sequence-aligned RNN과 convolution을 사용하지 않고, input과 output의 representation을 계산하기 위해 self-attention 만을 사용한 최초의 transduction(변환) 모델이다.

## Model Architecture

이제 **Transformer**의 구조에 대해 알아보자. $Transformer$도 기본적으로 Encoder가 input sequence를 받아 representation을 만들고, 이것을 통해 decoder가 output sequence를 생성해내는 구조를 가지고 있다. 먼저 Encoder의 구조를 살펴보자.

**Encoder**: encoder는 N = 6개의 같은 구조의 layer가 쌓인 형태로 이루어져 있다. 각각의 layer에는 2개의 sub-layer가 존재한다. 첫번째 layer는 attention을 위한 layer이고 두번째 layer는 간단한 fully connecte d feed-forward network이다. Encoder를 통해 생성되는 output의 dimension $d_model$ = 512이다. $\begin{equation} ... \end{equation}$
$$s_2$$ $ 111 $  
asd
