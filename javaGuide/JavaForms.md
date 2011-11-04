# Handling form submission

## Defining a form

The `play.data` package contains several helpers to handle data submission and validation. The easiest way to handle a form submission is to define a `play.data.Form` that wrap any existing class:

```java
public class User {
    public String email;
    public String password;
}
```

```java
Form<User> userForm = form(User.class);
```

Here the form is able to generate a `User` result value from a `HashMap<String,String>` set of data:

```java
Map<String,String> anyData = new HashMap();
anyData.put("email", "bob@gmail.com");
anyData.put("password", "secret");

User user = userForm.bind(anyData).get();
```

If you have a request available in the scope, you can bind directly from the request content:

```java
User user = userForm.bindFromRequest().get();
```

## Defining constraints

You can define additional constraints that will be checked during the binding using JSR-303 annotations:

```java
public class User {
    
    @Required
    public String email;
    public String password;
}
```

You can also define an adhoc validation by defining a `validate` method on your top object:

```java
public class User {
    
    @Required
    public String email;
    public String password;
    
    public String validate() {
        if(authenticate(e,p) == null) {
            return "Invalid user name or pasword";
        }
        return null;
    }
}
```

## Handling binding failure

Of course as you define constraints, you must handle the binding errors. 

```java
if(userForm.hasErrors()) {
    return badRequest(form.render(userForm));
} else {
    User user = userForm.get();
    return ok("Got user " + user);
}
```

## Fill a form with initial values

Sometimes you want to fill a form with existing values, typically for edition forms:

```scala
userForm.fill(new User("bob@gmail.com", "secret"))
```

## Displaying a form in a template

The `Form` value contains everything needed to display the form to the user:

```html
@(userForm: Form[models.User])

<form action="@routes.Users.create()" method="POST">
    
  <p>
    @userForm.forField("email") { field =>
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
    @userForm.forField("password") { field =>
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

The `views.html.helper` package contains several helpers to handle HTML forms construction.

```html
@(userForm: Form[models.User])

@import helper._

@form(action = routes.Users.create()) {
    @inputText(userForm("email"))
    @inputText(userForm("password"))
}
```




