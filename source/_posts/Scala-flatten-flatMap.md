title: Scala-flatten-flatMap
date: 2016-02-14 06:27:50
description:
categories: Note
tags:
- Scala
---

# GenTraversable
`GenTraversable` 可理解為可以被loop...被transverse(look over, examine)
{% quote %}
GenTraversable: A trait for all traversable collections which may possibly have their operations implemented in parallel.
{% endquote %}

Common sub-classes: List, Map, Option
GenTraversable內的super class是GenTraversableOnce (多了Once)

# Flatten
Flatten 的作用是將`GenTraversable`裡面的elements的elements 取出, 變做one dimension
`GenTraversable`裡面的elements 也需要是`GenTraversable`

例如
{% codeblock %}
scala> List(List(4, 6), List(3,4)).flatten
res40: List[Int] = List(4, 6, 3, 4)
{% endcodeblock %}
List 是GenTraversable，而他也包含了兩個List elements（也是GenTraversable)
flatten 就是將List(4, 6)抽出4, 6 和List(3,4) 抽出3, 4 變做one dimension

## List
{% codeblock %}
List(1, 2, 3).flatten
<console>:11: error: No implicit view available from Int => scala.collection.GenTraversableOnce[B].
       List(1, 2, 3).flatten
{% endcodeblock %}       
如果裡面的element 不是`GenTraversableOnce`, 會發生錯誤
1,2,3 都是Int, 不是`GenTraversableOnce`

{% codeblock %}
private def sequential: TraversableOnce[A] = this.asInstanceOf[GenTraversableOnce[A]].seq

def flatten[B](implicit asTraversable: A => /*<:<!!!*/ GenTraversableOnce[B]): CC[B] = {
    val b = genericBuilder[B]
    for (xs <- sequential)
      b ++= asTraversable(xs).seq
    b.result()
  }
{% endcodeblock %}
From source code: 如果是instance of GenTraversableOnce巳經可以被flatten

## Option
{% quote %}
trait Iterable[+A] extends Traversable[A] with GenIterable[A] with GenericTraversableTemplate[A, Iterable] with IterableLike[A, Iterable[A]]
{% endquote %}

{% codeblock %}
scala> val l = List(Some(4), Some(5), None)
l: List[Option[Int]] = List(Some(4), Some(5), None)

scala> l.flatten
res28: List[Int] = List(4, 5)
{% endcodeblock %}
有趣的Option 可以implicit converted into Iterable which is subclass of GenTraversable
可以直接flatten 出value, ignore None


# FlatMap
FlatMap 類似Map, 但接收的function 需要return `GenTraversable`，然後被flatten
{% codeblock %}
scala> val l = List(List(3, 4), (3, 4)).map (x => List(x)).flatten
l: List[Product with java.io.Serializable] = List(List(3, 4), (3,4))
{% endcodeblock %}

To combine with `Option`
{% codeblock %}
res87: List[Option[Int]] = List(Some(4), Some(5), None)
scala> l.flatMap( x => x)
res88: List[Int] = List(4, 5)
{% endcodeblock %}



It may looks very useless, but it could be used with exception
return Some(value) if everthing is OK 
otherwise just return None
{% codeblock %}
scala> val n = List(1,2,3,4)
n: List[Int] = List(1, 2, 3, 4)

scala> n.flatMap( x => if (x > 2) Some (x) else Nne)

def toInt(in: String): Option[Int] = {
  try {
    Some(Integer.parseInt(in.trim))
  } catch {
    case e: Exception => None
  }
}

scala> val s = List("1", "2", "god", "jason")
s: List[String] = List(1, 2, god, jason)

scala> s.flatMap(toInt(_))
res93: List[Int] = List(1, 2)
{% endcodeblock %}


# Reference
[Option Cheat SHeet](http://blog.tmorris.net/posts/scalaoption-cheat-sheet/)
[FlatMap](https://www.safaribooksonline.com/library/view/scala-cookbook/9781449340292/ch10s17.html)