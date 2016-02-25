title: group by key in haskell
date: 2016-02-26 01:28:28
description: 
categories: Problem
tags:
- Haskell
- Scala
- Programming
---

# 什麼是Group By Key
Group By Key 就是把Element 計算一個key，相同key的element 就放左一組內
例如group by key by the reminder equal to 3 in Scala

{% codeblock %}
val arr = Array(2, 4, 5, 6, 9, 23, 24, 25, 31, 37)
scala> val ans = arr.groupBy { n => n % 3 }
ans: scala.collection.immutable.Map[Int,Array[Int]] = Map(2 -> Array(2, 5, 23), 1 -> Array(4, 25, 31, 37), 0 -> Array(6, 9, 24))
{% endcodeblock %}

# 問題
Haskell 中把 List 中的element Group By Key 是麻煩的一件事
因為`Data.List.groupBy` 是linear scan 每兩個element, 然後比較他們是否相同

{% codeblock %}
import Data.List
Prelude Data.List> groupBy (==) [1, 1, 2, 3, 1] 
[[1,1],[2],[3],[1]]

{% endcodeblock %}

## 解決方法1
List 事前需要被sorted by key 
Scala 的例子在Haskell 可以寫成這樣
值得注意`sortBy 要求data `Ordering`, 需要用`compare`, 不是`>` `==` `<` 它們會出Bool

{% codeblock %}
Prelude Data.List> let allInOne = groupBy (\x y -> (x `mod` 3) == (y `mod` 3)) . sortBy (\x y -> compare (x `mod` 3) (y `mod` 3))
Prelude Data.List> allInOne [2, 4, 5, 6, 9, 23, 24, 25, 31, 37]
[[6,9,24],[4,25,31,37],[2,5,23]]
{% endcodeblock %}

## 解決方法2
[一個package](https://hackage.haskell.org/package/utility-ht-0.0.11/docs/Data-List-Key.html)解決這個問題, 當中的`groupBy` 是scala中的`groupByKey 但return 一個List

**安裝**
{% codeblock %}
> cabal install utility-ht
> ......
> ghci
{% endcodeblock %}

**例子**
{% codeblock %}
Prelude> import Data.List.HT
Prelude Data.List.HT> let a = [2, 4, 5, 6, 9, 23, 24, 25, 31, 37]
Prelude Data.List.HT> groupBy (\x y -> x `mod` 3 == y `mod` 3 )
[[2],[4],[5],[6,9],[23],[24],[25,31,37]]
{% endcodeblock %}



