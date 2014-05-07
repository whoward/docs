{
  title: "Status Images",
  description: "Show off your test status with Sauce Labs status images",
  category: "Reference",
  index: 4
}

If you want to keep track of your project's latest test run on Sauce, you can add one of our status images to your repo or website. Here is what the general status badges look like:

![sauce-badge-passing](https://saucelabs.com/images/status-passing.png) ![sauce-badge-fail](https://saucelabs.com/images/status-failed.png) ![sauce-badge-unknown](https://saucelabs.com/images/status-unknown.png)

We also have a browser matrix widget that helps you keep track of what platforms your tests were run on and the status of your tests on each of them:

![sauce-labs-browser-matrix](https://saucelabs.com/images/status-browser-matrix.svg)

## Reporting test results for badge status

1. Choose a Sauce account which corresponds to your project.
If you just have one project, you can use your main Sauce account name.
If you have multiple projects, you will want to [create a sub-account](/reference/user-management) for each project.

2. Run your tests for a given project on Sauce using that account's username and access key (found on the [account page](https://saucelabs.com/account)).

3. Make sure to set a [build](/reference/test-configuration/#record-the-build-number) and [pass/fail](/reference/test-configuration/#record-pass-fail-status) status for every test that runs.
*Note: If your tests don't have a build or pass/fail status, or if even one in a build is not public, you'll get the "Unknown" image for security reasons.*

## Adding the Standard Badge

Paste the following Markdown or HTML in your GitHub README or on your project site:

```bash
![Sauce Test Status](https://saucelabs.com/buildstatus/sauceUsername)](https://saucelabs.com/u/sauceUsername)
```

```html
<img src="https://saucelabs.com/buildstatus/sauceUsername" alt="Sauce Test Status">
```


## Adding the Browser Matrix Widget:

Paste the following Markdown or HTML in your GitHub README or on your project site:

```bash
![Sauce Test Status](https://saucelabs.com/browser-matrix/sauceUsername.svg)
```

```html
<img src="https://saucelabs.com/browser-matrix/sauceUsername" alt="Sauce Test Status">
```

## Browser Matrix Widget for Private Accounts

To display the build status of a private account, you need to provide a HMAC token generated from your username and access key. Here is how you could generate one in python:

```python
from hashlib import md5
import hmac
hmac.new("sauceUsername:sauceAccessKey", None, md5).hexdigest()
    'AUTH_TOKEN'
```
*Note: if you don't have python on your system, check out this link for HMAC http://en.wikipedia.org/wiki/HMAC#External_links

Once the auth token has been obtained, it can be used to build a link in the following format:

```html
<img src="https://saucelabs.com/browser-matrix/sauceUsername.svg?auth=AUTH_TOKEN" alt="Sauce Browser Matrix">
```
