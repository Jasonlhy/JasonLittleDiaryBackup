title: Play framework 表格提交
date: 2016-01-21 01:46:46
description:
categories: Note
tags:
- Play
---

# 環境設定

- PlayFramework: v2.4.6
- scala : 2.11.6

先import 以下
{% codeblock lang:scala %}
import play.api.data._
import play.api.data.Forms._
import play.api.data.format.Formats._
{% endcodeblock %}

# 先定義一個case class
case class 的作用是用來encapsulate 提交的資料為一個object, 正式名稱為[Algebraic data type
](https://en.wikipedia.org/wiki/Algebraic_data_type), 將data 定義為不同結溝和不同value，以下例子為Haskell
- `data Bool = False | True`
- `data Maybe a = Nothing | Just a`

{% quote %}
an algebraic data type is a kind of composite type, i.e. a type formed by combining other types
{% endquote %}

回到play中，加入`case class User(name: String, password: String)`
這定義User 有兩個String field, 當中有當中有name 和password，是否很像c中的struct, 但scala 中的Algebraic data type不只case class, `Option`也是其中一種


# 然後定義一個Form 
定義一個form 實現如何把form的parameter blind to the case class

{% codeblock %}
  val userForm = Form(
    mapping(
      "name" -> of[String],
      "password" -> of[String]
    )(User.apply)(User.unapply)
  )
{% endcodeblock %}

- 表格中的"name" 為String
- 表格中的"password" 為String

## apply 和unapply 的作用

{% codeblock %}
scala> User("jason", "password")
res6: User = User(jason,password)

scala> :t User.unapply _
User => Option[(String, String)]

scala> User.unapply(User("jason", "password"))
res8: Option[(String, String)] = Some((jason,password))
{% endcodeblock %}

`User.apply` 是用作創建新instance of the User
`User.unapply` 是用作extract 資料，用作patten matching (match & case)

{% codeblock %}
// when u is a User
// u is User(jason,password)
// User(name, password) actually calls `User.unapply(u)` which extract the data inside with return value: Some(name, password)
// therefore unapply method is called extractor pattern
u match {
  case User(name, password) => println(s"u is consist of $name and $password") 
  case _ => println("not matched")
}

// when o is Option
// o: Option[(String, String)] = Some((jason,password))
o match {
  case Some((name, password)) => println(s"u is consist of $name and $password") 
  case None => println("no user is found ")
}
{% endcodeblock %}

# GET Request
{% codeblock lang:scala %}
  def login = Action(parse.form(userForm)) { request =>
    Logger.debug("User logining")
    val user = request.body;
    Ok("Got: " + s"User name: ${user.name}, User password: ${user.password}");
  }
{% endcodeblock %}

request.body 裡就是User了
Result (content type: text/plain)
Got: request.body.class: class controllers.UserController$User User name: jason, User password: text
## Test with Curl
{% codeblock lang:bash %} 
curl --data http://localhost:9000/api/user/login2 \
      name=jason&password=password
{% endcodeblock %} 

# POST Request
{% codeblock lang:scala %}
 def login2 = Action(parse.urlFormEncoded) { implicit request =>
    /* Approach 1
    val body : Map[String, Seq[String]]= request.body;
    val mapping : Map[String, String] = body.mapValues(_.mkString)

    val user = userForm.bind(mapping).get;
    */

    /* Approach 2
    val user = userForm.bindFromRequest().get;
    */
    
    /* Approach 3*/
    val user = userForm.bindFromRequest(request.body).get

    // var user = userForm..get;
    Ok(s"Got User name: ${user.name}, User password: ${user.password}");
  }
{% endcodeblock %}
比較麻煩 需要把mapping 轉做form然後再map做base class

## Test with curl
{% codeblock lang:bash %}
curl -H "Content-Type:application/x-www-form-urlencoded" \
 -d 'name=jason&password=password' \
http://localhost:9000/api/user/login2
{% endcodeblock %}

# Reference
- [Introudction of algebraic data In Haskell  types](http://chris-taylor.github.io/blog/2013/02/10/the-algebra-of-algebraic-data-types/)
- [Introduction of Extractor patern](http://danielwestheide.com/blog/2012/11/21/the-neophytes-guide-to-scala-part-1-extractors.html)
- [Introduction of Option](http://danielwestheide.com/blog/2012/12/19/the-neophytes-guide-to-scala-part-5-the-option-type.html)