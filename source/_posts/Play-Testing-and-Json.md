title: Play Testing and Json
date: 2016-05-31 01:27:25
description:
categories:
- Note
tags:
- Play
- Scala
---

個人覺得Play 的文件比較亂。在這裡先記下簡約play testing 的用法

# Override configuration
因為我只預設用一個database。。。
{% codeblock %}
trait DAOTrait {
implicit val fakeApp = new FakeApplication(
  additionalConfiguration = Map("db.default.username" -> "YOURUSERNAME",
    "db.default.url" -> "jdbc:mysql://URDATABASE",
    "db.default.password" -> "YOURPASSWORD")
)
}
{% endcodeblock %}

# Unit testing
因為anorm database 要有execution context 所以要先用step 啟動application

{% codeblock %}
import play.api.test.PlaySpecification
import DAO.UserDAO

import play.api._
import play.api.test._
import play.api.test.Helpers._

class UserDAOSpec extends PlaySpecification with DAOTrait {


  val testUserEmail = "test@hotmail.com"
  val testUserPassword = "1234"

  step(Play.start(fakeApp))

  "The User DAO" should {
    "register" in {
      val id = UserDAO.register(testUserEmail, testUserPassword);
      (id.get > 0) must beTrue
    }
    "isExist" in {
      UserDAO.isExist(testUserEmail) must beTrue
      UserDAO.isExist("this is not a email") must beFalse
    }
    "validate" in {
      UserDAO.validate(testUserEmail, testUserPassword) must beTrue
      UserDAO.validate(testUserEmail, "9999") must beFalse
    }
    "delete" in {
      UserDAO.delete(testUserEmail) must beTrue
    }

  }

  step(Play.stop(fakeApp))
}
{% endcodeblock %}

# Controller testing
測試json post request
另外寫法`val request = FakeRequest(POST, "/").withJsonBody(Json.parse("""{ "field": "value" }"""))`
body 請放JsValue, 不要放String
`contentType` 和 `contentAsJson` 在play.api.test.Helpers
如果你只需測試routing，你不需要用step 啟動applicaton, 整個controller的method不會執行
return 的可以是Future[Result]

{% codeblock %}
import controllers.RecipeController
import play.api.Logger
import play.api.libs.json._

import play.api._
import play.api.test._
import play.api.test.Helpers._

class RecipeControllerSpec extends PlaySpecification with DAOTrait{
  
  step(Play.start(fakeApp))
    
  "Example Page#index" should {
    "should be valid" in {
      
      var jsonHeader = Seq(("Content-Type", "application/json; charset=utf-8"))
      val jsValue = Json.obj("ingredients" -> Json.arr("牛奶", "奶"))
      val controller = new RecipeController()
      val fakeRequest = FakeRequest(Helpers.POST, "api/user/searchIngredient", new FakeHeaders(jsonHeader), jsValue);

      val result  = controller.searchIngredient().apply(fakeRequest)
      val ct = contentType(result)
      ct must equalTo(Some("application/json"))
//      Logger.debug("really content type" + contentAsString(result))
      val jsContent : JsValue = contentAsJson(result)
      jsContent.as[JsArray].value.size must equalTo(6)
    }
  }
  
  step(Play.stop(fakeApp))
}
{% endcodeblock %}


# JSON Read & Write
play framework 設計JSON要read 進一個model & 或由一個model write 成JSON，如果json的數目不同比較麻煩

把一個value 變做object, 利用scala 的map pattern 提取value
{% codeblock %}
implicit val ingRead: Reads[IngIdWrap] = (JsPath \ "ingId").read[Int].map(IngIdWrap.apply);
{% endcodeblock %}

UserRecipeRating 是(Int, Int, Int)
read的read combinator 會產生`(Int, Int)`
寫一個function 接收`(Int, Int)` 變成UserRecipeRating      
{% codeblock %}
val applyJson = (recipeId: Int, rating: Int) => UserRecipeRating(0, recipeId, rating);
implicit val read : Reads[UserRecipeRating] = (
    (JsPath \ "recipeId").read[Int] and
    (JsPath \ "rating").read[Int]
  )(applyJson)
{% endcodeblock %}

反過來，把一個UserRecipeRating 寫兩個Int, write combinator 需要`(Int, Int)`
可以寫一個function 接收一個UserRecipeRating 變作`(Int, Int)`
{% codeblock %}
val writeJson = (obj: UserRecipeRating) => (obj.recipeId , obj.rating)
implicit val write : Reads[Write] = (
    (JsPath \ "recipeId").write[Int] and
    (JsPath \ "rating").write[Int]
  )((writeJson))
{% endcodeblock %}

