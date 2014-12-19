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
<pre><code>
	&lt;dependency&gt;
          &lt;groupId&gt;org.powermock&lt;/groupId&gt;
          &lt;artifactId&gt;powermock-module-junit4&lt;/artifactId&gt;
          &lt;version&gt;1.5&lt;/version&gt;
          &lt;scope&gt;test&lt;/scope&gt;                  
    &lt;/dependency&gt;
    &lt;dependency&gt;
          &lt;groupId&gt;org.powermock&lt;/groupId&gt;
          &lt;artifactId&gt;powermock-api-mockito&lt;/artifactId&gt;
          &lt;version&gt;1.5&lt;/version&gt;
          &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;  
    &lt;dependency&gt;
		&lt;groupId&gt;junit&lt;/groupId&gt;
		&lt;artifactId&gt;junit&lt;/artifactId&gt;
		&lt;version&gt;4.11&lt;/version&gt;
		&lt;scope&gt;test&lt;/scope&gt;
	&lt;/dependency&gt;
	&lt;dependency&gt;
		&lt;groupId&gt;org.hamcrest&lt;/groupId&gt;
		&lt;artifactId&gt;hamcrest-core&lt;/artifactId&gt;
		&lt;version&gt;1.3&lt;/version&gt;
		&lt;scope&gt;test&lt;/scope&gt;
	&lt;/dependency&gt;
</code></pre>

Java code for exmaple/reference
The class in test 
<pre><code>
public class PrivateMethodClass {

	private boolean imAPrivateMethod() {
	    return true;
	} 
} </code></pre>

The junit test class will like something below. Pay attention to the `@RunWith` annotation.

<pre><code>
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
    	Assert.assertTrue(Whitebox.&lt;Boolean&gt;invokeMethod(classinTest, "imAPrivateMethod"));

	} 
} 
</code></pre>

