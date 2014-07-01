 {
  title: "Node.js",
  description: "How to run Selenium tests on Sauce Labs using node.js",
  category: 'Tutorials',
  index: 4,
  image: '/images/tutorials/nodejs.png'
}

## Getting Started

In this tutorial we will run node.js Selenium tests using [grunt-mocha-webdriver](https://github.com/jmreidy/grunt-mocha-webdriver). We will begin by testing a website with the available browsers on your system and then thoroughly test a website in CI across many browsers and operating systems using Sauce Labs.

## Tools Used in the Tutorial

This tutorial uses [Grunt](http://gruntjs.com/) to automate running tasks, [Mocha](http://visionmedia.github.io/mocha/) to run tests and [WD.js]([WD.js](https://github.com/admc/wd) to run Selenium commands. However it is also possible to run tests on Sauce using [protractor](https://github.com/saucelabs/node-tutorials/tree/master/protractor-demo) or [mocha without Grunt](https://github.com/saucelabs/node-tutorials/tree/master/mocha-wd-parallel).

## Installing the Sample Repo

First clone the sample repo and enter the repo's directory with the following command:

```bash
git clone https://github.com/saucelabs/sauce-node-example &&
cd sauce-node-example
```

Then, make sure you have Grunt installed globally on your system:

```bash
npm install -g grunt-cli
```
*Note: make sure you have [node.js](http://nodejs.org/) installed before running the above command.*

Now run the following command to get all of the repo's local dependencies:

```bash
npm install
```

## Running Node.js Selenium Tests Locally

To run tests locally on Google Chrome, enter the following command in your terminal:

```bash
grunt test
```

This command will run a few Selenium tests on google.com. Note that the `grunt-mocha-webdriver` plugin will automatically download the Selenium standalone server for you as well as [Chrome Driver](https://code.google.com/p/chromedriver/) so that you can get testing immediately!

### Running Tests Locally on a Specific Browser

To run a specific browser, you can run the following command:

```bash
grunt test --browser=BROWSER_NAME
```

`BROWSER_NAME` may be either `firefox`, `safari`, `chrome`, or `internet explorer` depending on what browsers you have on your system.


### Running Tests Locally on All Available Browsers

To run the Selenium tests on all available browsers concurrently, run the tests with the `--all` flag set like this:

```bash
grunt test --all
```

## Running Node.js Selenium Tests on Sauce Labs

If you don't already have `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` available as environment variables in your terminal, run one of the below commands to setup your environment.

### Export Credentials on <i class="fa fa-apple"></i> Mac or <i class="fa fa-linux"></i> Linux


```bash
echo "export SAUCE_USERNAME=sauceUsername
export SAUCE_ACCESS_KEY=sauceAccessKey" >> ~/.bash_profile &&
source ~/.bash_profile
```

### Export Credentials on <i class="fa fa-windows"></i> Windows

```bat
setx SAUCE_USERNAME "sauceUsername"
setx SAUCE_ACCESS_KEY "sauceAccessKey"
```
*Note: for Windows you will need to restart your command prompt after running this command.*


While testing local browsers concurrently is helpful for testing a new website feature, you will want to run tests on many browsers and platforms before shipping code to your customers. To run the same tests on many browsers/platforms on Sauce Labs, run the following command:

```bash
grunt test --ci
```

This command will run the `ci` config target of the `mochaWebdriver` plugin which looks like this:
```js
ci: {
  src: ['tests/*.js'],
  options: {
    secureCommands: true,
    concurrency: 2,
    // Update with the name of your test suite
    testName: 'Sauce Labs Node.js Example Tests',
    // Update tags
    testTags: ['WD', 'Selenium 2'],
    // Check out https://saucelabs.com/platforms
    // to see what the available browser/platform combos are
    browsers: [{
      browserName: 'chrome',
      platform: 'OS X 10.8',
      version: '34'
    }, {
      browserName: 'safari',
      platform: 'OS X 10.9',
      version: '7'
    },{
      browserName: 'firefox',
      platform: 'OS X 10.9',
      version: '30'
    }, {
      browserName: "internet explorer",
      version: "11",
      platform: "Windows 8.1"
    }]
  }
}
```

This will create a secure tunnel to Sauce Labs using [Sauce Connect](/reference/sauce-connect/) and then run your tests on a number of different browsers and operating systems. Note that each browser in the ci target `browsers` array has a platform and version specified and you can find the full list of available configurations on the [platforms page](https://saucelabs.com/platforms).

### Running Node.js Selenium Tests on Sauce Labs in CI

Running tests on Sauce Labs locally is helpful in case you want to run a specific test on a browser you don't have on your system, but where it really shines is discovering regressions in CI. To setup Sauce in CI you can follow one of our tutorials for [Travis](/ci-integrations/travis-ci/), [Jenkins](/ci-integrations/jenkins/), [Team City](/ci-integrations/teamcity/), or [Bamboo](/ci-integrations/bamboo/).

## Conclusion

To incorporate Selenium testing with `grunt-mocha-webdriver` into your project, you can take the `Gruntfile.js` and `tests/test_ui.js` files from the sample repo. If you don't already have a `package.json` you can copy the existing one or else run the below command to get the necessary dependencies from the example:

```bash
npm install grunt-mocha-webdriver mocha mocha-as-promised chai chai-as-promised wd --save-dev
```

If you want to use a different async control flow than the one demonstrated in the tests, check out the [WD Usage Guide](https://github.com/admc/wd#usage). 