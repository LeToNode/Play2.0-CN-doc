# Computer database

[[computerdatabase.png]]

> This sample application is available both in Java and Scala.

This is a classic CRUD application, backed by a JDBC database. It demonstrates:

- accessing a JDBC database, using Ebean in Java and Anorm in Scala.
- achieving, table pagination and CRUD forms.
- integrating with a CSS framework ([[Twitter Bootstrap | http://twitter.github.com/bootstrap/]]).

Twitter Bootstrap requires a different form layout to the default one that the Play 2.0 form helper generates, so this application also provides an example of integrating a custom form input constructor:

```html
@(elements: helper.InputElements)

@**************************************************
* Generate input according Twitter Bootstrap rules *
**************************************************@
<div class="clearfix @if(!elements.errors.isEmpty) {error}">
    @elements.label
    <div class="input">
        @elements.input
        <span class="help-inline">@elements.infos</span> 
    </div>
</div>
```
