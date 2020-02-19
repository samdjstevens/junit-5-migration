# JUnit 5 Migration

[todo]

- gradle examples as well/instead as maven?



## Table of Contents

- [Why should we upgrade?](#why-should-we-upgrade)
- Differences between versions
- How to upgrade
- [Useful Links](#useful-links)



### Why should we upgrade?

- Modularity - can now import only what is needed rather than the entire framework.
- Java 8 features in tests.
- Multiple runners simultaneously on tests.
- @DisplayName







### Differences between versions



#### Annotation changes

The following annotations have changed FQNs:

| JUnit 4                                                      | JUnit 5                                         |
| ------------------------------------------------------------ | ----------------------------------------------- |
| @org.junit.**Test**                                          | @org.junit.jupiter.api.**Test**                 |
| @org.junit.**Before**                                        | @org.junit.jupiter.api.**BeforeEach**           |
| @org.junit.**After**                                         | @org.junit.jupiter.api.**AfterEach**            |
| @org.junit.**BeforeClass**                                   | @org.junit.jupiter.api.**BeforeAll**            |
| @org.junit.**AfterClass**                                    | @org.junit.jupiter.api.**AfterAll**             |
| @org.junit.**Ignore**                                        | @org.junit.jupiter.api.**Disabled**             |
| @org.junit.experimental.categories.**Category** (See [Filtering Tests](#filtering-tests)) | @org.junit.jupiter.api.**Tag**                  |
| @org.junit.**Rule** (See [Test Rules](#test-rules) for usage) | @org.junit.jupiter.api.extension**.ExtendWith** |
| @org.junit.**ClassRule** (See [Test Rules](#test-rules) for usage) | @org.junit.jupiter.api.extension.**ExtendWith** |
| @org.junit.runner.**RunWith** (See [Runners]() for usage) | @org.junit.jupiter.api.extension.**ExtendWith** |

[todo: cover all?]





#### Testing exceptions and timeouts

- Can't use @Test(expected = MyException.class) anymore. Instead, we have [assertThrows]().

  ```java
  Assertions.assertThrows(Exception.class, () -> {
    // test code here
  });
  ```

  Similarly, cant test for @Test(timeout = 1) anymore. Use assertTimeout.



#### Test Rules



#### Filtering Tests

In JUnit 4, you can choose to only execute certain tests by creater marker interfaces such as `IntegrationTest` and then applying the `@Category(IntegrationTest.class)` annotation to the test method. To then only run tests in a certain category, there are a couple of options. With maven surefire plugin do this:

[todo: test suite runner]

```xml
<plugin>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.20.1</version>
    <configuration>
        <groups>fully.qualified.name.IntegrationTest</groups>
    </configuration>
</plugin>
```

Similarly, the `excludedGroups` element can be used to exclude tests of a certain category.



In **JUnit 5**, the *@Category* annotation has been replaced with `@Tag` and now **reqhires a string argument**, and not a class. 

Filtering with Maven is done in the same way as JUnit 4, but using the *includeTags* tag, and passing in the tag string instead of a class name:

```xml
<plugin>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.20.1</version>
    <configuration>
        <includeTags>IntegrationTest</groups>
    </configuration>
</plugin>
```





### How to upgrade

#### JUnit 5 Vintage





### Useful Links

[Baeldung - Migrating from JUnit 4 to JUnit 5](https://www.baeldung.com/junit-5-migration)

https://dev.to/stealthmusic/why-you-should-start-using-junit-5-5gk2

https://medium.com/riag-digital-techblog/why-you-should-migrate-to-junit-5-15438f5c56f

