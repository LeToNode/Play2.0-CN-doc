# Handling form submission

## Defining a form

The `play.data` package contains several helpers to handle HTTP form data submission and validation. The easiest way to handle a form submission is to define a `play.data.Form` that wraps an existing class:

```java
public class User {
    public String email;
    public String password;
}
```

```java
Form<User> userForm = form(User.class);
```

> **Note:** Underlying binding is done using [[Spring data binder| http://static.springsource.org/spring/docs/3.0.7.RELEASE/reference/html/validation.html]].

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

> **Tip:** The `play.data.validation.Constraints` class contains several built-in validation annotations.

You can also define an ad-hoc validation by adding a `validate` method to your top object:

```java
public class User {
    
    @Required
    public String email;
    public String password;
    
    public String validate() {
        if(authenticate(email,password) == null) {
            return "Invalid email or password";
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

> **Next:** [[Using the form template helpers | JavaFormHelpers]]




