{
  title: "JS Unit Testing",
  description: "How to run JavaScript unit tests on Sauce Labs",
  category: "Tutorials",
  index: 5,
  image: "/images/tutorials/javascript.png"
}

![karma-plus-sauce](/images/tutorials/js-unit-testing/karma-plus-sauce.png)

In this tutorial we will use the [Karma](http://karma-runner.github.io/) test runner to run JavaScript unit tests on Sauce Labs. To get testing, you can either check out the docs for the [karma-sauce-launcher](https://github.com/karma-runner/karma-sauce-launcher) to add Sauce Labs testing to an existing Karma config, or follow along to setup a new project.

## Installation

Start by cloning the [karma-sauce-example](https://github.com/saucelabs/karma-sauce-example.git) repo by running the following command in your terminal:

```bash
git clone https://github.com/saucelabs/karma-sauce-example.git && cd karma-sauce-example
```

Then run the following command to install the Karma command line interface globally and the sample repo's local node.js dependencies:

```bash
npm install -g karma-cli && npm install 
```

*Note: make sure you have [node.js](http://nodejs.org/) installed before running the above command.* 

## Running Karma locally

You then can run Karma locally to see how it works with the following command:
```bash
karma start
```

Try saving a source file in the `src` folder or a test file in the `test` folder to see Karma automatically re-run the tests on the latest source code.

By default, this example runs [jasmine](http://jasmine.github.io/2.0/introduction.html) tests in Chrome and Firefox on your local machine, and you can add more browsers that you have installed in the `karma.conf.js`'s `browsers` array or change the test framework in the `frameworks` array.

## Running Karma with the [karma-sauce-launcher](https://github.com/karma-runner/karma-sauce-launcher) plugin

To use Karma with Sauce locally, export your Sauce Labs username and access key to your shell by running the following commands in your current terminal session, or adding them to your `~/.bash_profile` so that they will always be available:

```bash
export SAUCE_USERNAME=sauceUsername
export SAUCE_ACCESS_KEY=sauceAccessKey
```

You'll then need to re-load that profile with `source ~/.bash_profile`

On Windows, open your environment variables settings window (instructions [here](http://www.itechtalk.com/thread3595.html)) and set the following variables:

```yaml
    Name: SAUCE_USERNAME
    Value: sauceUsername

    Name: SAUCE_ACCESS_KEY
    Value: sauceAccessKey
```

*Note: if you don't have an account, you can sign up [here](https://saucelabs.com/signup/plan/free) for free.*

You can now run the unit tests on Sauce with the following command:
```bash
karma start karma.conf-ci.js
```

Note that this will by default start [Sauce Connect](/reference/sauce-connect/) to establish a secure tunnel between your local machine and Sauce's cloud. To speed up the time it takes to connect to Sauce's cloud, you can start Sauce Connect in the background by using one of the [binaries](/reference/sauce-connect/#getting-started) or the [Mac app](https://saucelabs.com/mac) and then setting the `startConnect` option to `false` in the `karma.conf-ci.js` file (make sure to change it back to `true` before running on CI).

## Using the karma-sauce-launcher in CI

It is cool to run your unit tests on Sauce locally while you develop, but even cooler to run them on a continuous integration system with every commit to your codebase. To integrate your CI with Sauce check out the instructions for [Travis](/ci-integrations/travis-ci/), [Jenkins](/ci-integrations/jenkins/), or [Bamboo](/ci-integrations/bamboo/).

The provided `karma.conf-ci.js` file already is set up to read environment variables on CI so you shouldn't need to modify it as long as the `process.env.SAUCE_USERNAME` and `process.env.SAUCE_ACCESS_KEY` are set properly during the build.

### Example CI integration

The `karma-sauce-example` repo demonstrates using Sauce with Travis CI. Here is a status badge which shows the build status of this repo's master branch and links to the latest build:
[![Build Status](https://travis-ci.org/saucelabs/karma-sauce-example.png?branch=master)](https://travis-ci.org/saucelabs/karma-sauce-example)

*Note: the build is failing on purpose as a demo of Sauce catching bugs in different browsers.*

## Conclusion

You can take the Karma config files resulting from this tutorial and add them to your project, you will simply need to modify the `src` and `test` config properties to point at your files. If you are using grunt or gulp you can also use the the [grunt plugin](https://github.com/karma-runner/grunt-karma) or [gulp integration](https://github.com/karma-runner/gulp-karma). Happy testing!