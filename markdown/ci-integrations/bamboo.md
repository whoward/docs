{
  title: "Bamboo",
  description: "Run automated tests on Sauce Labs using Atlassian Bamboo",
  category: "CI Integrations",
  index: 4,
  image: "/images/ci-integrations/bamboo.png",
}

This tutorial explains how to use integrate [Atlassian Bamboo](http://www.atlassian.com/software/bamboo) with tests run with the Sauce Labs cloud of Selenium servers.

We assume that you have some familiarity with Bamboo and the fundamentals of automated testing. However, even if this is your first time using Bamboo and automated testing you should be able to successfully follow these step-by-step instructions.

## Installing the Bamboo Sauce OnDemand plugin

The Sauce OnDemand plugin for Bamboo can be installed through the Bamboo Administration page.

To access this page, click the `Administration` link.

![Admin](/images/ci-integrations/bamboo/admin.png)

Then click the `Plugin Manager` link within the `Plugins` section.

![Plugins](/images/ci-integrations/bamboo/plugins.png)

Enter `sauce` into the search field and click the `Search` button

![Search](/images/ci-integrations/bamboo/sauce-search.png)

Expand the `Bamboo Sauce OnDemand plugin` item, and click the `Install button`

![Plugin install](/images/ci-integrations/bamboo/plugin-install.png)

The plugin jar is quite large, so the download process might take a while.

![Install progress](/images/ci-integrations/bamboo/install-progress.png)

After the plugin has been installed, we will need to restart Bamboo for the plugin to be loaded.

Once Bamboo has been restarted, we can then configure the plugin to work with our environment.

## Configuring the Bamboo Sauce OnDemand plugin

After the Sauce plugin has been installed, the username and access key of the Sauce OnDemand user account must be entered within the Bamboo administration interface.

To configure the authentication, click the `Administration` link.

![Admin](/images/ci-integrations/bamboo/admin.png)

Click on the `Sauce OnDemand` link within the `Communication` section.

![Sauce Admin Link](/images/ci-integrations/bamboo/admin_sauce_link.png)

This section contains the fields required to configure how the authentication for the Sauce plugin.  Enter the values of the username and access key you wish the Sauce plugin to use in the `Username` and `API Access Key` fields.

![Sauce User details](/images/ci-integrations/bamboo/admin-sauce-user.png)

Once the authentication details have been entered and saved on the `Sauce OnDemand` screen, you can then enable Sauce support on the Configuration screen for a Bamboo Job.

## Bamboo Configuration for a Java-based Project

To demonstrate the Sauce plugin for Bamboo, let's create a new Bamboo project for a Java project.

Click the `Create Plan` link included on the top-right of the header bar.

![Create Plan](/images/ci-integrations/bamboo/create-plan.png)

Click the `Create New Plan` link.

![Create New Plan](/images/ci-integrations/bamboo/create-new-plan.png)

Enter `Sauce Demo` in the `Project` field.

Enter `SAUCE` in the `Project Key` field.

Enter `Java` in the `Plan Name` field.

Enter `DEMO` in the `Plan Key` field.

![New Plan Details](/images/ci-integrations/bamboo/new-plan-details.png)

Select `Git` in the `Source Repository` drop down list.

Enter `https://github.com/rossrowe/sauce-ci-java-demo` in the `Repository URL` field.

Enter `master` in the `Branch` field

![Source Repository Details](/images/ci-integrations/bamboo/plan-git.png)

Click the `Configure Tasks` button to continue.

![Configure Tasks](/images/ci-integrations/bamboo/configure-tasks.png)

Click the `Add Task` button.

![Add Task](/images/ci-integrations/bamboo/add-task.png)

Click the `Maven Task 3.x` link.

![Maven Task](/images/ci-integrations/bamboo/maven-task.png)

Enter `Run Tests` in the `Task Description` field.  Click the `Save` button.

The default values entered should be fine, so click the `Create` button.  We need to enter the Sauce configuration after the plan has been configured, so don't select the `Enable this plan` checkbox just yet.

Click the `Default Job` link on the left-hand navigation pane.

![Default Job](/images/ci-integrations/bamboo/default-job.png)

Click on the `Miscellaneous` tab

![Misc tab](/images/ci-integrations/bamboo/misc-tab.png)

Select the `Enable Sauce OnDemand` checkbox to enable the plugin.  The `Enable Sauce Connect` checkbox should be selected by default.  When selected, the Sauce plugin will launch an instance of [Sauce Connect](http://saucelabs.com/docs/sauce-connect) prior to the running of your Job.  This instance will be closed when the Job completes.

Select a browser to run our tests against from the `Browser` drop down list (let's pick Firefox 15 running in Windows 2008)

![Sauce options](/images/ci-integrations/bamboo/sauce-options.png)

Click the `Save` button.  That's it, our configuration is all setup, let's run the tests!

## Bamboo Configuration for a Python-based Project

To demonstrate the Sauce plugin for Bamboo, let's create a new Bamboo project for a Python project.

Click the `Create Plan` link included on the top-right of the header bar.

![Create Plan](/images/ci-integrations/bamboo/create-plan.png)

Click the `Create New Plan` link.

![Create New Plan](/images/ci-integrations/bamboo/create-new-plan.png)

Enter `Sauce Demo` in the `Project` field.

Enter `SAUCE` in the `Project Key` field.

Enter `Python` in the `Plan Name` field.

Enter `PYTHON` in the `Plan Key` field.

![New Plan Details](/images/ci-integrations/bamboo/new-plan-details.png)

Select `Git` in the `Source Repository` drop down list.

Enter `https://github.com/rossrowe/sauce-ci-python-demo` in the `Repository URL` field.

Enter `master` in the `Branch` field

![Source Repository Details](/images/ci-integrations/bamboo/plan-git.png)

Click the `Configure Tasks` button to continue.

![Configure Tasks](/images/ci-integrations/bamboo/configure-tasks.png)

Click the `Add Task` button.

![Add Task](/images/ci-integrations/bamboo/add-task.png)

Click the `Command` link.

Enter `Run Tests` in the `Task Description` field.

Click on the `Add Executable` link.

![Add new executable](/images/ci-integrations/bamboo/add-new-executable.png)

Enter `--with-nosexunit simple_test.py` in the `Argument` field.

Click the `Save` button.

Click the `Create` button.  We need to enter the Sauce configuration after the plan has been configured, so don't select the `Enable this plan` checkbox just yet.

Click the `Default Job` link on the left-hand navigation pane.

![Default Job](/images/ci-integrations/bamboo/default-job.png)

Click on the `Miscellaneous` tab

![Misc tab](/images/ci-integrations/bamboo/misc-tab.png)

Select the `Enable Sauce OnDemand` checkbox to enable the plugin.  The `Enable Sauce Connect` checkbox should be selected by default.  When selected, the Sauce plugin will launch an instance of [Sauce Connect](http://saucelabs.com/docs/sauce-connect) prior to the running of your Job.  This instance will be closed when the Job completes.

Select a browser to run our tests against from the `Browser` drop down list (let's pick Firefox 15 running in Windows 2008)

![Sauce options](/images/ci-integrations/bamboo/sauce-options.png)

Click the `Save` button.  That's it, our configuration is all setup, let's run the tests!

## Integrating tests with the Bamboo Sauce OnDemand plugin

To run the tests, go the Bamboo dashboard. The plan wasn't enabled when we it was created, so click the `Enable plan` image.

![Enable plan](/images/ci-integrations/bamboo/enable-plan.png)

Once the plan has been enabled, click the `Run` image.

![Run plan](/images/ci-integrations/bamboo/run-plan.png)

This should compile and run three tests.

Once the build has finished, navigate to the `Sauce Jobs` tab.

![Sauce Jobs](/images/ci-integrations/bamboo/sauce-jobs-tab.png)

Clicking on one of the links will present a page containing the Sauce report, which will allow you to view the steps performed and watch a video of the test.

![Sauce Report](/images/ci-integrations/bamboo/sauce-report.png)

That's it, we've successfully configured Bamboo to run our tests against Sauce OnDemand!

To summarise, in order to make the most use out of the Sauce Bamboo plugin, the following steps should be performed:

* Update tests to reference the environment variables set by the plugin
* Output the Sauce session id to the stdout to allow the Sauce plugin to associate test results to Sauce Jobs

Referencing Job Configuration
---

If a single browser is selected, then the `SELENIUM_PLATFORM`, `SELENIUM_VERSION`, `SELENIUM_BROWSER` and `SELENIUM_DRIVER` environment variables will be populated to contain the details of the selected browser.  In addition, a `bamboo_SAUCE_ONDEMAND_BROWSERS` environment variable will be populated with a JSON-formatted string containing the attributes of the selected browsers.  An example of the JSON string is:

```json

[
    {
    "platform":"LINUX",
    "os":"Linux",
    "browser":"firefox",
    "url":"sauce-ondemand:?os=Linux&browser=firefox&browser-version=16",
    "browserVersion":"16"
    },
    {
    "platform":"VISTA",
    "os":"Windows 2008",
    "browser":"iexploreproxy",
    "url":"sauce-ondemand:?os=Windows 2008&browser=iexploreproxy&browser-version=9",
    "browserVersion":"9"
    }
]
```

As mentioned previously, the Sauce Bamboo plugin will set a series of environment variables that reflect the values entered on the Bamboo Job Configuration screen.  These environment variables can either be explicitly referenced by your unit tests, or through the use of the [selenium-client-factory](#selenium-client-factory) library.

* `SELENIUM_HOST` - The hostname of the Selenium server
* `SELENIUM_PORT` - The port of the Selenium server
* `SELENIUM_PLATFORM` - The operating system of the selected browser
* `SELENIUM_VERSION` - The version number of the selected browser
* `SELENIUM_BROWSER` - The browser name of the selected browser.
* `SELENIUM_DRIVER` - Contains the operating system, version and browser name of the selected browser, in a format designed for use by the [Selenium Client Factory]()
* `bamboo_SAUCE_ONDEMAND_BROWSERS` - A JSON-formatted string representing the selected browsers
* `SELENIUM_URL` - The initial URL to load when the test begins
* `SAUCE_USER_NAME` - The user name used to invoke Sauce OnDemand
* `SAUCE_API_KEY` - The access key for the user used to invoke Sauce OnDemand
* `SELENIUM_STARTING_URL` - The value of the `Starting URL` field
* `SAUCE_BAMBOO_BUILDNUMBER` - The Bamboo build number

Your test code will need to be updated to reference these environment variables.

Below is some sample Java code which demonstrates how to reference the environment variables that are set by the Bamboo plugin

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

    String message = String.format("SauceOnDemandSessionID=%1$s job-name=%2$s",
    (((RemoveWebDriver) driver).getSessionId()).toString(), "some job name");
    System.out.println(message);
}
```

Selenium Client Factory
---
An alternative to explicitly referencing the environment variables in your test code is to use the [Selenium Client Factory]() library.  This allows you to construct your SeleniumRC or WebDriver instances in a single line, eg.

```java
WebDriver webDriver = SeleniumFactory.createWebDriver();
```

Implementations of the library exist for [Java](https://github.com/infradna/selenium-client-factory) and [Python](http://sauceio.com/index.php/2012/01/selenium-client-factory-for-python/)

## Troubleshooting Bamboo

The source code for the plugin is available from [GitHub](https://github.com/saucelabs/ci-integrations/bamboo_sauce)

Bugs/enhancements/feature requests can be recorded within the [Issues Register](https://github.com/saucelabs/ci-integrations/bamboo_sauce/issues) for the GitHub project, or you can raise a [support request](https://support.saucelabs.com) with Sauce Labs.
