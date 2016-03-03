title: Introduction to formal language
date: 2016-03-03 00:30:40
description:
categories: Note
tags:
- Theory of Computation
- Formal Language
---

# 前言
在很久很久以前，數學家門研究怎樣產生電腦，和研究怎樣令電腦運作
這門的學問叫Theory of computation (真的是theory, 因為都是抽象的電腦)
當中有兩個分支
Automata theory: 研究機械怎樣計算
Formal language: 研究language, language 是給automata計算的東西, 因此Formal language是一門跟Automata 很相關的科

現在電腦中常用的[Von Neumann architecture](https://en.wikipedia.org/wiki/Von_Neumann_architecture) 是基於 Automata theory中的 [Universal Turing machine](https://en.wikipedia.org/wiki/Universal_Turing_machine)

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
輸入一個String, 一個個字母讀
不用temporary memory，利用在不同state 的轉移作computation
因此只需program memory (不同state)

Deterministic finite automaton: 每個state 只會轉移到另一個state
Nondeterministic finite automaton: 每個state 可以轉移到n 個state


