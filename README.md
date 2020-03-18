# JUnit 5 Migration



## Table of Contents

- [Why should we upgrade?](#why-should-we-upgrade)
- [New features in JUnit 5](#new-features-in-junit-5)
- [How to start using JUnit 5](#how-to-start-using-junit-5)
- [Differences between JUnit 4 and JUnit 5](#differences-between-junit-4-and-junit-5)
- [Help, questions, comments](#help-questions-comments)



## Why should we upgrade?

- Modularity - you can now import only what is needed rather than the entire framework.
- Java 8 features (lambdas, etc) in tests.
- Have multiple runners (extensions in JUnit 5) run simultaneously on tests.
- Lots of [new features](#new-features-in-junit-5) available.
- You will need to do it at some point.



## New features in JUnit 5

Below are some nice features that have been introduced with JUnit 5.

### Parameterized & repeated tests

Whilst parameterized tests are possible in JUnit 4, they require the test class to be ran with a special runner, and are a bit clunky to implement. In JUnit 5, running parameterized tests is a lot easier and more flexible, with support for many different types of ways of defining the values to run a test with.

```java
@ParameterizedTest
@ValueSource(strings = { "Hello", "World" })
public void aParameterizedTest(String param) {
}
```

> Note: the *org.junit.jupiter:junit-jupiter-params* dependency is required to run paramererized tests.



Tests can also be repeated more easily with the new @RepeatedTest annotation:

```java
@RepeatedTest(5)
public void testOnePlusOneEqualsTwo() {
  // test will be executed 5 times
}
```



### Tests with readable names

The @DisplayName annotation was introduced to let you name test methods with a more readable name:

```java
@Test
@DisplayName("1 + 1 = 2")
public void testOnePlusOneEqualsTwo() {
}
```



### Nested tests

In JUnit 5, tests can now be nested using inner classes. Nested tests can help express the relationship between closely related tests and cut down on boilerplate:

```java
@Test
@DisplayName("A calculator")
public class CalculatorTest {
  
  @Nested
  @DisplayName("performing operation")
  class Operations {
    
    @Test
    @DisplayName("addition: 1 + 1 = 2")
    public void testAdd() {
      //
    }
    
    @Test
    @DisplayName("subtraction: 9 - 4 = 5")
    public void testSubtraction() {
      //
    }
  }
}
```



### Extension API

The extension API lets third party libraries/code hook into virtually every part of the execution of tests, adding or changing existing behaviour. Unlike the concept of *runners* in JUnit 4, the extension API provides a mechanism to add behaviour to the part of a test's execution without needing to write a complete runner, and also allows **multiple extensions to run simultaneously** on a test, meaning that extensions like those that come with Spring and Mockito can now run together on the same test.



### Assumptions

Assumptions let you specify conditions which must be true at runtime for a given test to be ran. If the conditions are not met, then the test will be skipped:

```java
@Test
public void testSomething() {
    assumeTrue(false);
    // this test code won't run
}
```

Tests can also be disabled for certain operating systems or java versions:

```java
@Test
@DisabledOnOs(OS.MAC)
@DisabledOnJre(JRE.JAVA_8)
public void testSomething() {
}
```





## How to start using JUnit 5

### Build configuration

To get started with JUnit 5, add the following dependencies/configuration to the project's build config.

##### For Gradle

Add the following test dependencies to the build.gradle file:

```
dependencies {
    testImplementation group: 'org.junit.jupiter', name: 'junit-jupiter-api', version: '5.6.0'
    testRuntime group: 'org.junit.jupiter', name: 'junit-jupiter-engine', version: '5.6.0'
}
```

And add the below to tell Gradle to use JUnit 5 to run the tests:

```
test {
    useJUnitPlatform()
}
```

##### For Maven

Add the following test dependency to the pom.xml file:

```xml
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter-engine</artifactId>
  <version>5.6.0</version>
  <scope>test</scope>
</dependency>
```

The Maven Surefire or Failsafe plugins will now (from version 2.22.0) execute the projects tests with JUnit 5.



### Running JUnit 4 and JUnit 5 together

The quickest way to get started with migrating your project to use JUnit 5 is to have all newly created tests use the new JUnit 5 APIs, whilst existing tests are slowly migrated over from JUnit 4. This is possible thanks to JUnit 5 Vintage, which lets you execute tests written for JUnit 4 on the JUnit 5 Platform.

To have JUnit 5 run both JUnit 5 & JUnit 4 tests, add the `junit-vintage-engine` dependency to your project, as well as the standard build configuration required for JUnit 5:

##### For Gradle

```
dependencies {
	testRuntime group: 'org.junit.vintage', name: 'junit-vintage-engine', version: '5.6.0'
}
```

##### For Maven

Add the following test dependencies to the pom.xml file:

```xml
<dependency>
  <groupId>org.junit.vintage</groupId>
  <artifactId>junit-vintage-engine</artifactId>
  <version>5.6.0</version>
  <scope>test</scope>
</dependency>
```

With this dependency on the classpath, JUnit 5 will now discover & run the JUnit 4 tests in the project.



> **Using JUnit Vintage is a great option for legacy projects, or those that have a large amount of existing JUnit 4 tests, but having all the tests be on the JUnit 5 API should be the goal if feasible.**
>
> **The new features of JUnit 5 can decrease the size/number of tests needed and improve readability whilst maintaining the existing functionality.** 



## Differences between JUnit 4 and JUnit 5

This is not meant to be an exhaustive list of differences between the two versions, but the highlights of the more commonly used features of JUnit.



### Annotation changes

The following annotations have changed FQNs:

| JUnit 4                                                      | JUnit 5                                         |
| ------------------------------------------------------------ | ----------------------------------------------- |
| @org.junit.**Test**                                          | @org.junit.jupiter.api.**Test**                 |
| @org.junit.**Before**                                        | @org.junit.jupiter.api.**BeforeEach**           |
| @org.junit.**After**                                         | @org.junit.jupiter.api.**AfterEach**            |
| @org.junit.**BeforeClass**                                   | @org.junit.jupiter.api.**BeforeAll**            |
| @org.junit.**AfterClass**                                    | @org.junit.jupiter.api.**AfterAll**             |
| @org.junit.**Ignore**                                        | @org.junit.jupiter.api.**Disabled**             |
| @org.junit.experimental.categories.**Category** (See [Filtering/excluding tests](#filtering/excluding-tests)) | @org.junit.jupiter.api.**Tag**                  |
| @org.junit.**Rule** (See [Rules](#rules))                    | @org.junit.jupiter.api.extension.**ExtendWith** |
| @org.junit.**ClassRule** (See [Rules](#rules))               | @org.junit.jupiter.api.extension.**ExtendWith** |
| @org.junit.runner.**RunWith** (See [Runners]())              | @org.junit.jupiter.api.extension.**ExtendWith** |



### Assertions

The static assertion methods (e.g. *assertEquals*, *assertNotNull*, etc.) on the `org.junit.Assert` class now live in `org.junit.jupiter.api.Assertions`.

Replacing all instances of `import static org.junit.Assert.` with `import static org.junit.jupiter.api.Assertions.` should be enough to migrate these functions.



### Asserting exceptions and specifying timeouts

In JUnit 4, asserting that an exception should be thrown in the test is achieved with the *expected* parameter in the @Test annotation:

```java
@Test(expected = MyException.class)
public void testSomething() {
    // test code here
}
```

In JUnit 5, this is replaced by placing the test code that should throw the exception inside a lambda, and with the *assertThrows* method:

```java
@Test
public void testSomething() {
  MyException ex = Assertions.assertThrows(MyException.class, () -> {
      // test code that should throw exception here
  });
}
```



Similarly, in JUnit 4, specifying that a test should be halted (and failed) after a certain length of time is achieved with the *timeout* parameter in the @Test annotation:

```java
@Test(timeout = 5000)
public void testSomething() {
    // test code here
}
```

In JUnit 5, this is replaced by placing the test code that should be time-limited inside a lambda, and with the *assertTimeout* method:

```java
@Test
public void testSomething() {
  Assertions.assertTimeout(Duration.ofMillis(5000), () -> {
      // test code here
  });
}
```



### Rules

In JUnit 4, adding behaviour to test classes  (e.g. executing tasks before or after tests are ran) in a more re-usable way than *@Before* and *@After* was achieved with *rules*, which are applied to a test class with the *@Rule* and *@ClassRule* annotations.

```java
@Rule
public final TemporaryFolder tempFolder = new TemporaryFolder();
```

In JUnit 5, functionality given by rules should be re-written using the *Extension API*, a powerful API provided by JUnit 5 that lets you hook into every step of the test execution.



### Filtering/excluding tests

In JUnit 4, you can group tests together by creater marker interfaces such as `IntegrationTest` and then applying the @Category annotation to the test class or methods:

```java
@Category(IntegrationTest.class)
public class DatabaseTests
{
  ...
}
```

Groups of tests can then be ran either ran, or excluded, in various different ways.

In **JUnit 5**, the @Category annotation has been replaced with @Tag and now **requires a string argument**, not a class.

To find out more about how to use the new @Tag annotation to filter/exclude tests, see [Using Tags](using-tags.md).



### Runners

In JUnit 4, when running a test class, it was always executed by exactly one *runner*. A runner controls everything about how a test is executed, including actually invoking the test method. The default runner is `BlockJUnit4ClassRunner`, but this can be changed by using the *@RunWith* annotation:

```java
@RunWith(SpringRunner.class)
public class MyTest {
}
```

Like JUnit 4 *rules*, the concept of runners has been replaced with the *Extension API* in JUnit 5. Tests are no longer ran by custom runners, instead, extensions are applied with the *@ExtendWith* annotation, to implement the behaviours that their runner equiviolents previously did. 

Any JUnit 4 tests being ran with the *MockitoJUnitRunner* should be replaced with the *MockitoExtension* provided by the `org.mockito:mockito-junit-jupiter` dependency:

```java
@ExtendWith(MockitoExtension.class)
public class MyTest {
}
```

Similary, any JUnit 4 tests are being ran with the *SpringJUnit4ClassRunner* (or *SpringRunner*), should be replaced with the *SpringExtension* provided by Spring 5:

```java
@ExtendWith(SpringExtension.class)
public class MyTest {
}
```



## Help, questions, comments

If you have any questions or comments, join the *#junit5-adoption* channel or DM me (*@stevenss*) on Slack.
