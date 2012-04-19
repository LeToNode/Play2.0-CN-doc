# Anorm, simple SQL data access

Play includes a simple data access layer called Anorm that uses plain SQL to interact with the database and provides an API to parse and transform the resulting datasets.

**Anorm is Not an Object Relational Mapper**

> In the following documentation, we will use the [[MySQL world sample database | http://dev.mysql.com/doc/world-setup/en/world-setup.html]]. 
> 
> If you want to enable it for your application, follow the MySQL website instructions, and enable it for your application by adding the following configuration line in your `conf/application.conf` file:
>
> ```
> db.default.driver= com.mysql.jdbc.Driver
> db.default.url="jdbc:mysql://localhost/world"
> db.default.user=root
> db.default.password=secret
> ```

## Overview

It can feel strange to return to plain old SQL to access an SQL database these days, especially for Java developers accustomed to using a high-level Object Relational Mapper like Hibernate to completely hide this aspect.

Although we agree that these tools are almost required in Java, we think that they are not needed at all when you have the power of a higher-level programming language like Scala. On the contrary, they will quickly become counter-productive.

#### Using JDBC is a pain, but we provide a better API

We agree that using the JDBC API directly is tedious, particularly in Java. You have to deal with checked exceptions everywhere and iterate over and over around the ResultSet to transform this raw dataset into your own data structure.

We provide a simpler API for JDBC; using Scala you don’t need to bother with exceptions, and transforming data is really easy with a functional language. In fact, the goal of the Play Scala SQL access layer is to provide several APIs to effectively transform JDBC data into other Scala structures.

#### You don’t need another DSL to access relational databases

SQL is already the best DSL for accessing relational databases. We don’t need to invent something new. Moreover the SQL syntax and features can differ from one database vendor to another. 

If you try to abstract this point with another proprietary SQL like DSL you will have to deal with several ‘dialects’ dedicated for each vendor (like Hibernate ones), and limit yourself by not using a particular database’s interesting features.

Play will sometimes provide you with pre-filled SQL statements, but the idea is not to hide the fact that we use SQL under the hood. Play just saves typing a bunch of characters for trivial queries, and you can always fall back to plain old SQL.

#### A type safe DSL to generate SQL is a mistake

Some argue that a type safe DSL is better since all your queries are checked by the compiler. Unfortunately the compiler checks your queries based on a meta-model definition that you often write yourself by ‘mapping’ your data structure to the database schema. 

There are no guarantees that this meta-model is correct. Even if the compiler says that your code and your queries are correctly typed, it can still miserably fail at runtime because of a mismatch in your actual database definition.

#### Take Control of your SQL code

Object Relational Mapping works well for trivial cases, but when you have to deal with complex schemas or existing databases, you will spend most of your time fighting with your ORM to make it generate the SQL queries you want.

Writing SQL queries yourself can be tedious for a simple ‘Hello World’ application, but for any real-life application, you will eventually save time and simplify your code by taking full control of your SQL code.

## Executing SQL queries

To start you need to learn how to execute SQL queries.

First, import `anorm._`, and then simply use the `SQL` object to create queries. You need a `Connection` to run a query, and you can retrieve one from the `play.api.db.DB` helper:

```scala
import anorm._ 

DB.withConnection { implicit c =>
  val result: Boolean = SQL("Select 1").execute()    
} 
```

The `execute()` method returns a Boolean value indicating whether the execution was successful.

To execute an update, you can use `executeUpdate()`, which returns the number of rows updated.

```scala
val result: Int = SQL("delete from City where id = 99").executeUpdate()
```

Since Scala supports multi-line strings, feel free to use them for complex SQL statements:

```scala
val sqlQuery = SQL(
  """
    select * from Country c 
    join CountryLanguage l on l.CountryCode = c.Code 
    where c.code = 'FRA';
  """
)
```

If your SQL query needs dynamic parameters, you can declare placeholders like `{name}` in the query string, and later assign them a value:

```scala
SQL(
  """
    select * from Country c 
    join CountryLanguage l on l.CountryCode = c.Code 
    where c.code = {countryCode};
  """
).on("countryCode" -> "FRA")
```

## Retrieving data using the Stream API

The first way to access the results of a select query is to use the Stream API.

When you call `apply()` on any SQL statement, you will receive a lazy `Stream` of `Row` instances, where each row can be seen as a dictionary:

```scala
// Create an SQL query
val selectCountries = SQL("Select * from Country")
 
// Transform the resulting Stream[Row] as a List[(String,String)]
val countries = selectCountries().map(row => 
  row[String]("code") -> row[String]("name")
).toList
```

In the following example we will count the number of `Country` entries in the database, so result set will be a single row with a single column:

```scala
// First retrieve the first row
val firstRow = SQL("Select count(*) as c from Country").apply().head
 
// Next get the content of the 'c' column as Long
val countryCount = firstRow[Long]("c")
```

## Using Pattern Matching

You can also use Pattern Matching to match and extract the `Row` content. In this case the column name doesn’t matter. Only the order and the type of the parameters is used to match.

The following example transform each row to the correct Scala type:

```scala
case class SmallCountry(name:String) 
case class BigCountry(name:String) 
case class France
 
val countries = SQL("Select name,population from Country")().collect {
  case Row("France", _) => France()
  case Row(name:String, pop:Int) if(pop > 1000000) => BigCountry(name)
  case Row(name:String, _) => SmallCountry(name)      
}
```

Note that since `collect(…)` ignores the cases where the partial function isn’t defined, it allows your code to safely ignore rows that you don’t expect.

## Dealing with Nullable columns

If a column can contain `Null` values in the database schema, you need to manipulate it as an `Option` type.

For example, the `indepYear` of the `Country` table is nullable, so you need to match it as `Option[Int]`:

```scala
SQL("Select name,indepYear from Country")().collect {
  case Row(name:String, Some(year:Int)) => name -> year
}
```

If you try to match this column as `Int` it won’t be able to parse `Null` cases. Suppose you try to retrieve the column content as `Int` directly from the dictionary:

```scala
SQL("Select name,indepYear from Country")().map { row =>
  row[String]("name") -> row[Int]("indepYear")
}
```

This will produce an `UnexpectedNullableFound(COUNTRY.INDEPYEAR)` exception if it encounters a null value, so you need to map it properly to an `Option[Int]`, as:

```scala
SQL("Select name,indepYear from Country")().map { row =>
  row[String]("name") -> row[Option[Int]]("indepYear")
}
```

This is also true for the parser API, as we will see next.

## Using the Parser API

You can use the parser API to create generic and reusable parsers that can parse the result of any select query.

> **Note:** This is really useful, since most queries in a web application will return similar data sets. For example, if you have defined a parser able to parse a `Country` from a result set, and another `Language` parser, you can then easily compose them to parse both Country and Language from a join query.
>
> First you need to `import anorm.SqlParser._`

First you need a `RowParser`, i.e. a parser able to parse one row to a Scala value. For example we can define a parser to transform a single column result set row, to a Scala `Long`:

```scala
val rowParser = scalar[Long]
```

Then we have to transform it into a `ResultSetParser`. Here we will create a parser that parse a single row:

```scala
val rsParser = scalar[Long].single
```

So this parser will parse a result set to return a `Long`. It is useful to parse to result produced by a simple SQL `select count` query:

```scala
val count: Long = SQL("select count(*) from Country").as(scalar[Long].single)
```

Let’s write a more complicated parser:

`str("name") ~ int("population")`, will create a `RowParser` able to parse a row containing a String `name` column and an Integer `population` column. Then we can create a `ResultSetParser` that will parse as many rows of this kind as it can, using `*`: 

```scala
val populations:List[String~Int] = {
  SQL("select * from Country").as( str("name") ~ int("population") * ) 
}
```

As you see, this query’s result type is `List[String~Int]` - a list of country name and population items.

You can also rewrite the same code as:

```scala
val result:List[String~Int] = {
  SQL("select * from Country").as(get[String]("name")~get[Int]("population")*) 
}
```

Now what about the `String~Int` type? This is an **Anorm** type that is not really convenient to use outside of your database access code. You would want have a simple tuple `(String, Int)` instead. You can use the `map` function on a `RowParser` to transform its result to a more convenient type:

```scala
str("name") ~ int("population") map { case n~p => (n,p) }
```

> **Note:** We created a tuple `(String,Int)` here, but there is nothing stoping you from transforming the `RowParser` result to any other type, such as a custom case class.

Now, because transforming `A~B~C` types to `(A,B,C)` is a common task, we provide a `flatten` function that does exactly that. So you finally write:

```scala
val result:List[(String,Int)] = {
  SQL("select * from Country").as(
    str("name") ~ int("population") map(flatten) *
  ) 
}
```

Now let’s try with a more complicated example. How to parse the result of the following query to retrieve the country name and all spoken languages for a country code?

```
select c.name, l.language from Country c 
    join CountryLanguage l on l.CountryCode = c.Code 
    where c.code = 'FRA'
```

Let’s start by parsing all rows as a `List[(String,String)]` (a list of name,language tuple):

```scala
var p: ResultSetParser[List[(String,String)] = {
  str("name") ~ str("language") map(flatten) *
}
```

Now we get this kind of result:

```scala
List(
  ("France", "Arabic"), 
  ("France", "French"), 
  ("France", "Italian"), 
  ("France", "Portuguese"), 
  ("France", "Spanish"), 
  ("France", "Turkish")
)
```

We can then use the Scala collection API, to transform it to the expected result:

```scala
case class SpokenLanguages(country:String, languages:Seq[String])

languages.headOption.map { f =>
  SpokenLanguages(f._1, languages.map(_._2))
}
```

Finally, we get this convenient function:

```scala
case class SpokenLanguages(country:String, languages:Seq[String])

def spokenLanguages(countryCode: String): Option[SpokenLanguages] = {
  val languages: List[(String, String)] = SQL(
    """
      select c.name, l.language from Country c 
      join CountryLanguage l on l.CountryCode = c.Code 
      where c.code = {code};
    """
  )
  .on("code" -> countryCode)
  .as(str("name") ~ str("language") map(flatten) *)

  languages.headOption.map { f =>
    SpokenLanguages(f._1, languages.map(_._2))
  }
}
```

To continue, let’s complicate our example to separate the official language from the others:

```scala
case class SpokenLanguages(
  country:String, 
  officialLanguage: Option[String], 
  otherLanguages:Seq[String]
)

def spokenLanguages(countryCode: String): Option[SpokenLanguages] = {
  val languages: List[(String, String, Boolean)] = SQL(
    """
      select * from Country c 
      join CountryLanguage l on l.CountryCode = c.Code 
      where c.code = {code};
    """
  )
  .on("code" -> countryCode)
  .as {
    str("name") ~ str("language") ~ str("isOfficial") map {
      case n~l~"T" => (n,l,true)
      case n~l~"F" => (n,l,false)
    } *
  }

  languages.headOption.map { f =>
    SpokenLanguages(
      f._1, 
      languages.find(_._3).map(_._2),
      languages.filterNot(_._3).map(_._2)
    )
  }
}
```

If you try this on the world sample database, you will get:

```
$ spokenLanguages("FRA")
> Some(
    SpokenLanguages(France,Some(French),List(
        Arabic, Italian, Portuguese, Spanish, Turkish
    ))
)
```

> **Next:** [[Integrating with other database access libraries | ScalaDatabaseOthers]]