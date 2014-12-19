---
layout: post
title: Powermock and  testing private methods
published: true
---

When I first came across/heard about powermock, I was astonished, that why would i ever need something like this where I would need to mock or test a private method of a class. Sometime ago, I would just write off, such a case as a bad design and would look for ways to refactor the class so that I could easily write junits for the funcationality in it. 

Although the world is far from perfect and I have recently on multiple occurences witnessed and felt the need to be able to test the private methods that I write when adding a funcationality to an existing hierarchy of classes. 

Going deeper into continuous delivery, it becomes important to have faster test runs. Faster test runs ensure smaller build times, taking one closer to continous delivery. With CD, we just can`t be waiting for half an hour for the build to complete. Hence, it becomes a good use case to mock the computation intesive private methods and shorten the test execution time. But, do be wary to make sure that, the methods gets tested on it own, or atleast once for all the corner cases. 

Please see below a simple example of how it can be done. 

Maven dependencies needed :

```xml
		<dependency>
              <groupId>org.powermock</groupId>
              <artifactId>powermock-module-junit4</artifactId>
              <version>1.5</version>
              <scope>test</scope>                  
        </dependency>
        <dependency>
              <groupId>org.powermock</groupId>
              <artifactId>powermock-api-mockito</artifactId>
              <version>1.5</version>
              <scope>test</scope>
        </dependency>  
        <dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.11</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.hamcrest</groupId>
			<artifactId>hamcrest-core</artifactId>
			<version>1.3</version>
			<scope>test</scope>
		</dependency>
```

Java code for exmaple/reference
The class in test 
```java
public class PrivateMethodClass {

    private boolean imAPrivateMethod() {
        return true;
    }
}
```		
The junit test class will like something below.
Pay attention to the `@RunWith` annotation.

```java
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.powermock.modules.junit4.PowerMockRunner;
import org.powermock.reflect.Whitebox;

@RunWith(PowerMockRunner.class)
public class PrivateMethodClassTest {

    @Test
    public void testingthePrivateMethod() {
        PrivateMethodClass classinTest = new PrivateMethodClass();
        Assert.assertTrue(Whitebox.<Boolean>invokeMethod(classinTest, "imAPrivateMethod"));
    }
}
```