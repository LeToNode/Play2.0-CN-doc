# Handling form submission

## Defining a form

The `play.api.data` package contains several helpers to handle HTTP form data submission and validation. The easiest way to handle a form submission is to define a `play.data.Form` that wraps an existing class:

```java
public class User {
    public String email;
    public String password;
}
```

```java
Form<User> userForm = form(User.class);
```

This form can generate a `User` result value from `HashMap<String,String>` data:

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

You can define additional constraints that will be checked during the binding phase using JSR-303 (Bean Validation) annotations:

```java
public class User {
    
    @Required
    public String email;
    public String password;
}
```

You can also define an ad-hoc validation by adding a `validate` method to your top object:

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

Of course if you can define constraints, then you need to be able to handle the binding errors.

```java
if(userForm.hasErrors()) {
    return badRequest(form.render(userForm));
} else {
    User user = userForm.get();
    return ok("Got user " + user);
}
```

## Fill a form with initial default values

Sometimes you’ll want to fill a form with existing values, typically for editing:

```java
userForm.fill(new User("bob@gmail.com", "secret"))
```

## Displaying a form in a template

The `Form` value contains everything you need to display the form to the user:

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

The `views.html.helper` package contains several helpers to handle HTML form construction.

```html
@(userForm: Form[models.User])

@import helper._

@form(action = routes.Users.create()) {
    @inputText(userForm("email"))
    @inputText(userForm("password"))
}
```




