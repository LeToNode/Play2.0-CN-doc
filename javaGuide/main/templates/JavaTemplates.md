# 模版引擎

## 一套基于Scala的类型安全模版引擎

Play 2.0  使用了一套全新的且真正强大的基于Scala语言的模版引擎, 该模版引擎的设计灵感来源于 ASP.NET 的 Razor . 具体来说就是:

- **结构紧凑、有表现力并且编写流畅**: 它把一个文件中需要的键盘输入字符减少到最少, 并且提供一种快速、流畅的编码流程.不同于其他模版语言，新的Play模版引擎不需要你打乱自己的HTML编码，明确书写一些服务器端代码块。新的解析器足够聪明，可以帮你做这些工作.这就使得这种简单、快速和有趣的模版语言更加紧凑和具有表现力.
- **易于学习**: it allows you to quickly become productive, with a minimum of concepts. You use simple Scala constructs and all your existing HTML skills.
- **非新语言**: we consciously chose not to create a new language. Instead we wanted to enable Scala developers to use their existing Scala language skills, and deliver a template markup syntax that enables an awesome HTML construction workflow.
- **可由各种文本编辑器编写**: 编写模版并不需要特定的工具,模版文件可以由任何文本编辑器来进行编写.

> **Note:** Even though the template engine uses Scala as expression language, this is not a problem for Java developers. You can almost use it as if the language were Java. 
> 
> Remember that a template is not a place to write complex logic. You don’t have to write complicated Scala code here. Most of the time you will just access data from your model objects, as follows:
>
> ```
> myUser.getProfile().getUsername()
> ```
> Parameter types are specified using a suffix syntax. Generic types are specified using the `[]` symbols instead of the usual `<>` Java syntax. For example, you write `List[String]`, which is the same as `List<String>` in Java.

Templates are compiled, so you will see any errors in your browser:

[[images/templatesError.png]]

## Overview

A Play Scala template is a simple text file that contains small blocks of Scala code. Templates can generate any text-based format, such as HTML, XML or CSV.

The template system has been designed to feel comfortable to those used to working with HTML, allowing front-end developers to easily work with the templates.

Templates are compiled as standard Scala functions, following a simple naming convention. If you create a `views/Application/index.scala.html` template file, it will generate a `views.html.Application.index` class that has a `render()` method.

For example, here is a simple template:

```html
@(customer: Customer, orders: List[Order])
 
<h1>Welcome @customer.name!</h1>

<ul> 
@for(order <- orders) {
  <li>@order.getTitle()</li>
} 
</ul>
```

You can then call this from any Java code as you would normally call a method on a class:

```java
Content html = views.html.Application.index.render(customer, orders);
```

## Syntax: the magic ‘@’ character

The Scala template uses `@` as the single special character. Every time this character is encountered, it indicates the beginning of a dynamic statement. You are not required to explicitly close the code block - the end of the dynamic statement will be inferred from your code:

```
Hello @customer.getName()!
       ^^^^^^^^^^^^^^^^^^
          Dynamic code
```

Because the template engine automatically detects the end of your code block by analysing your code, this syntax only supports simple statements. If you want to insert a multi-token statement, explicitly mark it using brackets:

```
Hello @(customer.getFirstName() + customer.getLastName())!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
                          Dynamic Code
```

You can also use curly brackets, to write a multi-statement block:

```
Hello @{val name = customer.getFirstName() + customer.getLastName(); name}!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                  Dynamic Code
```

Because `@` is a special character, you’ll sometimes need to escape it. Do this by using `@@`:

```
My email is bob@@example.com
```

## Template parameters

A template is like a function, so it needs parameters, which must be declared at the top of the template file:

```scala
@(customer: models.Customer, orders: List[models.Order])
```

You can also use default values for parameters:

```scala
@(title: String = "Home")
```

Or even several parameter groups:

```scala
@(title:String)(body: Html)
```

## Iterating

You can use the `for` keyword, in a pretty standard way:

```html
<ul>
@for(p <- products) {
  <li>@p.getName() ($@p.getPrice())</li>
} 
</ul>
```

## If-blocks

If-blocks are nothing special. Simply use Scala’s standard `if` statement:

```html
@if(items.isEmpty()) {
  <h1>Nothing to display</h1>
} else {
  <h1>@items.size() items!</h1>
}
```

## Declaring reusable blocks

You can create reusable code blocks:

```html
@display(product: models.Product) = {
  @product.getName() ($@product.getPrice())
}
 
<ul>
@for(product <- products) {
  @display(product)
} 
</ul>
```

Note that you can also declare reusable pure code blocks:

```html
@title(text: String) = @{
  text.split(' ').map(_.capitalize).mkString(" ")
}
 
<h1>@title("hello world")</h1>
```

> **Note:** Declaring code block this way in a template can be sometime useful but keep in mind that a template is not the best place to write complex logic. It is often better to externalize these kind of code in a Java class (that you can store under the `views/` package as well if your want).

By convention a reusable block defined with a name starting with **implicit** will be marked as `implicit`:

```
@implicitFieldConstructor = @{ MyFieldConstructor() }
```

## Declaring reusable values

You can define scoped values using the `defining` helper:

```html
@defining(user.getFirstName() + " " + user.getLastName()) { fullName =>
  <div>Hello @fullName</div>
}
```

## Import statements

You can import whatever you want at the beginning of your template (or sub-template):

```scala
@(customer: models.Customer, orders: List[models.Order])
 
@import utils._
 
...
```

To make an absolute resolution, use **_root_** prefix in the import statement.

```scala
@import _root_.company.product.core._
```

## Comments

You can write server side block comments in templates using `@* *@`:

```
@*********************
 * This is a comment *
 *********************@   
```

You can put a comment on the first line to document your template into the Scala API doc:

```
@*************************************
 * Home page.                        *
 *                                   *
 * @param msg The message to display *
 *************************************@
@(msg: String)

<h1>@msg</h1>
```

## Escaping

By default, dynamic content parts are escaped according to the template type’s (e.g. HTML or XML) rules. If you want to output a raw content fragment, wrap it in the template content type. 

For example to output raw HTML:

```html
<p>
  @Html(article.content)    
</p>
```

> **Next:** [[Common use cases | JavaTemplateUseCases]]