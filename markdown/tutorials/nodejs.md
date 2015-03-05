 {
  title: "Node.js",
  description: "How to run Selenium tests on Sauce Labs using node.js",
  category: 'Tutorials',
  index: 4,
  image: '/images/tutorials/nodejs.png'
}

## Getting Started

In this tutorial of how to run tests on Sauce Labs using node.js we will use the following tools:
[Grunt](http://gruntjs.com/),
[Mocha](http://mochajs.org/) and
[WD.js](https://github.com/admc/wd).

However it is also possible to use Sauce with other libraries or
frameworks, for instance
[WebDriverJS](https://code.google.com/p/selenium/wiki/WebDriverJs)
and [Jasmine](https://github.com/pivotal/jasmine).
More tutorials are available [here](https://github.com/saucelabs/node-tutorial).

## Setting up a project

First open a new terminal and configure your Sauce Labs credentials:

```bash
export SAUCE_USERNAME=sauceUsername
export SAUCE_ACCESS_KEY=sauceAccessKey
```

Install the necessary tools:

```bash
npm install -g mocha grunt grunt-init grunt-cli
git clone https://github.com/saucelabs/grunt-init-sauce.git ~/.grunt-init/sauce
```

Generate the project and install the package dependencies:

```bash
mkdir tutorial && cd tutorial
grunt-init sauce # press enter when asked
npm install
```

### Inspect the code

- The browsers are configured in `desireds.js`
- The mocha test suite is in `test/sauce/tutorial-specs.js`
- The grunt configuration is in `Gruntfile.js`

Below are the lines where the browser and driver are configured
(in `test/sauce/tutorial-specs.js`):

```js
browser = wd.promiseChainRemote("ondemand.saucelabs.com", 80, username, accessKey);
browser
  .init(desired)
```

### Running the test suite


In the terminal run the following:

```
grunt
```

The output should look like below:

```
Running "env:chrome" (env) task

Running "simplemocha:sauce" (simplemocha) task

  tutorial (chrome)
    should get home page (2868ms)
    should go to the doc page (0) (3054ms)
    should return to the home page(0) (2568ms)
    should go to the doc page (1) (2496ms)
    should return to the home page(1) (2506ms)

  5 passing (20s)

Done, without errors.
```

To follow the test execution on the Sauce interface click [here](https://saucelabs.com/tests).

You may specify the browser as in the following:

```
grunt test:sauce:chrome
grunt test:sauce:firefox
grunt test:sauce:explorer
```

## Running tests in parallel

In the terminal run the following:

```bash
grunt test:sauce:parallel
```

The output should look like below (skipping some blank lines):

```
Running "concurrent:test-sauce" (concurrent) task

  Running "env:chrome" (env) task
  Running "simplemocha:sauce" (simplemocha) task
    tutorial (chrome)
      should get home page (2856ms)
      should go to the doc page (0) (4092ms)
      should return to the home page(0) (2536ms)
      should go to the doc page (1) (2399ms)
      should return to the home page(1) (2568ms)
    5 passing (20s)
  Done, without errors.

  Running "env:firefox" (env) task
  Running "simplemocha:sauce" (simplemocha) task
    tutorial (firefox)
      should get home page (2821ms)
      should go to the doc page (0) (2768ms)
      should return to the home page(0) (2696ms)
      should go to the doc page (1) (2498ms)
      should return to the home page(1) (2467ms)
    5 passing (26s)
  Done, without errors.

  Running "env:explorer" (env) task
  Running "simplemocha:sauce" (simplemocha) task
    tutorial (internet explorer)
      should get home page (2930ms)
      should go to the doc page (0) (3120ms)
      should return to the home page(0) (3381ms)
      should go to the doc page (1) (3051ms)
      should return to the home page(1) (3949ms)
    5 passing (28s)
  Done, without errors.

Done, without errors.
```

To follow the test execution on the Sauce interface click [here](https://saucelabs.com/tests).
