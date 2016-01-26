title: Anorm用法
date: 2016-01-26 08:28:55
description:
categories: Note
tags:
- Play
- SQL
---

# 什麼是Anrom
用作寫SQL，但免去JDBC 麻煩的煩惱，典型的JDBC需要create statement/PreparedStatement 師很多try catch，Anorm還簡化了煩人的resultset looping 以及如何將resultset的內容提取

From [Java Tutorial](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html)
{% codeblock %}
public void updateCoffeeSales(HashMap<String, Integer> salesForWeek)
    throws SQLException {

    PreparedStatement updateSales = null;
    PreparedStatement updateTotal = null;

    String updateString =
        "update " + dbName + ".COFFEES " +
        "set SALES = ? where COF_NAME = ?";

    String updateStatement =
        "update " + dbName + ".COFFEES " +
        "set TOTAL = TOTAL + ? " +
        "where COF_NAME = ?";

    try {
        con.setAutoCommit(false);
        updateSales = con.prepareStatement(updateString);
        updateTotal = con.prepareStatement(updateStatement);

        for (Map.Entry<String, Integer> e : salesForWeek.entrySet()) {
            updateSales.setInt(1, e.getValue().intValue());
            updateSales.setString(2, e.getKey());
            updateSales.executeUpdate();
            updateTotal.setInt(1, e.getValue().intValue());
            updateTotal.setString(2, e.getKey());
            updateTotal.executeUpdate();
            con.commit();
        }
    } catch (SQLException e ) {
        JDBCTutorialUtilities.printSQLException(e);
        if (con != null) {
            try {
                System.err.print("Transaction is being rolled back");
                con.rollback();
            } catch(SQLException excep) {
                JDBCTutorialUtilities.printSQLException(excep);
            }
        }
    } finally {
        if (updateSales != null) {
            updateSales.close();
        }
        if (updateTotal != null) {
            updateTotal.close();
        }
        con.setAutoCommit(true);
    }
}
{% endcodeblock %}

# 使用方法

在build.sbt 加入
{% codeblock %}
libraryDependencies ++= Seq(
  jdbc,
  "com.typesafe.play" %% "anorm" % "2.4.0"
)
{% endcodeblock %}

在program中加入
{% codeblock lang:scala %}
import anorm._
import play.api.db.DB

DB.withConnection { implicit c =>
  val result: Boolean = SQL("Select 1").execute()
}
{% endcodeblock %}

{% codeblock lang:scala %}
如果你擔心有exception 可以wrap 在try catch內
try {

} catch {
  case ex: Exception =˃ ex.getMessage
}
{% endcodeblock %}

DB是playframework的datasource

## 執行query
`SQL("Select 1")` 常用以下各種method
- `execute()` : 用作any query, 回傳一個Boolean, true 代表提取了resultset
- `executeUpdate()` : 用作update, insert, delete, 回傳受影響的行數
- `executeInsert()` : 用作插入資料, 回傳一個Option[Long] 作用generated ID
- `executeQuery()`  : 用作提取資料, 回傳SqlQueryResult (資料褲內容)
詳見Reference 2

# 在SQL 插入數值
Anrom提供方法簡化 PreparedStatement煩複的setXXXX
`updateSales.setInt(1, e.getValue().intValue());` 
`updateSales.setString(2, e.getKey());`

## 方法1： 使用on
{% codeblock lang:scala %}
  SQL(
    """
      select * from Country c 
      join CountryLanguage l on l.CountryCode = c.Code 
      where c.code = {countryCode}
    """)
    .on("countryCode" -> "FRA")
{% endcodeblock %}
`{countryCode}` 的數值變為 "FRA"

## 方法2: 使用String Interpolation (Scala > 2.10)
{% codeblock %}
val name = "Cambridge"
val country = "New Zealand"

SQL"insert into City(name, country) values ($name, $country)"
{% endcodeblock %}
將`name`的數值插中`$name` 當中

# 提取資料
這裡的API有點難理解, 請先import `import anorm.SqlParser._` 以使用built in parser

其中一項SQL最常用的功能為提取資料, Anorm 抽取了將SQLQueryResult (ResultSet)變為所需資料的過程， 我們最終需要`ResultSetParser`，但`ResultSetParser`由`RowParser`轉換而來 
- `RowParser` 將column data變為我們所需的data
- `ResultSetParser` 定義如何parse resultSet，例如不同行數會回傳不同data

## 例子

{% codeblock %}
val count: Long = 
SQL("select count(*) from Country").as(scalar[Long].single)
{% endcodeblock %}

`as`會自動執行`executeQuery()` 然後將結果給parser用作提取資料
當中RowParser是 `scalar[Long]`, scalar是anorm中用作唯一column的parser，將count(*)變為Long
當中ResultSetParser是`scalar[Long].single` api 中定義為`def single: ResultSetParser[A]`, 因此return Long

**利用column name的parser**

{% codeblock %}
val populations: List[String ~ Int] = 
  SQL("SELECT * FROM Country").as((str("name") ~ int("population")).*) 
{% endcodeblock %}
`str("name")` 將name 轉換String, `int("population")` 將population 轉換為int, 每行結果為`String ~ Int`
`*` 代表很多行, 所以總結果為`List[String ~ Int]`

`String ~ Int` 是anorm定義的type, 可用pattern matching 進行轉換
{% codeblock %}
val parser = str("name") ~ int("population") map { case n ~ p => (n, p) }
{% endcodeblock %}

## 將RowParser轉換為ResultSetParser
- `single`: ResultSetParser[A], Returns a result set parser expecting exactly one row to parse.
- `singleOpt`: ResultSetParser[Option[A]], Returns a result set parser for none or one parsed row.
- `*`: ResultSetParser[List[A]], Returns possibly empty list parsed from result.
- `+`: ResultSetParser[List[A]], Returns non empty list parse from result, or raise error if there is no result.
- `?`: RowParser[Option[A]], Returns a row parser for optional column, that will turn missing or null column as None.

# Reference
1. [Play 2.4 Anrom Introduction](https://www.playframework.com/documentation/2.4.x/ScalaAnorm)
2. [Simple SQL API](https://www.playframework.com/documentation/2.4.x/api/scala/index.html#anorm.SimpleSql)
3. [ResultSetParser API](https://www.playframework.com/documentation/2.4.x/api/scala/index.html#anorm.RowParser)
4. [Anorm built in rowParser API](https://www.playframework.com/documentation/2.4.x/api/scala/index.html#anorm.SqlParser$)