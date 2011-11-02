# Handling form submission

## Defining a form

The `play.api.data` package contains several helpers to handle data submission and validation. The easiest way to handle a form submission is to define a `play.api.data.Form` structure:

```scala
import play.api.data._

val loginForm = Form(
  of(
    'email -> of[String],
    'password -> of[String]
  )
)
```

Here the form is able to generate a `(String, String)` result value from a `Map[String,String]` set of value:

```scala
val anyData = Map("email" -> "bob@gmail.com", "password" -> "secret")
val (user,password) = loginForm.bind(anyData).get
```

If you have a request available in the scope, you can bind directly from the request content:

```scala
val (user,password) = loginForm.bindFromRequest.get
```

## Wrapping an existing case class

A form can use any function to construct the type. So you can for example define a form wrapping an existing case class:

```scala
case class User(name: String, age: Int)

val userForm = Form(
  of(User)(
    'name -> of[String],
    'age -> of[Int]
  )
)

val anyData = Map("email" -> "bob@gmail.com", "age" -> "18")
val user:User = userForm.bind(anyData).get
```

## Defining constraints

For each mapping you can define additional constraints that will be checked during the binding:

```scala
case class User(name: String, age: Int)

val userForm = Form(
  of(User)(
    'name -> of[String] verifying required,
    'age -> of[Int] verifying (min(0), max(100))
  )
)
```

You can also define adhoc constraints:

```scala
val loginForm = Form(
  of(
    'email -> of[String],
    'password -> of[String]
  ) verifying("Invalid user name or pasword", { 
      case (e,p) => User.authenticate(e,p).isDefined 
  })
)
```

## Handling binding failure

Of course as you define constraints, you must handle the binding errors. You can use the `fold` operation for that:

```scala
loginForm.bindFromRequest(
  f => // binding failure, you retrieve the form containing errors,
  v => // binding success, you get the actual value 
)
```




