# Unit Test Knowledge
## Architecture
<details>
  <summary>Overview</summary>
  <br/>
  
  ![](images/detailed_JUnit_5_architecture.png)
  
  Ref: https://nipafx.dev/junit-5-architecture-jupiter/
  
</details>

### Terminology
<details>
  <summary>What exactly is a "unit" in unit testing?</summary>
  <br/>
  
   A unit is "the smallest piece of the code that can be usefully tested".
  
</details>
<details>
  <summary>Unit Test vs Integration Test</summary>
  <br/>
  
  **Unit Test**
  
  A unit test covers a single “**unit**”, where a unit commonly is a single class.
  
  **Integration Test**
  
  An integration test can be any of the following:
  
  + A test that covers multiple “units”. It tests the interaction between two or more classes
  + A test that covers multiple layers, might cover the interaction between a business service and the persistence layer, for instance.
  + A test that covers the whole path through the application. We send a request to the application and check that it responds correctly and has changed the database state according to our expectations.
  
</details>
<details>
  <summary>@SpringBootTest vs Mockito</summary>
  <br/>
  
  In my opinion, even we're using a mocked bean, but we're still running within spring context and for me this is an **integration test**(a unit test doesn't need any spring context to run within).
  
  With unit testing  just use **Mockito** or another framework that doesn’t need spring context(`@SpringBootTest`). When writing a test for a service class to test some calculation logic, we don’t need spring context and this is a **PURE** unit test.
  
  When running a test in spring context, this is considered an integration test even if you're using `@MockBean`.
  
  Ref: https://stackoverflow.com/questions/54658563/unit-test-or-integration-test-in-spring-boot
</details>
<details>
  <summary>JUnit 5 vs Mockito</summary>
  <br/>

  JUnit 5 focuses on writing and running tests, while Mockito focuses on creating and managing mock objects.

  **JUnit 5**
  + Uses annotations like `@Test`, `@BeforeEach`, `@AfterEach` to define test methods and lifecycle methods.
  + Provides methods to assert conditions in your tests, such as assertEquals(), assertTrue(), and assertNull().

  **Mockito**
  + Allows creating mock objects to mimic the behavior of real objects.
  + Defines the behavior of mock objects.
  
</details>

### Annotation

## Mockito
### Terminology
<details>
  <summary>Stubbing vs Stub</summary>
  <br/>

  **Stub:**
  + A stub is a mock object that has been configured to return specific values or perform specific actions when certain methods are called.
  
  ```
  @Test
  void getUser_success() {
    UserDao userDao = Mockito.mock(UserDao.class); // Create a stub for the UserDAO
  }
  ```

  **Stubbing:**
  + Stubbing is the process of defining the behavior of a mock object’s method. When you stub a method, you specify what it should return when called with certain arguments.
  + The act of using fake objects.

  **Example:**
  ```
  when(mockObject.someMethod()).thenReturn(someValue);
  ```
</details>

<details>
  <summary>What is strict stubbing?</summary>
  <br/>
  
  + Strict stubbing in Mockito is a feature designed to make your tests cleaner and more maintainable. Strict stubbing is enabled by default in _Mockito 3.0 and later_

  Features of Strict Stubbing:
  + Detects Unused Stubs
  + Argument Mismatch Detection

  _Example:_

  ```
  // Stubbing a method
  when(mockObject.someMethod()).thenReturn(someValue);
  
  // If someMethod() is never called in the test, Mockito will throw an UnnecessaryStubbingException
  ```
</details>

### Annotation
<details>
  <summary>Popular annotation</summary>
  <br/>
  
  Annotation | Description |
  --- | --- |
  @Mock | Use to create and inject mocked instances without having to call `Mockito.mock` manually. |
  @Spy | Part of the object will be mocked and part will use real method invocations. |
  @Captor | To capture **arguments** that are passed to the methods of mocked objects.  |
  @InjectMocks | Creates an instance of the class and injects the mocks that are created with the `@Mock` (or `@Spy`). |

  
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
<details>
  <summary>@Mock vs @Spy</summary>
  <br/>
  
  `@Mock`

  + **Purpose:** Creates a mock object that simulates the behavior of a real object.
  + **Behavior:** By default, all methods of the mock return default values (e.g., null for objects, 0 for integers).

  _Example:_
  ```
  @Mock
  List<String> mockedList;
  
  @Test
  public void testMock() {
      mockedList.add("one");
      Mockito.verify(mockedList).add("one");
      assertEquals(0, mockedList.size()); // size is still 0 because it's a mock
  }
  ```
  
  `@Spy`
  
  + **Purpose:** Creates a spy object that wraps a real instance of the class.
  + **Behavior:** By default, all methods of the spy call the real methods unless they are stubbed.

  _Example:_
  ```
  @Spy
  List<String> spyList = new ArrayList<>();

  @Test
  public void testSpy() {
      spyList.add("one");
      spyList.add("two");

      verify(spyList).add("one");
      verify(spyList).add("two");

      assertEquals(2, spyList.size());
      assertEquals("one", spyList.get(0));
      assertEquals("two", spyList.get(1));
  }
  ```

  ```
  @Spy
  MyService myService = new MyService();

  @InjectMocks
  MyController myController;

  @Test
  public void testServiceSpy() {
      doReturn("Mocked Response").when(myService).someMethod();

      String response = myController.handleRequest();

      assertEquals("Mocked Response", response);
      assertEquals("Another Real Response", anotherResponse); // This will call the real method
      verify(myService).someMethod();
      verify(myService).anotherMethod();
  }

  class MyService {
      public String someMethod() {
          return "Real Response";
      }
  
      public String anotherMethod() {
          return "Another Real Response";
      }
  }

  class MyController {
      private final MyService myService;
  
      public String handleRequest() {
          return myService.someMethod();
      }
  }
  ```
  + In this example, The someMethod is stubbed to return a mocked response, while other methods of MyService can still be called normally.
  
</details>
<details>
  <summary>How @Captor work?</summary>
  <br/>

  + The `@Captor` annotation in Mockito is used to create an instance of `ArgumentCaptor`, which allows you to capture arguments passed to methods during testing. This is useful when you want to inspect the arguments that were passed to a method call.
  + You use the `capture(` method of ArgumentCaptor in conjunction with `verify()` to capture the arguments passed to a method.

  _Example:_

  ```
  public class NotificationService {
      private final MessageSender messageSender;
  
      public void sendNotification(String recipient, String message) {
          Message msg = new Message(recipient, message);
          messageSender.send(msg);
      }
  }
  ```

  ```
  public interface MessageSender {
      void send(Message message);
  }
  ```
  ```
  public class Message {
      private String recipient;
      private String content;
  
      ...
  }
  ```
  ```
  @ExtendWith(MockitoExtension.class)
  public class NotificationServiceTest {
  
      @Mock
      MessageSender messageSender;
  
      @InjectMocks
      NotificationService notificationService;
  
      @Captor
      ArgumentCaptor<Message> messageCaptor;
  
      @Test
      public void testSendNotification() {
          // Act
          notificationService.sendNotification("user@example.com", "Hello, User!");
  
          // Capture the argument
          verify(messageSender).send(messageCaptor.capture());
          Message capturedMessage = messageCaptor.getValue();
  
          // Assert
          assertEquals("user@example.com", capturedMessage.getRecipient());
          assertEquals("Hello, User!", capturedMessage.getContent());
      }
  }
  ```
  
</details>
<details>
  <summary>@InjectMocks vs @Mock</summary>
  <br/>

  `@InjectMocks`:

  + Used to create a mock object of the class under test. Classes have `@Mock` or `@Spy` will Automatically injects to class hold `@InjectMocks`.

  `@Mock`:
  
  + Used to create a mock object of any class.

  
</details>
<details>
  <summary>Explain @ExtendWith(MockitoExtension.class)</summary>
  <br/>
  
</details>
<details>
  <summary>Explain @RunWith(MockitoJUnitRunner.class)</summary>
  <br/>
  
</details>
<details>
  <summary>@Timeout</summary>
  <br/>
  
</details>

### Mocking Static Methods
<details>
  <summary>Example Mocking Static method</summary>
  <br/>
  
</details>

## Spring Boot Test

<details>
  <summary>@SpringBootTest</summary>
  <br/>
  
  Spring Boot provides the `@SpringBootTest` annotation which we can use to create an application context containing all the objects we need for all of the test types.
  
  The @SpringBootTest annotation loads the complete Spring application context.
  
  _However, that overusing `@SpringBootTest` might lead to very long-running test suites._
  
  Ref: https://reflectoring.io/spring-boot-test/
  
</details>
<details>
  <summary>@WebMvcTest</summary>
  <br/>
  
</details>
<details>
  <summary>@DataJpaTest</summary>
  <br/>
  
</details>
<details>
  <summary>@DataJdbcTest</summary>
  <br/>
  
</details>
<details>
  <summary>@WebFluxTest</summary>
  <br/>
  
</details>

