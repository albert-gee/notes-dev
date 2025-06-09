# Hibernate

## Configuration

Hibernate is typically configured via an XML file named `hibernate.cfg.xml`, located in the classpath:

```xml

<hibernate-configuration>
    <session-factory>
        <property name="connection.driver_class">org.h2.Driver</property>
        <property name="connection.url">jdbc:h2:./db1</property>
        <property name="connection.username">sa</property>
        <property name="connection.password"/>
        <property name="dialect">org.hibernate.dialect.H2Dialect</property>
        <property name="show_sql">true</property>
        <property name="hbm2ddl.auto">create-drop</property>
        <mapping class="chapter01.hibernate.Message"/>
    </session-factory>
</hibernate-configuration>
````

When using an application server or Spring, Hibernate can be integrated via resource injection or via configuration in
the Spring context.

## Integration Steps

To apply Hibernate to a project:

1. Identify POJOs with database representation.
2. Annotate POJOs to map fields to table columns.
3. Add Hibernate and database driver dependencies.
4. Configure `hibernate.cfg.xml` or use Spring configuration.
5. Create a `SessionFactory` and use `Session` objects for CRUD operations.

## Connection Pooling

Hibernate includes a basic connection pool, not suitable for production. In production:

* Use HikariCP (default in Spring Boot).
* Configure external pool via classpath or JNDI.
* Example dependency for HikariCP:

```xml

<dependency>
    <groupId>org.hibernate.orm</groupId>
    <artifactId>hibernate-hikaricp</artifactId>
    <version>${hibernate.core.version}</version>
</dependency>
```

## Entity Requirements

* Must have a `public` or `protected` no-arg constructor.
* Should not be `final`.

## JPA Annotations

* `@Entity`: declares a class as an entity.
* `@Id`: marks the primary key field.
* `@GeneratedValue`: configures ID generation strategy.
* `@Column`: customizes column mapping.

## ID Generation Strategies

* `IDENTITY`: best for MySQL (auto-increment).
* `SEQUENCE`: preferred (e.g., PostgreSQL, Oracle).
* `TABLE`: slow, not recommended.
* `AUTO`: delegates to provider.
* `NONE`: requires manual assignment.

```java

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

## UUID Identifier

```java

@Id
@GeneratedValue
private UUID courseId;
```

## Custom Identifier Generator

Use `@GenericGenerator` and implement `IdentifierGenerator`:

```java

@Id
@GeneratedValue(generator = "prod-generator")
@GenericGenerator(
        name = "prod-generator",
        strategy = "com.example.MyGenerator",
        parameters = @Parameter(name = "prefix", value = "prod")
)
private String prodId;
```

## Composite Identifiers

### `@EmbeddedId`

```java

@Embeddable
public class OrderEntryPK implements Serializable {
    private long orderId;
    private long productId;
}

@Entity
public class OrderEntry {
    @EmbeddedId
    private OrderEntryPK entryId;
}
```

### `@IdClass`

```java

@Entity
@IdClass(OrderEntryPK.class)
public class OrderEntry {
    @Id
    private long orderId;
    @Id
    private long productId;
}
```

## Derived Identifiers

Use `@MapsId` to derive one entity’s ID from another:

```java

@Entity
public class UserProfile {
    @Id
    private long profileId;

    @OneToOne
    @MapsId
    private User user;
}
```

## Persistence Context States

* **Transient**: newly created, not associated with persistence context.
* **Persistent**: managed by session, tracked for changes.
* **Detached**: was persistent, but session closed or evicted.
* **Removed**: marked for deletion.

## CRUD and Session Methods

* `save()`, `saveOrUpdate()`, `update()`
* `get()` (returns null if not found), `load()` (throws if not found)
* `merge()`, `refresh()`
* `delete()`, `flush()`
* `isDirty()`: check if session has unflushed changes

## Transactions

A transaction groups operations into a single atomic unit:

* Call `commit()` to save changes.
* Use `rollback()` to discard changes.

## Cascading

Cascade options allow operations on one entity to propagate to related entities:

* `PERSIST`
* `MERGE`
* `REFRESH`
* `REMOVE`
* `DETACH`
* `ALL`

Example:

```java

@OneToOne(cascade = {CascadeType.REFRESH, CascadeType.MERGE})
private EntityType otherSide;
```

## Lazy Loading

Hibernate loads collections and associations lazily by default unless configured otherwise.

---

## References

* [Hibernate 6 Documentation](https://hibernate.org/orm/documentation/6.0/)
* [Spring Data JPA Reference](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
* [Baeldung: Hibernate Identifiers](https://www.baeldung.com/hibernate-identifiers)
