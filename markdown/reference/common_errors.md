{
  title: "Troubleshooting Common Error Messages",
  description: "Common Issues When Running Tests on Sauce",
  category: "Reference",
  index: 7
}

If the below troubleshooting steps don't help, please contact our
[support team](mailto:help@saucelabs.com).


##Sauce Labs Authentication Error.

A variation of the following error message will be shown by your test:
```
Sauce Labs Authentication Error.
You used username 'None' and access key 'None' to authenticate, which are not valid Sauce Labs credentials.

The following desired capabilities were received:
...
```

This indicates that your Sauce Labs username and access key aren't provided by
your tests as expected. To address this, please [follow the right tutorial for
your programming language](https://docs.saucelabs.com/).

##User Terminated

This message is used for tests manually interrupted using the **Cancel** or
**Breakpoint** buttons in our website. Since both of these take control of
the virtual machine immediately, test assets like screenshots, video, or logs
that require additional execution time will not be collected and made
available afterwards.


##Timeout errors

Sauce Labs implements several types of timeouts for automated tests as safety
nets to prevent unwanted consumption of minutes by runaway or broken tests.

You can set these to different values using "desired capabilities" settings in
the setup of your test, as described in the
[timeout configuration docs](https://saucelabs.com/docs/additional-config#timeouts).

To better understand why these timeouts exist, let's look at the lifecycle of
a Selenium command through the Sauce Labs service:

![Command Lifecycle](/images/reference/common_errors/selenium-command-lifecycle.png)

Here you can see the different types of interactions that happen in real time
between the test script running in your own infrastructure, Sauce Labs' service
and the Selenium or Appium server running inside our virtual machines.

Here are some common timeout errors you may find, what causes them and how to
address them:


###Test did not see a new command for 90 seconds. Timing out

This error is shown when Sauce Labs doesn't receive a new command from your
Selenium script in more than 90 seconds (the timeout's default length.)

Using the previously shown graph as a reference, this would happen if **step
number 5 never happened**. Without such a timeout, any tests that lack a proper
session ending request (generally rendered as a call to `driver.quit()` or
`browser.stop()`) will keep running forever, consuming all test minutes
available in your account.

The most common cause for this is customers' **test scripts crashing, getting
forcefully interrupted or losing connectivity**.

A less common but still possible cause is tests legitimately needing more than
90 seconds to send a new command to the browser. This happens most often when
**network or disk IO occurs in between selenium API calls in your tests** (DB
queries, local file reads or changes.)

If that's the case, our `idleTimeout` desired capability can be used to modify
Sauce's wait time for further commands. Check our
[idleTimeout docs](https://docs.saucelabs.com/reference/test-configuration/#idle-test-timeout)
for more details on this.


###Selenium didn't complete your last command on time

This error is shown when Sauce Labs doesn't receive a response from Selenium to
your Script's last command in more than 5 minutes (the timeout's default length.)

Using the previously shown graph as a reference, this would happen if **step
number 3 never happened**. Without such a timeout, any tests in which the
Selenium, Appium server or browser crashes would keep running forever,
consuming all test minutes available in your account.

The most common causes for this are **unresponsive javascript in your
application** or **a bug in Selenium/Appium**.

A less common but still possible cause is Selenium or Appium legitimately
needing more than 5 minutes to run your command. If that's the case, our
`commandTimeout` desired capability can be used to have Sauce wait longer for
your commands to complete in Selenium. Check our
[commandTimeout docs](https://docs.saucelabs.com/reference/test-configuration/#command-timeout)
for more details on this.


###Test exceeded maximum duration after 1800 seconds

This error is shown when Sauce Labs finds an automated test still running after
30 minutes (the timeout's default length.)

The most common cause for this is **an infinite loop in your tests** that keep
sending commands without an end clause.

It's not rare to find cases where tests legitimately need more than 30 minutes
to complete. If that's the case, our `maxDuration` desired capability can be
used to have Sauce wait longer for your test to finish. Check our
[maxDuration docs](https://docs.saucelabs.com/reference/test-configuration/#maximum-test-duration)
for more details on this.


##ERROR user closed connection while waiting for command to complete

Similar to our command-timeout, this means that Selenium took a long time to run
the command -- usually the act of loading a page -- and your test runner shut
down the test before it might have timed out on Sauce's end. We recommend
checking into why the page would take a long time to load (perhaps because of
perpetually-loading items from another domain), and if need be, extending the
related timeout setting in your test runner.


##UnreachableBrowserException: Error communicating with the remote browser. It may have died

This error is thrown in Java when your test has timed out -- for example, due to
the automatic 90-second idle timeout, which most often is caused by a dropped
Internet connection. Then, the test script attempts to send another command to
Sauce, but the job has already finished, so the VM is no longer available. Check
your Internet connection, and try running your tests from a machine with a wired
Ethernet connection.


##Unsupported OS/browser/version combo

Check to make sure that the browser, browser-version, and platform settings
you're using are in our
[supported list of platforms](https://saucelabs.com/docs/platforms).

Note that there's a
[separate list for Appium tests](https://saucelabs.com/docs/platforms/appium).

Relatedly, you may see:

```
The requested combination of browser, version and OS is unsupported by the Selenium version requested and would lead to a test failure.
Please set a different Selenium version, or just don't set it at all to default
to a working version...
```

This error usually means that you've manually set a Selenium version that is too
old to handle a more recent browser version. It should be resolved by choosing
a newer version of Selenium, or omitting this setting altogether.


##The Sauce VMs failed to start the browser or device

The twin sibling of "Unsupported version", this message means that something
a little more unusual is off in your test setup. Usually, this means that you're
specifying a Selenium version that isn't compatible with the browser/version/OS
you've selected. (For example, you should not be setting this for any mobile
tests.)

Try simply omitting that setting, and if you still see the issue, feel free to
contact our [support team](mailto:help@saucelabs.com) with a description of the
issue and a copy of your setup code.


##Client disconnected during getNewBrowserSession request

This means that your test runner decided to end the job before it had fully
initialized on Sauce's end. There are a few potential causes:
* You're running too many tests at a time: Check the left sidebar on your
  Account page (https://saucelabs.com/account). It shows a "Parallel tests"
  number, which is the maximum number of tests you can run at a time, based on
  your subscription level. If your account can run 2 parallel tests, and you're
  launching 10, 8 will be "queued" until one of your tests finishes and a slot
  frees up. However, if this takes a long time, your test runner may choose to
  end the queued jobs after a few minutes instead of waiting. Just make sure
  you're launching a reasonable number of simultaneous tests for your account.
* High job wait times: Check our status page (http://status.saucelabs.com/)
  and/or follow @sauceops on Twitter for up-to-the-minute news about any issues
  within the service. If something causes demand for certain VMs to stack up,
  your jobs may be queued and (as above) terminated by your test runner.

Tests that end this way are never taken out of your minutes.


##Runaway execution. Please contact help@saucelabs.com for assistance

This message means that an error has been detected in the VM or in Selenium,
which caused the test to behave abnormally, and Sauce detected this and shut
down the job. These are very rare and usually do not recur. If you do see more
than one on the same test, let us know.


##UnreachableBrowserException: Could not start a new session. Possible causes are invalid address of the remote server or browser start-up failure

This error is thrown on the client side, when Selenium isn't able to reach our
testing servers to start a session. Try running this on the command line:
```
telnet ondemand.saucelabs.com 80
```

If your machine is able to hit our servers, you should see:
```
Trying 65.50.202.179...
Connected to ondemand.saucelabs.com.
Escape character is '^]'.
```

If you do receive this successful message, and still can't start a session, send
that output to [our support team](mailto:help@saucelabs.com) and we'll troubleshoot
further.

Otherwise, there's definitely something blocking the connection on your end. If
so, you'll need to talk with your IT/network team to ensure that the required
ports can be made available.
