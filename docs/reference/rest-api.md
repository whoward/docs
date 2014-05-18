{
  title: "REST API",
  description: "Documentation for the Sauce Labs REST API.",
  category: 'Reference',
  index: 2
}

**API Version 1.1**

## Introduction
The Sauce Labs REST API allows customers to retrieve information about Sauce Labs resources programmatically over HTTP using JSON. Customers can retrieve job information, video replays of tests, Selenium logs of tests, start and stop SauceTunnels, and so on.

## Sauce API libraries

You can use one of our below API libraries to conveniently access our API:

<ul class="list-inline inline-container">
  <li><a href="https://github.com/saucelabs/saucerest-java"><img src="/images/tutorials/Java.png" alt="Java"></a></li>
  <li><a href="https://github.com/holidayextras/node-saucelabs"><img src="/images/tutorials/nodejs.png" alt="Node.js"></a></li>
  <li><a href="https://github.com/saucelabs/sauce_whisk"><img src="/images/tutorials/Ruby_logo.png" alt=""></a></li>
  <li><a href="https://github.com/jlipps/sausage"><img src="/images/tutorials/PHP-logo.png" alt=""></a></li>

</ul>

## Authentication

Users can authenticate using a [HTTP Basic Authentication][5] base64 encoded _Username_ and _API Access Key_. The easiest way to authenticate is to include the Sauce username and access key in the URL.

*Note: All headers **must** have `Content-Type` set to `application/json`.*

Here is an example retreiving a user's recent jobs:

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs
```

Here is an example logining in a subaccount to saucelabs.com and retrieving login cookies:

```bash
curl -D -X POST https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/users/subaccount-username/login \
-H 'Content-Type: application/json' \
-d '{"password":"subaccount-password"}'
```

Result:


    HTTP/1.1 200 OK
      Content-Type: application/json
      Set-cookie: saucelabs-login=...
      Set-cookie: ...
```json
{
  "cookies": [
    "saucelabs-login=..."
  ]
}
```

## Account Settings

Access account details.

(Includes the number of minutes available.)

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/users/$SAUCE_USERNAME
```

Result:
```json
{
  "access_key": "sdgk234lkjl223j",
  "minutes": "200",
  "manual_minutes": "infinite",
  "mac_minutes": "100",
  "mac_manual_minutes": "infinite",
  "id": "sauce_username"
}
```
Check account limits.

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/users/v1/$SAUCE_USERNAME/concurrency
```
Result:

```json
{
  "timestamp": 1397657955659,
  "concurrency": 10
}
```

Create a new subaccount.

```bash
curl -X POST https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/users/$SAUCE_USERNAME \
-H 'Content-Type: application/json' \
-d '{"username":"subaccount-username", "password":"subaccount-password", "name":"subaccount-name", "email":"subaccount-email-address"}'
```
Result:

```json
{
  "access_key": "subaccount-api-key",
  "minutes": 200,
  "id": "new-subaccount-username"
}
```

## Usage

Access current account activity.

Returns active job counts broken down by job status and subaccount.

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/activity
```
Result:

```json
{
  "subaccounts": {
    "subaccount_user_1": {
      "in progress": 25,
      "all": 30,
      "queued": 5
    },
    "subaccount_user_2": {
      "in progress": 5,
      "all": 5,
      "queued": 0
    }
  },
  "totals": {
    "in progress": 30,
    "all": 35,
    "queued": 5
  }
}
```

Access historical account usage data.

Optional query parameters 'start' and 'end' in YYYY-MM-DD format. Returns array of ['YYYY-MM-DD', [, ]] pairs for each day that had activity )

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/users/$SAUCE_USERNAME/usage
```
Result:

```json
{
  "usage": [
    [
      "2011-3-18",
      [
        2,
        38
      ]
    ],
    [
      "2011-3-31",
      [
        5,
        5533
      ]
    ],
    [
      "2011-4-1",
      [
        7,
        5076
      ]
    ]
  ],
  "username": "sauceUsername"
}
```

## Jobs

  All URL's listed below are assumed to be against the base url of


    https://username:accessKey@saucelabs.com

Where _username_ is your Sauce Labs username, _accessKey_ is the Sauce Labs access key you can find on your accounts page.

  * **Attributes:**
    * 'id': [string] Job Id
    * 'owner': [string] Job owner
    * 'status': [string] Job status
    * 'error': [string] Error (if any) for the job
    * 'name': [string] Job name
    * 'browser': [string] Browser the job is using
    * 'browser_version': [string] Browser version the job is using
    * 'os': [string] Operating system the job is using
    * 'creation_time': [integer] The time the job was first requested
    * 'start_time': [integer] The time the job began executing
    * 'end_time': [integer] The time the job finished executing
    * 'video_url': [string] Full URL to the video replay of the job
    * 'log_url': [string] Full URL to the Selenium log of the job
    * 'public': [string or boolean] [Visibility mode][6] [public, public restricted, share (true), team (false), private]
    * 'tags': [array of strings] Tags assigned to a job

  * **List** \- List all job Id's belonging to a given user.
    * Relative URL: /rest/v1/:username/jobs
    * Method: GET
    * Authentication: **required**
    * Response fields: none
    * Parameters (optional):
      * [GET] limit -> displays the specified number of jobs, instead of truncating the list at the default 100.
      * [GET] full -> forces full job information to be returned, rather than just IDs.
      * [GET] skip -> skips the specified number of jobs.
      * [GET] from -> returns jobs since the specified time (in epoch time, calculated from UTC).
      * [GET] to -> returns jobs up until the specified time (in epoch time, calculated from UTC).
      * [GET] format -> returns jobs is specified format. Currently we support 'json' and 'csv' and the default one is 'json'

Example getting last 100 job ids:
```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs
```

Example getting last 200 job ids:
```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs?limit=200
```

Example getting full information about last 100 jobs:
```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs?full=true
```

Example getting last 100 job ids skipping 20 most recent jobs:
```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs?skip=20
```

Example getting last 100 job ids using the CSV format:
```bash
 curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs?format=csv
```

Example retrieving
```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs?from=1357747500&to=1357748700
```

Show - Show the full information for a job given its ID.
Relative URL: /rest/v1/:username/jobs/id
Method: GET
Authentication: required
Response fields: none
Parameters: none
Example:

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs/YOUR_JOB_ID
```
Create - Jobs cannot be created via API. They must be created/started via the selenium client, such as SauceIDE, or the Selenium bindings for the language of your choice.

* **Update** \- Changes a pre-existing job.
* Relative URL: /rest/v1/:username/jobs/:id
* Method: PUT
* Authentication: **required**
* Response fields:
  * name: [string] Change the job name
      * tags: [array of strings] Change the job tags
      * public: [string or boolean] Set [job visibility][6] to "public", "public restricted", "share" (true), "team" (false) or "private"
      * passed: [boolean] Set whether the job passed or not on the user end
      * build: [int] The AUT build number tested by this test
      * custom-data: [JSON] a set of key-value pairs with any extra info that a user would like to add to the job
    * Parameters: none
    * Example:


```bash
curl -X PUT https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs/ YOUR_JOB_ID\
-H "Content-Type: application/json" \
-d '{"tags":["test","example","taggable"],"public":true,"name":"changed-job-name","passed": false, "custom-data":{"error":"step 17 failed"}}'
```
Delete - Removes the job from the system with all the linked assets.
Relative URL: /rest/v1/:username/jobs/:id
Method: DELETE
Authentication: required
Response fields: none
Parameters: none
Example:

```bash
curl -v -X DELETE http://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs/YOUR_JOB_ID
```

Stop - Terminates a running job.
Relative URL: /rest/v1/:username/jobs/:id/stop
Method: PUT
Authentication: required
Response fields: none
Parameters: none
Example:

```bash
curl -X PUT https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs/YOUR_JOB_ID/stop \
-d ''
```

List Job Assets - Get a details about the static assets collected for a specific job.
Relative URL: /rest/v1/:username/jobs/:id/assets
Method: GET
Authentication: required
Response fields (each of these fields will be set to "null" if the specific asset isn't captured for a job):
sauce-log: [string] Name of the Sauce log recorded for a job
selenium-log: [string] Name of the selenium Server log file produced by a job
video: [string] Name of the video file name recorded for a job
screenshots: [array of strings] List of screenshot names captured by a job
Parameters: none
Example:
```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs/YOUR_JOB_ID/assets
```
Download Job Assets - You can download every asset created after your test runs on Sauce through our REST API. These include the video recording, Selenium log, and screenshots taken on crucial steps.
Relative URL: /rest/v1/:username/jobs/:id/assets/:file_name
Method: GET
Authentication: required
Parameters: none

Available Files:

selenium-server.log
video.flv
XXXXscreenshot.png (where XXXX is a number between 0000 and 9999)
final_screenshot.png
Example:
```bash
curl -O https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs/YOUR_JOB_ID/assets/final_screenshot.png
```
Remove Job Assets - You can delete all the data gathered during test run from our servers. That includes the video recording, Selenium log, and all the screenshots.
Relative URL: /rest/v1/:username/jobs/:id/assets
Method: DELETE
Authentication: required
Parameters: none
Example:
```bash
curl -X DELETE https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/jobs/YOUR_JOB_ID/assets
```
## Tunnels

Tunnels are used by [Sauce Connect][7] to redirect traffic for a given domain to a server on your internal network. They are unique to users and will not affect any other users. For more information, please read our [Sauce Connect documentation][7]

Attributes:
'id': [string] Tunnel ID
'owner': [string] Tunnel owner
'status': [string] Tunnel status
'host': [string] Public address of the tunnel
'creation_time': [integer]
List - Retrieves all running tunnels for a given user.
Relative URL: /tunnels
Method: GET
Authentication: required
Response fields: none
Parameters: none
Example:

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/tunnels
```
Show - Show the full information for a tunnel given its ID.

Relative URL: /tunnels/:id
Method: GET
Authentication: required
Response fields: none
Parameters: none
Example:

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/tunnels/YOUR_JOB_ID
```

Delete - Shuts down a tunnel given its ID.
Relative URL: /tunnels/:id
Method: DELETE
Authentication: required
Response fields: none
Parameters: none
Example:
```bash
curl -X DELETE https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/tunnels/YOUR_JOB_ID
```
## Information

Informational REST commands _do not_ require the username to be in the base URL. They are publicly available resources.

  * **Status** \- Returns the current status of Sauce Labs' services.
    * Relative URL: /info/status
    * Method: GET
    * Authentication: none
    * Response fields: none
    * Parameters: none
    * Examples:


```bash
curl -X GET http://saucelabs.com/rest/v1/info/status
```

Result:

```json
{
  "wait_time": 0.5,
  "service_operational": true,
  "status_message": "Basic service status checks passed."
}
```

  * **Browsers** \- Returns an array of strings corresponding to all the browsers currently supported on Sauce Labs. (Choose the termination that defines which list you need, bearing in mind that Selenium 1 [RC] and 2 [WebDriver] are compatible with different browser/OS combinations.)

Relative URLs: /info/browsers/all, /selenium-rc, /webdriver
Method: GET
Authentication: none
Response fields: none
Parameters: none
Example:

```bash
curl -X GET http://saucelabs.com/rest/v1/info/browsers/webdriver
```

* **Counter** \- Returns the number of test executed so far on Sauce Labs.

Relative URL: /info/counter
Method: GET
Authentication: none
Response fields: none
Parameters: none
Example:

```bash
curl -X GET http://saucelabs.com/rest/v1/info/counter
```

## Partners

This section is for use only by Sauce Labs partner accounts. If you use these commands with a standard account, they may cause unexpected behavior. To add and manage sub-accounts, head to [your sub-accounts page][8].

Create a new subaccount, specifying a Sauce Labs service plan.

```bash
curl -X POST https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/users/$SAUCE_USERNAME -H 'Content-Type: application/json' -d '{"username":"subaccount-username", "password":"subaccount-password", "name":"sub-account-name", "email":"subaccount-email-address", "plan":"free"}'
```
**Available plans: ** 'free', 'small', 'team', 'com', 'complus'

Result:

```json
{
  "access_key": "subaccount-api-key",
  "minutes": 200,
  "id": "new-subaccount-username",
  "plan": "free"
}
```
Update a subaccount Sauce Labs service plan.

```bash
curl -X POST https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/users/$SAUCE_USERNAME/subscription -H 'Content-Type: application/json' -d '{"plan":"small"}'
```
Unsubscribe a subaccount from it's Sauce Labs service plan.

```bash
curl -X DELETE https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/$SAUCE_USERNAME/subscription
```
## Temporary Storage

Sauce Labs provides temporary storage inside our network for mobile apps, Selenium jars, prerun executables, and other assets required by your tests. Storing assets in our network can eliminate network latency problems when sending big files to Sauce. Here's how you use our storage:

  * \- Before tests start, upload the file via our REST API as described below.
  * \- During tests, use a special URL for the file, in the following format: `"sauce-storage:your_file_name"`
  * \- Sauce will find the file, download it through our fast internal network and get your tests started right away

To upload the file via our REST API:

```bash
curl -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY \
-X POST "http://saucelabs.com/rest/v1/storage/$SAUCE_USERNAME/your_file_name?overwrite=true" \
    -H "Content-Type: application/octet-stream" --data-binary @/path/to/your_file_name
```
The overwrite=true URL parameter allows files already stored in the Sauce network to be overwritten. It can be removed if you prefer to prevent overwriting.

This can be scripted in any programming language. Just make sure the HTTP method being used is POST and the Content-Type header is correct.

Please note that our temporary storage retains files for only 24 hours. We recommend users of this service upload files via our REST API every time their tests are about to run, as part of their build process.

## JS Unit Testing

If you already have JS unit tests, running them on Sauce using the REST API is simple.

Start your tests on as many browsers as you like with a single request:

```bash
    curl -X POST https://saucelabs.com/rest/v1/$SAUCE_USERNAME/js-tests \
        -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY \
        -H 'Content-Type: application/json' \
        --data '{
            "platforms": [["Windows 7", "firefox", "20"],
                          ["Linux", "googlechrome", ""]],
            "url": "https://saucelabs.com/test_helpers/front_tests/index.html",
            "framework": "jasmine"}'
```

`url` should point to the page that hosts your tests.
`framework` can be `"qunit"`, `"jasmine"`, `"YUI Test"`, `"mocha"`, or `"custom"`.

The `"custom"` framework allows you to display generic test information on the Sauce Labs website. Set `window.global_test_results` on the javascript on your unit test page to an object that looks like the following and Sauce will report any failing tests: `

```json
{
      "passed": 1,
      "failed": 3,
      "total": 4,
      "duration": 4321,
      "tests": [
    {
          "name": "foo test",
          "result": false,
          "message": "sumthin bad",
          "duration": 4000
        },
    {
          "name": "bar test",
          "result": false,
          "message": "failure",
          "duration": 300
        },
    {
          "name": "baz test",
          "result": true,
          "message": "passed",
          "duration": 20
        },
    {
          "name": "qux test",
          "result": false,
          "message": "test bad",
          "duration": 1
        }
      ]
    }
```

Hosting your tests on your LAN or your laptop? You'll need to run [Sauce Connect][9] to bridge Sauce Labs to your local network. Optional parameters related to Sauce Connect include:

`tunnel_identifier` \- specifies the ID of a specific tunnel when using multiple Sauce Connect tunnels.
`parent_tunnel` \- specifies the username of a parent account whose shared Sauce Connect tunnel your tests should use.

Any other parameters get passed on as [Optional Desired Capabilities][10] for the selenium server. This means you can set things like: `max-duration`

The default `max-duration` for all JS Unit Tests is 180 seconds.

The response will look something like this:

```json
{
  "js tests": [
    "064df78366ea4b25b32f88878c9d7aa4",
    "1e5ed949711545bd952456ac37479ada"
  ]
}
```
To check the result, POST that response back to `.../js-tests/status` verbatim, like so:

```bash
curl -X POST https://saucelabs.com/rest/v1/$SAUCE_USERNAME/js-tests/status \
        -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY \
        -H 'Content-Type: application/json' \
        --data '{"js tests": ["064df78366ea4b25b32f88878c9d7aa4", "1e5ed949711545bd952456ac37479ada"]}'
```
Do that a few times as the tests run, waiting until the response contains `"completed": true`.

Once the tests are completed, the result will look something like this:

```json
{
  "completed": true,
  "js tests": [
    {
      "id": "064df78366ea4b25b32f88878c9d7aa4",
      "job_id": "1a7aec8ef0c64165bcde1230e213ad44",
      "platform": [
        "Windows 8",
        "internet explorer",
        "10"
      ],
      "url": "http://saucelabs.com/jobs/ff737d47e03e47bfb45100a45e4b5ca5",
      "result": {
        "durationSec": 0.005,
        "passed": true,
        "suites": [
          {
            "description": "Player",
            "durationSec": 0.005,
            "passed": true,
            "specs": [
              {
                "description": "should be able to play a Song",
                "durationSec": 0.002,
                "failedCount": 0,
                "passed": true,
                "passedCount": 2,
                "skipped": false,
                "totalCount": 2
              }
            ]
          }
        ]
      }
    }
  ]
}
```
You can control the job attached to the JS Unit test via the job_id.

## Bugs

Interacting with Jobs bug tracking system

Get list of available bug types

```bash
curl https://saucelabs.com/rest/v1/bugs/types
```
Result:

```json
[
  {
    "id": "bug-type-example-id",
    "name": "Bug-example-name",
    "description": "Bug example description"
  }
]
```
Get description of each field for a particular bug type

```bash
curl https://saucelabs.com/rest/v1/bugs/types/bug-type-example-id-1234
```
Result:

```json
[
  {
    "type": "string",
    "name": "Field-name",
    "id": "Field-id"
  },
  {
    "type": "int",
    "name": "Field-name",
    "id": "Field-id"
  }
]
```
Get detailed info for a particular bug

```bash
curl https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/bugs/detail/YOUR_JOB_ID
```
Result:

```json
{
  "Description": "Description of really nasty bug",
  "Title": "What a nasty bug",
  "ScreenshotEmbedURL": "https://saucelabs.com/jobs//YOUR_JOB_ID0001screenshot.png?auth=secret-auth-token",
  "CreationTime": 1234567890,
  "VideoEmbedURL": "https://saucelabs.com/bugs/?YOUR_JOB_IDshow_video=1&auth=secret-auth-token",
  "Job": "https://saucelabs.com/jobs/?YOUR_JOB_IDauth=secret-auth-token",
  "BugEmbedURL": "https://saucelabs.com/bugs/?YOUR_JOB_IDauth=secret-auth-token",
  "OS": "Linux",
  "Browser": "android 4."
}
```

Get detailed info for a specified list of bugs

```bash
curl -G https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/bugs/query/ --data-urlencode 'ids=[""YOUR_JOB_ID, "0123401234-example-id-12345"]'
```
Result:

    List of JSON objects containing detailed info on each queried bug id

Update bug id ''YOUR_JOB_ID with specified key-value pairs

```bash
curl -G https://$SAUCE_USERNAME:$SAUCE_ACCESS_KEY@saucelabs.com/rest/v1/bugs/update/ YOUR_JOB_ID--data-urlencode 'update={"Property-name-1": "Property-Value-1", "Property-name-2": "Property-Value-2"}
```

**Valid keys: **Only following bug properties can be modified with the API: 'Title', 'Description'.

Result(After successful command execution):

```json
{
  "status": "success"
}
```

   [1]: http://en.wikipedia.org/wiki/Representational_State_Transfer
   [2]: http://en.wikipedia.org/wiki/HTTP
   [3]: http://en.wikipedia.org/wiki/JSON
   [4]: http://en.wikipedia.org/wiki/Content-Type
   [5]: http://en.wikipedia.org/wiki/Basic_access_authentication
   [6]: https://saucelabs.com/docs/additional-config#public-jobs
   [7]: https://saucelabs.com/docs/sauce-connect
   [8]: https://saucelabs.com/sub-accounts
   [9]: https://saucelabs.com/docs/connect
   [10]: /reference/jobs
