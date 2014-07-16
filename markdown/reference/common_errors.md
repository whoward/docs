{ 
  title: "Troubleshooting Common Error Messages", 
  description: "Common Issues When Running Tests on Sauce", 
  category: "Reference", 
}

see also: http://support.saucelabs.com/entries/23001310-Common-Error-Messages-Automated-Testing-

If the below troubleshooting steps don't help, contact our support team using help@saucelabs.com

##Invalid credentials
Some combination of the following error messages will be thrown:
OpenQA.Selenium.WebDriverException : Unexpected error. Unknown username.
You sent username 'None' in your browser string, which is not a valid Sauce Labs account.
OpenQA.Selenium.WebDriverException : Unexpected error. Invalid Credentials.
org.openqa.selenium.UnsupportedCommandException: Invalid Credentials.
You sent the access key 'None' but it does not match the access key associated with your account. Please login to saucelabs.com to retrieve a valid access key.
 
This indicates that Selenium could not parse the credentials you sent. If you're using "access-key" in your test setup, try replacing it with "accessKey", or vice versa, as it's language-dependent.
 
##User Terminated
This means that you ended the job, either through the "Cancel Job" button, by jumping into an automated session from the Sauce website, or via a breakpoint (http://sauceio.com/index.php/2012/09/using-sauce-breakpoints-to-fin...). Since this terminates the job immediately and hands over control to you, the test will not upload any screenshots, video, or logs that were collected previously.
 
##Timeout errors
Sauce builds in a few default timeouts to keep uncontrolled tests from eating up your minutes. You can set these to different values using "desired capabilities" settings in the setup of your test, as described here: https://saucelabs.com/docs/additional-config#timeouts
Here's a brief rundown on what each of the available timeout settings does:
1. "Test did not see a new command for 90 seconds. Timing out."
The "idle-timeout" (default 90 seconds) ends the job if no commands are received from your Selenium script.
It's usually triggered when your test runner loses its connection to Sauce for an extended period, or if you are not calling the "driver.quit()" method at the end of your script. Make sure that you have stable Internet (ideally via a wired Ethernet connection), you're shutting down your test correctly, and your test runner is not stalling.
2. "Selenium took too long to run your command"
The "command-timeout" (default 5 minutes) ends the job if a single command takes too long to run.
This usually indicates a Selenium issue.
3. "Test exceeded maximum duration after 1800 seconds"
The "max-duration" timeout (default 30 minutes) ends the job if your entire test takes longer than you want.
This could be triggered if you have a repeating command that begins to cycle forever, or if there's a connectivity issue slowing down the test.
 

##ERROR user closed connection while waiting for command to complete
Similar to our command-timeout, this means that Selenium took a long time to run the command -- usually the act of loading a page -- and your test runner shut down the test before it might have timed out on Sauce's end. We recommend checking into why the page would take a long time to load (perhaps because of perpetually-loading items from another domain), and if need be, extending the related timeout setting in your test runner.
 

##UnreachableBrowserException: Error communicating with the remote browser. It may have died.

This error is thrown in Java when your test has timed out -- for example, due to the automatic 90-second idle timeout, which most often is caused by a dropped Internet connection. Then, the test script attempts to send another command to Sauce, but the job has already finished, so the VM is no longer available. Check your Internet connection, and try running your tests from a machine with a wired Ethernet connection.

 

##Unsupported OS/browser/version combo
Check to make sure that the browser, browser-version, and platform settings you're using are in our supported list: https://saucelabs.com/docs/platforms
And note that there's a separate list for Appium tests (as they use a different mechanic to drive the tests): https://saucelabs.com/docs/platforms/appium
Relatedly, you may see: "The requested combination of browser, version and OS is unsupported by the Selenium version requested and would lead to a test failure. Please set a different Selenium version, or just don't set it at all to default to a working version..."
This error usually means that you've manually set a Selenium version that is too old to handle a more recent browser version. It should be resolved by choosing a newer version of Selenium, or omitting this setting altogether.
 
##Browser failed to start
The twin sibling of "Unsupported version", this message means that something a little more unusual is off in your test setup. Usually, this means that you're specifying a Selenium version that isn't compatible with the browser/version/OS you've selected. (For example, you should not be setting this for any mobile tests.)
Try simply omitting that setting, and if you still see the issue, feel free to write to help@saucelabs.com with a description of the issue and a copy of your setup code.
 
##Client disconnected during getNewBrowserSession request
This means that your test runner decided to end the job before it had fully initialized on Sauce's end. There are a few potential causes:
1. You're running too many tests at a time: Check the left sidebar on your Account page (https://saucelabs.com/account). It shows a "Parallel tests" number, which is the maximum number of tests you can run at a time, based on your subscription level. If your account can run 2 parallel tests, and you're launching 10, 8 will be "queued" until one of your tests finishes and a slot frees up. However, if this takes a long time, your test runner may choose to end the queued jobs after a few minutes instead of waiting. Just make sure you're launching a reasonable number of simultaneous tests for your account.
2. High job wait times: Check our status page (http://status.saucelabs.com/) and/or follow @sauceops on Twitter for up-to-the-minute news about any issues within the service. If something causes demand for certain VMs to stack up, your jobs may be queued and (as above) terminated by your test runner.
 
Tests that end this way are never taken out of your minutes.
 
##Runaway execution. Please contact help@saucelabs.com for assistance.
This message means that an error has been detected in the VM or in Selenium, which caused the test to behave abnormally, and Sauce detected this and shut down the job. These are very rare and usually do not recur. If you do see more than one on the same test, let us know.
 

##UnreachableBrowserException: Could not start a new session. Possible causes are invalid address of the remote server or browser start-up failure.
This error is thrown on the client side, when Selenium isn't able to reach our testing servers to start a session. Try running this on the command line:
telnet ondemand.saucelabs.com 80

If your machine is able to hit our servers, you should see:
Trying 65.50.202.179...
Connected to ondemand.saucelabs.com.
Escape character is '^]'.

If you do receive this successful message, and still can't start a session, send that output to help@saucelabs.com and we'll troubleshoot further.
Otherwise, there's definitely something blocking the connection on your end. If so, you'll need to talk with your IT/network team to ensure that the required ports can be made available.

