# Computer database

[[computerdatabase.png]]

> This sample application is available both in Java and Scala.

This is a classical CRUD application, backed by a JDBC database. It demonstrates:

- Accessing a JDBC database, using Ebean in Java and Anorm in Scala.
- Achieving, table pagination and CRUD forms.
- Integrating with a CSS framework (twitter bootsrap).

Because [[twitter bootstrap | http://twitter.github.com/bootstrap/]] requires a form layout that is different from the default one generate by Play 2.0 form helper, it provides an example of integrating a custom form input constructor:

```html
@(elements: helper.InputElements)

@**************************************************
* Generate input according twitter bootsrap rules *
**************************************************@
<div class="clearfix @if(!elements.errors.isEmpty) {error}">
    @elements.label
    <div class="input">
        @elements.input
        <span class="help-inline">@elements.infos</span> 
    </div>
</div>
```
