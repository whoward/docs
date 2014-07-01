{
  title: "Test Configuration",
  description: "How to configure your jobs on Sauce Labs",
  category: "Reference",
  index: 1
}

Sauce Labs refers to an individual test session as a "job". For example, if your test suite contains 100 Selenium tests, and you run the entire suite 3 times, Sauce will keep records of 300 jobs, one for each test session. The following are additional settings you can use to annotate your jobs and configure Sauce on a per-job basis to collect more data, improve performance, set timeouts, and more. This is done differently depending on the API you are using: [WebDriver][1], [Selenium RC][2], or the [Sauce Labs REST API][3].

## WebDriver API
For Selenium and Appium tests using the WebDriver API, settings are provided using the `DesiredCapabilities` object provided by Remote WebDriver libraries. Any key-value pair specified in this documentation can be set through this hash-like object.
Find more about `RemoteDriver` and the `DesiredCapabilities` object on [Selenium's RemoteDriver wiki][5].

## Selenium RC API
For Selenium RC tests, settings are given in Selenium's "browser" parameter. In Selenium RC tests this is ordinarily a string like "\*iexplore" or "\*firefox", but for use with Sauce Labs it will need to contain a full [JSON object][4], like this:
```
 '{"username": "your username here",
   "access-key": "your access key here",
   "os": "Windows 8",
   "browser": "firefox",
   "browser-version": "29"}'
```

Any key-value pair specified in this documentation can be set through this JSON object.

## Job Annotation with the REST API

The Sauce Labs REST API provides a way to set the same additional information in jobs via a JSON object sent with an HTTP PUT command. Your tests can use this API to set job info even after the test is over. For example, this method is often used to update the job with information that couldn't be foreseen at the time the test was created, like the pass/fail status of a test. Here's an example of setting job info using curl, from the command line:
```bash
curl -X PUT \
-s -d '{"passed": true}' \ 
-u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID
```

### Accepted Keys

name: string

public: string

tags: array

build: integer

passed: boolean

custom-data: JSON object

Here's a more comprehensive example of the JSON accepted by this method:
```json
 {
      "name": "my job name",
      "passed": true,
      "public": "public",
      "tags": ["tag1", "tag2", "tag3"],
      "build": 234,
      "custom-data": {
          "release": "1.0",
          "server": "test.customer.com"
      }
  }
```

If you were to use this from your tests, you would probably want to build a simple set of functions that do the request for you. We've created a [Java library][6] for this, and here are some examples for [Python][7] and [Ruby][8]. We would love to see users share libraries for other languages!

[Read more about our REST API.][9]

### setContext()
setContext is an alternative to the REST API available for Selenium RC tests. In Selenium RC, the `setContext()` command is meant only for sending advisory information to the Selenium server for logging purposes. When the value passed to setContext starts with "sauce:", Sauce intercepts the command and parses it for job annotations. We allow two formats for setContext: basic and advanced. The basic format lets you set tags, name, and pass/fail status for jobs. The advanced format lets you set more fields, and you can set them all in a single command.

Basic format:
```java
  this.selenium.setContext("sauce:job-tags=tag1,tag2,tag3")
  this.selenium.setContext("sauce:job-name=My awesome job")
  this.selenium.setContext("sauce:job-result=passed")
  this.selenium.setContext("sauce:job-result=failed")
```
 
Advanced format:

Our advanced format involves submitting a JSON-encoded dictionary as the value of sauce:job-info. You can set as many or as few of the fields as you wish. For example, in Java, include the following code in your test to add information to a job:

```java
 this.selenium.setContext("sauce:job-info={\"name\": \"my job name\"," +
                          "\"tags\": [\"tag1\", \"tag2\", \"tag3\"]," +
                          "\"passed\": true,"+
                          "\"build\": \"103\","+
                          "\"custom-data\": {\"field\": \"value\"}"+
                          "}");
``` 

## Job Annotation

### Recording Test Names
To make it easier to find and identify individual tests, use the `name` setting to record test names on your jobs:

Key: `name`

Value type: string

Example:

```python
"name": "my example name"
```

### Recording Build Numbers
When looking through test results on our website, you'll probably want to know which version of your app the test was running against. Use this setting to annotate test jobs with a build number or app version. Once you set the build info on your job, it will be displayed on the job results page.

Key: `build`

Value type: string

Example:

```python
"build": "build-1234"
```

### Tagging
To facilitate filtering and grouping of jobs, arbitrary tags may be provided.

Key: `tags`

Value type: list

Example:

```python
"tags": [ "tag1", "tag2", "tag3" ]
```

### Recording Pass/Fail Status
Selenium and Appium handle sending commands to control a browser or app, but don't report to the server whether a test passed or failed. To record pass/fail status on Sauce, set the `passed` flag on the job.

Since you can't know in advance whether a test passed or failed, this flag can't be set in the initial configuration. Instead, you'll need to use one of our alternative job annotation methods, such as our [REST API][3].

Key: `passed`

Value type: bool

Example:

```python
"passed": true
```

### Recording Custom Data
To give you an extensible way to annotate and differentiate tests, Sauce provides a custom annotation you can set that will accept any valid JSON object. This field is limited to 64KB in size.

Key: `custom-data`

Value type: object

Example:

```python
"custom-data": {"release": "1.0", 
                "commit": "0k392a9dkjr", 
                "staging": true, 
                "execution_number": 5, 
                "server": "test.customer.com"}
```

## Optional Features

### Disabling Video Recording
By default, Sauce records a video of every test you run. This is generally handy for debugging failing tests, as well as having a visual confirmation that certain feature works (or still works!) However, there is an added wait time for screen recording during a test run. You can avoid this by optionally disabling video recording with this setting:

Key: `record-video`

Value type: bool

Example: 

```python
"record-video": false
```

As an alternative, the `video-upload-on-pass` setting will let you discard videos for passing tests identified using the<a href="#recording-pass-fail-status"><em>passed</em> setting</a>. This disables video post-processing and uploading that may otherwise consume some extra time after your test is complete.

Key: `video-upload-on-pass`

Value type: bool

Example:

```python
"video-upload-on-pass": false
```

### Disabling Step-by-step Screenshots
Sauce captures step-by-step screenshots of every test you run. Most users find it very useful to get a quick overview of what happened without having to watch the complete video. However, this feature may add some extra time to your tests. You can avoid this by optionally turning off this feature.

Key: `record-screenshots`

Value type: bool

Example:

```python
"record-screenshots": false
```
### Disabling log recording
By default, Sauce creates a log of all the actions that you execute to create a report for the test run that lets you troubleshoot test failures more easily.

Key: `record-logs`

Value type: bool

Example:

```python
"record-logs": false
```

### Enabling HTML Source Capture
In the same way Sauce [captures step-by-step screenshots][11], we can capture HTML source at each step. This feature is disable by default, but you can turn it on anytime and find the HTML source captures on your job result page:

Key: `capture-html`

Value type: bool

Example:

```python
"capture-html": true
```

### Enabling WebDriver's Automatic Screenshots
Selenium WebDriver captures automatic screenshots in every server side failure (e.g. element not found). Sauce prevents this by default to reduce network traffic during tests, resulting in a considerable performance improvement in most tests. If you'd prefer, you can re-enable screenshots. Note that enabling this feature may incur a performance penalty.

Key: `webdriver.remote.quietExceptions`

Value type: bool

Example:

```python
"webdriver.remote.quietExceptions": false
```

### Disabling Sauce Advisor
Sauce Advisor analyzes your tests and suggests ways to make them faster and more robust. It may add a small amount of extra time to your tests. To disable this feature, use the following setting:

Key: `sauce-advisor`

Value type: bool

Example:

```python
"sauce-advisor": false
```

## Selenium-specific Modifications

### Specifying a Selenium Version
To keep our service up to date with the current state of the Selenium project, we periodically update the default version of Selenium that we are running on our test servers.

If you find any problems with a particular version of Selenium, or for any other reason you'd like to keep your tests running on a specific version rather than the Sauce Labs default, you can do so using this setting.

Key: `selenium-version`

Value type: string

Example:

```python
"selenium-version": "2.41.0"
```


The current version being used as default is: **`2.30.0`**.<br/>
Supported versions you can choose from include:<br/>
`2.26.0` `2.27.0` `2.28.0` `2.29.0` `2.30.0` `2.31.0` `2.32.0` `2.33.0` `2.34.0` `2.35.0` `2.36.0` `2.37.0` `2.38.0` `2.39.0` `2.40.0` `2.41.0`

You can also specify the URL of a Selenium jar file (including Sauce Storage URLs) to use any custom version of Selenium.

### Selenium RC Single Window Mode
By default, to get the most out of videos and screenshots, Sauce Labs runs Selenium RC tests in multi-window mode. You can switch to single window mode with this setting.

Notice: this setting only affects Selenium RC tests.

Key: `single-window`

Value type: bool

Example:

```python
"single-window": true
```

### Selenium RC User Extensions
User extensions are available for custom Selenium RC functionality on the Sauce service. Given the URL of a file on an accessible HTTP or FTP server (public or connected with Sauce Connect), Sauce will download it and use it in your test. A list of several extensions can be provided.

Notice: this setting only affects Selenium RC tests.

Key: `user-extensions-url`

Value type: list

Example:

```python
"user-extensions-url": [
  "http://saucelabs.com/ext/flex.js",
  "ftp://username:password@server.com/bleh.js"
]
```

### Selenium RC Custom Firefox Profiles
Custom Firefox profiles allow you to configure the browser running in our cloud on a per-job basis. This includes both plugins and any particular setting your tests may need.

This feature is provided for Selenium RC tests. WebDriver users should use the official FirefoxProfile class [ as specified in the WebDriver documentation][13].

To use this feature, a zip file with the contents of the Firefox profile directory you wish to use needs to be provided. Given the URL of a file on an accessible HTTP or FTP server (public or connected with Sauce Connect), Sauce will download it and use it in your test.

For more info on Firefox profiles, you can check [Mozilla's knowledge base][14].

Key: `firefox-profile-url`

Value type: string

Example:

```python
"firefox-profile-url": "http://saucelabs.com/example_files/notls.zip"
```

**Note**: If you actually zip the directory, it will not work. The zip file needs to contain the contents of the profile, not a directory with the contents of it.

## Timeouts

### Maximum Test Duration
As a safety measure to prevent broken tests from running indefinitely, Sauce limits the duration of tests to 30 minutes by default. You can adjust this limit on per-job basis. The value of this setting is given in seconds.

Key: `max-duration`

Value type: integer

Example:

```python
"max-duration": 1800
```

### Command Timeout
As a safety measure to prevent Selenium crashes from making your tests run indefinitely, Sauce limits how long Selenium can take to run a command in our browsers. This is set to 300 seconds by default. The value of this setting is given in seconds.

Key: `command-timeout`

Value type: integer

Example

```python
"command-timeout": 300
```

### Idle Test Timeout
As a safety measure to prevent tests from running too long after something has gone wrong, Sauce limits how long a browser can wait for a test to send a new command. This is set to 90 seconds by default. You can adjust this limit on a per-job basis. The value of this setting is given in seconds.

Key: `idle-timeout`

Value type: integer

Example:

```python
"idle-timeout": 60
```

## Sauce-specific settings

### Pre-run Executables
Sauce allows users to provide a URL to an executable file, which we will download and run before tests start. For example, you can use pre-run executables to configure the VM before your test starts.

This capability takes a JSON object with 3 main keys:

Key: `prerun`

Value type: JSON object, with 3 keys:

  * **executable**: The url to the executable that you want to be run before your browser session starts
  * **args**: A list of the command line parameters that you want the executable to receive
  * **background**: A boolean that defines whether Sauce should wait for this executable to finish before your browser session starts. If background isn't set or is set to `false`, Sauce will wait for up to 90 seconds for the executable to finish. At that point, the browser will start and your test will proceed.

Example:

```python
"prerun": { "executable": "http://url.to/your/executable.exe",
            "args": [ "--silent", "-a", "-q" ], "background": true }
```

**A Note about AutoIt:** If you want to run an AutoIt script during your test, compile it as an exe, send it using this capability and set `background` to `true` to allow AutoIt to continue running throughout the full duration of your test.

**Multiple Pre-run Executables:** If you need to send multiple pre-run executables, the best way is to bundle them into a single executable file, such as a self-extracting zip file.

### Identified Tunnels
If an [identified tunnel][15] is started using Sauce Connect, your jobs can choose to proxy through it using this set of keys with the right identifier. See the [Sauce Connect documentation][15] for more information on identified tunnels.

Key: `tunnel-identifier`

Value type: string

Example:

```python
"tunnel-identifier": "MyTunnel01"
```

### Specifying the Screen Resolution
This setting specifies which screen resolution should be used during the test session. This feature is in beta and is available for tests running on Windows XP, Windows 7 (except Windows 7 with IE 9), Windows 8, Windows 8.1, OS X 10.6 and OS X 10.8. We do not yet offer specific resolutions for OS X 10.9.

Key: `screen-resolution`

Value type: string

Example:

```python
"screen-resolution": "1280x1024"
```

Valid values for Windows XP, Windows 7, and OS X 10.6 are:<br/> `800x600` `1024x768` `1280x1024` `1440x900` `1920x1200`

Valid values for OS X 10.8 are:<br/> `1024x768` `1280x1024` `1400x900` `1920x1200`

Valid values for Windows 8 and 8.1 are:<br/> `1024x768` `1280x1024`

### Custom Time Zones

Test VMs can be configured with custom time zones. This feature should work on all operating systems, however time zones on Windows VMs are approximate. They will default to the time zone that the provided location falls into. A complete list of valid locations [can be found here on Wikipedia][20]. Sauce takes only location names (not their paths), as shown in the example below.

Key: `time-zone`

Value type: string

Example:

```python
"time-zone": "Samoa"
```

###Internet Explorer Driver Version

The specific version of the IE Driver executable can be customized using the `iedriver-version` key.

In particular, Sauce supports launching 64-bit IE on our 64-bit VMs: Windows 7, Windows 8, and Windows 8.1. This provides a workaround for a known Selenium bug causing screencaptures using the 32-bit driver on a 64-bit operating system to fail to capture the whole web page.

Key: `iedriver-version`

Value Type: string

Example:

```python
"iedriver-version": "x64_2.41.0"
```

The list of supported IE Drivers you can choose from:<br/>

`2.21.1`, `2.21.2`, `2.24.0`, `2.25.3`, `2.26.0`, `2.28.0`, `2.29.0`, `2.30.1`, `2.31.0`, `2.32.2`, `2.33.0`, `2.34.0`, `2.35.0`, `2.35.1`, `2.35.2`, `2.35.3`, `2.36.0`, `2.37.0`, `2.38.0`, `2.39.0`, `2.40.0`, `2.41.0`, `2.42.0`, `x64_2.29.0`, `x64_2.39.0`, `x64_2.40.0`, `x64_2.41.0`, `x64_2.42.0`

### Pop-up Handling
Sauce provides a pop-up handler that automatically clicks through some types of browser pop-up windows, to allow tests to continue. By default, this feature is turned on for Selenium RC and off for WebDriver tests. You can control the pop-up handler yourself with the following capability:

Key: `disable-popup-handler`

Value type: bool

Example:

```python
"disable-popup-handler": true
```
### Avoiding the Selenium Proxy
By default, Sauce routes traffic from all Selenium RC and some WebDriver browsers through the Selenium HTTP proxy server so that HTTPS connections with self-signed certificates work everywhere. The Selenium proxy server can cause problems for some users. If that's the case for you, you can configure Sauce to avoid using the proxy server and have browsers communicate directly with your servers.

Note: Firefox and Google Chrome under WebDriver aren't affected by this flag as they handle invalid certificates automatically and there isn't a need to proxy through Selenium.

Note: This flag incompatible with [Sauce Connect][16].

Note: Under Selenium RC, `avoid-proxy` set to `true` will break safariproxy, firefoxproxy, iexploreproxy and opera browsers.

Key: `avoid-proxy`

Value type: bool

Example:

```python
"avoid-proxy": true
```
### Job Visibility
Sauce Labs supports several job visibility levels, which control who can view job details. The visibility level for a job can be set manually from the test result page, but also programatically when starting a test or with our REST API. 

Key: `public`

Value type: string

Example:

```python
"public": "team"
```

The available visibility levels are as follows:

- `public` 

    Making your test public means that it is accessible to everyone, and may be listed on public web pages and indexed by search engines.

- `public restricted`

    If you want to share your job's result page and video, but keep the logs only for you, you can certainly do so with public restricted visiblity mode. This visibility mode will hide the fancy job log as well as prohibit access to the raw Selenium log, so that anonymous users with the link will be able to watch the video and screen shots but won't be able to see what's being typed and done to get there.

- `share`

    You can also decide to make your test sharable. Making your test sharable means that it is only accessible to people having valid link and it is not listed on publicly available pages on saucelabs.com or indexed by search engines.

- `team`

    If you want to share your jobs with other team members (that were created as a sub-accounts of one parent account), you can use team visiblity mode. Making your test acessible by team means that it is only accessible to people under the same root account as you.

- `private`

    If you don't want to share your test's result page and video with anyone, you should use private job visibility mode. This way, only you (the owner) will be able to view assets and test result page.
    

**Note**: For more details about sharing jobs, check our [Job Results Integration][19] docs.

## Mobile Testing Options

### Device Orientation
By default, mobile emulators are run in portrait orientation. You can also set them to landscape orientation.

Key: `device-orientation`

Value type: string

Example:

```python
"device-orientation": "landscape"
```

## Building links to jobs

### Login required links to jobs

In Selenium, when a client requests a new browser session, the server returns a session ID, which is used to identify that session throughout the test. The session ID is stored as a member variable of the instantiated Selenium object and named "sessionId" or "session_id," depending on the client library. We use that session ID as the job id for accessing test results on our website.

To directly access a specific job, you will first need to note the session ID locally, usually by writing it to a log file. You can then use it to create a URL with the following format and replace **YOUR_JOB_ID** with the session ID.


    http://saucelabs.com/jobs/YOUR_JOB_ID

Notice that links to jobs in this format will only work if you are logged in with the account that ran the job or if that account is a sub-account of yours. For generating public links, read the section below, [ no-login links to jobs](#no-login-links-to-jobs).

**Note**: Selenium RC's Java client does not give public access to the session ID attribute of the DefaultSelenium object. However, we store a `selenium.sessionId` JavaScript variable that you can access using [getEval](http://bit.ly/cI51Dv).

### No-login links to jobs

The links generated in [login required links to jobs](#login-required-links-to-jobs) can be made in a way that doesn't require anonymous viewers to login and use your credentials. This mechanism is based in authentication tokens.

Auth tokens are generated on a per-job basis and give the receiver access using an [hmac-based algorithm](http://en.wikipedia.org/wiki/HMAC). You can also find [hmac implementations for different programming languages](http://en.wikipedia.org/wiki/HMAC#External_links).

The digest algorithm to use is **MD5**. The message and key used to generate the token should be the following:

- Key: `sauceUsername`:`sauceAccessKey`
- Message: `job-id`

Here's an example in Python for generating the token for a job with id: `5f9fef27854ca50a3c132ce331cb6034`

```python
import hmac
from hashlib import md5
hmac.new("sauceUsername:sauceAccessKey",
         "5f9fef27854ca50a3c132ce331cb6034", md5).hexdigest()
```

Once the auth token has been obtained, it can be used to build a link in the following format:

`https://saucelabs.com/jobs/YOUR_JOB_ID?auth=AUTH_TOKEN`

### Temporary links to jobs

There's a way to extend the links generated in [no-login links to jobs][1] to make them work only temporarily.

The authentication token can be generated in a way that provides 1 hour or 1 day of access to the job by using the following information for the hmac generation:

- Key: `YOUR_USERNAME`:`YOUR_ACCESS_KEY`:`YOUR_DATE_RANGE`
- Message: `YOUR_JOB_ID`

The date range can take two formats: **YYYY-MM-DD-HH** and **YYYY-MM-DD**. These **should be set in UTC time** and will only work during the date or hour chosen and the following.

## Embedding Results in HTML Pages

### Embedding full job pages

We offer a simple way to embed job pages in CI test results or other test reports. Using the format below, add the HTML to any page you need to embed job results on, replacing **YOUR_JOB_ID** with the ID of the job you want:

```html
<script src="https://saucelabs.com/job-embed/YOUR_JOB_ID.js">
</script>
```

**Note**: this will only work for browsers logged in using your account, and authentication tokens can be used to make this work for anonymous viewers. Check out [no-login links to jobs][1] for directions on generating these tokens.

### Embedding the video player

In addition to full job results, we offer a simple way to embed videos as well. Using the format below, add the HTML to any page you need to embed job videos on, replacing **YOUR_JOB_ID** with the ID of the job you want:

```html
<script src="https://saucelabs.com/video-embed/YOUR_JOB_ID.js">
</script>
```

**Note**: this will only work for browsers logged in using your account, and authentication tokens can be used to make this work for anonymous viewers. Check out [no-login links to jobs][1] for directions on generating these tokens. Here's how such a script might look:

```html
<script src="https://saucelabs.com/video-embed/YOUR_JOB_ID.js?auth=AUTH_TOKEN">
</script>
```

   [1]: #webdriver-api
   [2]: #selenium-rc-api
   [3]: #job-annotation-with-the-rest-api
   [4]: http://www.json.org
   [5]: http://code.google.com/p/selenium/wiki/RemoteWebDriver
   [6]: https://github.com/saucelabs/saucerest-java
   [7]: https://gist.github.com/1644439
   [8]: https://gist.github.com/DylanLacey/5218959
   [9]: /reference/rest-api/
   [10]: #recording-pass-fail-status
   [11]: #disabling-step-by-step-screenshots
   [12]: http://seleniumhq.org/docs/05_selenium_rc.html#multi-window-mode
   [13]: http://code.google.com/p/selenium/wiki/FirefoxDriver
   [14]: http://support.mozilla.com/en-US/kb/Managing-profiles
   [15]: /reference/sauce-connect/#managing-multiple-tunnels
   [16]: /reference/sauce-connect/
   [17]: #Webdriver-API
   [18]: https://saucelabs.com/now
   [19]: https://saucelabs.com/docs/integration
   [20]: http://en.wikipedia.org/wiki/List_of_tz_database_time_zones
   [21]: #setcontext-
   [22]: #job-annotation-with-the-rest-api
   [23]: #recording-test-names
   [24]: #job-visibilitiy
   [25]: #tagging
   [26]: #recording-build-numbers
   [27]: #recording-custom-data
