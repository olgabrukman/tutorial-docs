
# Unit Testing with Mocking
http://tutorials.jenkov.com/java-unit-testing/stub-mock-and-proxy-testing.html
http://www.vogella.com/tutorials/Hamcrest/article.html#hamcrestoverview
## JUnit
TestNG (another framework for unit testing), runs tests in parallel, thus in a unit test should not use test class  non-constant variables.

Some tags: @BeforeClass, @BeforeMethod, @BeforeTest (there are matching @After tags for each such tag), @Test(ExpectedException=InterruptedException.class).

	
## Mocks
So you know how to write a unit test to a data structure class or a thread class. 
How do you go about testing a class that need to store data in DB or send a REST 
request and get response from Web server during its life cycle? 
Setting up a DB for testing only is a heavy task and will require careful DB state 
management to avoid inconsistent state. Same with setting up and then tearing down 
of a Web server instance for each test is very resource heave.

To deal with this kind of problems several testing concepts/tools were invented (survey of testing techniques):

* Dummy objects are passed around but never actually used. Usually they are just used to fill parameter lists.
	
* Fake objects actually have working implementations, but usually take some shortcut which makes them not suitable for production (an in memory database is a good example).
	
* Stubs provide canned answers to calls made during the test, usually not responding at all to anything outside what is programmed in for the test. Stubs may also record information about calls, such as an email gateway stub that remembers the messages it "sent", or maybe only how many messages it "sent".
	
* Mocks are what we are talking about here: objects pre-programmed with expectations which form a specification of the calls they are expected to receive.


## Mocks Are Bad Idea
Should design a system in layers, so that a layer that usually would be mocked can be replaced by a trivial simulation of the component, i.e., REST server implementation  would implement interface RestService. When testing an object that accesses REST server implementation, we would instantiate a REST service interface with test-specific implementation of the REST server.

[Guide for Writing More Testable Code](http://misko.hevery.com/code-reviewers-guide/)

	
