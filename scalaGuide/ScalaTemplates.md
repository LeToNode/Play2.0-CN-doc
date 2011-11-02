# The templating system

Play 2.0 comes with a new and really poweful Scala based template engine. The design of this new template engine is really inspired by ASP.NET Razor, especially:

- **Compact, Expressive, and Fluid**: Minimizes the number of characters and keystrokes required in a file, and enables a fast, fluid coding workflow. Unlike most template syntaxes, you do not need to interrupt your coding to explicitly denote server blocks within your HTML. The parser is smart enough to infer this from your code. This enables a really compact and expressive syntax which is clean, fast and fun to type.
- **Easy to Learn**: Enables you to quickly be productive with a minimum of concepts. You use all your existing Scala language and HTML skills.
- **Is not a new language**: We consciously chose not to create a new language. Instead we wanted to enable developers to use their existing Scala language skills, and deliver a template markup syntax that enables an awesome HTML construction workflow with your language of choice.
- **Works with any Text Editor**: Doesn’t require a specific tool and enables you to be productive in any plain old text editor.

## Overview

A Play Scala template is a simple text file text file, that contains small blocks of Scala code. It can generate any text-based format (HTML, XML, CSV, etc.).

It’s particularely designed to feel comfortable to those used to working with HTML, allowing Web designers to work with.

They are compiled as standard Scala functions, following a simple naming convention: If you create a `views/Application/index.scala.html` template file, it will generate a `views.html.Application.index` function.

Here is for example, a classic template content:

```html
@(customer:Customer, orders:Seq[Order])
 
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



