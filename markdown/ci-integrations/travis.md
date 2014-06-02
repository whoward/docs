{
  title: "Travis CI",
  description: "Run automated tests on Sauce Labs using Travis CI",
  category: "CI Integrations",
  index: 1,
  image: "/images/ci-integrations/travis.png",
}

## Getting Started

In this tutorial we will get you setup to run automated tests on
[Travis CI](https://travis-ci.org/) using browsers and platforms available on
[Sauce Labs](https://saucelabs.com).

## Securely use your Sauce Credentials on Travis CI

To test with Sauce on Travis, you need to make sure your Sauce credentials are available to your tests. To do so, use the [Travis gem](https://rubygems.org/gems/travis) to encrypt your credentials as environment variables so that they aren't visible in your source code, but instead available as global variables. 

### Setting up Travis

First install the Travis gem with the following command:

```bash
gem install travis
```
*This assumes you have Ruby installed on your system, and you may have to add `sudo` to the beginning of the command dependending on your system permissions.*

If you don't have a `.travis.yml` file in your repo yet, you can run the following command to scaffold one:

```bash
travis init
```
*You can learn more about setting up Travis for your project's programming language [here](http://about.travis-ci.org/docs/user/getting-started/#Getting-started).*

### Adding secure credentials

Then run the following command to encrypt your username:

```bash
travis encrypt SAUCE_USERNAME=sauceUsername --add
```

Then encrypt your access key with this command:

```bash
travis encrypt SAUCE_ACCESS_KEY=sauceAccessKey --add
```

*If you want to learn more about secure environment variables, check out the
[Travis
CI documentation](http://about.travis-ci.org/docs/user/build-configuration/#Secure-environment-variables).*

These commands will add the following content to your  `.travis.yml` file:

```yaml
env:
  global:
    - secure: "Secure username token goes here!"
    - secure: "Secure access key token goes here!"
```

Now you can access the `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables in your tests to authenticate with Sauce.

## Configure Travis CI to Start Sauce Connect

Some test tools will start [Sauce Connect](/reference/sauce-connect) for you, but if the one you are using doesn't, you can [configure Travis to start Sauce Connect](http://docs.travis-ci.com/user/sauce-connect/).

To do so, you will need to add the following content to your `.travis.yml` file:

```yaml
addons:
  sauce_connect: true
```
*This assumes that the `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables are set on Travis using the [instructions above](#adding-secure-credentials).*

## Provide Additional Details to Sauce

Travis provides a set of environment variables which you can send to Sauce in order to annotate your tests. You can add [tags](/reference/test-configuration/#tag-your-jobs) using the `TRAVIS_PULL_REQUEST` string and `TRAVIS_BRANCH` string and a [build number](/reference/test-configuration/#record-the-build-number) using `TRAVIS_BUILD_NUMBER`.