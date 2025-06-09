# Testing Java Applications

## JUnit 5

JUnit 5 is composed of three subprojects:

- **JUnit Platform** is responsible for launching testing frameworks on the JVM. It also provides a `TestEngine` API
  that can be used for implementing testing frameworks that can be used with the JUnit platform. It also provides a
  `ConsoleLauncher` that build tools like Gradle and Maven can use.
- **JUnit Jupiter** is a test framework for writing tests in JUnit 5 that implements the `TestEngine` API provided by
  the JUnit Platform. It also provides annotations, assertions, and other APIs for writing tests.
- **JUnit Vintage** is a test engine that provides support for running JUnit 3 and JUnit 4 tests on the JUnit 5
  platform. It allows developers to migrate their existing JUnit 3 and JUnit 4 tests to JUnit 5 without the need for
  significant code changes.

Test methods are annotated with `org.junit.jupiter.api.Test`.

Test methods aren’t required to be public, can be package-private.

Test methods can have parameters. This is achieved by providing a `ParameterResolver`.

Test classes aren't required to have no-args constructors.

### LifeCycle {collapsible="true"}

Each test case is governed by a test life cycle which consists of the following three phases:

- **Setup phase**, where the test infrastructure is put in place. Two levels of setup:
    - class level:
        - Method annotated with `org.junit.jupiter.api.BeforeAll` where a costly object like database connection can be
          created for all the tests in a class.
        - It must be static and non-private.
    - individual test setup methods:
        - Method annotated with `org.junit.jupiter.api.BeforeEach` executes before each and every test, thus doing away
          with any side effects from other test executions.
        - A test case can have any number of methods marked with `BeforeEach`, but the execution order is not
          guaranteed.
- **Test execution phase**. Result verification is also part of the test execution phase. The execution result will
  signify a success or failure.
- **Cleanup phase**, where any cleanup required after posttest execution is performed. Two levels of cleanup:
    - `@AfterAll` performs a single time method invocation (i.e., post the execution of all test cases of a test class).
      It must be static and non-private.
    - `@AfterEach` - posttest execution cleanup. The method must not be static.

Optionally, we could extract out the `@BeforeAll` and `@AfterAll` methods to a superclass. This would clean up the
duplicate code if the database is being used in more test cases.

### JUnit Annotations {collapsible="true"}

* `@Test`
* `@ParameterizedTest`
* `@ValueSource`, `@CsvSource`
* `@RepeatedTest`
* `@BeforeAll`
* `@AfterAll`
* `@BeforeEach`
* `@AfterEach`
* `@Disabled`
* `@DisplayName`
* `@Tag`
* `@Nested`

### Assertions {collapsible="true"}

Assertions are static methods from `org.junit.jupiter.api.Assertions`.

| Assert Method    | What It Does                                          |
|------------------|-------------------------------------------------------|
| assertTrue       | Assert that condition is true                         |
| assertFalse      | Assert that condition is false                        |
| assertNull       | Assert that object is null                            |
| assertNotNull    | Assert that object is not null                        |
| assertEquals     | Assert that expected and actual are equal             |
| assertNotEquals  | Assert that expected and actual are not equal         |
| assertArrayEqual | Assert that expected and actual arrays are equals     |
| assertSame       | Assert that expected and actual refer to the same obj |
| assertNotSame    | Assert that expected and actual do not refer to same  |
| assertThrow      | Assert that an exception is thrown                    |
| assertAll        | Group multiple assertions and evaluate all            |

Each `assertXXX` method has at least three overloaded methods:

- `assertNull(str);`
- `assertNull(str, "str should be null");`
- `assertNull(str, () -> "str should be null"); // lazy`

Most `assert` methods take:

- Expected value
- Actual value

### AssertJ {collapsible="true"}

AssertJ is a third-party library of assertions.

```java
import static org.assertj.core.api.Assertions.assertThat;
```

- Basic assertions:
    - assertThat(actual).isEqualTo(expected);
    - assertThat(actual).isNotNull();
- String
    - assertThat(actual).startsWith(expected);
    - assertThat(actual).endsWith(expected);
    - assertThat(actual).isEqualToIgnoringCase(expected);
- Collections
    - assertThat(list).contains(expected);
    - assertThat(list).hasSize(size);
- Map
    - assertThat(map).containsKey(key);
    - assertThat(map).hasSize(size);
- Number
    - assertThat(value).isGreaterThan(expected);
    - assertThat(value).isBetween(lower, upper);
- Object
    - assertThat(obj).isInstanceOf(expected);
    - assertThat(obj).hasNoNullFieldsOrProperties();

## Mockito

```java

@RunWith(MockitoJUnitRunner.class)
public class MyTest {
    @Mock
    private MyClass myMock;
}
```

* **Mocks**: Fake implementations

* **Two ways to mock**:

    * `Mockito.mock(SomeClass.class)`
    * `@Mock` annotation

* **Stubs**:

```java
when(mockedList.get(0)).

thenReturn("first");
```

* **Spies**:

```java

@Spy
private MyClass mySpy;
```

### BDDMockito {collapsible="true"}

```java
import static org.mockito.BDDMockito.*;

given(seller.askForBread()).

willReturn(new Bread());
```

### Argument Matchers {collapsible="true"}

```java
when(mockList.get(anyInt())).

thenReturn(42);
```

Custom matcher:

```java
when(mockList.get(argThat(arg ->arg >=0&&arg <=9))).

thenReturn(42);
```

### Verification {collapsible="true"}

```java
verify(myMock).

doSomething();

verify(myMock, times(2)).

doSomething();
```

* For void methods:

```java
doThrow(RuntimeException .class).

when(repository).

delete(null);
```

* `doReturn`, `doThrow`, `doAnswer`, `doNothing`, `doCallRealMethod`

## Testing Spring Applications

### Spring TestContext Framework

Unit and integration testing in Spring is supported by the annotation-based **Spring TestContext Framework**, found in
the `org.springframework.test.context` package.

It supports multiple test frameworks like **JUnit** and **TestNG**.

To improve performance, it reuses the same application context across tests instead of reloading it each time.

You configure the test context using annotations such as `@ContextConfiguration` and `@ActiveProfiles`.

### Spring Boot Testing

Spring Boot provides test support through two main modules:

* `spring-boot-test` for core testing utilities
* `spring-boot-test-autoconfigure` for autoconfiguration in tests

Most projects use the `spring-boot-starter-test` dependency, which includes both modules and libraries like **JUnit
Jupiter**, **AssertJ**, **Hamcrest**, **JSONassert**, and **JsonPath**.

To load the full Spring Boot context in tests, use the `@SpringBootTest` annotation. It replaces `@ContextConfiguration`
and enables access to Spring Boot features. For narrower tests, other annotations are available (e.g. `@WebMvcTest`,
`@DataJpaTest`).

* With **JUnit 4**, annotate the test class with `@RunWith(SpringRunner.class)`.
* With **JUnit 5**, no extra annotation is needed—`@SpringBootTest` and similar annotations already include
  `@ExtendWith(SpringExtension.class)`.

These annotations enable features like dependency injection and support for `@Autowired`, `@MockBean`, and others in
test classes.

### Annotations

#### Mocking {collapsible="true"}

`@Mock` and `@InjectMocks` are Mockito annotations that used to create mock objects and inject them into the class under
test.

`@MockBean` is a Spring Boot annotation that replaces a Spring-managed bean with a mock in tests that load the
application context.

#### Auto-Configured Tests {collapsible="true"}

Spring Boot's autoconfigured annotations help to load parts of the complete application and test specific layers of the
codebase:

- `@DataMongoTest`. Test MongoDB applications. By default, it configures an in-memory embedded MongoDB if the driver is
  available through dependencies, configures a `MongoTemplate`, scans for `@Document` classes, and configures Spring
  Data MongoDB repositories.
- `@DataRedisTest`. Test Redis applications. It scans for `@RedisHash` classes and configures Spring Data Redis
  repositories by default.
- `@RestClientTest`. Test REST clients. It autoconfigures different dependencies such as Jackson, GSON, and Jsonb
  support; configures a `RestTemplateBuilder`; and adds support for `MockRestServiceServer` by default.
- `@JsonTest`. Initializes the Spring application context only with those beans needed to test JSON serialization.

#### Other Annotations {collapsible="true"}

- `@AutoConfigureMockMvc`. Used to autoconfigure a `MockMvc` instance for testing Spring MVC controllers.
- `@TestPropertySource`. Used to specify one or more properties files to load for testing.
- `@Transactional`. Used to indicate that a test method should be run within a transaction, which will be rolled back
  after the test completes.
- `@DirtiesContext`. Used to indicate that a test method modifies the application context and should cause the context
  to be recreated for subsequent tests.
- `@DatabaseSetup`. Allows you to populate a database with test data before running a test. This annotation is typically
  used in integration tests where you want to ensure that the database is in a specific state before the test starts.

## References

- [](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations)
