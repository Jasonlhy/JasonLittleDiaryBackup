title: Introduction to formal language
date: 2016-03-03 00:30:40
description:
categories: Note
tags:
- Theory of Computation
- Formal Language
---

# 前言
在很久很久以前，數學家門研究怎樣產生電腦，和研究怎樣令電腦運作, 這門的學問叫Theory of computation (真的是theory, 因為都是抽象的電腦), 當中有兩個分支:
- Automata theory 研究機械怎樣計算
- Formal language 研究language, language 是給automata計算的東西

因此Formal language是一門跟Automata 很相關的科. 現在電腦中常用的[Von Neumann architecture](https://en.wikipedia.org/wiki/Von_Neumann_architecture) 是基於 Automata theory中的 [Universal Turing machine](https://en.wikipedia.org/wiki/Universal_Turing_machine)
By the way, Automata和Machine在一些文章中經常互用


# Formal Language

## 一些定義

Language 由String 的集合(set)，String由字母所組成
Language 可以由Grammar 所產生(describe)
當每個grammar 產生的language 是一樣，那他們就是相同的grammar


## Regular Language

Regular Language 就是Finite State Machine 內可以接受Language
Regular expression 是 Regular Language 的Implementation.

## Context-free grammar (上下文無關連文法)
即是不管上下句子是什麼，最常用作programming language parsing
定義variable 和rule
rule 會形容如何把variable 轉換成最終的string

{% math %}
G = (V, T, S, P)
{% endmath %}
V = variable 
T = Terminals 最後產生的terminals
S = Starting variable
P = set of rules

Context-free grammar 的rule 可以是left-recursion or right recursion
right recursion 的話可以由naive recursive parser 進行parsing, 例子: JSON

PS: recursive parser 是top down parser

# Automata Theory

## Finite State Machine
**簡單解釋**
輸入一個String, 一個個字母讀
不用temporary memory，利用在不同state 的轉移作computation
因此只需program memory (不同state)
停在final state 代表接受

**數學定義**
一推function, 定義每個state 接受什麼字母，然後跳到那個state
例如state q0 接受a，跳去state q1就是
{% math %}
\delta(q0, a) = q1
{% endmath %}

### Deterministic finite automaton
state接受某個字母，只可以轉移到一個state
function 需要是total function (接受每種可能字母)

### Nondeterministic finite automaton
state接受某個字母，可以轉移到多於一個state
function 不需要是total function
state 可以接受empty string (根本是外掛。。)

### NFA = DFA
DNF不就是NFA嘛...
而NFA可以轉換為DNF

**首先移除empty string transition**
`lambda closure`: 接受empty string 可以到達的state
假設q0 可以接受empty string，移除他的empty string transition

{% math %}
\delta'(q0, a) = \lambda-closure( \lambda-closure(q0), a )
{% endmath %}

q0 接受empty string 到達的states `(set A)`，他們接受a 到那個states `(set B)` （很直覺吧...但不是最後答案)
`(set B)` 再擴展，別忘了它們可以接受empty string 再走到 `(set C)`
`Set C` 才是q0 不用emtpy string transition 接受`a` 可以走到的地方

**產生DNF的state**
例如NFA 有三個state: q0, q1, q2
DNF 最多有的state是q0, q1, q2的powerset (2^n)
{% math %}
\left \{  \varnothing \right \}, \left \{  q0\right \}, \left \{  q1\right \}, \left \{  q2\right \},\left \{  q0, q1 \right \}, \left \{  q0, q2\right \}, \left \{  q1,q2\right \}, \left \{  q0,q1,q2\right \}
{% endmath %}

計算transition function 時，把destination union 就可以

{% math %}
\delta(q0, a) = q1 \\
\delta(q1, a) = q2 \\
\therefore \delta (\left \{  q0, q1 \right \}, a) = \left \{ q1, q2 \right \}
{% endmath %}