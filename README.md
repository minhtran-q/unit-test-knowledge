# Junit 5 Knowledge
## Architecture

<details>
  <summary>Overview</summary>
  <br/>
  
  ![](images/detailed_JUnit_5_architecture.png)
  
  Ref: https://nipafx.dev/junit-5-architecture-jupiter/
  
</details>

## Annotation
## In Spring Boot

<details>
  <summary>@SpringBootTest</summary>
  <br/>
  
  Ref: https://reflectoring.io/spring-boot-test/
  
</details>

## Mockito
### Annotation
<details>
  <summary>Popular annotation</summary>
  <br/>
  
  Annotation | Description |
  --- | --- |
  @Mock | Use to create and inject mocked instances without having to call `Mockito.mock` manually. |
  @Spy |  |
  @Captor |  |
  @InjectMocks |  |
   
  Ref: https://www.baeldung.com/mockito-annotations
  
</details>
<details>
  <summary>@Mock vs @MockBean</summary>
  <br/>
  
  **@Mock**
  
  This annotation is a shorthand for the `Mockito.mock()` method. The `Mockito.mock()` method allows us to create a mock object of a class or an interface. 
  
  **@MockBean**
  
  Use the `@MockBean` to add mock objects to the _Spring application context_.
   
  Ref: https://www.baeldung.com/java-spring-mockito-mock-mockbean
  
</details>

Ref: https://www.javadoc.io/doc/org.mockito/mockito-core/2.23.4/org/mockito/Mockito.html#3
