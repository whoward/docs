 {
  title: "Appium",
  description: "How to run Appium tests on Sauce Labs",
  category: 'Tutorials',
  index: 7,
  image: "/images/tutorials/appium.png",
}

Appium is an open source mobile automation tool for iOS and Android. Appium can test native and hybrid applications, and can even be used to automate Mobile Safari on iOS. 

There are several advantages to using Appium on Sauce Labs to test your mobile applications:

  - Appium does not require any modification to the source code of your application before running tests.
  - Since Appium uses the Selenium JSON Wire Protocol, you can write your tests in any language supported by Selenium.
  - Appium uses first-party automation frameworks from Apple and Google, so tests replicate user behavior very accurately.
  - With Appium on Sauce, your tests require no setup or virtualization, and can be scaled instantly.

For an in-depth explanation of how Appium works, and the various technologies used to make Appium work, visit the [Appium Documentation](http://appium.io/documentation.html). 

## Writing Tests With Appium


Getting started with Appium on Sauce Labs is easy:

 - Get a [Sauce Labs account](https://saucelabs.com/signup).
 - Write your tests using the WebDriver client library for the language of your choice.
 - Configure your selenium tests to use Sauce Labs' simulators and emulators in the cloud.

For information on writing, configuring, and running your tests in Sauce Labs' cloud, check out our Getting Started Guides:

  - [iOS Getting Started guide](#appium-for-ios-on-sauce-labs)
  - [Android Getting Started guide](#appium-for-android-on-sauce-labs)

## Appium Client Libraries


Appium works with an extension of WebDriver's client bindings, so you'll need to install the bindings for your favorite language:

* Ruby - [https://github.com/appium/ruby_lib](https://github.com/appium/ruby_lib)
* Java - [https://github.com/appium/java-client](https://github.com/appium/java-client)
* PHP - [https://github.com/appium/php-client](https://github.com/appium/php-client)
* JavaScript - [https://github.com/admc/wd](https://github.com/admc/wd)
* Python - [https://github.com/appium/python-client](https://github.com/appium/python-client)
* C# - [https://github.com/appium/appium-dotnet-driver](https://github.com/appium/appium-dotnet-driver)

## Complete Tutorial


Want to know all the ins and outs of testing your iOS app with Appium on Sauce? Then you're in the right place. These tutorials explain how to automate testing of your apps with Appium.

We assume that you have some familiarity with Selenium WebDriver (the protocol and libraries on which the Appium server and Appium clients are based), your operating system, and the fundamentals of automated testing. However, even if this is your first time automating an app, you should still be able to follow along!

Let's get started! Click the first link below:

  - [Running iOS Tests on Sauce](#appium-for-ios-on-sauce-labs)
  - [Running Android Tests on Sauce](#appium-for-android-on-sauce-labs)

## Appium Resources


For more information, feel free to visit the resources listed below:

  - [Appium](http://appium.io)
  - [Introduction to Appium Concepts](http://appium.io/introduction.html)
  - [Appium Docs](http://appium.io/slate/en/v1.0.0/)
  - [Appium Discussion Group](https://discuss.appium.io)
  - [Appium on Github](http://github.com/appium/appium)
  - [Selenium WebDriver JSON Wire Protocol](http://code.google.com/p/selenium/wiki/JsonWireProtocol) 
  - [Selenium](http://www.seleniumhq.org)
  - [Apple UI Automation Documentation](http://developer.apple.com/library/ios/#documentation/DeveloperTools/Reference/UIAutomationRef/_index.html)
  - [Android UI Testing Documentation](http://developer.android.com/tools/testing/testing_ui.html)
  - [Sauce Labs Rest API](http://saucelabs.com/docs/rest)
  - [Sauce Connect](https://saucelabs.com/docs/connect)

## Appium for iOS on Sauce Labs


### Running Your First iOS Test

Your first test will run against our Test Application, which is hosted on Sauce. When you run your own tests you'll need to provide your own app. Instructions for providing your own app are provided in the Setup section.

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

### Viewing iOS Tests on Sauce

Congratulations! You've run your first Appium test on Sauce! To see the output from your test, head over to your [Sauce account page](https://saucelabs.com/account) where you can see the important data from your test run, plus video, screenshots, and logs.

You may notice that Appium tests take a bit longer than regular selenium tests. This is due to the additional startup time required by the iOS simulator and Instruments - something we are looking to alleviate in the near future.

### System Requirements

You'll need the following to start testing your app using Appium on Sauce:

  - A computer running Mac OS 10.8.5 or higher
  - Xcode and the iOS 7 SDK or higher
  - The Xcode command line tools installed
  - Your app source code or a prebuilt .app bundle for your app
  - A [Sauce Labs account](https://saucelabs.com/account)
  - An Appium client library for your langauge of choice (see the Tutorial Introduction)

If you do not have a Mac, you will not be able to compile your app for Appium. However, you only need a Mac for compiling the app, so if you already have a compiled '.app' folder, then you can run your tests from Windows, Mac, or Linux.

### Compiling to Specify a Simulator

Your app can optionally be compiled to support only the simulator you want your test app to run in. If your app is compiled as a Universal app (typically the default) it will launch in an iPad simulator. To launch your app in an iPhone simulator compile the app for iPhone.

To compile the app, use the `xcodebuild` command line utility with the TARGETED_DEVICE_FAMILY parameter. The value for this parameter may be `1`, `2`, or `1,2`.

  - `1` designates iPhone only.
  - `2` designates iPad only.
  - `1,2` designates a Universal app, which Instruments will *always open in an iPad simulator*.

So, for example, to build an app to launch in the iPhone simulator, change to the command prompt for the top level directory of your app's source code (before it's been built), then run the following command:

```bash
xcodebuild -sdk iphonesimulator7.0 TARGETED_DEVICE_FAMILY=1
```

After your app compiles you'll have a `build/<*target*>` directory in your app's source code that contains a `.app` directory. Change to the command prompt for your `build/<*target*>` directory, zip the entire `.app` directory, and use it as the value for the `app` capability in your DesiredCapabilities object as discussed in [Running Your First Test](#running-your-first-ios-test).

### Setting Up Your iOS Tests for Sauce


First, zip and upload your app to Sauce Labs temporary storage using our [Temporary Storage REST API](https://docs.saucelabs.com/reference/rest-api/#temporary-storage):

```bash
zip -r my_app.zip MyApp.app/
```

```bash
curl -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY -X POST "http://saucelabs.com/rest/v1/storage/$SAUCE_USERNAME/my_app.zip?overwrite=true" -H "Content-Type: application/octet-stream" --data-binary @my_app.zip
```

Then, in your test code, point your RemoteWebDriver instance to `http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub`.

You'll also need to add the following fields (key/value pairs) to DesiredCapabilities:

  - `appium-version`: `"1.0"`
  - `app`: `"sauce-storage:my_app.zip"` The URL of a zip file containing your `.app` directory. (Usually a `sauce-storage:` URL to access files uploaded to Sauce temporary storage.)
  - `platformName`: `"iOS"`
  - `platformVersion`: `"7.1"`
  - `deviceName`: `"iPhone Simulator"`

**Note:** The `iPhone Simulator` value for the `deviceName` key will launch an iPhone or iPad simulator depending on whether your app was compiled with `1`, `2`, or `1,2` as the `TARGETED_DEVICE_FAMILY`  as discussed in the [Compiling Your App for Sauce](#compiling-to-specify-a-simulator) section.

That's it! You're ready to run your test script against your app on Appium on Sauce!

For a complete list of supported Appium platforms and code examples for using them, see our [Appium platforms documentation](https://saucelabs.com/docs/platforms/appium).

### Understanding Appium for iOS on Sauce


Here's how running tests on Appium on Sauce work.

When you run your test script its configuration points it to Appium on the Sauce cloud. Appium's implementation of the [Selenium WebDriver JSON Wire Protocol API](http://code.google.com/p/selenium/wiki/JsonWireProtocol) receives your script's commands, launches your test app in the simulator that you specified, converts your Selenium commands into UI Automation JavaScript commands, and sends the JavaScript commands to your iOS app via Apple Instruments. Instruments executes the commands against your test app in the simulator.

The responses from your test app are received by Instruments, sent to UI Automation, and relayed to Appium. Appium's implementation of the Selenium API converts the UI Automation JavaScript responses into the Selenium WebDriver JSON Wire Protocol, and sends the JSON responses back to your test script.

Appium hides all of this complexity from your test script (and from you!). Your test script thinks it's communicating with your test app, but it's really communicating with Appium's implementation of the Selenium API. The fact that Appium is running your app in the appropriate simulator and wrapping UI Automation, Instruments, and all of the communications with your test app including the conversions to and from JavaScript and JSON remains completely hidden, and your test script is none the wiser.

Before you can run your apps on Appium you'll need the .app directory created by XCode for your app. Sometimes, using special build settings will make Appium more useful; these are covered in the [Compiling Your App for Sauce](#compiling-to-specify-a-simulator) section.

To specify an Appium session in Sauce, simply use the same DesiredCapabilities object that you already use in your Selenium tests, with a slightly different set of fields. Configuring DesiredCapabilities is discussed in the [Setting Up Your Tests](#setting-up-your-ios-tests-for-sauce) section.

## Appium for Android on Sauce Labs 



Now let's get started with Android tests on Sauce Labs.

### Running Your First Android Test


Your first test will run against our Test Application, which is hosted on Sauce. When you run your own tests you'll need to provide your own app. Instructions for providing your own app are provided in the [Setup](#setting-up-your-android-tests-for-sauce) section.

To run your first test, simply run the quick start code below. Our example is in Ruby using RSpec, though Appium can be used with almost any language and test framework:

To run the Ruby example, you need to have rspec installed, in addition to the Selenium WebDriver client library. Run the simple install script by copying and pasting:

```bash
curl -L https://raw.github.com/saucelabs/appium-tutorial/master/bin/install_tutorial_android_ruby.sh | bash -s
```

This will create a file `AndroidSauce.rb` in the current directory, and make sure your environment is correct.

Now you are ready to run your first test! The `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables authenticate you with Sauce Labs:

```bash
SAUCE_USERNAME=sauceUsername SAUCE_ACCESS_KEY=sauceAccessKey rspec AndroidSauce.rb
```

Take a look at AndroidSauce.rb to see what an Appium test looks like in Ruby.

### Viewing Android Tests on Sauce


Congratulations! You've run your first Appium test on Sauce! To see the output from your test, head over to your [Sauce account page](https://saucelabs.com/account) where you can see the important data from your test run, plus video, screenshots, and logs.

You may notice that Appium tests take a bit longer than regular selenium tests. This is due to the additional startup time required by the Android Emulator- something we are looking to alleviate in the near future.

### System Requirements for Android


  - Your app compiled as an .apk.
  - A [Sauce Labs account](https://saucelabs.com/account)
  - A Selenium WebDriver [client library](#appium-client-libraries) for your language of choice

### Setting Up Your Android Tests for Sauce


First, upload your app to Sauce Labs temporary storage using our [Temporary Storage REST API](http://saucelabs.com/docs/rest#storage):

```bash
curl -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY -X POST "http://saucelabs.com/rest/v1/storage/$SAUCE_USERNAME/my_app.apk?overwrite=true" -H "Content-Type: application/octet-stream" --data-binary @my_app.apk
```

Then, in your test code, point your RemoteWebDriver instance to `http://sauceUsername:sauceAccessKey@ondemand.saucelabs.com:80/wd/hub`.

You'll also need to add the following fields (key/value pairs) to DesiredCapabilities:

  - `appium-version`: `"1.0"`
  - `app`: `"sauce-storage:my_app.apk"` The URL of your `.apk` directory. (Usually a `sauce-storage:` URL to access files uploaded to Sauce temporary storage.)
  - `platformName`: `"Android"`
  - `platformVersion`: `"4.2"` (Currently we support 2.3, 4.0, 4.1, 4.2 and 4.3. See below for using 2.3, 4.0, and 4.1)
  - `deviceName`: `"Android Emulator"`
  - `deviceType`: `"phone"` (the default if this field is missing), or `"tablet"`

If you want to test against earlier versions like 2.3, 4.0, or 4.1, you'll need to access Appium's built-in [Selendroid](http://selendroid.io) support by using the `automationName` capability:

  - `appium-version`: `"1.1"`
  - `app`: `"sauce-storage:my_app.apk"`
  - `automationName`: `"Selendroid"`
  - `platformName`: `"Android"`
  - `platformVersion`: `"2.3"` (or 4.0, 4.1)
  - `deviceName`: `"Android Emulator"`
  - `deviceType`: `"phone"` (the default if this field is missing), or `"tablet"`

That's it! You're ready to run your test script against your app on Appium on Sauce!

For a complete list of supported Appium platforms and code examples for using them, see our [Appium platforms documentation](https://saucelabs.com/docs/platforms/appium).

### Understanding Appium for Android on Sauce


Appium automates Android using the UIAutomator library, which is provided by Google as part of the Android SDK, for the purpose of automating your app. When you change the configuration of your tests to point to the Sauce Labs cloud, we handle the command, launch the emulator, download your app, and install and launch the app in the emulator. Then as each command is sent by your script, it is converted to a UIAutomator command, and run against your app in the emulator.

The responses from your app (whether a success or an error) are converted from UIAutomator responses back into Selenium JSON responses, and are sent back to your script.

Appium hides all this complexity from you and your script. Your test script thinks it's communicating with your test app using the Selenium JSON Wire Protocol, but it's really communicating with an Appium server via the Selenium API.

## Tips for Writing Better Appium Tests


There are a few things to keep in mind while writing tests for Appium. This section of the tutorial covers some common 'gotchas' that you may run into when writing Appium tests.

## Accessibility

Because Appium uses the UI Automation framework for running tests, it's a good idea to conform to best practices for UI Automation testing. One of these is providing good accessibility information in your UI, since UI Automation uses that information to automate your app. 

Fortunately, this is something that everyone should be doing already, and it's easy to do while writing your apps. UI Automation looks for the UIViews `accesibilityLabel` attribute. For information about setting accessibility information, check out the [UI Automation accessibility documentation](http://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/UsingtheAutomationInstrument/UsingtheAutomationInstrument.html#//apple_ref/doc/uid/TP40004652-CH20-SW86).


## Next Steps and More Information

Congratulations! You're all set up to test your iOS apps using Appium on the Sauce Labs cloud of Selenium servers. 

There are many resources available to help you write the best tests possible, some of which are listed below.

## Documentation


  - [Appium](http://appium.io)
  - [Introduction to Appium Concepts](http://appium.io/introduction.html)
  - [Appium Docs](http://appium.io/slate/en/v1.0.0/)
  - [Appium Discussion Group](https://discuss.appium.io)
  - [Appium on Github](http://github.com/appium/appium)
  - [Selenium WebDriver JSON Wire Protocol](http://code.google.com/p/selenium/wiki/JsonWireProtocol) 
  - [Selenium](http://www.seleniumhq.org)
  - [Apple UI Automation Documentation](http://developer.apple.com/library/ios/#documentation/DeveloperTools/Reference/UIAutomationRef/_index.html)
  - [Android UI Testing Documentation](http://developer.android.com/tools/testing/testing_ui.html)
