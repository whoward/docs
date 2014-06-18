{
  title: "Jenkins",
  description: "Run automated tests on Sauce Labs using Jenkins",
  category: "CI Integrations",
  index: 0,
  image: "/images/ci-integrations/jenkins.png",
}

## Sauce Labs and Jenkins Tutorial

This tutorial explains how to integrate [Jenkins](http://jenkins-ci.org) with tests run with the Sauce Labs cloud of Selenium servers. 

We assume that you have some familiarity with Jenkins and the fundamentals of automated testing. However, even if this is your first time using Jenkins and automated testing you should be able to successfully follow these step-by-step instructions. 

## Installing the Jenkins Sauce OnDemand plugin

The Sauce OnDemand plugin for Jenkins can be installed through the Jenkins Administration page.

To access this page, click `Manage Jenkins` from the left-hand navigation pane.

![Manage Jenkins](/images/ci-integrations/jenkins/manage-jenkins.png)

Then click on the `Manage Plugins` link:

![Manage Plugins](/images/ci-integrations/jenkins/manage-plugins.png)

Select the `Available` tab:

![Available Tab](/images/ci-integrations/jenkins/available-tab.png)

Scroll down the list of plugins to find the `Sauce OnDemand plugin`, select the check box and click the `Download and install after restart` buton:

![Install Plugin](/images/ci-integrations/jenkins/install-plugin.png)

This will download the Sauce OnDemand plugin for Jenkins.  The plugin file is quite large, so the download may take some time to complete.  Select the `Restart Jenkins when installation is complete and no jobs are running`, and when the download has finished, the Jenkins instance will restart.

![Restart Jenkins](/images/ci-integrations/jenkins/restart-jenkins.png)

## Configuring the Jenkins Sauce OnDemand plugin

After the Sauce plugin has been installed, the username and access key of the Sauce OnDemand user account must be entered within the Jenkins administration interface.

To configure the authentication, first click `Manage Jenkins` from the left-hand navigation pane.

![Manage Jenkins](/images/ci-integrations/jenkins/manage-jenkins.png)

Click the `Configure System` link

![Configure System](/images/ci-integrations/jenkins/configure-system.png)

Scroll down to the `Sauce OnDemand` section.

![Sauce Admin](/images/ci-integrations/jenkins/sauce-admin.png)

This section contains the fields required to configure how the authentication for the Sauce plugin.  Enter the values of the username and access key you wish the Sauce plugin to use in the `Username` and `API Access Key fields`.  

By default, the Sauce plugin will use the user home directory as the working directory when extracting the Sauce Connect Jar file.  You can override this behaviour by specifying a directory location in the `Sauce Connect Working Directory` field.

The plugin also supports reading authentication details from a `.sauce-ondemand` file located in the user home directory.  If you wish the plugin to use this file, then select the `Use authentication details in ~/.sauce-ondemand?` checkbox.

Once the authentication details have been entered, clicking on the `Test Connection` button will connect to Sauce OnDemand to verify that the plugin can authenticate with the entered details.

The plugin includes a copy of the Sauce Connect Jar file.  When the administration screen is displayed, the plugin will check to see if a later version of Sauce Connect is available, and if so, will provide a link that will download the updated version without requiring a new version of the Sauce plugin to be installed.

Once the authentication details have been entered and saved on the `Configure System` screen, you can then enable Sauce OnDemand support on the Configuration screen for a Jenkins Job.

## Jenkins Configuration for a Java-based Project

To demonstrate the Sauce plugin for Jenkins, let's create a new Jenkins Freestyle project for a Java project.

From the Jenkins dashboard page, click `New Job`

![New Job](/images/ci-integrations/jenkins/new-job.png)

Enter 'Sauce Java Demo' in the Job Name field and select `Build a free-style software project`.

![New Freestyle Project](/images/ci-integrations/jenkins/new-freestyle-project.png)

Our sample code is located in [github](https://github.com/rossrowe/sauce-ci-java-demo), so select `Git` in the `Source Code Management` section, enter `https://github.com/rossrowe/sauce-ci-java-demo` as the repository URL and enter `master` in the branch specifier.

![Git Setup](/images/ci-integrations/jenkins/git-setup.png)

Now let's add a build step which will run our tests.  Click on the `Add Build Step` menu in the `Build` section, and select `Invoke top-level Maven targets`

![Invoke Maven](/images/ci-integrations/jenkins/invoke-maven.png)

Enter `test` in the `Goals` field.

![Maven Goals](/images/ci-integrations/jenkins/maven-goal.png)

Now let's enable the Sauce OnDemand support for a Jenkins Job can be enabled by checking the `Sauce OnDemand Support` checkbox.

![Sauce Configure](/images/ci-integrations/jenkins/sauce-configure.png)

Select the `Enable Sauce Connect?` check box.  When selected, the Sauce plugin will launch an instance of [Sauce Connect](http://saucelabs.com/docs/sauce-connect) prior to the running of your Job.  This instance will be closed when the Job completes.

Click on the `WebDriver` radio button and select a browser to run our tests against (let's pick Firefox 15 running in Windows 2008)

![Sauce Configure](/images/ci-integrations/jenkins/sauce-configure.png)

Sauce OnDemand supports a wide range of browsers, but some browser combinations are only supported for SeleniumRC or WebDriver tests.  The multi-select lists beneath the `SeleniumRC` and `WebDriver` radio buttons are populated by retrieving the list of respective supported browsers via the Sauce REST API.

If a single browser is selected, then the `SELENIUM_PLATFORM`, `SELENIUM_VERSION`, `SELENIUM_BROWSER` and `SELENIUM_DRIVER` environment variables will be populated to contain the details of the selected browser.  If multiple browsers are selected, then the `SAUCE_ONDEMAND_BROWSERS` environment variable will be populated with a JSON-formatted string containing the attributes of the selected browsers.  An example of the JSON string is:

```json

[
    {
    "platform":"LINUX",
    "os":"Linux",
    "browser":"firefox",
    "url":"sauce-ondemand:?os=Linux&browser=firefox&browser-version=16",
    "browser-version":"16"
    },
    {
    "platform":"VISTA",
    "os":"Windows 2008",
    "browser":"iexploreproxy",
    "url":"sauce-ondemand:?os=Windows 2008&browser=iexploreproxy&browser-version=9",
    "browser-version":"9"
    }
]
```

If the `SeleniumRC` radio button is selected, then a `Starting URL` field will also be displayed.

The plugin will set a series of environment variables based on the information provided on the Job Configuration. These environment variables can either be explicitly referenced by your unit tests, or through the use of the [selenium-client-factory] library.

* `SELENIUM_HOST` - The hostname of the Selenium server
* `SELENIUM_PORT` - The port of the Selenium server
* `SELENIUM_PLATFORM` - The operating system of the selected browser
* `SELENIUM_VERSION` - The version number of the selected browser
* `SELENIUM_BROWSER` - The browser name of the selected browser.
* `SELENIUM_DRIVER` - Contains the operating system, version and browser name of the selected browser, in a format designed for use by the [Selenium Client Factory]()
* `SAUCE_ONDEMAND_BROWSERS` - A JSON-formatted string representing the selected browsers
* `SELENIUM_URL` - The initial URL to load when the test begins
* `SAUCE_USER_NAME` - The user name used to invoke Sauce OnDemand
* `SAUCE_API_KEY` - The access key for the user used to invoke Sauce OnDemand
* `SELENIUM_STARTING_URL` - The value of the `Starting URL` field

By default, the plugin will use the authentication details specified in the Jenkins administration section.  However, this can be overriden at the Job level by enabling the `Override default authentication?` checkbox and specifying values in the `Username` and `Access key` fields.

Note: These values are set automatically by the Jenkins plugin. If the `Enable Sauce Connect?` checkbox is selected, then the `SELENIUM_HOST` and `SELENIUM_PORT` variables will default to localhost:4445.  If the checkbox is not set, then the `SELENIUM_HOST` and `SELENIUM_PORT` variables will be set to ondemand.saucelabs.com:4444.  

The values for the `SELENIUM_HOST` and `SELENIUM_PORT` environment variables can be overridden by explicitly specifying the host and port in the `Sauce OnDemand Host` and `Sauce OnDemand Port` fields, which can be displayed by clicking on the `Sauce Connect Advanced Options` button.

![Sauce Configure](/images/ci-integrations/jenkins/sauce-configure.png)

## Embedding Sauce Reports


The plugin also supports the embedding of Sauce Job reports within the display of test results.  This requires the tests executed by the Jenkins job to produce result files in the [JUnit XML]() report format. 

To enable this, select the `Add post-build Action` within the `Post-build Actions` section. 

![Add Post-build action](/images/ci-integrations/jenkins/post-build-action.png)

From the pop-up menu, select the `Publish JUnit test result report` option.

![JUnit Post-build action](/images/ci-integrations/jenkins/junit-post-build-action.png)

Enter `target/surefire-reports/*.xml` as the path to the test reports that are produced by your Jenkins Job, and check the `Embed Sauce OnDemand reports` checkbox.

![Embed Sauce Reports](/images/ci-integrations/jenkins/embed-sauce-reports.png)

That's it, our configuration is all setup, let's run the tests!

## Integrating tests with the Jenkins Sauce OnDemand plugin

To run the tests, click the `Build Now` link on the Job navigation pane.  This should compile and run three tests.

Once the build has finished, navigate to the Build Summary page.

![Sauce Build Summary](/images/ci-integrations/jenkins/sauce-build-summary.png)

On this page, you will see that links to the Sauce Jobs executed as part of the build are listed. 

![Sauce Summary Links](/images/ci-integrations/jenkins/sauce-summary-links.png)

Clicking on one of the links will present a page containing the Sauce report, which will allow you to view the steps performed and watch a video of the test.

![Sauce Report](/images/ci-integrations/jenkins/sauce-report.png)

The embedded Sauce reports can also be displayed on the individual test result pages. To view these, click on the `Test Result` link.

![Sauce Test Result](/images/ci-integrations/jenkins/sauce-test-result.png)

Navigate to individual test result, and the embedded report will be displayed with the test details.

![Sauce Embed Test Result](/images/ci-integrations/jenkins/sauce-embed-test-result.png)

That's it, we've successfully configured Jenkins to run our tests against Sauce OnDemand!

To summarise, in order to make the most use out of the Sauce Jenkins plugin, the following steps should be performed:

* Update tests to reference the environment variables set by the plugin
* Output the Sauce session id to the stdout to allow the Sauce plugin to associate test results to Sauce Jobs 

## Referencing Job Configuration

As mentioned previously, the Sauce Jenkins plugin will set a series of environment variables that reflect the values entered on the Jenkins Job Configuration screen.

Your test code will need to be updated to reference these environment variables.  

Below is some sample Java code which demonstrates how to reference the environment variables that are set by the Jenkins plugin

```java
    DesiredCapabilities desiredCapabilities = new DesiredCapabilities();
    desiredCapabilities.setBrowserName(System.getenv("SELENIUM_BROWSER"));
    desiredCapabilities.setVersion(System.getenv("SELENIUM_VERSION"));
    desiredCapabilities.setCapability(CapabilityType.PLATFORM, System.getenv("SELENIUM_PLATFORM"));
    WebDriver driver = new RemoteWebDriver(
                new URL("http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub"),
                desiredCapabilities);

```


As part of the post build activities, the Sauce plugin will parse the test result files. It attempts to identify lines in the stdout or stderr that are in the following format:

    SauceOnDemandSessionID=<some session id> job-name=<some job name>
    
The session id can be obtained from the `RemoteWebDriver` instance and the job-name can be any string, but is generally the name of the test class being executed.

Below is a Java sample that demonstrates outputting the session id to the Java stdout.

```java
private void printSessionId() {
        
        String message = String.format("SauceOnDemandSessionID=%1$s job-name=%2$s", (((RemoveWebDriver) driver).getSessionId()).toString(), "some job name");
        System.out.println(message);
    }
```

## Selenium Client Factory

An alternative to explicitly referencing the environment variables in your test code is to use the [Selenium Client Factory]() library.  This allows you to construct your SeleniumRC or WebDriver instances in a single line, eg.

```java
WebDriver webDriver = SeleniumFactory.createWebDriver();
```

Implementations of the library exist for [Java](https://github.com/infradna/selenium-client-factory) and [Python](http://sauceio.com/index.php/2012/01/selenium-client-factory-for-python/)


## Maven Configuration for a Java-based Project

The setup for Maven projects is essentially the same as for Freestyle projects, but there's a couple of extra steps.  Let's create a new Jenkins Maven project for our Java demo project.

From the Jenkins dashboard page, click `New Job`

![New Job](/images/ci-integrations/jenkins/new-job.png)

Enter 'Sauce Java Maven Demo' in the Job Name field and select `Build a maven 2/3 project`.

![New Freestyle Project](/images/ci-integrations/jenkins/new-maven-project.png)

Our sample code is located in [github](https://github.com/rossrowe/sauce-ci-java-demo), so select `Git` in the `Source Code Management` section, enter `https://github.com/rossrowe/sauce-ci-java-demo` as the repository URL and enter `master` in the branch specifier.

![Git Setup](/images/ci-integrations/jenkins/git-setup.png)

Enter `test` in the `Goals and options` field.

![Maven Goals](/images/ci-integrations/jenkins/maven-goal-option.png)

Now let's enable the Sauce OnDemand support for a Jenkins Job can be enabled by checking the `Sauce OnDemand Support` checkbox.

![Sauce Configure](/images/ci-integrations/jenkins/sauce-configure.png)

Select the `Enable Sauce Connect?` check box.  When selected, the Sauce plugin will launch an instance of [Sauce Connect](http://saucelabs.com/docs/sauce-connect) prior to the running of your Job.  This instance will be closed when the Job completes.

Click on the `WebDriver` radio button and select a browser to run our tests against (let's pick Firefox 15 running in Windows 2008)

![Sauce Configure](/images/ci-integrations/jenkins/sauce-configure.png)

To enable the embedding of Sauce OnDemand reports for test results, select the `Add post-build Action` within the `Post-build Actions` section, and select the `Additional test report feature (Sauce OnDemand)` option.

Select the `Add post-build Action` action again, and select the `Additional test report feature` option, and check the `Embed Sauce OnDemand reports` checkbox.

![Maven Post-build action](/images/ci-integrations/jenkins/maven-post-build.png)

That's it, our configuration is all setup, let's run the tests!

## Jenkins Configuration for a Python-based Project


Now let's create a new Jenkins Freestyle project for a Python project.

The test results will need to be in the [JUnit XML format]() in order for them to be displayed in Jenkins user interface and so that the Sauce plugin can parse the results.  There are several Python libraries which can produce the appropriate XML output, for this tutorial we are going to use [NoseXUnit](http://nosexunit.sourceforge.net/).

From the Jenkins dashboard page, click `New Job`.

![New Job](/images/ci-integrations/jenkins/new-job.png)

Enter 'Sauce Java Demo' in the Job Name field and select `Build a free-style software project`.

![New Freestyle Project](/images/ci-integrations/jenkins/new-freestyle-project.png)

Our sample code is located in [github](https://github.com/rossrowe/sauce-ci-python-demo), so select `Git` in the `Source Code Management` section, enter `https://github.com/rossrowe/sauce-ci-python-demo` as the repository URL and enter `master` in the branch specifier.

![Git Setup](/images/ci-integrations/jenkins/git-setup.png)

Now let's add a build step which will run our tests.  Click on the `Add Build Step` menu in the `Build` section, and select `Execute Shell`

![Execute Shell](/images/ci-integrations/jenkins/execute-shell.png)

Enter `nosetests -s --with-xunit simple_test.py` in the `Command` field.

![Nose command](/images/ci-integrations/jenkins/nose-command.png)

Now let's enable the Sauce OnDemand support for a Jenkins Job can be enabled by checking the `Sauce OnDemand Support` checkbox.

![Sauce Configure](/images/ci-integrations/jenkins/sauce-configure.png)

Select the `Enable Sauce Connect?` check box.  When selected, the Sauce plugin will launch an instance of [Sauce Connect](http://saucelabs.com/docs/sauce-connect) prior to the running of your Job.  This instance will be closed when the Job completes.

Click on the `WebDriver` radio button and select a browser to run our tests against (let's pick Firefox 15 running in Windows 2008)

![Sauce Configure](/images/ci-integrations/jenkins/sauce-configure.png)

Further details on the environment variables set by the Sauce plugin can be found on the [Java sample project tutorial page](/tutorials/java/)

To enable this, select the `Add post-build Action` within the `Post-build Actions` section. 

![Add Post-build action](/images/ci-integrations/jenkins/post-build-action.png)

From the pop-up menu, select the `Publish JUnit test result report` option.

![JUnit Post-build action](/images/ci-integrations/jenkins/junit-post-build-action.png)

Enter `nosetests.xml` as the path to the test reports that are produced by your Jenkins Job, and check the `Embed Sauce OnDemand reports` checkbox.

![Embed Sauce Reports](/images/ci-integrations/jenkins/embed-nose-reports.png)

That's it, our configuration is all setup, let's run the tests! 

## Multi-configuration projects

Jenkins [Multi-configuration projects](https://wiki.jenkins-ci.org/display/JENKINS/Building+a+matrix+project) allow you to run the same build with different input parameters.  The Sauce plugin for Jenkins provides an additional option for multi-configuration projects to specify the browser combination for each build.

To configure this on a multi-configuration job, click on the `Add Axis` button and select either the `Sauce OnDemand WebDriver tests` or `Sauce OnDemand SeleniumRC tests' item.  

![Sauce Configure](/images/ci-integrations/jenkins/multi-config-matrix.png)

This will present a list of browser combinations that are supported by Sauce Labs for WebDriver and SeleniumRC.

![WebDriver browsers](/images/ci-integrations/jenkins/sauce-matrix-webdriver.png)

For each selected browsers, a separate job will run when the build is invoked.  This job will include `SELENIUM_PLATFORM`, `SELENIUM_VERSION`, `SELENIUM_BROWER`, and `SELENIUM_DRIVER` system properties which will be set to the corresponding browser.

## Troubleshooting Jenkins

The source code for the plugin is available from [github](https://github.com/jenkinsci/sauce-ondemand-plugin)

Bugs/enhancements/feature requests can be recorded within the [Jira](https://issues.jenkins-ci.org/browse/JENKINS/component/15751) instance, or you can raise a [support request](https://support.saucelabs.com) 
