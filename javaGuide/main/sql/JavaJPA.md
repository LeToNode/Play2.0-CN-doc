# Integrating with JPA

## Exposing the datasource through JNDI

JPA requires the datasource to be accessible via JNDI. You can expose any Play-managed datasource via JDNI by adding this configuration in `conf/application.conf`:

```
db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:mem:play"
db.default.jndiName=DefaultDS
```

## Adding a JPA implementation to your project

There is no built-in JPA implementation in Play 2.0; you can choose any available implementation. For example, to use Hibernate, just add the dependency to your project:

```
val appDependencies = Seq(
  "org.hibernate" % "hibernate-entitymanager" % "3.6.9.Final"
)
```

## Creating a persistence unit

Next you have to create a proper `persistence.xml` JPA configuration file. Put it into the `conf/META-INF` directory, so it will be properly added to your classpath.

Here is a sample configuration file to use with Hibernate:

```
<persistence xmlns="http://java.sun.com/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://java.sun.com/xml/ns/persistence http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd"
             version="2.0">
             
    <persistence-unit name="defaultPersistenceUnit" transaction-type="RESOURCE_LOCAL">
        <provider>org.hibernate.ejb.HibernatePersistence</provider>
        <non-jta-data-source>DefaultDS</non-jta-data-source>
        <properties>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
        </properties>
    </persistence-unit>
    
</persistence>
```

## Annotating JPA actions with `@Transactional`

Every JPA call must be done in a transaction so, to enable JPA for a particular action, annotate it with `@play.db.jpa.Transactional`. This will compose your action method with a JPA `Action` that manages the transaction for you:

```
@Transactional
public static Result index() {
  ...
}
```

If your action perfoms only queries, you can set the `readOnly` attribute to `true`:

```
@Transactional(readOnly=true)
public static Result index() {
  ...
}
```

## Using the `play.db.jpa.JPA` helper

At any time you can retrieve the current entity manager from the `play.db.jpa.JPA` helper class:

```
public static Company findById(Long id) {
  return JPA.em().find(Company.class, id);
}
```

> **Next:** [[Using the cache | JavaCache]]

