{
  title: "Travis CI",
  description: "Run automated tests on Sauce Labs using Travis CI",
  category: "CI Integrations",
  index: 1,
  image: "/images/ci-integrations/travis-ci.png",
  libraries: ["vendor/jsencrypt/bin/jsencrypt.min.js"],
}

## Getting Started

In this tutorial we will get you setup to run automated tests on
[Travis CI](https://travis-ci.org/) using browsers and platforms available on
[Sauce Labs](https://saucelabs.com).

## Securely use your Sauce Credentials on Travis CI

To test with Sauce on Travis, you need to make sure your Sauce credentials are available to your tests.

<span class="show-when-un-authenticated">To do so, <b>login to your account</b> to allow us to encrypt your credentials as environment variables so that they aren't visible in your source code, but instead available as global variables.</span>
<span class="show-when-authenticated">To do so, we will encrypt your credentials as environment variables so that they aren't visible in your source code, but instead available as global variables.</span>

Create a `.travis.yml` file in your repo if you don't have one yet.

*You can learn more about setting up Travis for your project's programming language [here](http://about.travis-ci.org/docs/user/getting-started/#Getting-started).*

### Adding secure credentials

<span class="show-when-un-authenticated"><b>Login to your account to allow us to automatically encrypt your username and access key for your `.travis.yml` file.</b></span>
<span class="show-when-authenticated">Enter your GitHub Repository to allow us to automatically encrypt your username and access key:</span>
<div class="show-when-authenticated">
  <div class="control-group">
    <div class="controls">
      <input class="span4" id="repo" pattern="[^\/\s]+\/[^\/\s]+" placeholder="owner/name" required="required" type="text">
      <button class="btn" id="encrypt">Submit</button>
    </div>
  </div>
  <div class="span6" id="output"></div>
  <span><em>Note: This encryption only works for public GitHub Repositories.</em></span>
</div>

<span class="show-after-encryption" style="display:none" >For the project:  <span id="project"></span>, add your secure username token and secure access key token to your `.travis.yml` file with the following:</span>

<pre><code class="lang-yaml"><div class="button-container"><button data-clipboard-target="travis-auto-encryption" class="btn btn-default clipboard"><span class="fa fa-clipboard"></span></button><div id="code-4" class="hidden">env:
  global:
    - secure: "Secure username token goes here!"
    - secure: "Secure access key token goes here!"</div></div><div class="highlight" id="travis-auto-encryption"><pre><span class="l-Scalar-Plain">env</span><span class="p-Indicator">:</span>
  <span class="l-Scalar-Plain">global</span><span class="p-Indicator">:</span>
    <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">secure</span><span class="p-Indicator">:</span> <span class="s" id="encryptUsername">"Secure username token goes here!"</span>
    <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">secure</span><span class="p-Indicator">:</span> <span class="s" id="encryptAccessKey">"Secure access key token goes here!"</span>
</pre></div>
</code></pre>

<span class="show-after-encryption" style="display:none" >Now you can access the `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables in your tests to authenticate with Sauce.</span>


*If you want to learn more about secure environment variables, check out the
[Travis
CI documentation](http://about.travis-ci.org/docs/user/build-configuration/#Secure-environment-variables).*

## Configure Travis CI to Start Sauce Connect

Some test tools will start [Sauce Connect](/reference/sauce-connect) for you, but if the one you are using doesn't, you can [configure Travis to start Sauce Connect](http://docs.travis-ci.com/user/sauce-connect/).

To do so, you will need to add the following content to your `.travis.yml` file:

```yaml
addons:
  sauce_connect: true
```
*This assumes that the `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables are set on Travis using the [instructions above](#adding-secure-credentials).*

## Provide Additional Details to Sauce

Travis provides a set of environment variables which you can send to Sauce in order to annotate your tests. You can add [tags](/reference/test-configuration/#tagging) using the `TRAVIS_PULL_REQUEST` string and `TRAVIS_BRANCH` string and a [build number](/reference/test-configuration/#recording-build-numbers) using `TRAVIS_BUILD_NUMBER`.