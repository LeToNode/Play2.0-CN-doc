# The templating system

Play 2.0 comes with a new and really poweful Scala based template engine. The design of this new template engine is really inspired by ASP.NET Razor, especially:

- **Compact, Expressive, and Fluid**: Minimizes the number of characters and keystrokes required in a file, and enables a fast, fluid coding workflow. Unlike most template syntaxes, you do not need to interrupt your coding to explicitly denote server blocks within your HTML. The parser is smart enough to infer this from your code. This enables a really compact and expressive syntax which is clean, fast and fun to type.
- **Easy to Learn**: Enables you to quickly be productive with a minimum of concepts. You use all your existing Scala language and HTML skills.
- **Is not a new language**: We consciously chose not to create a new language. Instead we wanted to enable developers to use their existing Scala language skills, and deliver a template markup syntax that enables an awesome HTML construction workflow with your language of choice.
- **Works with any Text Editor**: Doesn’t require a specific tool and enables you to be productive in any plain old text editor.

The template are compiled and you will see any errors right into your browser:

[[templatesError.png]]

## Overview

A Play Scala template is a simple text file text file, that contains small blocks of Scala code. It can generate any text-based format (HTML, XML, CSV, etc.).

It’s particularely designed to feel comfortable to those used to working with HTML, allowing Web designers to work with.

They are compiled as standard Scala functions, following a simple naming convention: If you create a `views/Application/index.scala.html` template file, it will generate a `views.html.Application.index` function.

Here is for example, a classic template content:

```html
@(customer: Customer, orders: Seq[Order])
 
<h1>Welcome @customer.name!</h1>

<ul> 
@orders.map { order =>
  <li>@order.title</li>
} 
</ul>
```

And you can easily use it from any Scala code:

```scala
val html = views.html.Application.index(customer, orders)
```

## Syntax: the magic ‘@’ character

The Scala template uses `@` as single special character. Each time this character is encountered, it indicates the begining of a Scala statement. It does not require you to explicitly close the code-block, and will infer it from your code:

```
Hello @customer.name!
       ^^^^^^^^^^^^^
        Scala code
```

Because the template engine will automatically detect the end of your code block by analysing your code, it only allow for simple statements. If you want to insert a multi-token statement, just make it more explicit using brackets:

```
Hello @(customer.firstName + customer.lastName)!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
                    Scala Code
```

You can also use curly bracket, like in plain Scala code, to write a multi-statements block:

```
Hello @{val name = customer.firstName + customer.lastName; name}!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                             Scala Code
```

Because `@` is the only special character, if you want to escape it, just use `@@`:

```
My email is bob@@gmail.com
```

## Template parameters

Because a template is a function, it needs parameters. Template parameters must be declared on the first template line:

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

You can use the Scala `for comprehension`, is a pretty standard way. Just note that the template compiler will just add a `yield` keyword before your block:

```html
<ul>
@for(p <- products) {
  <li>@p.name ($@p.price)</li>
} 
</ul>
```

But as you probably know, here the for comprehension is just syntaxic sugar for a classic map:

```html
<ul>
@products.map { p =>
  <li>@p.name ($@p.price)</li>
} 
</ul>
```

## If-Blocks

Nothing special here. Just use the if instruction from Scala:

```html
@if(items.isEmpty) {
  <h1>Nothing to display</h1>
} else {
  <h1>@items.size items!</h1>
}
```

## Pattern matching

You can also use pattern matching in templates:

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

You can create reusable code block:

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

## Import statements

You can import whatever you want at the begining of your template (or of a sub template):

```scala
@(customer: models.Customer, orders: Seq[models.Order])
 
@import utils._
 
...
```

## Comments

You can write server side comments in templates:

```
@*********************
 * This is a comment *
 *********************@   
```

## Escaping

By default the dynamic content parts are escaped following the template type (Html,Xml,...) rules. If you want to output a raw content fragment, wrap it into the template content type. For example to output raw Html:

```html
<p>
  @Html(article.content)    
</p>
```

## Composing templates (tags, layouts, includes, etc.)

Templates being simple functions you can compose them in any way you want. Below are a few examples of other common scenarios:

### Layout

Let’s declare a `views/main.scala.html` template that will act as main layout:

```html
@(title: String)(content: Html)
 
<h1>@title</h1>
 
<div id="main">
  @content
</div>
```

As you see this template takes 2 parameters: a title and an HTML block. Now we can use it from another `views/Application/index.scala.html` template:

```html
@main(title = "Home") {
    
  <h1>Home page</h1>
    
}
```

### Tags

Let’s write a simple `views/tags/notice.scala.html` tag that display an HTML notice:

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

And let’s use it from any template:

```html
@import tags._
 
@notice("error") { color =>
  Oops, something is <span style="color:@color">wrong</span>
}
```

### Includes

Nothing special, you can just call any other templates (and in fact any other function coming from anywhere):

```html
<h1>Home</h1>
 
<div id="side">
  @common.sideBar()
</div>
```
