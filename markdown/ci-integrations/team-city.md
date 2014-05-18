{
  title: "Team City",
  description: "Run automated tests on Sauce Labs using Team City",
  category: "CI Integrations",
  index: 2,
  image: "/images/ci-integrations/team-city.png",
}

Sauce Labs and JetBrains TeamCity Tutorials
============
These tutorials explain how to use integrate [TeamCity](http://www.jetbrains.com/teamcity) with tests run with the Sauce Labs cloud of Selenium servers.

We assume that you have some familiarity with TeamCity and the fundamentals of automated testing. However, even if this is your first time using TeamCity and automated testing you should be able to successfully follow these step-by-step instructions.

Let's get started!

The Sauce OnDemand plugin for TeamCity can be installed by following these steps

Download the [plugin zip file](https://repository-saucelabs.forge.cloudbees.com/release/com/saucelabs/teamcity/sauceplugin/1.3/sauceplugin-1.3.zip)

Copy the zip file into your ~/.BuildServer/plugins directory

After the plugin has been installed, we will need to restart TeamCity for the plugin to be loaded.

Once TeamCity has been restarted, we can then configure the plugin to work with our environment.

TeamCity Configuration for a Java-based Project
=============

To demonstrate the Sauce plugin for TeamCity, let's create a new TeamCity project for a Java project.

To create a new project, first click the `Administration` link on the top-right of the page.

![Administration](/images/ci-integrations/team-city/administration.png)

Click the `Create Project` button.

Enter `Sauce Demo` in the `Name` field, this should populate the `Project ID` field with `SauceDemo`.

Click the `Create` button.

![Create New Plan](/images/ci-integrations/team-city/create-new-project.png)

Click the `VCS Roots` link.

![VCS Root Link](/images/ci-integrations/team-city/vcs-root-link.png)

Click the `Create VCS Root` button.

![VCS Root Button](/images/ci-integrations/team-city/vcs-root-button.png)

Enter `https://github.com/rossrowe/sauce-ci-java-demo.git` in the `Fetch URL` field.

Enter `master` in the `Default Branch` field.

Click the `Save Button`.

![Source Repository Details](/images/ci-integrations/team-city/plan-git.png)

Click the `Create Build Configuration` button.

![Create Build Configuration](/images/ci-integrations/team-city/create-build-configuration.png)

Enter `Maven` in the `Name` field

Click the `VCS Settings` button.

![Build Settings](/images/ci-integrations/team-city/build-settings.png)

Select the `https://github.com/rossrowe/sauce-ci-java-demo.git#master` option within the `attach existing VCS root` drop down list.

Click the `Add Build Step` button.

![Build Git](/images/ci-integrations/team-city/build-git.png)

Select `Maven` from the `Runner type` drop down list.

Enter `test` in the `Goals` field.

Click the `Save` button.

![Build Maven](/images/ci-integrations/team-city/build-maven.png)

Click the `Add Build Feature` button.

![Add Build Feature](/images/ci-integrations/team-city/add-build-feature.png)

Select `Sauce Labs Build Feature` from the drop down list.

![Sauce build feature](/images/ci-integrations/team-city/sauce-build-feature.png)

Enter the values of the username and access key you wish the Sauce plugin to use in the `Sauce User` and `Sauce Access Key` fields.

When the `Enable Sauce Connect` checkbox is selected, the Sauce plugin will launch an instance of [Sauce Connect](http://saucelabs.com/docs/sauce-connect) prior to the running of your Job.  This instance will be closed when the Job completes.

If a single browser is selected, then the `SELENIUM_PLATFORM`, `SELENIUM_VERSION`, `SELENIUM_BROWSER` and `SELENIUM_DRIVER` environment variables will be populated to contain the details of the selected browser.  If multiple browsers are selected, then the `SELENIUM_BROWSER` environment variable will be populated with a JSON-formatted string containing the attributes of the selected browsers.  An example of the JSON string is:

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

The plugin will set a series of environment variables based on the information provided on the Job Configuration. These environment variables can either be explicitly referenced by your unit tests, or through the use of the [selenium-client-factory](https://github.com/infradna/selenium-client-factory) library.

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

![Sauce options](/images/ci-integrations/team-city/sauce-options.png)

Click the `Save` button.  That's it, our configuration is all setup, let's run the tests!
Integrating tests with the TeamCity Sauce OnDemand plugin
=============

To run the tests, go the TeamCity dashboard. Click the `Run` button.

![Run build](/images/ci-integrations/team-city/run-build.png)

This should compile and run three tests.


****

Once the build has finished, click on the build results link and navigate to the `Sauce Labs Results` tab.

![Build Results](/images/ci-integrations/team-city/build-results.png)

Clicking on one of the links will present a page containing the Sauce report, which will allow you to view the steps performed and watch a video of the test.

![Sauce Report](/images/ci-integrations/team-city/sauce-report.png)

That's it, we've successfully configured TeamCity to run our tests against Sauce Labs!

To summarise, in order to make the most use out of the Sauce TeamCity plugin, the following steps should be performed:

* Update tests to reference the environment variables set by the plugin
* Output the Sauce session id to the stdout to allow the Sauce plugin to associate test results to Sauce Jobs

Referencing Job Configuration
---

As mentioned previously, the Sauce TeamCity plugin will set a series of environment variables that reflect the values entered on the TeamCity Build Feature screen.

Your test code will need to be updated to reference these environment variables.

Below is some sample Java code which demonstrates how to reference the environment variables that are set by the TeamCity plugin

```java
    DesiredCapabilities desiredCapabilities = new DesiredCapabilities();
    desiredCapabilities.setBrowserName(System.getenv("SELENIUM_BROWSER"));
    desiredCapabilities.setVersion(System.getenv("SELENIUM_VERSION"));
    desiredCapabilities.setCapability(CapabilityType.PLATFORM, System.getenv("SELENIUM_PLATFORM"));
    WebDriver driver = new RemoteWebDriver(
                new URL("http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub"),
                desiredCapabilities);

```

Output Sauce Session id
---

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

Selenium Client Factory
---
An alternative to explicitly referencing the environment variables in your test code is to use the [Selenium Client Factory]() library.  This allows you to construct your SeleniumRC or WebDriver instances in a single line, eg.

```java
WebDriver webDriver = SeleniumFactory.createWebDriver();
```

Implementations of the library exist for [Java](https://github.com/infradna/selenium-client-factory) and [Python](http://sauceio.com/index.php/2012/01/selenium-client-factory-for-python/)

Troubleshooting
======

The source code for the plugin is available from [GitHub](https://github.com/rossrowe/sauce-teamcity-plugin)

Bugs/enhancements/feature requests can be recorded within the [Issues Register](https://github.com/rossrowe/sauce-teamcity-plugin/issues) for the GitHub project, or you can raise a [support request](https://support.saucelabs.com) with Sauce Labs.