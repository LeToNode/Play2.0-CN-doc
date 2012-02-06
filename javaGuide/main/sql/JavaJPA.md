# Integrating with JPA

## First, expose the datasource through JNDI

JPA requires to retrieve the datasource from JNDI. You can expose any Play managed datasource via JDNI by adding this configuration in `conf/application.conf`:

```
db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:mem:play"
db.default.jndiName=DefaultDS
```

## Add a JPA implementation to your project

There is no built-in JPA implementation in Play 2.0. You can choose any available implementation. For example, to use hibernate, just add the Hibernate dependency to your project:

```
val appDependencies = Seq(
  "org.hibernate" % "hibernate-entitymanager" % "3.6.9.Final"
)
```

## Create a persistence Unit

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

## Annotate JPA actions with `@Transactional`

Every JPA call must be done in a transaction. So to enable JPA for a particular action, annotate it with `@play.db.jpa.Transactional`. It will compose your action method with a JPA `Action` that will manage the transaction for you:

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

## Use the `play.db.jpa.JPA` helper

At any time you can retrieve the current entity manager from the `play.db.jpa.JPA` helper class:

```
public static Company findById(Long id) {
  return JPA.em().find(Company.class, id);
}
```

> **Next:** [[Using the Cache | JavaCache]]

