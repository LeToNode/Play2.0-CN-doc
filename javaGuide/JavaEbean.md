# Using the Ebean ORM

## Configuring Ebean

Play 2.0 comes with the [[Ebean | http://www.avaje.org/]] ORM. To enable it, add the following line to `conf/application.conf`:

```properties
ebean.default=models.*
```

This defines a `default` Ebean server, using the `default` data source, which must be properly configured. You can actually create as many Ebean servers you need, and explicitly define the mapped class for each server.

```properties
ebean.orders=models.Order,models.OrderItem
ebean.customers=models.Customer,models.Address
```

In this example, we have access to two Ebean servers - each one using its own database.

## Using the play.db.ebean.Model superclass

Play 2.0 defines a convenient superclass for your Ebean model classes. Here is a typical Ebean class mapped in Play 2.0:

```java
package models;

import java.util.*;
import javax.persistence.*;

import play.db.ebean.*;
import play.data.format.*;
import play.data.validation.*;

@Entity 
public class Task extends Model {

  @Id
  @Constraints.Min(10)
  public Long id;
  
  @Constraints.Required
  public String name;
  
  public boolean done;
  
  @Formats.DateTime(pattern="dd/MM/yyyy")
  public Date dueDate = new Date();
  
  public static Finder<Long,Task> find = new Finder(
    Long.class, Task.class
  ); 

}
```

As you can see we've added a `find` static field, defining a `Finder` for an entity of type `Task` with a `Long` identifier. This helper field is then used to simplify querying our model:

```
// Find all tasks
List<Task> tasks = Task.find.all();
    
// Find a task by id
Task anyTask = Task.find.byId(34);

// Delete a task
Task.find.ref(34).delete();

// More complex task query
List<Task> tasks = find.where()
    .ilike("name", "%coco%")
    .orderBy("dueDate asc")
    .findPagingList(25)
    .getPage(1);
```

> For more information about Ebean, see the [[Ebean documentation | http://www.avaje.org/ebean/documentation.html]].