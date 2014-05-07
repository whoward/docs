{
  title: "Travis CI",
  description: "Run automated tests on Sauce Labs using Travis CI",
  category: "CI Integrations",
  image: "/images/ci-integrations/travis.png",
  index: 1
}

## Getting Started

In this tutorial we will get you setup to run automated tests on
[Travis CI](https://travis-ci.org/) using real browsers on
[Sauce Labs](https://saucelabs.com).

## Securely use your Sauce Credentials on Travis CI

To test with Sauce on Travis, you need to make your Sauce Labs credentials are available to your tests. Using the [Travis gem](https://rubygems.org/gems/travis), you can encrypt your credentials as environment variables so that they aren't visible in your source code, but instead available as global variables. First install the Travis gem with the following command:

```bash
gem install travis
```
*Note: this assumes you have Ruby installed on your system, and you may have to add `sudo` to the begining dependending on your system permissions.*

If you don't have a `.travis.yml` file in your repo yet, you can run the following command to scaffold one:

```bash
travis init
```
*Note: you can learn more about setting up Travis for your project's programming language [here](http://about.travis-ci.org/docs/user/getting-started/#Getting-started)*

Then run the following command(replacing your GitHub username and repo) to encrypt your username:

```bash
travis encrypt SAUCE_USERNAME=sauceUsername -r GITHUB_USERNAME/GITHUB_REPO --add
```

Then encrypt your access key with this command:
```bash
travis encrypt SAUCE_ACCESS_KEY=sauceAccessKey -r GITHUB_USERNAME/GITHUB_REPO --add
```

*Note: If you aren't using GitHub or want to learn more about secure environment variables, check out the
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

Some test tools will start [Sauce Connect](/docs/reference/sauce-connect) for you, but if the one you are using doesn't, you can [configure Travis to start Sauce Connect](http://docs.travis-ci.com/user/sauce-connect/).

You will need the add the following content to your `.travis.yml` file:

```yaml
addons:
  sauce_connect:
    username:
      secure: "The secure string output by the first `travis encrypt` above"
    access_key:
      secure: "The secure string output by the second `travis encrypt` above"
```

## Provide Additional Details to Sauce

Travis provides a set of environment variables which you can send to Sauce to annotate your tests. Here are some that are particularly useful for annotating Sauce Labs tests: `TRAVIS_BUILD_NUMBER`. You can add tags and a [build number](/reference/jobs/#record-the-build-number) to your test suite using Travis' provided environment variables.