{
  title: "Java",
  description: "How to run Selenium tests on Sauce Labs using Java",
  category: 'Tutorials',
  index: 0,
  image: "/images/tutorials/java.png"
}

## Getting Started

We suggest that you consider using [Maven](http://maven.apache.org) to build your Java project and either the 
[JUnit](http://www.junit.org) or the [TestNG](http://www.testng.org) library to write Selenium tests. While neither 
Maven, JUnit nor TestNG are required to write Java tests for Sauce, this is the framework that we'll use for this 
tutorial.

Although this tutorial is not a comprehensive guide for getting Java and Maven set up on
your system, here are some guidelines:

## Java and Maven Setup

You will need to use **JDK** 1.6 (or higher) (*not the JRE*) and Maven 2.2.1 (or higher) in order to complete this tutorial.

Download and install [Java](http://www.java.com/en/download/manual.jsp) if it isn't already installed on your system.

**Note:** Ensure the **JAVA_HOME** environment variable is defined appropriately.
For MacOS, add the following to ~/.bash_profile:
```bash 
export JAVA_HOME="$( /usr/libexec/java_home )”
```

Go to the [Maven download](http://maven.apache.org/download.html) page to download the Maven binary distribution and extract it to your file system.  Add the `bin` directory to your path, for example,

On Mac or Linux:

```bash
export PATH=YOUR_MAVEN_PATH/bin:$PATH
```

On Windows:

```bash
set PATH=YOUR_MAVEN_PATH/bin:%PATH%
```
## Setting Up a Project
Setting up a project is a process of either building one from scratch or pulling a pre-existing project. For this tutorial you will pull a sample project we'll call *quickstart-webdriver-junit* (The artifact) from the project *com.saucelabs* (The GroupId). Refer to the [maven documentation](http://maven.apache.org/guides/index.html) for more information about these maven terms.

First, create a project directory:
```bash
mkdir -p ~/sauce-tutorial/sauce-project && cd ~/sauce-tutorial/sauce-project
```
Next, download and install a sample project using your chosen testing framework using one of these Maven commands. You will be prompted to enter a group id (for example, `com.yourcompany`), artifact id (for example, `sauce-project`), version (defaults to `1.0-SNAPSHOT`), and package (default to the group id).
**Note:** This step uses your Sauce username and access key. You can find your Sauce access key on your [Sauce account page]/(https://saucelabs.com/account).
**JUnit example:**
```bash
mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DgroupId=com.saucelabs -DartifactId=quickstart-webdriver-junit -Dversion=1.0-SNAPSHOT -Dpackage=com.saucelabs -DsauceUserName=<!—- SAUCE:USERNAME -—> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```
**Note:** There may be a few prompts, use the Defaults except for ```Y: :``` enter ```Y```.
There should be quite a bit of output. If there are any errors check the ```-D``` values above and ensure there are no errors. If values are left off the command line, they will be prompted for instead.

## Running Your First Test

Now that you've got a JUnit or TestNG Maven project created, let's run the tests that were created by the Maven
archetype generation to make sure that everything works.

Run this command from your `sauce-project` directory:
```bash
mvn test
```
This launches Maven and will download the dependencies, compiles the source code and run the tests. After a few
moments you should see that JUnit/TestNG has started. You might not see any output instantaneously, but
eventually you will see the following output:
```
------------------------------------------------------
T E S T S
-------------------------------------------------------
Running WebDriverTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 14.384 sec
Running WebDriverWithHelperTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 14.743 sec

Results :

Tests run: 2, Failures: 0, Errors: 0, Skipped: 0
```
(The exact output will depend on the test framework you chose, but you
should see all tests passing.)


While the tests are running, navigate to your [Sauce Labs tests page](https://saucelabs.com/tests).
From there you'll be able to see each test as it queues, runs, and finishes.
You'll notice that each test has a name -- that information is sent
automatically at the beginning of each test.

Right now each test runs one at a time because the sample project created by the archetype isn't setup to run multiple tests in parallel, however we'll describe how to enable this in one of the later tutorials. For now,
take advantage of the serial nature of the tests and click on a running test
on your tests page. You'll jump to a detail view where, if you caught the
test while it was running, you'll be able to watch Selenium controlling the
browser. When the test finishes, the page updates with a video of the test
and a list of the various Selenium commands that were sent.

If you don't catch a test while it's running, you can click the test's link on the
[Sauce Labs tests page](https://saucelabs.com/tests) to see the test's details and video.

Now that you know that your setup worked and you were able to run your first
test suite on Sauce, let's look at what actually happened under the
hood. The simplest test is in the file
`src/test/java/com/yourcompany/WebDriverTest.java`.

The tests are very similar for both JUnit and TestNG, so we'll
only describe the JUnit test in detail.

## JUnit

```java
public class WebDriverTest {

    private WebDriver driver;

    @Before
    public void setUp() throws Exception {
        // Choose the browser, version, and platform to test
        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "5");
        capabilities.setCapability("platform", Platform.XP);
        // Create the connection to Sauce Labs to run the tests
        this.driver = new RemoteWebDriver(
                new URL("http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
    }

    @Test
    public void webDriver() throws Exception {
        // Make the browser get the page and check its title
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
    }

}
```

Let's break this test class down, chunk by chunk. First, we use the
`setUp()` method to initialize the browser testing environment we will
need for the tests:

```java
    @Before
    public void setUp() throws Exception {
        // Choose the browser, version, and platform to test
        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "5");
        capabilities.setCapability("platform", Platform.XP);
        // Create the connection to Sauce Labs to run the tests
        this.driver = new RemoteWebDriver(
                new URL("http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
    }
```

This method is run before every test in the class (by virtue of the
JUnit `org.junit.Before` annotation). We create an
`org.openqa.selenium.remote.DesiredCapabilities` instance and use it
to specify the browser version and platform. We then create an
`org.openqa.selenium.remote.RemoteWebDriver` instance pointing at
`ondemand.saucelabs.com` and using the `DesiredCapabilities`
instance. This driver makes the tests use the specified browser and
platform running on Sauce Labs servers to execute the test.

Next, we write a simple test (annotated with org.junit.Test):

```java
@Test
public void webDriver() throws Exception {
    // Make the browser get the page and check its title
    driver.get("http://www.amazon.com/");
    assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
}
```

The test accesses www.amazon.com and uses a [JUnit assertion](https://github.com/junit-team/junit/wiki/Assertions)
to check that the page title has the expected value. The call to
`driver.getTitle()` is a
[Selenium RemoteWebDriver method](http://selenium.googlecode.com/git/docs/api/java/org/openqa/selenium/remote/RemoteWebDriver.html#getTitle%28%29)
that tells Selenium to return the title of the
current page.

```java
@After
public void tearDown() throws Exception {
  driver.quit();
}
```

Finally, the `tearDown()` method is run after every test in the class (by virtue of the JUnit `org.junit.After` annotation).  We call `driver.quit()` to close the Selenium session.

## TestNG

The TestNG version of the `WebDriverTest` class is very similar. The
main difference is that the desired browser settings are provided as
parameters using the `org.testng.annotations.Parameters` and
`org.testng.annotations.Optional` annotations.

```java
public class WebDriverTest {

    private WebDriver driver;

    @Parameters({"username", "key", "os", "browser", "browserVersion"})
    @BeforeMethod
    public void setUp(@Optional("sauceUsername") String username,
                      @Optional("sauceAccessKey") String key,
                      @Optional("mac") String os,
                      @Optional("iphone") String browser,
                      @Optional("5.0") String browserVersion,
                      Method method) throws Exception {

        // Choose the browser, version, and platform to test
        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName(browser);
        capabilities.setCapability("version", browserVersion);
        capabilities.setCapability("platform", Platform.valueOf(os));
        capabilities.setCapability("name", method.getName());
        // Create the connection to Sauce Labs to run the tests
        this.driver = new RemoteWebDriver(
                new URL("http://" + username + ":" + key + "@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
    }

    @Test
    public void webDriver() throws Exception {
        // Make the browser get the page and check its title
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @AfterMethod
    public void tearDown() throws Exception {
        driver.quit();
    }

}
```


This is a very simple test, but the creation of the [`RemoteWebDriver`
instance](http://selenium.googlecode.com/git/docs/api/java/index.html?org/openqa/selenium/remote/RemoteWebDriver.html)
gives you access to the full power of Selenium.

This test gives you the basic structure for any Selenium test that
will run on Sauce Labs. Next, let's look at how you can use more
Selenium functionality to create more realistic tests of your own web
app.


## Using the Java Helper Library

The [Java helper library](https://github.com/saucelabs/sauce-java) provides additional test functionality when
using Sauce (like pass/fail reporting), and it only requires minimal changes to the test class. There are
JUnit and TestNG versions of the Java helper library. The version you are using is included as a dependency in the
Maven pom file.

The sample project from this tutorial already includes the dependency and a
test that uses it. There is nothing new to add or run here: the rest of this
page explains how to include the dependency in your own project and use it in
your tests. However, you can see the effect of using these features on your
[Sauce Labs tests page](https://saucelabs.com/tests). The tests for the
`WebDriverWithHelperTest.java` test will have a name specified in the
Session column and be marked as Pass in the Results column,
whereas all other tests will simply be marked as Finished.

## JUnit Test Helper

To include the Java helper libraries in a JUnit project, add the following dependency to the pom.xml file (this was
already created by Maven for this tutorial):

```xml
<dependency>
    <groupId>com.saucelabs</groupId>
    <artifactId>sauce_junit</artifactId>
    <version>2.0.5</version>
    <scope>test</scope>
</dependency>
```

In addition to the WebDriver.java class, Maven creates the `WebDriverWithHelperTest` class that demonstrates how 
to update tests to use the Java helper library. You can find this class in the 
`src/test/java/com/yourcompany/WebDriverWithHelperTest.java` file shown below:


```java
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider {

    public SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication(
        "sauceUsername", "sauceAccessKey");

    /**
     * JUnit Rule that marks the Sauce Job as passed/failed when the test succeeds or fails.
     * You can see the pass/fail status on your [Sauce Labs test page](https://saucelabs.com/tests).
     */
    public @Rule
    SauceOnDemandTestWatcher resultReportingTestWatcher = new SauceOnDemandTestWatcher(this, authentication);

    /**
     * JUnit Rule that will record the test name of the current test. This is referenced when creating the 
     * {@link DesiredCapabilities}, so the Sauce Job is created with the test name.
     */
    public @Rule TestName testName = new TestName();

    private WebDriver driver;

    private String sessionId;

    @Before
    public void setUp() throws Exception {

        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "17");
        // Note: XP is tested as Windows 2003 Server on the Sauce Cloud
        capabilities.setCapability("platform", Platform.XP); 
        capabilities.setCapability("name",  testName.getMethodName());
        this.driver = new RemoteWebDriver(
                new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"), capabilities);
        this.sessionId = ((RemoteWebDriver)driver).getSessionId().toString();
    }

    @Override
    public String getSessionId() {
        return sessionId;
    }

    @Test
    public void webDriverWithHelper() throws Exception {
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
    }

}
```

The `WebDriverWithHelperTest` class is fundamentally the same as the WebDriverTest class, with a couple of additions. 
First it implements the Sauce `SauceOnDemandSessionIdProvider` interface, which requires that a `getSessionId()` method 
be implemented:


```java
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider {
```

Pass your Sauce user name and Sauce access key as parameters to the `SauceOnDemandAuthentication` constructor. (You can find your 
Sauce access key on your [Sauce account page](https://saucelabs.com/account).) The 
object that is returned is passed as a parameter to the `SauceOnDemandTestWatcher` constructor. `SauceOnDemandTestWatcher` 
notifies Sauce if the test passed or failed.

```java
public SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication("sauceUsername", "sauceAccessKey");

public @Rule
SauceOnDemandTestWatcher resultReportingTestWatcher = new SauceOnDemandTestWatcher(this, authentication);

```

The SauceOnDemandTestWatcher instance invokes the [Sauce REST API](http://saucelabs.com/docs/rest). This is how JUnit
notifies the Sauce environment if the test passed or failed. It also outputs the Sauce session id 
to stdout so the Sauce plugins for [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Sauce+OnDemand+Plugin) 
and [Bamboo](https://marketplace.atlassian.com/plugins/com.saucelabs.bamboo.bamboo-sauceondemand-plugin) 
can parse the session id.

Finally, notice the `testName` rule, which is used when building the
`DesiredCapabilities`. This lets you specify a name for the test which
will be included in reports on the Sauce Labs site so you can quickly
identify which tests are failing.

### TestNG Java Helpers

To include the Java helper libraries in a TestNG project, add the following dependency to the pom.xml file (this 
was automatically created by Maven for this tutorial):

```xml
<dependency>
    <groupId>com.saucelabs</groupId>
    <artifactId>sauce_testng</artifactId>
    <version>2.0.5</version>
    <scope>test</scope>
</dependency>
```

As with the JUnit example, the TestNG Maven archetype creates a `WebDriverWithHelperTest` class that demonstrates 
how to update tests to use the Java helper library.  This class is located in the 
`src/test/java/com/yourcompany/WebDriverWithHelperTest.java` file shown below:

```java
@Listeners({SauceOnDemandTestListener.class})
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider, SauceOnDemandAuthenticationProvider {

    public SauceOnDemandAuthentication authentication;

    private WebDriver driver;

    @Parameters({"username", "key", "os", "browser", "browserVersion"})
    @BeforeMethod
    // Note: XP is tested as Windows 2003 Server on the Sauce Cloud
    public void setUp
        (@Optional("sauceUsername") String username,
            @Optional("sauceAccessKey") String key,
            @Optional("XP") String os,
            @Optional("firefox") String browser,
            @Optional("17") String browserVersion, Method method) throws Exception {

        if (StringUtils.isNotEmpty(username) && StringUtils.isNotEmpty(key)) {
            authentication = new SauceOnDemandAuthentication(username, key);
        } else {
            authentication = new SauceOnDemandAuthentication();
        }

        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName(browser);
        capabilities.setCapability("version", browserVersion);
        capabilities.setCapability("platform", Platform.valueOf(os));
        capabilities.setCapability("name", method.getName());
        this.driver = new RemoteWebDriver(
            new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"), capabilities);
    }

    @Override
    public String getSessionId() {
        SessionId sessionId = ((RemoteWebDriver)driver).getSessionId();
        return (sessionId == null) ? null : sessionId.toString();
    }

    @Test
    public void webDriverWithHelper() throws Exception {
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @AfterMethod
    public void tearDown() throws Exception {
        driver.quit();
    }

    @Override
    public SauceOnDemandAuthentication getAuthentication() {
        return authentication;
    }
}
```

This WebDriverWithHelperTest class is fundamentally the same as the WebDriverTest class, with a couple of additions. It 
is annotated with the TestNG annotation `@Listeners`, which includes the 
`SauceOnDemandTestListener` class. The SauceOnDemandTestListener class invokes 
the [Sauce REST API](http://saucelabs.com/docs/rest), which notifies Sauce if the test passed or failed. 
It also outputs the Sauce session id to stdout so the Sauce plugins 
for [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Sauce+OnDemand+Plugin) 
and [Bamboo](https://marketplace.atlassian.com/plugins/com.saucelabs.bamboo.bamboo-sauceondemand-plugin) 
can parse the session id.

## Running Tests Against Web Applications

Testing a static sandbox is one thing. Testing a real application's functionality
is another. In this tutorial we'll run Selenium tests against a real
live app to test signup, login and logout
behaviours. When you have finished this tutorial, you'll have a good
idea how to write Selenium tests for basic, multi-page interactions
with a web app.


The Test App
---

Normally, you would test your own web app. For the purposes of this
tutorial, we provide a [demo app at
`tutorialapp.saucelabs.com`](http://tutorialapp.saucelabs.com) that
we can run
Selenium scripts against. It is an "idea competition" app called
[Shootout](https://github.com/Pylons/shootout) where users vote for ideas designed for the Pyramid Python
web framework. We won't test voting functionality in this demo, but feel free to play around with it.


## The Test Class


The sample project in your `sauce-project` directory includes a test
for this app, reproduced below. Since you have already run all the
tests for the project, you have already run these 8 tests. You can
view them in your [Sauce Labs tests
page](https://saucelabs.com/tests).

The key idea of Selenium tests is to specify input to the browser and
make sure the app's response is what we want. In this case, we use
Selenium to set form input values, such as username and password, and
then click the submission button and check the output.

The goal of this particular test is to verify login, logout, and registration
functionality. To do so, we first build a few utilities to make
testing each of these processes simple. Then, we use these to write
short tests of both successful and unsuccessful attempts at these
operations. Recall that the test code is all standard Selenium
functionality -- we have only had to request that the Selenium code
execute on browsers hosted by Sauce Labs.



```java
public class WebDriverDemoShootoutTest {

    private SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication("sauceUsername", "sauceAccessKey");

    private WebDriver driver;

    @Before
    public void setUp() throws Exception {
        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "17");
        capabilities.setCapability("platform", Platform.XP);
        this.driver = new RemoteWebDriver(
                new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
        driver.get("http://tutorialapp.saucelabs.com");
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
    }

    @Test
    public void testLoginFailsWithBadCredentials() throws Exception {
        String userName = getUniqueId();
        String password = getUniqueId();
        driver.findElement(By.name("login")).sendKeys(userName);
        driver.findElement(By.name("password")).sendKeys(password);
        driver.findElement(By.cssSelector("input.login")).click();
        assertNotNull("Text not found", driver.findElement(By.id("message")));
    }

    @Test
    public void testLogout() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        doRegister(userDetails, true);
    }

    @Test
    public void testLogin() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        doRegister(userDetails, true);
        doLogin(userDetails.get("username"), userDetails.get("password"));
    }

    @Test
    public void testRegister() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        doRegister(userDetails, false);
        assertTrue("Message not found", driver.findElement(By.cssSelector(".username")).getText().contains("You are logged in as "));
    }

    @Test
    public void testRegisterFailsWithoutUsername() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        userDetails.put("username", "");
        doRegister(userDetails, false);
        assertEquals("Message not found", "Please enter a value", driver.findElement(By.cssSelector(".error")).getText());

    }

    @Test
    public void testRegisterFailsWithoutName() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        userDetails.put("name", "");
        doRegister(userDetails, false);
        assertEquals("Message not found", "Please enter a value", driver.findElement(By.cssSelector(".error")).getText());
    }

    @Test
    public void testRegisterFailsWithMismatchedPasswords() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        userDetails.put("confirm_password", getUniqueId());
        doRegister(userDetails, false);
        assertEquals("Message not found", "Fields do not match", driver.findElement(By.cssSelector(".error")).getText());
    }

    @Test
    public void testRegisterFailsWithBadEmail() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        userDetails.put("email", "test");
        doRegister(userDetails, false);
        assertEquals("Message not found", "An email address must contain a single @", driver.findElement(By.cssSelector(".error")).getText());
        driver.findElement(By.id("email")).clear();
        driver.findElement(By.id("email")).sendKeys("@example.com");
        driver.findElement(By.id("form.submitted")).click();
        assertEquals("Message not found", "The username portion of the email address is invalid (the portion before the @: )", driver.findElement(By.cssSelector(".error")).getText());
        driver.findElement(By.id("email")).clear();
        driver.findElement(By.id("email")).sendKeys("test@example");
        driver.findElement(By.id("form.submitted")).click();
        assertEquals("Message not found", "The domain portion of the email address is invalid (the portion after the @: bob)", driver.findElement(By.cssSelector(".error")).getText());
    }

    private String getUniqueId() {
        return Long.toHexString(Double.doubleToLongBits(Math.random()));
    }

    private void doRegister(Map<String, String> userDetails, boolean logout) {
        userDetails.put("confirm_password", userDetails.get("confirm_password") != null ?
                userDetails.get("confirm_password") : userDetails.get("password"));
        driver.get("http://tutorialapp.saucelabs.com/register");
        driver.findElement(By.id("username")).sendKeys(userDetails.get("username"));
        driver.findElement(By.id("password")).sendKeys(userDetails.get("password"));
        driver.findElement(By.id("confirm_password")).sendKeys(userDetails.get("confirm_password"));
        driver.findElement(By.id("name")).sendKeys(userDetails.get("name"));
        driver.findElement(By.id("email")).sendKeys(userDetails.get("email"));
        driver.findElement(By.id("form.submitted")).click();

        if (logout) {
            doLogout();
        }
    }

    private void doLogout() {
        driver.get("http://tutorialapp.saucelabs.com/logout");
        assertEquals("Message not found", "Logged out successfully.", driver.findElement(By.id("message")).getText());
    }

    private Map<String, String> createRandomUser() {
        Map<String, String> userDetails = new HashMap<String, String>();
        String fakeId = getUniqueId();
        userDetails.put("username", fakeId);
        userDetails.put("password", "testpass");
        userDetails.put("name", "Fake " + fakeId);
        userDetails.put("email", fakeId + "@example.com");
        return userDetails;
    }

    private void doLogin(String username, String password) {
        driver.findElement(By.name("login")).sendKeys(username);
        driver.findElement(By.name("password")).sendKeys(password);
        driver.findElement(By.cssSelector("input.login")).click();
        assertEquals("Message not found", "Logged in successfully.", driver.findElement(By.id("message")).getText());
    }

}
```

Let's start with the utilities.
The `createRandomUser()` function generates unique random user details for the registration
and login tests. The randomness is important because it allows our tests to
run in parallel as many times as we want without fear of collisions.

```java
private Map<String, String> createRandomUser() {
    Map<String, String> userDetails = new HashMap<String, String>();
    String fakeId = getUniqueId();
    userDetails.put("username", fakeId);
    userDetails.put("password", "testpass");
    userDetails.put("name", "Fake " + fakeId);
    userDetails.put("email", fakeId + "@example.com");
    return userDetails;
}
```

The `doRegister()`, `doLogin()` and `doLogout()` issue requests that
register, login, and logout the user, respectively. Recall that the
tests, Selenium web browser client, and web app are all running on
separate servers communicating over the internet. We use Selenium to
manipulate the browser state (e.g. fill in forms) and submit requests
(e.g. click the submit button) in order to put the user into these
states.

```java
private void doRegister(Map<String, String> userDetails, boolean logout) {
       userDetails.put("confirm_password", userDetails.get("confirm_password") != null ?
               userDetails.get("confirm_password") : userDetails.get("password"));
       driver.get("http://tutorialapp.saucelabs.com/register");
       driver.findElement(By.id("username")).sendKeys(userDetails.get("username"));
       driver.findElement(By.id("password")).sendKeys(userDetails.get("password"));
       driver.findElement(By.id("confirm_password")).sendKeys(userDetails.get("confirm_password"));
       driver.findElement(By.id("name")).sendKeys(userDetails.get("name"));
       driver.findElement(By.id("email")).sendKeys(userDetails.get("email"));
       driver.findElement(By.id("form.submitted")).click();

       if (logout) {
           doLogout();
       }
   }

   private void doLogout() {
       driver.get("http://tutorialapp.saucelabs.com/logout");
       assertEquals("Message not found", "Logged out successfully.", driver.findElement(By.id("message")).getText());
   }

   private void doLogin(String username, String password) {
      driver.findElement(By.name("login")).sendKeys(username);
      driver.findElement(By.name("password")).sendKeys(password);
      driver.findElement(By.cssSelector("input.login")).click();
      assertEquals("Message not found", "Logged in successfully.", driver.findElement(By.id("message")).getText());
}
```

In our first test, we check that logging in doesn't work with a bad
username/password. Instead of using the `doLogin()` utility, which
checks for successful login, we use a similar process but check that
an error message is returned.

```java
@Test
public void testLoginFailsWithBadCredentials() throws Exception {
    String userName = getUniqueId();
    String password = getUniqueId();
    driver.findElement(By.name("login")).sendKeys(userName);
    driver.findElement(By.name("password")).sendKeys(password);
    driver.findElement(By.cssSelector("input.login")).click();
    assertNotNull("Text not found", driver.findElement(By.id("message")));
}
```

Next we test normal login and logout functionality using the
`doLogin()` and `doLogout()` methods. The first test creates a new user
and uses the `doLogout()` helper method to assert that the logout message
appears. The second test creates a random user, logs it out, and then uses
the `doLogin()` helper method to assert that the successful login message
appears.

```java
@Test
public void testLogin() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    doRegister(userDetails, true);
    doLogin(userDetails.get("username"), userDetails.get("password"));
}

@Test
public void testLogout() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    doRegister(userDetails, true);
}
```

Then we test Shootout's signup functionality by using the
registration helper function to create a new user and assert that the
user is logged in (which happens after a successful registration).

```java
@Test
public void testRegister() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    doRegister(userDetails, false);
    assertTrue("Message not found", driver.findElement(By.cssSelector(".username")).getText().contains("You are logged in as "));
}
```

And finally we have a set of tests for validation logic in the signup form. First we test that each of the required
fields results in an error on signup if the field is empty. Next we test if a mismatched password and password
confirmation generate the desired error, and then we test to make sure that the app doesn't allow a successful
registration if various incorrect email formats are used.

```java
@Test
public void testRegisterFailsWithoutUsername() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    userDetails.put("username", "");
    doRegister(userDetails, false);
    assertEquals("Message not found", "Please enter a value", driver.findElement(By.cssSelector(".error")).getText());

}

@Test
public void testRegisterFailsWithoutName() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    userDetails.put("name", "");
    doRegister(userDetails, false);
    assertEquals("Message not found", "Please enter a value", driver.findElement(By.cssSelector(".error")).getText());
}

@Test
public void testRegisterFailsWithMismatchedPasswords() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    userDetails.put("confirm_password", getUniqueId());
    doRegister(userDetails, false);
    assertEquals("Message not found", "Fields do not match", driver.findElement(By.cssSelector(".error")).getText());
}

@Test
public void testRegisterFailsWithBadEmail() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    userDetails.put("email", "test");
    doRegister(userDetails, false);
    assertEquals("Message not found", "An email address must contain a single @", driver.findElement(By.cssSelector(".error")).getText());
    driver.findElement(By.id("email")).clear();
    driver.findElement(By.id("email")).sendKeys("@example.com");
    driver.findElement(By.id("form.submitted")).click();
    assertEquals("Message not found", "The username portion of the email address is invalid (the portion before the @: )", driver.findElement(By.cssSelector(".error")).getText());
    driver.findElement(By.id("email")).clear();
    driver.findElement(By.id("email")).sendKeys("test@example");
    driver.findElement(By.id("form.submitted")).click();
    assertEquals("Message not found", "The domain portion of the email address is invalid (the portion after the @: bob)", driver.findElement(By.cssSelector(".error")).getText());
}
```

## Next Steps for Testing


As simple as they are, these signup/login/logout tests are extremely
valuable. Running them before every deployment helps to ensure that
you can welcome new users into your community and get them where they
need to go.

If you are new to Selenium, try adding a new test to this suite. For
example, reuse the registration and login methods to setup a user and
test the voting functionality. To do so, you will need to try the
voting functionality for yourself to understand how it *should*
function. You could test that a logged in user sees voting buttons,
can click them, and that the next page shows the adjusted scores.

When you are comfortable with writing these types of tests, you can
move on to learn more about how Sauce Labs lets you do more with
Selenium.


## Running Tests in Parallel


As you may recall from earlier tutorials, Selenium tests can take a long time! They may take even longer on Sauce
because we start each test on a new virtual machine that has never been used before (don't worry, we don't charge
you for the spin-up time).

To make tests run faster, run more than one test at a time. As long as
the tests are independent --- whether you're running the same test
across different browsers or the tests just don't interact with each
other --- there should be no problem running them
simultaneously. Since we have thousands of
clean virtual machines on standby, we encourage you to run as many tests
as you can at once. For an overview of how many tests you can run in parallel, see the parallelization section of the
[Sauce plan page](http://saucelabs.com/pricing).

Keep in mind, Sauce Labs accounts have limits on the number of parallel tests they can run at once.  Try to start too many, and you'll end up with tests timing out.  You can find the number of parallel tests your account can run in the sidebar of your [account page](http://www.saucelabs.com/account).

### Parallel Tests in JUnit


Tests can be run in parallel using JUnit, but it takes a bit of work.
The [Java helper library](https://github.com/saucelabs/sauce-java) includes a `Parallelized`
class that creates a dynamic thread pool that holds each thread that is running a test.

**Parallelizing the WebDriverTest Class**

The following `WebDriverParallelTest` class demonstrates how to update
the `WebDriverTest` class so its tests run in parallel. The test is
parallelized by specifying the different parameters to test with, in this
case the browser and platform. Behind the scenes, the test framework
creates a different instance of the test class for each set of parameters
and runs them in parallel. The parameters are passed to the
constructor so each instance customizes it's behavior using those
parameters.

In this example, we're parallelizing tests across different browsers
on different platforms. Since testing an app in Firefox on Linux is
independent of testing it in Chrome on Windows, we can safely run both
tests in parallel. The static method `browsersStrings()` is
annotated with `org.junit.runners.Parameterized.Parameters`,
indicating it should be used to determine the parameters for each
instance of the test. The method returns a `LinkedList` of parameters
to use for each test instance's constructor. The
`WebDriverParallelTest` constructor captures these
parameters and `setUp()` uses them to configure the `DesiredCapabilities`.


```java
@RunWith(Parallelized.class)
public class WebDriverParallelTest {

    private String browser;
    private String os;
    private String version;

    public WebDriverParallelTest(String os, String version, String browser) {
        super();
        this.os = os;
        this.version = version;
        this.browser = browser;
    }

    @Parameterized.Parameters
    public static LinkedList browsersStrings() throws Exception {
        LinkedList browsers = new LinkedList();
        browsers.add(new String[]{Platform.XP.toString(), "17", "firefox"});
    //add any additional browsers here
        return browsers;
    }

    private WebDriver driver;

    @Before
    public void setUp() throws Exception {

        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setCapability(CapabilityType.BROWSER_NAME, browser);
        capabilities.setCapability(CapabilityType.VERSION, version);
        capabilities.setCapability(CapabilityType.PLATFORM, os);
        this.driver = new RemoteWebDriver(
                new URL("http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub"), capabilities);
    }

    @Test
    public void webDriver() throws Exception {
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
    }
}
```

As shown above (and as included in the sample project) only one
platform is returned, so only that one test will be run in
parallel. Let's fix that! Add a few more platforms or browser versions
(you might need to refer to [the Selenium
`org.openqa.selenium.Platform`
documentation](http://selenium.googlecode.com/git/docs/api/java/index.html?org/openqa/selenium/Platform.html)
to specify other platforms). Now, when you run the
tests, you should see these tests running in
parallel on the [Sauce Labs tests page](https://saucelabs.com/tests).

### Setting a parallelism limit

To stop tests from timing out when you're already using all your Sauce Labs parallel slots, we need to limit the number of threads.

The Sauce Labs Parallelized JUnit runner we used above uses the `junit.parallel.threads` System property to control how many threads it runs.  Let's set this to 2, to match the limit for free accounts:

```bash
mvn test -Djunit.parallel.threads=2
```

### Parallel Tests in TestNG


TestNG has built in support for running tests in parallel that is configured by the following line in the
`src\test\resources\xml\testng.xml` file:

```xml
<suite name="ParallelTests" verbose="5" parallel="tests" thread-count="10">
```

Don't forget to match the `thread-count` to your concurrency limit, as mentioned above.

For more information about the options available for running parallel tests using TestNG, see the
[TestNG website](http://testng.org/doc/documentation-main.html#parallel-running)

Next Steps
---

Parallelizing tests will make them run significantly faster. It is
only a little bit of work to parallelize them and it lets you test
your code for deployment much more quickly. Use parallelization to run
the same test across many browsers and platforms at once, or just to
run many tests that are independent simultaneously.

You're almost done! We have covered all the major functionality. Now,
we'll give you a few tips about how to get the best performance out of
Selenium and Sauce Labs.

## Tips for Better Selenium Test Performance

This section discusses some ideas for improving the performance of Selenium tests.

## Avoid Test Dependencies


Dependencies between tests prevent tests from being able to run in parallel. And running tests in parallel is by far
the best way to speed up the execution of your entire test suite. It's much easier to add a virtual machine than to 
try to figure out how to squeeze out another second of performance from a single test.

What are dependencies? Imagine if we had a test suite with these two tests:

```java
@Test
public void testLogin()
{
    // do some stuff to trigger a login
    assertEquals("My Logged In Page", driver.getTitle());
}

@Test
public void testUserOnlyFunctionality()
{
    driver.findElement(By.id("userOnlyButton")).click();
    assertEquals("Result of clicking userOnlyButton", driver.findElement(By.id("some_result")));
}
```

The `testLogin()` method in the first function's pseudocode triggers the browser to log in 
and asserts that the login was successful. The second test clicks a button on the
logged-in page and asserts that a certain result occurred.

This test class works fine as long as the tests run in order. But the
assumption the second test makes (that we are already logged in) creates a 
dependency on the first test. If these tests run at the same time, or if the
second one runs before the first test, the browser's cookies will
not allow Selenium to access the logged-in page and the second test fails.

The right way to remove these dependencies is to make sure that each test can 
run completely independently of the other. Let's fix the example above so
there are no dependencies:


```java
private void doLogin()
{
    // do some stuff to trigger a login
    assertEquals("My Logged In Page", driver.getTitle());
}

@Test
public void testLogin()
{
    doLogin();
}

@Test
public void testUserOnlyFunctionality()
{
    doLogin();
    driver.findElement(By.id("userOnlyButton")).click();
    assertEquals("Result of clicking userOnlyButton", driver.findElement(By.id("some_result")));
}
```

The main point is that it's dangerous to assume any state whatsoever when
developing tests for your app. Instead, find ways to quickly generate
desired states for individual tests the way we did in the `doLogin()` method above
-- it generates a logged-in state instead of assuming it. 

You might even want to develop an API for the development and test versions of your app
that provides URL shortcuts that generate common states. For example, 
a URL that's only available in test that creates a random user account and 
logs it in automatically.

## Don't Use Brittle Locators

WebDriver provides a number of 
[locator strategies](http://code.google.com/p/selenium/wiki/JsonWireProtocol#/session/:sessionId/element) for accessing 
elements on a webpage. 

It's tempting to use complex XPath expressions like `//body/div/div/*[@class="someClass"]`
or CSS selectors like `#content .wrapper .main`. While these might work when 
you are developing your tests, they will almost certainly break when you make 
unrelated refactoring changes to your HTML output.

Instead, use sensible semantics for CSS IDs and form element names and try to 
restrict yourself to using `By.id()` or `By.name()`. This makes it 
much less likely that you'll inadvertently break your page by shuffling some 
lines of code around.

## Use WebDriverWait

Selenium doesn't know when it's supposed to wait 
before executing the next command. When users click a submit button, 
they know not to try another action until the next page 
loads. Selenium, however, immediately executes the next command. If 
the next command is part of an assertion in your code the assertion may 
fail, even though if Selenium had waited a little bit longer the assertion would have succeeded.

The solution is to use 
[WebDriverWait](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/support/ui/WebDriverWait.html), 
which in conjunction with an ExpectedConditions instance 
will wait until the expected condition is found before continuing to your next line in the test as shown below:

```java
WebDriverWait wait = new WebDriverWait(driver, 5); // wait for a maximum of 5 seconds
wait.until(ExpectedConditions.presenceOfElementLocated(By.id("pg")));
```

Using WebDriverWait is a great way to make tests less brittle and more
accepting of differences in network speeds, surges in traffic, and other challenges in the test environment.
