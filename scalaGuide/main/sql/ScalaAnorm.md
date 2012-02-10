# Anorm, simple SQL data access

Play includes a simple data access layer called Anorm that uses plain SQL to make your database request and provides several API to parse and transform the resulting dataset.

**Anorm is Not a Object Relational Mapper**

> In the following documentation, we will use the [[MySQL world sample database | http://dev.mysql.com/doc/world-setup/en/world-setup.html]]. 
> 
> If you want to enable it for your application, follow the MySQL website instruction, and enable it for your application by adding the following configuration line in your `conf/application.conf` file:
>
> ```
> db.default.driver= com.mysql.jdbc.Driver
> db.default.url="jdbc:mysql://localhost/world"
> db.default.user=root
> db.default.password=secret
> ```

## Overview

It can feel strange to fallback to plain old SQL to access an SQL Database these days. In particular for Java developers that are accustomed to use high level Object Relational Mapper like Hibernate to completely hide this aspect.

Now if we agree that these tools are almost required in Java, we think that they are not needed at all when you have the power of a higher level programming language like Scala, and in the contrary they will quickly become counter productive.

#### Using JDBC is a pain, but we provide a better API

We agree, using directly the JDBC API is tedious. Particularly in Java. You have to deal everywhere with checked exceptions and iterate over and over around the ResultSet to transform this raw dataset into your own data structure.

But we provide a simpler API for JDBC, using Scala you don’t need to bother with exceptions, and transforming data is really easy with a functional language; in fact it is the point of the Play Scala SQL access layer to provide several API to effectively transform JDBC data into other Scala structures.

#### You don’t need another DSL to access relational Database

SQL is already the best DSL to access relational Databases. We don’t need to invent something new. Moreover the SQL syntax and features can differ from one database vendor to another. 

If you try to abstract this point with another proprietary SQL like DSL you will have to deal with several ‘dialects’ dedicated for each vendor (like Hibernate ones), and limit yourself of using interesting features of a particular Database.

Sometimes we will provide you with prefilled SQL statements. But the idea is not to hide you the fact that we use SQL under the hood. Just save a bunch of characters to type for trivial queries, and you can always fallback to plain old SQL.

#### A type safe DSL to generate SQL is a mistake

Some argue that a type safe DSL is better since all your queries are checked by the compiler. Unfortunately the compiler check your queries based on a MetaModel definition that you often write yourself by ‘mapping’ your data structure to the database schema. 

And there are no guarantees at all that this MetaModel is correct. Even if the compiler says that your code and your queries are correctly typed, it can still miserably fail at runtime because of a mismatch in your actual database definition.

#### Take Control of your SQL code

Object Relationnal Mapper work well for trivial cases. But when you have to deal with complex schemas or existing databases, you will spend most of your time to fighting with your ORM to make it generate the SQL queries you want.

Writing yourself SQL queries can be tedious for a simple ‘Hello World’ application, but for any real life application, you will eventually save time and simplify your code by taking the full control of your SQL code.

## Executing SQL queries

To start you need to learn how to execute SQL queries.

Well, import `anorm._`, and then simply use the `SQL` object to create queries. Of course you need a `Connection` to run a query, so you can retrieve one from the `play.api.db.DB` helper:

```
import anorm._ 

DB.withConnection { implicit c =>
  val result: Boolean = SQL("Select 1").execute()    
} 
```

The `execute()` method returns a Boolean value indicating if the execution was succesful.

To execute an update query, you can use `executeUpdate()` that returns the number of rows updated.

```
val result: Int = SQL("delete from City where id = 99").executeUpdate()
```

Since Scala supports multiline String, feel free to use them for complex SQL statements:

```
val sqlQuery = SQL(
  """
    select * from Country c 
    join CountryLanguage l on l.CountryCode = c.Code 
    where c.code = 'FRA';
  """
)
```

If your SQL query needs dynamic parameters, you can declare placeholders like `{name}` in the query String, and assign them later to any value:

```
SQL(
  """
    select * from Country c 
    join CountryLanguage l on l.CountryCode = c.Code 
    where c.code = {countryCode};
  """
).on('countryCode -> "FRA")
```

## Retrieving data using the Stream API

The first way to access data coming from a Select query, is to use the Stream API.

When you call `apply()` on any SQL statement, you will receive a lazy `Stream` of `Row`, where each row can be seen as a dictionary:

```
// Create an SQL query
val selectCountries = SQL("Select * from Country")
 
// Transform the resulting Stream[Row] as a List[(String,String)]
val countries = selectCountries().map(row => 
  row[String]("code") -> row[String]("name")
).toList
```

In the following example we will count the number of Country in the database. So the resultSet will be a single row with a single column:

```
// First retrieve the first row
val firstRow = SQL("Select count(*) as c from Country").apply().head
 
// Next get the content of the 'c' column as Long
val countryCount = firstRow[Long]("c")
```

## Using Pattern Matching

You can also use Pattern Matching to match and extract the Row content. In this case the column name doesn’t matter. Only the order and the type of the parameters is used to match.

The following example transform each row to the correct Scala type:

```
case class SmallCountry(name:String) 
case class BigCountry(name:String) 
case class France
 
val countries = SQL("Select name,population from Country")().collect {
  case Row("France", _) => France()
  case Row(name:String, pop:Int) if(pop > 1000000) => BigCountry(name)
  case Row(name:String, _) => SmallCountry(name)      
}
```

Note that since `collect(…)` ignore the cases where the partial function isn’t defined, it allow your code to safely ignore rows that you don’t expect.

## Dealing with Nullable columns

If a column can contain `Null` values in the database schema, you need to manipulate it as an `Option` type.

For example, the `indepYear` of the `Country` table being nullable, you need to match it as `Option[Int]`:

```
SQL("Select name,indepYear from Country")().collect {
  case Row(name:String, Some(year:Int)) => name -> year
}
```

If you try to match this column as `Int` it won’t be able to parse `Null` cases. If you try to retrieve the column content as `Int` directly from the dictionnary:

```
SQL("Select name,indepYear from Country")().map { row =>
  row[String]("name") -> row[Int]("indepYear")
}
```

It will produce an `UnexpectedNullableFound(COUNTRY.INDEPYEAR)` exception if it encounter a null value. So you need to map it properly to an `Option[Int]`, as:

```
SQL("Select name,indepYear from Country")().map { row =>
  row[String]("name") -> row[Option[Int]]("indepYear")
}
```

This rule is also true for the parser API we will just see.

## Using the Parser API

You can use the parser API to create generic and reusable parsers able to parse the result of any Select query.

> **Note:** It is really useful since most queries in a Web application will return similar set of data. For example if you have defined a parser able to parse a `Country` from a result set, and another `Language` parser, you can then easily compose them to parse both Country and Language from a join query.
>
> First you need to `import anorm.SqlParser._`

First you need a `RowParser`, ie. a parser able to parse one row to a Scala value. For example we can define a parser to transforn a single column result set row, to as Scala `Long`:

```
val rowParser = scalar[Long]
```

Then we have to transform it into a `ResultSetParser`. Here we will create a parser that parse a single row:

```
val rsParser = scalar[Long].single
```

So this parser will parse a result set to return a `Long`. It is useful to parse to result produced by a simple SQL **select count** query:

```
val count: Long = SQL("select count(*) from Country").as(scalar[Long].single)
```

Let’s write a more complicated parser:

`str("name") ~ int("population")`, will create a `RowParser` able to parse a row containing a String **name** column and a Integer **population** column. Then we can create a `ResultSetParser` that will parse as many rows of this kind as it can using `*`: 

```
val populations:List[String~Int] = {
  SQL("select * from Country").as( str("name") ~ int("population") * ) 
}
```

As you see, the result type of this query is a `List[String~Int]`, so a list of country name and population items.

You can also rewrite the same code as:

```
val result:List[String~Int] = {
  SQL("select * from Country").as(get[String]("name")~get[Int]("population")*) 
}
```

Now what about the `String~Int` type? It is an **anorm** type that it is not really convenient to use outside of your database access code. You could want have a simple tuple `(String,Int)` instead. You can use the `map` function on a `RowParser` to transform its result to a more convenient type:

```
str("name") ~ int("population") map { case n~p => (n,p) }
```

> **Note:** We create a tuple `(String,Int)` here but nothing prevent you to transform the `RowParser` result to any other type, like a custom case class.

Now because transforming `A~B~C` types to `(A,B,C)` is a common task, we provide a `flatten` function that do exatcly that. So you finally write:

```
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

Let's start by parsing all rows as a `List[(String,String)]` (a list of name,language tuple):

```
var p: ResultSetParser[List[(String,String)] = {
  str("name") ~ str("language") map(flatten) *
}
```

Now we get this kind of result:

```
List(
  (France,Arabic), 
  (France,French), 
  (France,Italian), 
  (France,Portuguese), 
  (France,Spanish), 
  (France,Turkish)
)
```

We can then use the Scala collection API, to transform it the the expected result:

```
case class SpokenLanguages(country:String, languages:Seq[String])

languages.headOption.map { f =>
  SpokenLanguages(f._1, languages.map(_._2))
}
```

Finally we get this convenient function:

```
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

Finally, let’s complicate our example to separate the official language and the other ones:

```
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