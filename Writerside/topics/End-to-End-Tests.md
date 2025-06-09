# End-to-End Tests

## WebTestClient

[WebTestClient documentation](https://docs.spring.io/spring-framework/reference/testing/webtestclient.html)

`WebTestClient` is an HTTP client used for performing end-to-end HTTP tests in Spring applications.  
It allows testing against:

- a mock server (without starting a real HTTP server)
- a running server (when bound to a `@SpringBootTest` with `RANDOM_PORT`)

Example use cases include verifying HTTP endpoints, request/response behavior, and full request processing logic in
Spring WebFlux or Web MVC applications.

### WebTestClient Setup Options

You can bind `WebTestClient` to:

- application context via mock infrastructure
- a real HTTP server using `@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)`

---

## Cucumber

[Cucumber documentation](https://cucumber.io/)

Cucumber is a popular open-source BDD (Behavior-Driven Development) tool. It allows writing executable specifications in
plain English using **Gherkin** syntax.

### Key Concepts

- A **scenario** is a test case written in Gherkin.
- Each scenario consists of steps: **Given**, **When**, and **Then**.
- Steps are mapped to Java methods via **step definitions**.
- Scenarios are stored in `.feature` files under `src/test/resources`.

Scenarios are written before production code to define behavior. Once implemented, they serve as both **documentation**
and **automated tests**.

Example file structure:

