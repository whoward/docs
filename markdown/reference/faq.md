{
  title: "FAQ",
  description: "Frequently asked questions about Sauce Labs",
  category: "Reference",
  index: 7
}

With Automated Sauce, we leverage Selenium's client/server architecture and add the internet between your local scripts (made using Selenium's client libraries) and the Selenium Server (which is on our side, as are the browsers). From your machine, you will kick off several test scripts, which will then communicate with our servers and send step-by-step instructions of what the browser needs to do. Once the script is finished, we will close the machine and send you a video and screenshot of every action your test took in the cloud.

To get the full picture, see [How Sauce works](https://saucelabs.com/docs/sauce-vs-local)

## How does billing work? What do you charge per minute?

For every test you run, the actual test time will be slightly shorter than the overall job duration, or what is billed by Sauce. This is because after the test finishes, our test servers remain fully dedicated to your account as it processes the video and screenshots, and uploads all artifacts to your account page.

It's important to point out that this time is necessary only on our servers. If you are running multiple tests at once, they will be assigned to new machines so there shouldn't be any impact on the overall test suite execution time.

Since we believe video and screenshots are helpful features for debugging, we have them on by default. However, if you want to save yourself a couple of test seconds, you can disable those features on a per-job basis.

Here's our documentation on how to do that:

- [Disabling video recording](/reference/test-configuration/#disabling-video-recording)
- [Disabling automatic screenshot capture](/reference/test-configuration/#disabling-step-by-step-screenshots)

## What OS and browser combinations do you support?

We support Firefox, Internet Explorer, Google Chrome, Safari, and Opera on Windows, Linux and Mac. Check out our complete list of [browser and operating system platforms](https://saucelabs.com/platforms) for details.

## Will my firewall block your service?

No. By using [Sauce Connect](/reference/sauce-connect), you can connect to our service securely without opening any ports in your firewall.

Sauce Connect creates a reliable, encrypted connection between your firewalled server and ours, and eliminates the need to whitelist IP addresses (since all requests come from the machine in which Sauce Connect is running).

## Can I use Sauce if my tool is behind basic HTTP Auth?

For HTTP Authentication, we behave as Selenium does, leveraging the use of the [RFC 1738](http://freesoft.org/CIE/RFC/1738/7.htm) specification, which you enter within the url. So you just have to change the url in your tests to include the Auth info. Here's an example: [http://username:password@www.mydomain.com/secretarea/](http://username:password@www.mydomain.com/secretarea/). You can also read more about [this here](http://wiki.openqa.org/display/SEL/Selenium%2BCore%2BFAQ#SeleniumCoreFAQ-HowdoIuseSeleniumtologintositesthatrequireHTTPbasicauthentication%28wherethebrowsermakesamodaldialogaskingforcredentials%29%3F). This should work with all our browsers, but if you find any obstacles with your site, please [let us know](mailto:help@saucelabs.com).

Note: If you don't yet have any HTTP Auth setup and all you want to do is have Sauce test your internal app, we strongly recommend using [Sauce Connect](/reference/sauce-connect) instead.

## My automated tests run serially. Can I still use Sauce?

Yes. With Sauce, you'll benefit from our many browser configurations and [features](https://saucelabs.com/features) such as live video recording.

## How do I run my automated tests in parallel?

Check out our various blog posts on how to do that with different programming languages: [ Running Selenium tests in parallel](http://sauceio.com/index.php/tag/parallel-testing/)

## Can I use Sauce for load testing my app?

Sauce is meant to be used for browser-based functional testing (also sometimes called "acceptance testing" or "UI testing"). And even though parallelization inherently causes load, the features other load testing services provide are probably better for your needs. We're friends with and fans of [Neustar (formally BrowserMob)](http://www.neustar.biz/enterprise/web-performance).

## How is Sauce different from Selenium Grid?

Check out our [Sauce vs. Local](https://saucelabs.com/docs/sauce-vs-local) doc to see some differences.

## Can I reuse a session within multiple tests?

One of our main criteria for a mature set of tests is "test independence." If your tests are completely independent from each other, the complete test suite will be reading for scaling. And believe us, scaling is never too far off. Once you reach a certain amount of tests, the time needed to run them serially will exceed the practical limit and:

  1. Your team will stop running the tests as regularly as needed because it takes too long
  2. The tests will start eating more and more of the development time

This can also depend on the context. In case it's really needed, we suggest preventing the test framework on your side from calling the start() and stop() commands between tests.

## My tests can't connect to Sauce. What should I do?

A general problem some users face is when outgoing connections from private networks on port 4444 are blocked, causing the tests to not reach Sauce. As a fast solution to that problem, Sauce also listens at ondemand.saucelabs.com:80. Just use ondemand.saucelabs.com as the Selenium host and 80 as the Selenium port.

## The video is missing, even though the test finished. What happened?

Missing videos are generally caused by the need to first post-process and upload the recorded video for it to be accessible to our users through the web interface. The time these tasks take will depend on the duration of the test (longer tests will produce longer videos). A reasonable estimation is that video processing and uploading consumes around 30% of total test time. This means that if your test takes 1 minute total (between browser start and shutdown), the video will take 20 seconds to upload to your account page. Additionally, if a test takes 10 minutes, it may take us up to 3 minutes to have the video ready.

Please [let us know](http://support.saucelabs.com/forums) if you find videos are taking longer than that to process.

## Open Commands time out, even though I see the app loaded in the video. Why is this?

This is generally caused by a connection gap or a problem with the application's server handling requests incorrectly. As a first step, you should proceed with a deep analysis of the network traffic. If you make it automated and run several tests at the same time, you will have higher chances of replicating the error.

Another good recommendation is to try out the [captureNetworkTraffic command](http://stackoverflow.com/questions/5103127/can-selenium-monitor-xhr-requests), which requires the Selenium instance to be started with the option captureNetworkTraffic=true and your test to use Firefox. This will let you pull the request info back out as JSON/XML/plain text. Then you can parse that content and find any problems.

## My tests are taking too long to start. What should I do?

We're constantly working to making our resource allocation as slick as possible, but at certain times, when our service is under very high load, this could take longer than expected. Please [check our status page](http://status.saucelabs.com) to see if there's an ongoing issue and [let us know](http://support.saucelabs.com/forums) if you find this happening too often.

## Tests that failed on my end appear to have passed in Sauce. How did that happen?

Because of the client/server architecture that Selenium employs, there's no information about assertion results on the server side (which, in this case, is Sauce). Here's an example. If your test has a step for validating that the title of your AUT is "My Shiny WebApp's Title", all that Sauce sees is a request to get the title from the current page. Therefor, it will only return the result, without even knowing what was expected.

Your test:


      assertEquals(sel.getTitle(), "My Shiny WebApp's Title");


Sauce Labs:


      Command requested: getTitle()
      Result: Your Page's Title


Notice that we use the same criteria for other kinds of failures, such as a Selenium exception for trying to click on a non-existent element. The reality is that tests on your end could be coded in such a way that failures won't always end up as a failed job.

**The good news is that you can let Sauce know what actually happened with your tests. ** Check out our [Pass/Fail API](/reference/test-configuration/#recording-pass-fail-status) to do it from within your Selenium tests.

## Your service is down. What should I do?

We're constantly checking our service so we're likely already aware of an outage. Please check [our status page](http://status.saucelabs.com) and [let us know](http://support.saucelabs.com/forums) if you don't see anything reported there.
  