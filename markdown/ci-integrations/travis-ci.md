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
[Sauce Labs](https://saucelabs.com). First, create a `.travis.yml` file in your repo if you don't have one yet.

*You can learn more about setting up Travis for your project's programming language [here](http://about.travis-ci.org/docs/user/getting-started/#Getting-started).*

## Securely use your Sauce Labs credentials on Travis CI

To test with Sauce Labs on Travis, you need to make your credentials available to your tests.<span class="show-inline-when-un-authenticated">To do so, <a href="#" id="travis-login-redirect">login to your account</a> to allow us to [encrypt your credentials](#adding-credentials-for-a-public-github-repo) as environment variables so that they aren't visible in your source code, but instead available as global variables.</span><span class="show-inline-when-authenticated">The best way to achieve this is to encrypt your credentials as environment variables so that they aren't visible in your source code, but instead available as global variables.</span>

*If you want to learn more about secure environment variables, check out the
[Travis
CI documentation](http://docs.travis-ci.com/user/environment-variables/#Secure-Variables).*

### Adding credentials for a public GitHub repo

<span>For public GitHub repos we can automatically encrypt your credentials (for private GitHub repositories follow [the steps below](#adding-credentials-for-a-private-github-repo)).</span><span class="show-inline-when-un-authenticated"><b><a href="#" id="travis-login-redirect-adding-credentials"> Login to your account</a> to allow us to automatically encrypt your username and access key for your `.travis.yml` file.</b></span><span class="show-inline-when-authenticated"> To automatically encrypt the `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables, enter your public GitHub repo below (case sensitive in the `username/repo` format ie. <a href="https://github.com/saucelabs/docs" target="_blank">saucelabs/docs</a>) :</span>
<div class="show-when-authenticated">
<div class="container-fluid">
  <div class="row">
  <div class="input-group col-md-6">
      <input class="form-control" id="repo" placeholder="username/repo" required="required" type="text">
      <span class="input-group-btn">
      <button class="btn btn-default" type="button" id="encrypt">Submit</button>
      </span>
  </div>
  <div id="output"></div>
  </div>
</div>
</div>

<pre><code class="lang-yaml"><div class="button-container"><button data-clipboard-target="travis-auto-encryption" class="btn btn-default clipboard"><span class="fa fa-clipboard"></span></button><div id="code-4" class="hidden">env:
  global:
    - secure: "Secure username token goes here!"
    - secure: "Secure access key token goes here!"</div></div><div class="highlight" id="travis-auto-encryption"><pre><span class="l-Scalar-Plain">env</span><span class="p-Indicator">:</span>
  <span class="l-Scalar-Plain">global</span><span class="p-Indicator">:</span>
    <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">secure</span><span class="p-Indicator">:</span> <span class="s" id="usernameContainer">"Secure username token goes here!"</span>
    <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">secure</span><span class="p-Indicator">:</span> <span class="s" id="accessKeyContainer">"Secure access key token goes here!"</span>
</pre></div>
</code></pre>

<span class="show-after-encryption" style="display:none" >Now you can skip to our section on [configuring Travis CI to start Sauce Connect](#configure-travis-ci-to-start-sauce-connect).</span>

### Adding credentials for a private GitHub repo

First install the Travis gem with the following command:

```bash
gem install travis
```
*This assumes you have Ruby installed on your system, and you may have to add `sudo` to the beginning of the command dependending on your system permissions.*

If you don't have a `.travis.yml` file in your repo yet, you can run the following command to scaffold one:

```bash
travis init
```

Then run the following command to encrypt your username:

```bash
travis encrypt SAUCE_USERNAME=sauceUsername --add
```

Then encrypt your access key with this command:

```bash
travis encrypt SAUCE_ACCESS_KEY=sauceAccessKey --add
```

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
*This assumes that the `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` environment variables are set on Travis using the [instructions above](#adding-credentials-for-a-public-github-repo).*

**NOTE**: This tunnel will be a [named (identified) tunnel](/reference/sauce-connect/#using-tunnel-identifiers). The tunnel's identifier is set to the `TRAVIS_JOB_NUMBER` environment variable when launched. **You must set your test's desired capability `tunnelIdentifier` to the Travis job number to use the tunnel.** Otherwise, the test will run without using Sauce Connect.

## Provide Additional Details to Sauce

Travis provides a set of environment variables which you can send to Sauce in order to annotate your tests. You can add [tags](/reference/test-configuration/#tagging) using the `TRAVIS_PULL_REQUEST` string and `TRAVIS_BRANCH` string and a [build number](/reference/test-configuration/#recording-build-numbers) using `TRAVIS_BUILD_NUMBER`.
