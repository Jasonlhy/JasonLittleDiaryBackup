title: import(匯入) in scala
date: 2016-01-20 21:00:20
description:
categories: Note
tags:
- Scala
---


Import(匯入) 在scala有很多方法
# 最常用 
`import jason.playground._` 
匯入jason.playground package 底下的所有classes and object, 包括package object(package object 的所有member會以static 方面匯入)

# 匯入特定class的member
`import jason.playground.JasonPhone._`
匯入jason.playground.Jason 底下的所有member


# 例子
{% codeblock %}
package jason.playground

object JasonPhone {
  def name() = "Iphone 6"
}

package jason

import jason.playground.JasonPhone

package object playground {
  // static method to be imported into class
  def getName() = "Iphone 6 (from helper method)"

  // align to another object's method
  def nameFromPackageObject() = JasonPhone.name()
}

package jason.program

// this line import classes & object under jason.playground (not recurisly)
import jason.playground._;

// this line import "members" under jason.playground.JasonPhone
// ~ import static in Java
import jason.playground.JasonPhone._;

/**
  * Created by Jason on 1/20/16.
  */
object Main {
  def main(args: Array[String]): Unit = {
    println("I am using: " + JasonPhone.name()) // this is declared in JasonPhone.scala
    println("I am using: " + nameFromPackageObject()); // this is declared in package.scala
    println("I am using: " + name()); // this is a short cut with static import
  }
}

{% endcodeblock %}

- `JasonPhone.name()` 由匯入`jason.playground._` 而來
- `nameFromPackageObject()` 由匯入`jason.playground._` 而來，是package object裡的method
- `name()` 由static匯入 `jason.playground.JasonPhone._`而來，不用打JasonPhone作為prefix

## Play Framework 匯入應用
{% codeblock %}
val userForm = Form(
  mapping(
    "name" -> of[String],
    "age" -> of[Int],
    "email" -> of[String]
  )(User.apply)(User.unapply)
)
{% endcodeblock %}
`of` 由匯入`play.api.data.Forms._` 得來, `Forms`是一個object定義helper methods

