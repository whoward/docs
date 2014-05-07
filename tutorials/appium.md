 {
  title: "Appium",
  description: "Runnin Appium tests on Sauce Labs",
  category: 'Tutorials',
  image: "/images/tutorials/Appium.png",
  index: 7
}
Appium on Sauce Labs
====================

Appium is an open source mobile automation tool for iOS and Android. Appium can test native and hybrid applications, and can even be used to automate Mobile Safari on iOS. 

There are several advantages to using Appium on Sauce Labs to test your mobile applications:

  - Since Appium uses the Selenium JSON Wire Protocol, you can write your tests in any language supported by Selenium.
  - Appium does not require any modification to the source code of your application before running tests.
  - Appium uses first-party automation frameworks from Apple and Google, so tests replicate user behavior very accurately.
  - With Appium on Sauce, your tests require no setup or virtualization, and can be scaled instantly.

For an in-depth explanation of how Appium works, and the various technologies used to make Appium work, visit the [Appium Documentation](http://appium.io). 

Writing Tests With Appium
---

Getting started with Appium on Sauce Labs is easy:

 - Get a [Sauce Labs account](https://saucelabs.com/signup).
 - Write your tests using the WebDriver client library for the language of your choice.
 - Configure your selenium tests to use Sauce Labs' simulators and emulators in the cloud.

For information on writing, configuring, and running your tests in Sauce Labs' cloud, check out our Getting Started Guides:

  - [iOS Getting Started guide](##01-iOS-on-Sauce.md##)
  - [Android Getting Started guide](##02-Android-on-Sauce.md##)

WebDriver Client Libraries
---

Appium works with WebDriver's client bindings, so you'll need to install the bindings for your favorite language. This tutorial provides examples for node.js, PHP, Ruby, and Python, but other languages with a WebDriver client library will work including Java, C#, and Perl.

Complete Tutorial
---

Want to know all the ins and outs of testing your iOS app with Appium on Sauce? Then you're in the right place. These tutorials explain how to automate testing of your apps with Appium.

We assume that you have some familiarity with Selenium, your operating system, and the fundamentals of automated testing. However, even if this is your first time automating an app, you should still be able to follow along!

Let's get started! Click the first link below:

  - [Running iOS Tests on Sauce](##01-iOS-on-Sauce.md##)
  - [Running Android Tests on Sauce](##02-Android-on-Sauce.md##)
  - [Tips for Writing Better Appium Tests](##03-Tips.md##)
  - [Next Steps and More Information](##04-Next-Steps.md##)

Appium for iOS on Sauce Labs
===

Let's get started with running iOS tests on Sauce Labs! This section is a bit long, so here's what we'll cover:

  - [Running Your First Test](#run-test)
  - [System Requirements](#system-requirements)
  - [Compiling to Specify a Simulator](#compiling)
  - [Setting Up Your Tests for Sauce](#setting-up-tests)
  - [Understanding Appium on Sauce](#understanding-appium)

<a name="run-test"></a>Running Your First Test
---

Your first test will run against our Test Application, which is hosted on Sauce. When you run your own tests you'll need to provide your own app. Instructions for providing your own app are provided in the [Setup](#setting-up-tests) section.

To run your first test, simply run the quick start code below. Our example is in Ruby using RSpec, though Appium can be used with almost any language and test framework:



To run the Ruby example, you need to have rspec installed, in addition to the Selenium WebDriver client library. To install these, and download a simple example test, run:

```bash
curl -L https://raw.github.com/saucelabs/appium-tutorial/master/bin/install_tutorial_ruby.sh | bash -s
```

Now you are ready to run your first test! The `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables authenticate you with Sauce Labs:

```bash
SAUCE_USERNAME=sauceUsername SAUCE_ACCESS_KEY=sauceAccessKey rspec SauceTest.rb
```

Take a look at SauceTest.rb to see what an Appium test looks like in Ruby.

<a name="your-account"></a>Your Account Page
---
Congratulations! You've run your first Appium test on Sauce! To see the output from your test, head over to your [Sauce account page](https://saucelabs.com/account) where you can see the important data from your test run, plus video, screenshots, and logs.

You may notice that Appium tests take a bit longer than regular selenium tests. This is due to the additional startup time required by the iOS simulator and Instruments - something we are looking to alleviate in the near future.

<a name="system-requirements"></a>System Requirements
---

You'll need the following to start testing your app using Appium on Sauce:

  - A computer running Mac OS 10.7.4 or higher
  - Xcode and the iOS 6 SDK or higher
  - The Xcode command line tools installed
  - Your app source code or a prebuilt .app bundle for your app
  - A [Sauce Labs account](https://saucelabs.com/account)
  - A Selenium WebDriver client library for your langauge of choice (see the [Tutorial Introduction](##00-Introduction.md##))

If you do not have a Mac, you will not be able to compile your app for Appium. However, you only need a Mac for compiling the app, so if you already have a compiled '.app' folder, then you can run your tests from Windows, Mac, or Linux.

<a name="compiling"></a>Compiling to Specify a Simulator
---

Your app can optionally be compiled to support only the simulator you want your test app to run in. If your app is compiled as a Universal app (typically the default) it will launch in an iPad simulator. To launch your app in an iPhone simulator compile the app for iPhone.

To compile the app, use the `xcodebuild` command line utility with the TARGETED_DEVICE_FAMILY parameter. The value for this parameter may be `1`, `2`, or `1,2`.

  - `1` designates iPhone only.
  - `2` designates iPad only.
  - `1,2` designates a Universal app, which Instruments will *always open in an iPad simulator*.

So, for example, to build an app to launch in the iPhone simulator, change to the command prompt for the top level directory of your app's source code (before it's been built), then run the following command:

```
xcodebuild -sdk iphonesimulator6.0 TARGETED_DEVICE_FAMILY=1
```

After your app compiles you'll have a `build/<*target*>` directory in your app's source code that contains a `.app` directory. Change to the command prompt for your `build/<*target*>` directory, zip the entire `.app` directory, and use it as the value for the `app` capability in your DesiredCapabilities object as discussed in [Running Your First Test](#run-test).

<a name="setting-up-tests"></a>Setting Up Your Tests for Sauce
---

First, zip and upload your app to Sauce Labs temporary storage using our [Temporary Storage REST API](http://saucelabs.com/docs/rest#storage):

```bash
$ zip -r my_app.zip MyApp.app/
...
$ curl -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY -X POST "http://saucelabs.com/rest/v1/storage/$SAUCE_USERNAME/my_app.zip?overwrite=true" -H "Content-Type: application/octet-stream" --data-binary @my_app.zip
```

Then, in your test code, point your RemoteWebDriver instance to `http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub`.

You'll also need to add the following fields (key/value pairs) to DesiredCapabilities:

  - `app`: `sauce-storage:my_app.zip` The URL of a zip file containing your `.app` directory. (Usually a `sauce-storage:` URL to access files uploaded to Sauce temporary storage.)
  - `device`: `iPhone Simulator`
  - `platform`: `OS X 10.8`
  - `version`: `6.0`

**Note:** The `iPhone Simulator` value for the `device` key will launch an iPhone or iPad simulator depending on whether your app was compiled with `1`, `2`, or `1,2` as the `TARGETED_DEVICE_FAMILY`  as discussed in the [Compiling Your App for Sauce](#compiling) section.

That's it! You're ready to run your test script against your app on Appium on Sauce!

For a complete list of supported Appium platforms and code examples for using them, see our [Appium platforms documentation](https://saucelabs.com/docs/platforms/appium).

<a name="understanding-appium"></a>Understanding Appium on Sauce
---

Here's how running tests on Appium on Sauce work.

When you run your test script its configuration points it to Appium on the Sauce cloud. Appium's implementation of the [Selenium WebDriver JSON Wire Protocol API](http://code.google.com/p/selenium/wiki/JsonWireProtocol) receives your script's commands, launches your test app in the simulator that you specified, converts your Selenium commands into UI Automation JavaScript commands, and sends the JavaScript commands to your iOS app via Apple Instruments. Instruments executes the commands against your test app in the simulator.

The responses from your test app are received by Instruments, sent to UI Automation, and relayed to Appium. Appium's implementation of the Selenium API converts the UI Automation JavaScript responses into the Selenium WebDriver JSON Wire Protocol, and sends the JSON responses back to your test script.

Appium hides all of this complexity from your test script (and from you!). Your test script thinks it's communicating with your test app, but it's really communicating with Appium's implementation of the Selenium API. The fact that Appium is running your app in the appropriate simulator and wrapping UI Automation, Instruments, and all of the communications with your test app including the conversions to and from JavaScript and JSON remains completely hidden, and your test script is none the wiser.

Before you can run your apps on Appium you'll need the .app directory created by XCode for your app. Sometimes, using special build settings will make Appium more useful; these are covered in the [Compiling Your App for Sauce](#compiling) section.

To specify an Appium session in Sauce, simply use the same DesiredCapabilities object that you already use in your Selenium tests, with a few added fields. Your test script remains the same, assuming that it's using [Selenium commands supported by Appium](https://github.com/appium/appium/wiki/JSON-Wire-Protocol:-Supported-Methods). Configuring DesiredCapabilities is discussed in the [Setting Up Your Tests](#setting-up-tests) section.

Appium for Android on Sauce Labs
===

Now let's get started with Android tests on Sauce Labs. Here's what this section covers:

  - [Running Your First Test](#run-test)
  - [System Requirements](#system-requirements)
  - [Setting Up Your Tests for Sauce](#setting-up-tests)
  - [Understanding Appium for Android on Sauce](#understanding-appium)

<a name="run-test"></a>Running Your First Test
---

Your first test will run against our Test Application, which is hosted on Sauce. When you run your own tests you'll need to provide your own app. Instructions for providing your own app are provided in the [Setup](#setting-up-tests) section.

To run your first test, simply run the quick start code below. Our example is in Ruby using RSpec, though Appium can be used with almost any language and test framework:
 


To run the Ruby example, you need to have rspec installed, in addition to the Selenium WebDriver client library. To install these, run:

```bash
sudo gem install rspec selenium-webdriver net-http-persistent rest_client
```

Then download the example test:

```bash
curl -s https://raw.github.com/appium/appium/master/sample-code/examples/ruby/android_on_sauce.rb > AndroidSauce.rb
```

Now you are ready to run your first test! The `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables authenticate you with Sauce Labs:

```bash
SAUCE_USERNAME=sauceUsername SAUCE_ACCESS_KEY=sauceAccessKey rspec AndroidSauce.rb
```

Take a look at AndroidSauce.rb to see what an Appium test looks like in Ruby.

Your Account Page
---

Congratulations! You've run your first Appium test on Sauce! To see the output from your test, head over to your [Sauce account page](https://saucelabs.com/account) where you can see the important data from your test run, plus video, screenshots, and logs.

You may notice that Appium tests take a bit longer than regular selenium tests. This is due to the additional startup time required by the Android Emulator- something we are looking to alleviate in the near future.

<a name='system-requirements'></a>System Requirements
---

  - Your app compiled as an .apk.
  - A [Sauce Labs account](https://saucelabs.com/account)
  - A Selenium WebDriver client library for your langauge of choice (see the [Tutorial Introduction](##00-Introduction.md##))

<a name='setting-up-tests'></a>Setting Up Your Tests for Sauce
---

First, upload your app to Sauce Labs temporary storage using our [Temporary Storage REST API](http://saucelabs.com/docs/rest#storage):

```bash
$ zip -r my_app.zip MyApp.apk/
...
$ curl -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY -X POST "http://saucelabs.com/rest/v1/storage/$SAUCE_USERNAME/my_app.zip?overwrite=true" -H "Content-Type: application/octet-stream" --data-binary @my_app.zip 
```

Then, in your test code, point your RemoteWebDriver instance to `http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub`.

You'll also need to add the following fields (key/value pairs) to DesiredCapabilities:

  - `app`: `"sauce-storage:my_app.zip"` The URL of a zip file containing your `.apk` directory. (Usually a `sauce-storage:` URL to access files uploaded to Sauce temporary storage.)
  - `app-package`: The package name of your app, like `"com.example.android.notepad"`
  - `app-activity`: The name of the activity to start, with a dot in front of it, like `".NotesList"` in the example. This is appended to the package to start your app, like `com.example.android.notepad.NotesList`.
  - `device`: `"android"` 
  - `version`: `4.2` (Currently we support 2.3, 4.0, 4.1, 4.2 and 4.3)
  - `deviceType`: `"phone"` (the default if this field is missing), or `"tablet"`

That's it! You're ready to run your test script against your app on Appium on Sauce!

For a complete list of supported Appium platforms and code examples for using them, see our [Appium platforms documentation](https://saucelabs.com/docs/platforms/appium).

<a name='understanding-appium'></a>Understanding Appium for Android on Sauce
---

Appium automates Android using the UIAutomator library, which is provided by Google as part of the Android SDK, for the purpose of automating your app. When you change the configuration of your tests to point to the Sauce Labs cloud, we handle the command, launch the emulator, download your app, and install and launch the app in the emulator. Then as each command is sent by your script, it is converted to a UIAutomator command, and run against your app in the emulator. 

The responses from your app (whether a success or an error) are converted from UIAutomator responses back into Selenium JSON responses, and are sent back to your script. 

Appium hides all this complexity from you and your script. Your test script thinks it's communicating with your test app using the Selenium JSON Wire Protocol, but it's really communicating with an Appium server via the Selenium API. 

Next: [Tips and Tricks for Writing Appium Tests](##03-Tips.md##)

Tips for Writing Better Appium Tests
===

There are a few things to keep in mind while writing tests for Appium. This section of the tutorial covers some common 'gotchas' that you may run into when writing Appium tests.

Accessibility
---

Because Appium uses the UI Automation framework for running tests, it's a good idea to conform to best practices for UI Automation testing. One of these is providing good accessibility information in your UI, since UI Automation uses that information to automate your app. 

Fortunately, this is something that everyone should be doing already, and it's easy to do while writing your apps. UI Automation looks for the UIViews `accesibilityLabel` attribute. For information about setting accessibility information, check out the [UI Automation accessibility documentation](http://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/UsingtheAutomationInstrument/UsingtheAutomationInstrument.html#//apple_ref/doc/uid/TP40004652-CH20-SW86).

Timeouts
---

Appium uses the Instruments default timeout of 5 seconds. Although this timeout can be adjusted, it is not recommended as it can take UI Automation some time to resolve elements in the UI, especially for larger applications. For more information, check out the [UIAutomation timeouts documentation](http://developer.apple.com/library/ios/documentation/ToolsLanguages/Reference/UIATargetClassReference/UIATargetClass/UIATargetClass.html#//apple_ref/javascript/instm/UIATarget/setTimeout).

[Next Steps and More Information](##04-Next-Steps.md##)


Appium Resources
---

For more information, feel free to visit the resources listed below:

  - [Appium](http://appium.io)
  - [Appium Mailing List](https://groups.google.com/forum/#!forum/appium-discuss)
  - [Appium on Github](http://github.com/appium/appium)
  - [Supported Selenium WebDriver JSON Wire Protocol methods on Appium](https://github.com/appium/appium/wiki/JSON-Wire-Protocol:-Supported-Methods). We'll update the list as we add more Selenium support to Appium, so check back from time to time to see what's new.
  - [Selenium WebDriver JSON Wire Protocol](http://code.google.com/p/selenium/wiki/JsonWireProtocol) 
  - [Selenium](http://www.seleniumhq.org)
  - [Apple UI Automation Documentation](http://developer.apple.com/library/ios/#documentation/DeveloperTools/Reference/UIAutomationRef/_index.html)
  - [Android UI Testing Documentation](http://developer.android.com/tools/testing/testing_ui.html)
  - [Sauce Labs Rest API](http://saucelabs.com/docs/rest)
  - [Sauce Connect](https://saucelabs.com/docs/connect)

