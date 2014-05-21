{
  title: "Status Images",
  description: "Show off your test status with Sauce Labs status images",
  category: "Reference",
  index: 4
}

If you want to keep track of your project's latest test run on Sauce, you can add one of our status images to your repo or website. Here are what the general status badges look like:

![sauce-badge-passing](https://saucelabs.com/images/status-passing.png) ![sauce-badge-fail](https://saucelabs.com/images/status-failed.png) ![sauce-badge-unknown](https://saucelabs.com/images/status-unknown.png)

We also have a browser matrix widget that helps you keep track of the status of your tests on different browsers:

![sauce-labs-browser-matrix](https://saucelabs.com/images/status-browser-matrix.svg)

## Reporting test results for badge status

1. Choose a Sauce account to associate with your project.
If you just have one project, you can use your main Sauce account name.
If you have multiple projects, you will want to [create a sub-account](/reference/user-management/) for each project.

2. Run your tests for a given project on Sauce using that account's username and access key. If you are logged in as the account you want to use, you can find your credentials on the [account page](https://saucelabs.com/account). If you are logged in as a parent account, you can see your subaccount usernames and access keys on the [subaccounts](https://saucelabs.com/sub-accounts) page.

3. Make sure to set a [build number](/reference/jobs/#record-the-build-number) and a [pass/fail](/reference/jobs/#record-pass-fail-status) status for every test that runs. You will be able to see that these are set correctly by seeing that your tests say "Pass" or "Failed" instead of "Finished" and that a build number is visible in the UI.

*Note: If your tests don't have a build or pass/fail status, you'll get the "Unknown" image for security reasons.*

## Adding the Standard Badge

Paste the following Markdown or HTML in your GitHub README or on your project site:

```bash
[![Sauce Test Status](https://saucelabs.com/buildstatus/sauceUsername)](https://saucelabs.com/u/sauceUsername)
```

```html
<a href="https://saucelabs.com/u/sauceUsername">
    <img src="https://saucelabs.com/buildstatus/sauceUsername" alt="Sauce Test Status">
</a>
```


## Adding the Browser Matrix Widget:

Paste the following Markdown or HTML in your GitHub README or on your project site:

```bash
[![Sauce Test Status](https://saucelabs.com/browser-matrix/sauceUsername.svg)](https://saucelabs.com/u/sauceUsername)
```

```html
<a href="https://saucelabs.com/u/sauceUsername">
    <img src="https://saucelabs.com/browser-matrix/sauceUsername.svg" alt="Sauce Test Status">
</a>
```

## Status Images for Private Accounts

To display the build status of a private Sauce account, you need to provide a HMAC token generated from your username and access key. Here is how you could generate one in python:

*Note: if you don't have python on your system, check out this link for HMAC http://en.wikipedia.org/wiki/HMAC#External_links *

First start the python interpreter with the following command:
```bash
python
```

Then run the following code in the interpreter to generate a query param to add to badge image URLs:
```python
from hashlib import md5
import hmac
"?auth=" + hmac.new("sauceUsername:sauceAccessKey", None, md5).hexdigest()
```

Once the auth token has been obtained, it can be appended to one of the above badge URLs as the value of the `auth` query like this:

```bash
![Sauce Test Status](https://saucelabs.com/browser-matrix/sauceUsername.svg?auth=AUTH_TOKEN)
```