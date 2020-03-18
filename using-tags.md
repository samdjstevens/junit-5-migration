# Using Tags

Tags are the JUnit 5 version of JUnit 4's Categories. This guide will show how tags can be used in JUnit 5 to filter or exclude tests from being ran.



### Applying Tags

To specify that a test, or a class of tests, belongs to a group (e.g. unit tests, fast tests, slow tests, etc) you must annotate the class or method with the @Tag annotation:

```java
@Tag("fast")
@Tag("calculator")
public class CalculatorTests {
  ...
}
```



### Filtering Tests

Once tests have been grouped with @Tags, they can be filtered/excluded in various ways, depending on the build system being used.




#### With Gradle

To include/exclude tests from being ran with Gradle, use the `includeTags` and `excludeTags`:

```
test {
	useJUnitPlatform{
		includeTags 'UnitTest', 'FastTest'
		excludeTags 'IntegrationTest'
	}
}
```

To have Gradle be able to run different groups of tests, create different tasks that execute the specified tests:

```
task slowTest(type: Test) {
	useJUnitPlatform {
		includeTags 'SlowTest'
	}
}
```

And then run with:

```bash
$ gradle slowTest
```



#### With Maven Surefire Plugin

Filtering tests with the Maven Surefure Plugin is done in the same way as JUnit 4, but using the *includeTags* tag, and passing in the tag string instead of a class name:

```xml
<plugin>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.20.1</version>
    <configuration>
        <includeTags>UnitTest</groups>
    </configuration>
</plugin>
```


Similarly, tags can be excluded using the `excludeTags` tag:

```xml
<plugin>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.20.1</version>
    <configuration>
        <excludeTags>IntegrationTest</groups>
    </configuration>
</plugin>
```

To dynamically set which tags are included/excluded on a run, use the command arguments:

```bash
$ mvn -Dtags="UnitTest, FastTest"
$ mvn -DexcludedTags="IntegrationTest"
```
