# The templating system

Play 2.0 comes with a new and really powerful Scala based template engine. The design of this new template engine has been inspired by ASP.NET Razor, specifically:

- **Compact, Expressive, and Fluid**: Minimizes the number of characters and keystrokes required in a file, and enables a fast, fluid coding workflow. Unlike most template syntaxes, you do not need to interrupt your coding to explicitly denote server blocks within your HTML. The parser is smart enough to infer this from your code. This enables a really compact and expressive syntax which is clean, fast and fun to type.
- **Easy to Learn**: Enables you to quickly be productive with a minimum of concepts. You use all your existing Scala language and HTML skills.
- **Is not a new language**: We consciously chose not to create a new language. Instead we wanted to enable developers to use their existing Scala language skills, and deliver a template markup syntax that enables an awesome HTML construction workflow with your language of choice.
- **Works with any Text Editor**: Doesn’t require a specific tool and enables you to be productive in any plain old text editor.

Play templates are compiled, so you'll be notified of any errors directly in your browser:

[[templatesError.png]]

## Overview

A Play template is a simple text file, that contains small blocks of Scala code. It can generate any text-based format (HTML, XML, CSV, etc.).

The template system has been designed to feel comfortable to those used to working with HTML, allowing Web designers to easily work with the templates.

They are compiled as standard Scala functions, following a simple naming convention: If you create a `views/Application/index.scala.html` template file, it will generate a `views.html.Application.index` function.

Here's an example of simple Play template:

```html
@(customer: Customer, orders: Seq[Order])
 
<h1>Welcome @customer.name!</h1>

<ul> 
@for(order <- orders) {
  <li>@order.title</li>
} 
</ul>
```

You can then call this from any Java code:

```java
Html html = views.html.Application.index.render(customer, orders);
```

> Note even though you have to write the dynamic expressions in Scala, it's not very different than how you'd write it in Java - you don't have to learn much Scala before writing your first templates.

## Syntax: the magic ‘@’ character

The Scala template uses `@` as the single special character. Every time this character is encountered, it indicates the beginning of a Scala statement. It does not require you to explicitly close the code-block - this will be inferred from your code:

```
Hello @customer.name!
       ^^^^^^^^^^^^^
        Scala code
```

Because the template engine automatically detects the end of your code block by analysing your code, it only allow for simple statements. If you want to insert a multi-token statement, explicitly mark it using brackets:

```
Hello @(customer.firstName + customer.lastName)!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
                    Scala Code
```

You can also use curly bracket (like in plain Scala code) to write a multi-statements block:

```
Hello @{val name = customer.firstName + customer.lastName; name}!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                             Scala Code
```

Because `@` is the only special character, you'll sometimes need to escape it. Do this by using `@@`:

```
My email is bob@@gmail.com
```

## Template parameters

A template is simply a function, so it needs parameters. Template parameters must be declared on the first line of the template file:

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

You can use the Scala `for comprehension`, in a pretty standard way. Note that the template compiler will add a `yield` keyword before your block:

```html
<ul>
@for(p <- products) {
  <li>@p.name ($@p.price)</li>
} 
</ul>
```

## If-Blocks

Nothing special here. Simply use the standard Scala `if` instruction:

```html
@if(items.isEmpty) {
  <h1>Nothing to display</h1>
} else {
  <h1>@items.size items!</h1>
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

You can import whatever you want at the beginning of your template (or sub template):

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

By default the dynamic content parts are escaped following the template type (Html,Xml,...) rules. If you want to output a raw content fragment, wrap it in the template content type. For example to output raw Html:

```html
<p>
  @Html(article.content)    
</p>
```

## Composing templates (tags, layouts, includes, etc.)

Templates, being simple functions, can be composed in any way you want. Below are a few examples of some common scenarios:

### Layout

Let’s declare a `views/main.scala.html` template that will act as our main layout:

```html
@(title: String)(content: Html)
 
<h1>@title</h1>
 
<div id="main">
  @content
</div>
```

As you can see, this template takes 2 parameters: a title and an HTML block. Now we can use it from another `views/Application/index.scala.html` template:

```html
@main(title = "Home") {
    
  <h1>Home page</h1>
    
}
```

### Tags

Let’s write a simple `views/tags/notice.scala.html` tag that display an HTML notice:

```html
@(level: String = "error")(body: (String) => Html)

@if(level == "success") {
  <p class="success">
    @body("green")
  </p> 
} 

@if(level == "warning") {
  <p class="warning">
    @body("orange")
  </p>    
}

@if(level == "error") {
  <p class="error">
    @body("red")
  </p>    
}
```

And noe let’s use it from any template:

```html
@import tags._
 
@notice("error") { color =>
  Oops, something is <span style="color:@color">wrong</span>
}
```

### Includes

Again, there's nothing special here. You can just call any other template you like (and in fact any other function coming from anywhere at all):

```html
<h1>Home</h1>
 
<div id="side">
  @common.sideBar()
</div>
```
