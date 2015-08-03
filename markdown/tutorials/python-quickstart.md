#Getting Started with Python

Sauce Labs is a cloud platform for executing automated and manual mobile and web tests. Sauce Labs supports running automated tests with Selenium Webdriver (for web applications) and Appium (for native and mobile web applications). 

In this tutorial we are going to show you how to run a test with Selenium Webdriver on Sauce Labs. 
For a mobile web application example with Appium please follow the instructions covered [here]().

##Table of Contents
1. [Dependencies]()
2. [Code Example]()
3. [Running Tests on Sauce]()
4. [Running Against Local Applications]()
5. [Reporting to the Sauce Labs Dashboard]()
6. [Running Tests in Parallel]()

##Dependencies
First, add the [Selenium WebDriver API](http://www.seleniumhq.org/download/) to your local Python environment using [pip](https://pypi.python.org/pypi/pip):

```
pip install selenium
```
	
##Code Example
Now let's look at a simple Python test script testing the Google front page. Despite the simplicity of this example test, it actually contains everything you need to know in order to run an automated test on Sauce Labs:

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities

# This is the only code you need to edit in your existing scripts. 
# The command_executor tells the test to run on Sauce; the desired_capabilties 
# parameter tells us which browsers and OS to spin up.
desired_cap = {
    'platform': "Mac OS X 10.9",
    'browserName': "chrome",
    'version': "31",
}
driver = webdriver.Remote(
   command_executor='http://USERNAME:ACCESS_KEY@ondemand.saucelabs.com:80/wd/hub',
   desired_capabilities=desired_cap)

# This is your test logic. You can add multiple tests here.
driver.implicitly_wait(10)
driver.get("http://www.google.com")
if not "Google" in driver.title:
    raise Exception("Unable to load google page!")
elem = driver.find_element_by_name("q")
elem.send_keys("Sauce Labs")
elem.submit()
print driver.title

# This is where you tell Sauce Labs to stop running tests on your behalf.  
# It is important so that you aren't billed after your test finishes.
driver.quit()
```
Copy the above code and save it into a file called first_test.py. Make sure your username and access key are correct. Then open terminal, navigate to the directory where the file is located and execute the test by typing:

```
python first_test.py
```

Check your [dashboard](http://www.saucelabs.com/dashboard) and you will see that you test has just run on Sauce!

__Note:__ The [implicitly wait](https://selenium-python.readthedocs.org/waits.html#implicit-waits) method tells the browser to wait a set amount of time (in seconds) for elements to appear on the page before giving up. Without it, slow loading DOMs could cause our tests to fail when they might otherwise pass.



Let's look at the test a little closer so you can write one like it or set your existing tests to run on Sauce.


##Running Tests on Sauce
If you wanted to run Selenium locally, you might initiate a driver for the browser that you want to test on like so:
```python
driver = webdriver.Firefox()
```

However, to run on Sauce we use the general webdriver.Remote() instead of the specific webdriver.Firefox() and then pass it two paramaters: command_executor and desired_capabilties.

(__Note:__ webdriver.Remote() is a standard Selenium interface, so you can do anything that you could do with a local Selenium test. The only code specific to Sauce Labs was the URL that makes the test run using a browser on Sauce Labs' servers.)


```python
# this is how you set up a test to run on Sauce Labs
desired_cap = {
    'platform': "Mac OS X 10.9",
    'browserName': "chrome",
    'version': "31",
}
driver = webdriver.Remote(
    command_executor='http://USERNAME:ACCESS_KEY@ondemand.saucelabs.com:80/wd/hub',
    desired_capabilities=desired_cap)
```

####Command Executor
The command_executor code tells our script to use browsers in the Sauce Labs cloud instead of a local browser. Here we simply pass in a URL that contains your Sauce username and access key which can be found on your [dashboard](http://www.saucelabs.com/dashboard).

####Testing on Different Platforms
Because we are not specifying webdriver.Firefox() as we were before we must use desired capabilities to specify what browser/OS combination(s) to spin up and execute against.

If you want to run against different platforms, simply update your desired capabiltlies to something new like so:

```python
desired_cap = {'browserName': "chrome", 'platform': "Windows 8.1", 'version': "42.0"}
```

The desired capabilities are a set of keys and values that will be sent to the Selenium server running in the Sauce Labs cloud. These keys and values tell the Selenium server the specifications of the automated test that you will be running. Using our [Platforms Configurator](https://docs.saucelabs.com/reference/platforms-configurator/#/) you can easily determine the correct desired capabilities for your test.

##Running tests against local applications
If your test application is not publicly available you will need to use Sauce Connect so that Sauce can reach it. 

Sauce Connect is a tunneling app which allows you to execute tests securely when testing behind firewalls or on localhost. For more detailed information, please visit the [Sauce Connect docs](https://docs.saucelabs.com/reference/sauce-connect/). 
 
## Reporting to the Sauce Labs Dashboard
####Recording Pass/Failure Results
"Wait," you might be asking. "My test says Finished but what happens if it fails?"

Unfortunately, Sauce has no way to determine whether your test passed or failed automatically, since it is determined entirely by your business logic. We can, however, tell Sauce about the results of our tests automatically using the [Sauce python client](https://pypi.python.org/pypi/sauceclient):
```
pip install sauceclient
```
Then add this to your test:
```python
# this authenticates you 
from sauceclient import SauceClient
sauce_client = SauceClient("USERNAME", "ACCESS KEY")

# this belongs in your test logic
sauce_client.jobs.update_job(driver.session_id, passed=True)
```
####Setting a Build Number
Now, you may want to associate this with a build id in your Continuous Integration pipeline. To do this just include a build number in your desired capabilities:

```python
desired_cap = {
    'platform': "Mac OS X 10.9",
    'browserName': "chrome",
    'version': "31",
    'build': "build-1234",
}
```

This is important for organizing tests in our new dashboard. Please see our docs for more information [here](https://docs.saucelabs.com/reference/test-configuration/#recording-build-numbers). 

####Tagging Your Tests

You can also create custom tags for your tests that you can use to search on the [archives](https://saucelabs.com/beta/archives) page:

```python
desired_cap = {
    'platform': "Mac OS X 10.9",
    'browserName': "chrome",
    'version': "31",
    'build': "build-1234",
    'tags': [ "tag1", "tag2", "tag3" ]
}
```

##Running tests in parallel
Now that you are running tests on Sauce, you may wonder how you can run your tests more quickly. Running tests in parallel is the answer! 

To do this we will need to use a third party test runner. Sauce Labs recommends using [py.test](http://pytest.org/latest/). (Py.test is an independent open source project and is not maintained by Sauce Labs.)
```
pip install -U pytest # or
easy_install -U pytest
```
Now, let's use the [platform configurator](https://docs.saucelabs.com/reference/platforms-configurator/) to add four platforms that we want to test on. Replace "desired_cap" in our example script above with an array of configs like so: 

```python
# replace this:
# desired_cap = {'os': 'Windows', 'os_version': 'xp', 'browser': 'IE', 'browser_version': '7.0' }

# with:
browsers = [{"platform": "Mac OS X 10.9",
             "browserName": "chrome",
             "version": "31"},
            {"platform": "Windows 8.1",
             "browserName": "internet explorer",
             "version": "11"},
             {"platform": "OS X 10.9",
             "browserName": "safari",
             "version": "7.0"},
             {"platform": "OS X 10.9",
             "browserName": "chrome",
             "version": "33.0"}]
```
Just add and remove browsers here as you see fit. 

Then create a decorator like so. You can actually just use the one here: 

```python
import sys
import json
import new
import unittest

def on_platforms(platforms):
    def decorator(base_class):
        module = sys.modules[base_class.__module__].__dict__
        for i, platform in enumerate(platforms):
            d = dict(base_class.__dict__)
            d['desired_capabilities'] = platform
            name = "%s_%s" % (base_class.__name__, i + 1)
            module[name] = new.classobj(name, (base_class,), d)
    return decorator
```
And finally put your test script in an object based structure and add the decorator to your test. Here is a full example with 2 tests on 4 platforms: 
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities

import sys
import json
import new
import unittest

browsers = [{"platform": "Mac OS X 10.9",
             "browserName": "chrome",
             "version": "31"},
            {"platform": "Windows 8.1",
             "browserName": "internet explorer",
             "version": "11"},
             {"platform": "OS X 10.9",
             "browserName": "safari",
             "version": "7.0"},
             {"platform": "OS X 10.9",
             "browserName": "chrome",
             "version": "33.0"}]

# This decorator is required to iterate over browsers
def on_platforms(platforms):
    def decorator(base_class):
        module = sys.modules[base_class.__module__].__dict__
        for i, platform in enumerate(platforms):
            d = dict(base_class.__dict__)
            d['desired_capabilities'] = platform
            name = "%s_%s" % (base_class.__name__, i + 1)
            module[name] = new.classobj(name, (base_class,), d)
    return decorator

@on_platforms(browsers)
class FirstSampleTest(unittest.TestCase):
	def setUp(self):
		self.desired_capabilities['name'] = self.id()
		self.driver = webdriver.Remote(
		   command_executor='http://jsmoxon:135be0f0-cc13-4faf-ae37-94e1092aa543@ondemand.saucelabs.com:80/wd/hub',
		   desired_capabilities=self.desired_capabilities)
		self.driver.implicitly_wait(30)

# This is your test logic. You can add multiple tests here.
	def test_google    (self):
		self.driver.get("http://www.google.com")
		if not "Google" in self.driver.title:
   			raise Exception("Unable to load google page!")
		elem = self.driver.find_element_by_name("q")
		elem.send_keys("Sauce Labs")
		elem.submit()
		print self.driver.title

	def test_amazon(self):
		self.driver.get("http://www.amazon.com")
		if not "Amazon.com" in self.driver.title:
   			raise Exception("Unable to load amazon page!")
		print self.driver.title		

	def tearDown(self):
		# This is where you tell Sauce Labs to stop running tests on your behalf.  
		# It is important so that you aren't billed after your test finishes.
		self.driver.quit()
```
Since we are running on 4 platforms use -n4 like so to run your tests in parallel:

```
py.test -n4 first_test.py
```
Visit your [dashboard]() and you should see 4 tests running at once! Use the [py.test docs](http://pytest.org/latest/) to learn more about how to properly use py.test for running tests in parallel or use the [Sauce TestRunner]().


## Next Steps
This is the basic set up for getting most of the value out of using Sauce Labs as your testing platform. 

However, there are a number of other best practices for configuring your tests. For more information please visit our [Best Practices]() page or our [Other Frameworks]() page. 
