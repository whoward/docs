{
  title: "Java",
  description: "How to run Selenium tests on Sauce Labs using Java",
  category: 'Tutorials',
  index: 0,
  image: "/images/tutorials/java.png"
}

## Getting Started

You will need to use **JDK** 1.6 (or higher) (*not the JRE*) in order to complete this tutorial. To verify what version of Java you have we can run:
```bash
java -version
```

Download and install [Java](http://www.java.com/en/download/manual.jsp) if it isn't already installed on your system.

**Note:** Ensure the **JAVA_HOME** environment variable is defined appropriately.
For MacOS, add the following to ~/.bash_profile:
```bash 
export JAVA_HOME="$( /usr/libexec/java_home )"
```

## Basic Test

With Java installed, you are close to running your first WebDriver test on Sauce. Java doesn't know what Selenium is right away, so 
you'll need to download the [Selenium server standalone jar](http://selenium-release.storage.googleapis.com/2.44/selenium-server-standalone-2.44.0.jar). You can put this jar file in your
[class path](http://docs.oracle.com/javase/tutorial/essential/environment/paths.html), or simply save it to a directory for now. Please make sure the directory contains no other .jars to get used instead of Selenium in the Java classpath.

Here is a basic WebDriver test in Java: [WebDriverBasic.java](https://raw.githubusercontent.com/saucelabs/sauce-support/master/Java/WebDriverBasic.java)
```java
import java.net.URL;
import java.util.concurrent.TimeUnit;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;

public class WebDriverBasic {
    public static void main(String[] args) throws Exception {
        WebDriver driver;
    
        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setCapability("browserName", "Chrome");
        capabilities.setCapability("version", "36");
        capabilities.setCapability("name", "Basic Java WebDriver Test");

        System.out.println("Running on Sauce Labs...");
        driver = new RemoteWebDriver(new URL("http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub"), capabilities);
        driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
        
        driver.get("https://saucelabs.com/test/guinea-pig");
        System.out.println("Page title is: " + driver.getTitle());
        driver.quit();

        System.out.println("Done! View this test on your dashboard at https://saucelabs.com/tests");
    }
}
```
**Note:** Be sure the `username` and `accessKey` contain your own valid Sauce Labs credentials from your [account page](https://saucelabs.com/account).

To compile and run this file, Java needs the Selenium server jar in the classpath. If you permanently set the class path for your platform, as in the
link above (see: [class path](http://docs.oracle.com/javase/tutorial/essential/environment/paths.html)), you should be able to compile and run the file with:

First;
```bash
javac WebDriverBasic.java
```
Second;
```bash
java WebDriverBasic
```
Alternately, if you have not modified your class path, the simplest way to compile and run the example is storing `WebDriverBasic.java` in the same directory as your Selenium server jar, and specifying your class path to be the current directory with these commands:

First;
```bash
javac -classpath *:. WebDriverBasic.java
```
Second;
```bash
java -classpath *:. WebDriverBasic
```
You should see that the basic test ran on your [tests page](https://saucelabs.com/tests). Congratulations! You can click on the test to watch the recorded video and view the Selenium commands that were run.

You'll notice that this test didn't do much - it only sets up the RemoteWebDriver connection, visits a website, and quits. Next we'll write a test that plays with the website we loaded in WebDriverBasic.java.

## Involved Test

After completing the basic test lets add some more selenium commands to test a sample [website](https://saucelabs.com/test/guinea-pig). Let's now interact with buttons, checkboxes, textfields, and more in the following test case.

Here is the involved WebDriver test in Java: [WebDriverInvolved.java](https://raw.githubusercontent.com/saucelabs/sauce-support/master/Java/WebDriverInvolved.java)
```java
import java.net.URL;
import java.util.concurrent.TimeUnit;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;

public class WebDriverInvolved {
  public static void main(String[] args) throws Exception {
    WebDriver driver;

    DesiredCapabilities capabilities = new DesiredCapabilities();
    capabilities.setCapability("browserName", "Chrome");
    capabilities.setCapability("version", "36");
    capabilities.setCapability("name", "Involved Java WebDriver Test");

    System.out.println("Running on Sauce Labs...");
    driver = new RemoteWebDriver(new URL("http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub"), capabilities);
    driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);

    driver.get("https://saucelabs.com/test/guinea-pig");
    System.out.println("Page title is: " + driver.getTitle());

    //Find contents of a div
    WebElement div = driver.findElement(By.id("i_am_an_id"));
    System.out.println("Output of element: " + div.getText());

    //Clear the 'i has no focus' textbox
    WebElement textbox = driver.findElement(By.name("i_am_a_textbox"));
    textbox.clear();

    //Check uncheckedBox
    WebElement uncheckedBox = driver.findElement(By.name("unchecked_checkbox"));
    if( !uncheckedBox.isSelected() ) {
      uncheckedBox.click();
    }

    //UnCheck checkedBox
    WebElement checkedBox = driver.findElement(By.name("checked_checkbox"));
    checkedBox.click();

    //Fill out email
    WebElement email = driver.findElement(By.id("fbemail"));
    email.sendKeys("thebestemail@ever.hi");

    //Fill out comments
    WebElement comment = driver.findElement(By.id("comments"));
    comment.sendKeys("Hello, I'm currently running a test on Sauce Labs :).");

    //Click send
    WebElement submit = driver.findElement(By.id("submit"));
    submit.click();

    //Output comments: changed
    WebElement your_comment = driver.findElement(By.id("your_comments"));
    System.out.println("Output of your_comments: " + your_comment.getText());

    //Closing down the WebDriver
    driver.quit();

    System.out.println("Done! View this test on your dashboard at https://saucelabs.com/tests");
  }
}
```
**Note:** Be sure the `username` and `accessKey` contain your own valid Sauce Labs credentials from your [account page](https://saucelabs.com/account).

To compile this file please follow these two steps.

First;
```bash
javac -classpath *:. WebDriverInvolved.java
```
Second;
```bash
java WebDriverInvolved
```

## Parallel Tests
After completing the Involved Test lets start running tests in parallel. To get this working initially we will use shell scripting, but there are Test Runngers and Contiunous Integration systems that can help us here.

Here is the bash scrupt to do parallel testing: [script.sh](https://raw.githubusercontent.com/saucelabs/sauce-support/master/Java/script.sh)
```bash
#!/bin/bash
echo "---Starting script---"
javac -classpath *:. WebDriverInvolved.java; java -classpath *:. WebDriverInvolved &
pid1=$!
java -classpath *:. WebDriverInvolved &
pid2=$!
java -classpath *:. WebDriverInvolved &
pid3=$!
wait $pid1
wait $pid2
wait $pid3
echo "---Ending script---"
```
Next run this shell script with
```bash
./script.sh
```

Congradulations at this point you have run a test in parallel on Sauce Labs!
