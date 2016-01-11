title: 初試Scala
date: 2016-01-11 07:39:49
description: 
categories: Note
tags:
- Scala
---

# 感想
很複雜的語言......很多feature(果然是教授的產物)

除了method 外可以自動估算data type
`val r = 1`
Object 像一Java 的static method, 不用new object 便可以使用
最後一句預設是回傳值
很多method 沒有side effect

# Object type
![Overview](http://docs.scala-lang.org/resources/images/classhierarchy.img_assist_custom.png)
最頂 Any
其次 AnyVal, AnyRef



## AnyVal
AnyVal is primitive datatype in Java
底下有
- Int
- Double
- Flot
- Long
- Short
- Byte
- Char
- Boolean
- Unit (Single Value, ~Void in Java)

## AnyRef
底下有AnyObject
- List


## Other
Null <- Nothing

# Method

## 定義
method 定義有點像haskell, 
名在前, data type 在後
= 之後是回傳數值
`def hi(a: String, b: String) = a + b`

**多種括號**
`def hi(a: String)(b: String) = a + b`
可以這樣呼叫
`hi("hi")"Jason"`
or 
`hi("hi") {"jason"}`

**Accept an function**
{% codeblock %}
def goodBye(f: (String) => String, b:String) = f("Jason") + b
goodBye: (f: String => String, b: String)String
{% endcodeblock %}

**functional map on array**
{% codeblock %}
val a = Array(1,2,3)
a.map(n => n + 10)
res2: Array[Int] = Array(11, 12, 13)
{% endcodeblock %}



{% codeblock %} 

def test(l : String) = l match {
  case r(_, id) => s"It is a youtube with id: $id"
  case _ => "Not youtube"
}

{% endcodeblock %}


## 強大的pattern matching
相比java 麻複的regex......
- r 為regex
- l 為testing string
- _ 指不在乎

{% codeblock %}

val r = """\[url\]https:\/\/(youtu\.be\/|www\.youtube\.com\/)(.+)\[\/url\]""".r.unanchored // anywhere pattern
val l = "[url]https://www.youtube.com/watch?v=Pt7dTw8pm30[/url]"

def test(l : String) = l match {
  case r(_, id) => s"It is a youtube with id: $id"
  case _ => "Not youtube"
}

{% endcodeblock %}

# Syntactic sugar
() 可以當 { }

{% codeblock %}
val echo = Action { request =>
  Ok("Got request [" + request + "]")
}

// is equal to 
val echo = Action.apply(request => {} )
{% endcodeblock %}

還有implicit
搭少麻煩的variable passing
e.g. 在play framework
{% codeblock %}
def index = Action { implicit request =>
    val str = sayHi("Jason")
    Ok(views.html.index("Your new application is ready." + str))
  }

  private def sayHi(name: String)(implicit req: Request[AnyContent]) = name + ", you can the following content" + req.body
{% endcodeblock %}




# Eco system
type safe activator 太強大了吧...懶人傻瓜包，下載了，就可以在瀏覧器上試玩當中scala各種應用程式template

sbt 不只是用來build, 他還有sbt-launcher.jar
sbt run 會開一個local server 測試用...