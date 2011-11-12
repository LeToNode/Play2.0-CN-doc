# Handling form submission

## Defining a form

The `play.api.data` package contains several helpers to handle HTTP form data submission and validation. The easiest way to handle a form submission is to define a `play.api.data.Form` structure:

```scala
import play.api.data._

val loginForm = Form(
  of(
    "email" -> of[String],
    "password" -> of[String]
  )
)
```

This form can generate a `(String, String)` result value from `Map[String,String]` data:

```scala
val anyData = Map("email" -> "bob@gmail.com", "password" -> "secret")
val (user,password) = loginForm.bind(anyData).get
```

If you have a request available in the scope, you can bind directly to it from the request content:

```scala
val (user,password) = loginForm.bindFromRequest.get
```

## Wrapping an existing case class

A form can use any function to construct the type. So you can, for example, define a form that wraps an existing case class:

```scala
case class User(name: String, age: Int)

val userForm = Form(
  of(User)(
    "name" -> of[String],
    "age" -> of[Int]
  )
)

val anyData = Map("email" -> "bob@gmail.com", "age" -> "18")
val user:User = userForm.bind(anyData).get
```

## Defining constraints

For each mapping you can also define additional validation constraints that will be checked during the binding phase:

```scala
case class User(name: String, age: Int)

val userForm = Form(
  of(User)(
    "name" -> of[String] verifying required,
    "age" -> of[Int] verifying (min(0), max(100))
  )
)
```

You can even define ad-hoc constraints on the fields:

```scala
val loginForm = Form(
  of(
    "email" -> of[String],
    "password" -> of[String]
  ) verifying("Invalid user name or pasword", { 
      case (e,p) => User.authenticate(e,p).isDefined 
  })
)
```

## Handling binding failure

Of course, if you define constraints - you must handle the binding errors. You can use the `fold` operation for that:

```scala
loginForm.bindFromRequest.fold(
  f => // binding failure, you retrieve the form containing errors,
  v => // binding success, you get the actual value 
)
```

## Fill a form with initial default values

Sometimes you’ll want to fill a form with existing values, typically for editing:

```scala
userForm.fill(User("Bob", 18))
```

## Using high-level mapping

The `play.api.data` package defines several high-level mappings that you can use directly.

```scala
val userForm = Form(
  of(User)(
    "email" -> email,
    "age" -> number(min=0)
  )
)
```

Where the `email` mapping is defined as:

```scala
val email = of[String] verifying pattern(
  """\b[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}\b""".r,
  "constraint.email",
  "error.email"
)
```

## Nested values

A form can define a nested value:

```scala
case class User(name: String, address: Address)
case class Address(street: String, city: String)

val userForm = Form(
  of(User)(
    "name" -> text,
    "address" -> of(Address)(
        "street" -> text,
        "city" -> text
    )
  )
)
```

## Displaying a form in a template

The `Form` value contains everything needed to display the form to the user:

```html
@(userForm: Form[models.User])

<form action="@routes.Users.create()" method="POST">
    
  <p>
    @userForm.forField("name") { field =>
      <label>@field.name</label>
      <input type="text" value="@field.value">
      @field.error.map { error =>
        <p class="error">
          @error
        </p>
      } 
    }    
  </p>
  
  <p>
    @userForm.forField("age") { field =>
      <label>@field.name</label>
      <input type="text" value="@field.value">
      @field.error.map { error =>
        <p class="error">
          @error
        </p>
      } 
    }    
  </p>
    
</form>
```

## Using the template form helpers

The `views.html.helper` package contains several helpers to handle HTML form construction.

```html
@(userForm: Form[models.User])

@import helper._

@form(action = routes.Users.create()) {
    @inputText(userForm("name"))
    @inputText(userForm("age"))
}
```




