# The templating system

Play 2.0 comes with a new and really powerful Scala-based template engine. This new template engine’s design was inspired by ASP.NET Razor, specifically it is:

- **compact, expressive, and fluid**: it minimizes the number of characters and keystrokes required in a file, and enables a fast, fluid coding workflow. Unlike most template syntaxes, you do not need to interrupt your coding to explicitly denote server blocks within your HTML. The parser is smart enough to infer this from your code. This enables a really compact and expressive syntax which is clean, fast and fun to type.
- **easy to learn**: it enables you to quickly be productive with a minimum of concepts. You use all your existing Scala language and HTML skills.
- **not a new language**: we consciously chose not to create a new language. Instead we wanted to enable developers to use their existing Scala language skills, and deliver a template markup syntax that enables an awesome HTML construction workflow with your language of choice.
- **editable in any text editor**: it doesn’t require a specific tool and enables you to be productive in any plain old text editor.

Templates are compiled, so you will see any errors right in your browser:

[[templatesError.png]]

## Overview

A Play Scala template is a simple text file, that contains small blocks of Scala code. They can generate any text-based format, such as HTML, XML or CSV.

The template system has been designed to feel comfortable to those used to working with HTML, allowing web designers to easily work with the templates.

Templates are compiled as standard Scala functions, following a simple naming convention: If you create a `views/Application/index.scala.html` template file, it will generate a `views.html.Application.index` function.

For example, here is a simple template:

```html
@(customer: Customer, orders: Seq[Order])
 
<h1>Welcome @customer.name!</h1>

<ul> 
@orders.map { order =>
  <li>@order.title</li>
} 
</ul>
```

You can then call this from any Scala code:

```scala
val html = views.html.Application.index(customer, orders)
```

## Syntax: the magic ‘@’ character

The Scala template uses `@` as the single special character. Every time this character is encountered, it indicates the begining of a Scala statement. It does not require you to explicitly close the code-block - this will be inferred from your code:

```
Hello @customer.name!
       ^^^^^^^^^^^^^
        Scala code
```

Because the template engine automatically detects the end of your code block by analysing your code, this syntax only supports simple statements. If you want to insert a multi-token statement, explicitly mark it using brackets:

```
Hello @(customer.firstName + customer.lastName)!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
                    Scala Code
```

You can also use curly brackets, as in plain Scala code, to write a multi-statements block:

```
Hello @{val name = customer.firstName + customer.lastName; name}!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                             Scala Code
```

Because `@` is a special character, you’ll sometimes need to escape it. Do this by using `@@`:

```
My email is bob@@example.com
```

## Template parameters

A template is simply a function, so it needs parameters, which must be declared on the first line of the template file:

```scala
@(customer: models.Customer, orders: Seq[models.Order])
```

You can also use default values for parameters:

```scala
@(title: String = "Home")
```

Or even several parameter groups:

```scala
@(title:String)(body: => Html)
```

And even implicit parameters:

```scala
@(title: String)(body: => Html)(implicit request: play.api.mvc.Request)
```

## Looping

You can use the Scala for-comprehension, in a pretty standard way. But note that the template compiler will add a `yield` keyword before your block:

```html
<ul>
@for(p <- products) {
  <li>@p.name ($@p.price)</li>
} 
</ul>
```

As you probably know, this for-comprehension is just syntactic sugar for a classic map:

```html
<ul>
@products.map { p =>
  <li>@p.name ($@p.price)</li>
} 
</ul>
```

## If-blocks

If-blocks are nothing special. Simply use Scala’s standard `if` statement:

```html
@if(items.isEmpty) {
  <h1>Nothing to display</h1>
} else {
  <h1>@items.size items!</h1>
}
```

## Pattern matching

You can also use pattern matching in your templates:

```html
@connected match {
    
  case models.Admin(name) => {
    <span class="admin">Connected as admin (@name)</span>
  }

  case models.User(name) => {
    <span>Connected as @name</span>
  }
    
}
```

## Declaring reusable blocks

You can create reusable code blocks:

```html
@display(product: models.Product) = {
  @product.name ($@product.price)
}
 
<ul>
@products.map { p =>
  @display(product = p)
} 
</ul>
```

Note that you can also declare reusable pure Scala blocks:

```html
@title(text: String) = @{
  text.split(' ').map(_.capitalize).mkString(" ")
}
 
<h1>@title("hello world")</h1>
```

## Declaring reusable values

You can define scoped values using the `defining` helper:

```html
@defining(user.firstName + " " + user.lastName) { fullName =>
  <div>Hello @fullName</div>
}
```

## Import statements

You can import whatever you want at the beginning of your template (or sub-template):

```scala
@(customer: models.Customer, orders: Seq[models.Order])
 
@import utils._
 
...
```

## Comments

You can write server side block comments in templates using `@* *@`:

```
@*********************
 * This is a comment *
 *********************@   
```

## Escaping

By default the dynamic content parts are escaped following the template type (e.g. HTML or XML) rules. If you want to output a raw content fragment, wrap it in the template content type. For example to output raw HTML:

```html
<p>
  @Html(article.content)    
</p>
```

## Composing templates (tags, layouts, includes, etc.)

Templates, being simple functions, can be composed in any way you want. Below are a few examples of some common scenarios:

### Layout

Let’s declare a `views/main.scala.html` template that will act as a main layout template:

```html
@(title: String)(content: Html)
<!DOCTYPE html>
<html>
  <head>
    <title>@title</title>
  </head>
  <body>
    <section class="content">@content</section>
  </body>
</html>

```

As you can see, this template takes two parameters: a title and an HTML content block. Now we can use it from another `views/Application/index.scala.html` template:

```html
@main(title = "Home") {
    
  <h1>Home page</h1>
    
}
```

Sometimes you need a second page-specific content block for a sidebar or breadcrumb trail, for example. You can do this with an additional parameter:

```html
@(title: String)(sidebar: Html)(content: Html)
<!DOCTYPE html>
<html>
  <head>
    <title>@title</title>
  </head>
  <body>
    <section class="sidebar">@sidebar</section>
    <section class="content">@content</section>
  </body>
</html>
```

Using this from our ‘index’ template, we have:

```html
@main("Home") {
  <h1>Sidebar</h1>

} {
  <h1>Home page</h1>

}
```

Alternatively, we can declare the sidebar block separately:

```html
@sidebar = {
  <h1>Sidebar</h1>
}

@main("Home")(sidebar) {
  <h1>Home page</h1>

}
```


### Tags

Let’s write a simple `views/tags/notice.scala.html` tag that displays an HTML notice:

```html
@(level: String = "error")(body: (String) => Html)
 
@level match {
    
  case "success" => {
    <p class="success">
      @body("green")
    </p>
  }

  case "warning" => {
    <p class="warning">
      @body("orange")
    </p>
  }

  case "error" => {
    <p class="error">
      @body("red")
    </p>
  }
    
}
```

And now let’s use it from another template:

```html
@import tags._
 
@notice("error") { color =>
  Oops, something is <span style="color:@color">wrong</span>
}
```

### Includes

Again, there’s nothing special here. You can just call any other template you like (and in fact any other function coming from anywhere at all):

```html
<h1>Home</h1>
 
<div id="side">
  @common.sideBar()
</div>
```
