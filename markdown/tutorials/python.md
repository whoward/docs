 {
  title: "Python",
  description: "How to run Selenium tests on Sauce Labs using python",
  category: 'Tutorials',
  index: 3,
  image: "/images/tutorials/python.png"
}
## Setting Up a Project

Let's start with a sample test based on Python's unittest
framework. Download the [sample test](http://saucelabs.com/examples/example.py) with this command:

```bash
python -c "import urllib2;
open('example.py', 'wb').write(urllib2.urlopen('http://saucelabs.com/examples/example.py')
.read()
  .replace('_U_', '\"sauceUsername\"')
  .replace('_K_', '\"sauceAccessKey\"'))"
```

And install some dependencies:

```bash
pip install selenium pytest pytest-xdist sauceclient
```

(If your setup doesn't have `pip install`, try `easy_install` in its place.)

## Writing tests

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

## Running tests

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
