---
layout: post
title: Attention Is All You Need review
---

## Abstract

기존의 좋은 성능을 갖는 sequence 변환 모델들은 RNN이나 CNN encoder-decoder framework를 기반으로 하고 있다. 하지만 이 논문에서는 RNN이나 CNN을 사용하지 않은 간단한 network 구조인 Transformer를 소개한다. 

## Introduction

RNN, LSTM, GRU 등은 machine translation과 language modeling 같은 sequence modeling과 변환에서 state of the art 접근 방식들이다. 많은 노력들이 recurrent language model과 encoder-decoder 구조를 계속해서 발전시키고 있다.

RNN 모델들은 이전의 hidden state를 받아서 다음 time step의 hidden state를 생성하는데, 이러한 sequential한 특징이 전달되면서 학습 데이터에서 parallelization이 힘들어져 긴 sequence 에서는 memory constraints limit으로 인한 문제가 생기게 된다. 최근 다양한 연구(factorization trick, conditional computation) 들로 인하여 computational efficiency 가 좋아졌지만, 기본적으로 sequential computation 한계는 아직도 남아있다.

Attention mechanism 들은 input과 output sequence에서 그들의 거리와 관계 없이 dependency를 modeling 할 수 있다는 점에서 강력한 sequence modeling이나 변환 model 같은 다양한 task에서 필수적인 부분이 되어가고 있다. 

이 논문에서는 recurrence를 사용하지 않고, input과 output 사이의 global dependencies를 뽑아내기 위한 attention mechanism 에만 의존하는 model 구조인 Transformer를 제안한다.
