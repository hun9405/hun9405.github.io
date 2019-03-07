---
layout: default
title: Attention Is All You Need review
use_math: true
---

## Abstract

기존의 좋은 성능을 갖는 sequence 변환 모델들은 RNN이나 CNN encoder-decoder framework를 기반으로 하고 있다. 하지만 이 논문에서는 RNN이나 CNN을 사용하지 않은 간단한 network 구조인 Transformer를 소개한다. 

## Introduction

RNN, LSTM, GRU 등은 machine translation과 language modeling 같은 sequence modeling과 변환에서 state of the art 접근 방식들이다. 많은 노력들이 recurrent language model과 encoder-decoder 구조를 계속해서 발전시키고 있다.

RNN 모델들은 이전의 hidden state를 받아서 다음 time step의 hidden state를 생성하는데, 이러한 sequential한 특징이 전달되면서 학습 데이터에서 parallelization이 힘들어져 긴 sequence 에서는 memory constraints limit으로 인한 문제가 생기게 된다. 최근 다양한 연구(factorization trick, conditional computation) 들로 인하여 computational efficiency 가 좋아졌지만, 기본적으로 sequential computation 한계는 아직도 남아있다.

Attention mechanism 들은 input과 output sequence에서 그들의 거리와 관계 없이 dependency를 modeling 할 수 있다는 점에서 강력한 sequence modeling이나 변환 model 같은 다양한 task에서 필수적인 부분이 되어가고 있다. 

이 논문에서는 recurrence를 사용하지 않고, input과 output 사이의 global dependencies를 뽑아내기 위한 attention mechanism 에만 의존하는 model 구조인 **Transformer**를 제안한다.



## Background

Self-attention은 하나의 sequence의 representation을 계산하기 위해 sequence내의 다른 위치에 있는 것들과 계산하는 것이다. Self-attention은 reading comprehension, abstractive summarization, textual entailment 등과 같은 task에서 좋은 성능을 얻어내고 있다.

Transformer는 sequence-aligned RNN과 convolution을 사용하지 않고, input과 output의 representation을 계산하기 위해 self-attention 만을 사용한 최초의 transduction(변환) 모델이다.

## Model Architecture

이제 **Transformer**의 구조에 대해 알아보자. Transformer도 기본적으로 Encoder가 input sequence를 받아 representation을 만들고, 이것을 통해 decoder가 output sequence를 생성해내는 구조를 가지고 있다. 먼저 Encoder의 구조를 살펴보자.

**Encoder**: encoder는 N = 6개의 같은 구조의 layer가 쌓인 형태로 이루어져 있다. 각각의 layer에는 2개의 sub-layer가 존재한다. 첫번째 layer는 attention을 위한 layer이고 두번째 layer는 간단한 fully connecte d feed-forward network이다. Encoder를 통해 생성되는 output의 dimension d_model = 512이다. 

**Decoder**: decoder 또한 N = 6개의 identical layer가 쌓인 형태로 구성된다. decoder에서는 3개의 sub-layer로 encoder의 구조에서 하나의 sub-layer가 추가되는데, 이것은 encoder stack의 output을 받아 multi-head self-attention을 실행한다. 또한, Encoder와 같은 방식으로 각각의 sub-layer에 residual connection을 이용한다. 또한 self-attention sub layer에서 각 포지션이 뒤따라 오는 position에 attend 하는 것을 방지하기 위해 self-attention을 약간 수정하였다고 한다. 이는 i번째 position에 대한 prediction이 i번째 이전의 output 에만 의존할 수 있도록 만들어 준다.

이제 model 구조를 좀 더 세밀하게 살펴보도록하자. 먼저 Attention Layer에 대하여 알아보자. 이 model의 attention function은 query와 key, value가 주어졌을 때, 이것을 output으로 mapping하는 함수이다. 이때 output은 value들의 weighted sum으로 표현되고, 이 weight는 query와 key의 연관성을 통해 계산된다. 아래에서 좀 더 자세하게 알아보도록 하자.

**Scaled Dot-Product Attention**
<img src="/images/scaled dot product attention.png" />

Scaled dot-product attention의 input 은 dimension d_k 인 query Q와 key K, 그리고 dimension d_v인 value V로 구성된다. 여기서 query와 모든 key의 dot product를 계산하고, 이것을 d_K^(1/2)로 나눈 뒤, value들의 weight를 구하기 위해 softmax function을 취해준다. 

가장 많이 쓰이는 attention function은 additive attention과 dot-product attention이다. 이 논문에서 쓰인 attention은 scaling factor만 제외하면 dot-product attention과 같다. Additive function은 하나의 hidden layer를 가진 feed-forward network를 사용한다. 두개의 complexity는 비슷하지만, dot-product attention이 훨씬 빠르고 space-efficient하다.

D_k 값이 작다면 두 attention mechanism은 비슷한 성능을 보이지만, d_k가 커진다면 additive attention이 outperform 한다. 그래서 scaling factor를 통해 성능을 좋게 한 것이다. 


**Multi-Head Attention**

하나의 attention function을 d_model dimensional keys, values, queries에 적용하는 것 대신에, 이 논문에서는 query, key, value들을 서로 다르게 h번 linearly project 시켜 d_k, d_k, d_v dimension의 linear projection을 학습시켰다. 다음으로 query, key, value에 대한 각각의 projected version에 대하여, d_v dimensional output value를 얻기 위해 attention function을 parallel 하게 적용시켰다. 이 vector들은 모여서 concatenate되고, 그 후 linear 하게 projected 되어 마지막 output value를 생성하게 된다.

이 모델에서는 h= 8 개의 parallel한 attention layer를 사용하였고, 각각의 dimension 은 d_k = d_v = d_model / h = 64 로 설정하였다. Head의 줄어든 dimension으로 인해, total computation cost는 single-head attention 을 full dimensionality 사용했을 때와 비슷하게 된다.

<img src="/images/scaled dot product attention.png" />

**Positional Encoding**

Transformer 모델에서는 recurrence나 convolution이 없어, 모델이 sequence token들의 절대적이나 상대적인 순서를 고려할 수 있도록 해줘야 한다. 이것을 transformer에서는 encoder와 decoder의 input embedding에 _positional encodings_ 를 더해주는 것을 통하여 고려해 주었다. Positional encoding 은 d_model dimension 으로 embedding 과 vector의 크기가 같아 합쳐줄 수 있다. Positional embedding에는 학습하거나 고정할 수 있는 다양한 선택지가 존재한다.

논문에서는 sine cosine함수를 사용한 positional embedding을 사용하였다.

## Follow the Flow

### Encoder

지금까지 살펴본 Attention Layer의 성질을 가지고 Transformer가 작동하는 과정을 살펴보자. 먼저 Encoder의 경우부터 아래의 그림에서 살펴보자.(잘못되었을 가능성 있음)

<img src="/images/Encoder.png" />

먼저 Transformer input으로 N개의 word가 embedding 되어져 있는 matrix X를 갖게된다. 이 matrix X에 각각의 word의 position을 고려할 수 있도록 해주는 Positional Encoding vector(여기선 matrix)를 더하여, Encoder에 들어갈 matrix를 얻게 된다. 이렇게 input으로 주어지는 Matrix는 위의 Multi-Head Attention Layer Section에서 보았듯이 Query, Key, Value 로 나누어지게 되어, Multi-Head Attention Layer를 통과하게 된다. 이때 통과되어 나온 output은 Layer를 통과하기 전의 matrix와 더해진 후, Normalize를 해주게 된다. 이는 수식으로는 _Norm(x + Multi-Head Attention(x))_ 와 같이 나타내어진다. 

다음으로, 이 output을 Feed Forward Layer의 input으로 준 후, 다시 더하여 normalize하는 과정을 거치면, Encoder의 output을 구하게 되고, 이 output은 당연하게도 Encoder input의 dimension이 같다.

이렇게 Encoder를 통과하는 과정(Encoding)을 N번(Encoder의 갯수) 반복하면, 최종적인 Encoder의 output을 구할 수 있게 된다.

### Decoder

이제 Encoder의 output을 Decoder의 input으로 하여 출력값을 얻어내는 과정에 대하여 




**Applications of Attention in Transformer**

Transformer에서는 multi-head attention을 세가지 방법으로 사용하고 있다.

*	Encoder-decoder attention layer에서 query들은 이전의 decoder layer로부터 들어오고, memory key와 value들은 encoder의 output으로부터 들어오게 된다. 이를 통해서 decoder의 모든 position이 input sequence의 모든 position을 attend 할 수 있게 된다. 이것은 전형적인 seq2seq 의 attention mechanism과 같다.

*	Encoder는 self-attention layer를 가지고 있다. Self-attention layer의 모든 key, value, query들은 이전 layer의 output이다. Encoder의 각각의 position은 이전 layer encoder의 모든 position을 attend 할 수 있다.

* 비슷하게 decoder의 self-attention layer은 decoder의 각각의 position에서 그 position을 포함한 이전 모든 position을 attend 할 수 있게 한다. 이때 우리는 decoder 에서 auto-regressive property를 유지해야 하기 때문에 leftward information flow를 막아야 한다. 이것을 scaled dot-product attention에서 부적절한 connection 들에 대해 softmax의 input 을 masking(-무한대)로 하는 것으로 구현해주었다.


