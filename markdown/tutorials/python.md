 {
  title: "Python",
  description: "How to run Selenium tests on Sauce Labs using python",
  category: 'Tutorials',
  index: 3,
  image: "/images/tutorials/python.png"
}
## Basic Setup
### Dependencies

First, let's install the dependencies we'll need for the tutorial:

```bash
pip install selenium pytest pytest-xdist sauceclient
```

Note that you must have [pip installed]
(https://pip.pypa.io/en/latest/installing.html).  Also note that `sauceclient` does not yet support Python 3.

### Running a Test
Let's get started with the simplest thing we can imagine.  Let's make
sure Google hasn't changed their name.

First, let's set up our browser:

```python
from selenium import webdriver

sauce_url = "http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub"

desired_capabilities = {
	'platform': "Mac OS X 10.9",
	'browserName': "chrome",
	'version': "31",
}

driver = webdriver.Remote(desired_capabilities=desired_capabilities,
                          command_executor= sauce_url)
driver.implicitly_wait(10)
```
Now we have our own prestine Virtual Machine in the Sauce cloud,
ready to accept commands!

The [implicitly wait](# https://selenium-python.readthedocs.org/waits.html#implicit-waits) method tells the browser to wait a set
amount of time (in seconds) for elements to appear on the page 
before giving up.  Without it, slow loading DOMs could
cause our tests to fail when they might otherwise pass.

Now, let's use our new browser session!

```python
driver.get('http://google.com')
assert "Google" in driver.title
driver.quit()
```

Wahoo!  We just ran our first test!  You can view the results of your
tests on [your dashboard](https://saucelabs.com/tests).

### Reporting Test Results

"Wait," you might be asking.  "My test says ``Finished`` but what 
happens if it fails?"

Unfortunately, Sauce has no way to determine whether your test passed
or failed automatically, since it is determined entirely by your
business logic.  We can, however, tell Sauce about the results of our
tests automatically:

```python
from sauceclient import SauceClient

sauce_client = SauceClient("sauceUsername", "sauceAccessKey")

sauce_client.jobs.update_job(driver.session_id, passed=True)
```

Now, if you check [your dashboard](https://saucelabs.com/tests), you 
will see the status for the job as ``Passed``.

### Other Browsers

In our previous example, we ran our test on a very specific browser 
/ OS combo.  Sauce Labs actually supports *hundreds* of combinations
of browsers and operating systems, including mobile!  You can see the
full list of options on our [platform configurator page]
(https://docs.saucelabs.com/reference/platforms-configurator/).

## Advanced Usage

The previous example was good as an introduction, but as your tests
grow, you are likely to find that you need a testing setup that scales
as well.

Let's start with a sample test based on Python's unittest
framework. Download the [sample test](http://saucelabs.com/examples/example.py) with this command:

```bash
python -c "import urllib2;
open('example.py', 'wb').write(urllib2.urlopen('http://saucelabs.com/examples/example.py')
.read()
  .replace('_U_', '\"sauceUsername\"')
  .replace('_K_', '\"sauceAccessKey\"'))"
```

### Writing tests

Let's look at `example.py` to see how it works.

The `setUp()` method initializes the browser testing environment by
specifying the browser, version, and platform to test, then creates a
`webdriver.Remote` to run the tests remotely:

```python
sauce_url = "http://%s:%s@ondemand.saucelabs.com:80/wd/hub"
self.driver = webdriver.Remote(
    desired_capabilities=self.desired_capabilities,
    command_executor=sauce_url % (USERNAME, ACCESS_KEY)
)
```

The `webdriver.Remote` is a standard Selenium interface, so you can do
anything that you could do with a local Selenium test. The only code
specific to Sauce Labs was the URL that makes the test run using a
browser on Sauce Labs' servers.

Once connected to Sauce Labs, the test runs commands to remote-control
a browser. This simple example test simply requests a page and makes a
few simple assertions. It runs against several browsers
simultaneously, to demonstrate parallelized testing, and reports its
status to Sauce Labs when complete.

### Running tests

We recommend running tests in parallel using py.test. Since our
example test runs in two browsers, lets run both at the same time with -n2:

```bash
py.test -n2 --boxed example.py
```

Eventually you will see output like this:
```
======================= test session starts =======================
platform darwin -- Python 2.7.5 -- pytest-2.5.1
plugins: xdist
gw0 [2] / gw1 [2]
scheduling tests via LoadScheduling
..
==================== 2 passed in 17.30 seconds =====================
```
(The exact output will depend on your setup, but you should see all
tests passing.)
