# Integration Tests

## About

Integration tests focus on how multiple components work together.

## Integration Testing in Spring Framework

The Spring Framework provides first-class support for integration testing through the `spring-test` module, which
includes classes in the `org.springframework.test` package.

## Comparison to Unit and End-to-End (E2E) Tests

Integration tests in Spring do not require an application server or external deployment environment.  
They are slower than unit tests but significantly faster than full end-to-end tests like those using Selenium.

While integration tests often avoid mocking, mocking is still appropriate when isolating external systems or avoiding
dependencies such as third-party APIs or message brokers.

Because integration tests can be time-consuming and may require resources like a real or embedded database, they should
be separated from unit tests.  
It is recommended to use a dedicated test profile and run them separately (e.g., using a `test` or `integration`
Maven/Gradle task).

## Common Use Cases

- Testing Spring MVC endpoints, including request-response flow, error handling, and security
- Verifying interaction between services and external dependencies (e.g., databases, messaging systems, web services)
- Testing Spring configuration: bean definitions, property sources, and active profiles
- Full-stack testing across the web, service, and persistence layers

## Integration Testing in Spring Boot

### `@SpringBootTest`

Used to load the complete application context for integration testing.  
Allows the use of `@Autowired` to inject any bean discovered through component scanning.  
Can be applied at the class level or to individual methods for customized test contexts.

## MockMvc

The Spring MVC Test framework (`MockMvc`) supports testing MVC controllers without starting a real server.

Unlike unit tests of controllers, `MockMvc` tests involve a full request-processing pipeline—filters, interceptors, and
controller logic—though still within the test environment.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@SpringBootTest
@AutoConfigureMockMvc
class MyMockMvcTests {

    @Test
    void testWithMockMvc(@Autowired MockMvc mvc) throws Exception {
        mvc.perform(get("/"))
                .andExpect(status().isOk())
                .andExpect(content().string("Hello World"));
    }
}
````

Documentation:
[https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework/server-setup-options.html](https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework/server-setup-options.html)

### MockMvc Setup Options

* Programmatically configure Spring MVC to point directly to controllers
* Use Spring configuration that includes the full MVC and controller infrastructure

## WebTestClient

`WebTestClient` is used for testing reactive applications built with Spring WebFlux.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.context.SpringBootTest.WebEnvironment;
import org.springframework.test.web.reactive.server.WebTestClient;

@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
class MyRandomPortWebTestClientTests {

    @Test
    void exampleTest(@Autowired WebTestClient webClient) {
        webClient.get().uri("/")
                .exchange()
                .expectStatus().isOk()
                .expectBody(String.class).isEqualTo("Hello World");
    }
}
```

**Note**: This requires the following dependency:

```groovy
implementation 'org.springframework.boot:spring-boot-starter-webflux'
```

## Performing Requests with MockMvc

Documentation:
[https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework/server-performing-requests.html](https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework/server-performing-requests.html)

## Active Profiles

Use `@ActiveProfiles` to declare which profile should be active when loading the `ApplicationContext` for an integration
test.

```java

@ContextConfiguration
@ActiveProfiles("dev")
class DeveloperTest {
}
```

## Example Integration Test Configuration

`application-integrationtest.properties`

```properties
spring.datasource.url=jdbc:h2:mem:test
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect
```
