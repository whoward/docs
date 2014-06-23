{
  title: "REST API",
  description: "Documentation for the Sauce Labs REST API.",
  category: 'Reference',
  index: 2
}

The Sauce Labs REST API is accessed over HTTPS, with standard HTTP methods and authentication, and using [JSON][1] encoding for request and response data.

The API is versioned by URL. The current version is v1, and resides under the `saucelabs.com/rest/v1/` base URL. Some v1.1 methods have been introduced under `saucelabs.com/rest/v1.1/`.

## Sauce API libraries

If you use Java, Ruby, PHP or node.js, you can use one of the below API libraries to conveniently access our API:

<ul class="list-inline inline-container">
  <li><a title="Java API Library" href="https://github.com/saucelabs/saucerest-java"><img src="/images/tutorials/java.png" alt="Java API Library"></a></li>
  <li><a title="Ruby API Library" href="https://github.com/saucelabs/sauce_whisk"><img src="/images/tutorials/ruby.png" alt="Ruby API Library"></a></li>
  <li><a title="PHP API Library" href="https://github.com/jlipps/sausage"><img src="/images/tutorials/php.png" alt="PHP API Library"></a></li>
  <li><a title="Node.js API Library" href="https://github.com/holidayextras/node-saucelabs"><img src="/images/tutorials/nodejs.png" alt="Node.js API Library"></a></li>
</ul>

## Getting Started

The Sauce Labs REST API uses [HTTP Basic Authentication][2]. The easiest way to authenticate is to include the Sauce username and access key in the request URL.

Note that all below endpoints default to a `GET` request unless specified, and all `POST` requests **must** have the `Content-Type` header set to `application/json`.

## Account

These methods provide user account information and management.

### Get User

Access basic account information.

URL: `https://saucelabs.com/rest/v1/users/:username`

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/users/sauceUsername
```

### Create User

Create a sub-account.

URL: `https://saucelabs.com/rest/v1/users/:username`

METHOD: `POST`

**Request Fields:**
* `username`(required)
* `password`(required)
* `name`(required): full name of sub-account user
* `email`(required)

**Example request:**
```bash
curl -X POST https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/users/sauceUsername \
     -H 'Content-Type: application/json' \
     -d '{"username": "subaccount-username", 
          "password": "subaccount-password", 
          "name": "subaccount-name", 
          "email": "subaccount-email-address"}'
```

### Get User Concurrency

Check account concurrency limits.

URL: `https://saucelabs.com/rest/v1/users/:username/concurrency`

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/users/sauceUsername/concurrency
```

## Test Activity and Usage

### Get User Activity

URL: `https://saucelabs.com/rest/v1/:username/activity`

Get active job counts broken down by job status and sub-account.

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/activity
```

### Get User Account Usage
Access historical account usage data.

URL: `https://saucelabs.com/rest/v1/users/:username/usage`

**Optional Query Params:**
* `start` and `end` in YYYY-MM-DD format.

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/users/sauceUsername/usage
```

## Jobs

### Get Jobs

List recent jobs belonging to a given user.

URL: `https://saucelabs.com/rest/v1/:username/jobs`

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs
```

**Optional query params:**

#### number of jobs
Specifies the number of jobs to be returned.

URL: `https://saucelabs.com/rest/v1/:username/jobs?limit=:number_of_jobs`

Default: `100`

**Example getting last 200 job IDs:**

```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs?limit=200
```

#### full jobs
Get full job information, rather than just IDs.

URL: `https://saucelabs.com/rest/v1/:username/jobs?full=:get_full_info`

Default: `false`

**Response Fields:**

* `id`: [string] Job Id
* `owner`: [string] Job owner
* `status`: [string] Job status
* `error`: [string] Error (if any) for the job
* `name`: [string] Job name
* `browser`: [string] Browser the job is using
* `browser_version`: [string] Browser version the job is using
* `os`: [string] Operating system the job is using
* `creation_time`: [integer] The time the job was first requested
* `start_time`: [integer] The time the job began executing
* `end_time`: [integer] The time the job finished executing
* `video_url`: [string] Full URL to the video replay of the job
* `log_url`: [string] Full URL to the Selenium log of the job
* `public`: [string or boolean] [Visibility mode](/reference/test-configuration/#job-visibility) [public, public restricted, share (true), team (false), private]
* `tags`: [array of strings] Tags assigned to a job

**Example getting full information about the last 100 jobs:**

```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs?full=true
```

#### skip jobs
Skips the specified number of jobs.

URL: `https://saucelabs.com/rest/v1/:username/jobs?skip=:number_of_jobs`

Default: `0`

**Example getting the last 100 job IDs, skipping 20 most recent jobs:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs?skip=20
```

#### jobs to and from time
Get jobs since/until the specified time (in epoch time, calculated from UTC).

URL: `https://saucelabs.com/rest/v1/:username/jobs?to=:time` and `https://saucelabs.com/rest/v1/:username/jobs?from=:time`

**Example request(replace `EPOCH_TIME` with an [epoch time](http://en.wikipedia.org/wiki/Unix_time)):**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs?from=EPOCH_TIME
```

#### format jobs
Get job info in the specified format. Currently we support `json` and `csv`.

URL: `https://saucelabs.com/rest/v1/:username/jobs?format=:job_format`

Default: `json`

**Example getting last 100 job IDs using the CSV format:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs?format=csv
```

### Get Job
Show the full information for a job given its ID.

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID
```

### Update Job
Edit an existing job

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id`

METHOD: `PUT`

**Request Fields:**
* `name`: [string] Change the job name
* `tags`: [array of strings] Change the job tags
* `public`: [string or boolean] Set [job visibility](/reference/test-configuration/#job-visibility) to "public", "public restricted", "share" (true), "team" (false) or "private"
* `passed`: [boolean] Set whether the job passed or not on the user end
* `build`: [int] The build number tested by this test
* `custom-data`: [JSON] a set of key-value pairs with any extra info that a user would like to add to the job

**Example request:**
```bash
curl -X PUT https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID \
     -H "Content-Type: application/json" \
     -d '{"tags": ["test", "example", "taggable"],
          "public": true,
          "name": "changed-job-name",
          "passed": false, 
          "custom-data": {"error": "step 17 failed"}}'
```

### Delete Job

Removes the job from the system with all the linked assets.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id`

METHOD: `DELETE`

**Example request:**
```bash
curl -v -X DELETE http://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID
```

### Stop Job

Terminates a running job.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id/stop`

METHOD: `PUT`

**Example request:**
```bash
curl -X PUT https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID/stop \
-d ''
```

### Get Job Asset Names

Get details about the static assets collected for a specific job.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id/assets`

**Response fields:** (each of these fields will be set to "null" if the specific asset isn't captured for a job):

* `sauce-log`: [string] Name of the Sauce log recorded for a job
* `selenium-log`: [string] Name of the selenium Server log file produced by a job
* `video`: [string] Name of the video file name recorded for a job
* `screenshots`: [array of strings] List of screenshot names captured by a job

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID/assets
```

### Get Job Asset Files

Download job assets. After a job completes, all assets created during the job are available via this API. These include the screencast recording, logs, and screenshots taken on crucial steps.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id/assets/:file_name`

**Available Values for `:file_name`**:

* selenium-server.log
* video.flv
* XXXXscreenshot.png (where XXXX is a number between 0000 and 9999)
* final_screenshot.png

**Example request:**
```bash
curl -O https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID/assets/final_screenshot.png
```

### Delete Job Assets

Delete all the assets captured during a test run. This includes the screencast recording, logs, and all screenshots.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id/assets`

Method: `DELETE`

**Example request:**
```bash
curl -X DELETE https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID/assets
```

## Tunnels

Tunnels are used by [Sauce Connect][4] to redirect traffic for a given domain to a server on your internal network. They are unique to users and will not affect any other users. For more information, please read our [Sauce Connect documentation][4]

### Get Tunnels

Retrieves all running tunnels for a given user.

URL: `https://saucelabs.com/rest/v1/:username/tunnels`

**Response Fields:**
* `id`: [string] Tunnel ID
* `owner`: [string] Tunnel owner
* `status`: [string] Tunnel status
* `host`: [string] Public address of the tunnel
* `creation_time`: [integer]

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/tunnels
```

### Get Tunnel
Get information for a tunnel given its ID.

URL: `https://saucelabs.com/rest/v1/:username/tunnels/:tunnel_id`

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/tunnels/YOUR_TUNNEL_ID
```

### Delete Tunnel
Shuts down a tunnel given its ID.

URL: `https://saucelabs.com/rest/v1/:username/tunnels/:tunnel_id`

Method: `DELETE`

**Example request:**
```bash
curl -X DELETE https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/sauceUsername/tunnels/YOUR_TUNNEL_ID
```

## Information

Information resources are publicly available data about Sauce Lab's service.

### Get Sauce Labs Status

Get the current status of Sauce Labs services.

URL: `https://saucelabs.com/rest/v1/info/status`

**Example request:**
```bash
curl -X GET http://saucelabs.com/rest/v1/info/status
```

### Get Supported Browsers

URL: `https://saucelabs.com/rest/v1/info/browsers/:automation_api`

Get an array of strings corresponding to all the browsers currently supported on Sauce Labs. Choose the automation API you need, bearing in mind that WebDriver and Selenium RC are each compatible with a different set of OS and browser platforms.

**Accepted Values for `automation_api`:**

* `all`
* `webdriver`
* `selenium-rc`

**Example request:**
```bash
curl -X GET http://saucelabs.com/rest/v1/info/browsers/webdriver
```

## Partners

This section is for use only by Sauce Labs partner accounts. If you use these commands with a standard account, they may cause unexpected behavior. To add and manage sub-accounts, head to [your sub-accounts page][5].

### Create Account With Plan

Create a new subaccount, specifying a Sauce Labs service plan.

URL: `https://saucelabs.com/rest/v1/users/:username`

Method: `POST`

**Request Fields:**
* `username`(required)
* `password`(required)
* `name`(required): full name of sub-account user
* `email`(required)
* `plan`(required): Either 'free', 'small', 'team', 'com', or 'complus'

**Example request:**
```bash
curl -X POST https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/users/sauceUsername \
     -H 'Content-Type: application/json' \
     -d '{"username": "subaccount-username", 
          "password": "subaccount-password", 
          "name": "sub-account-name", 
          "email": "subaccount-email-address", 
          "plan": "free"}'
```

### Update Subaccount Plan

Update a subaccount Sauce Labs service plan.

URL: `https://saucelabs.com/rest/v1/:subaccount_username/subscription`

Method: `POST`

**Request Fields:**
* `plan`(required): Either 'free', 'small', 'team', 'com', or 'complus'

**Example request:**
```bash
curl -X POST https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/users/SUBACCOUNT_USERNAME/subscription \
     -H 'Content-Type: application/json' \
     -d '{"plan": "small"}'
```

### Unsubscribe a Subaccount

Unsubscribe a subaccount from it's Sauce Labs service plan.

URL: `https://saucelabs.com/rest/v1/:subaccount_username/subscription`

Method: `DELETE`

**Example request:**
```bash
curl -X DELETE https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/SUBACCOUNT_USERNAME/subscription
```

## Temporary Storage

Sauce Labs provides temporary storage inside our network for mobile apps, Selenium jars, prerun executables, and other assets required by your tests. Storing assets in our network can eliminate network latency problems when sending big files to Sauce. Here's how you use our storage:

* Before tests start, upload the file via our REST API as described below.
* During tests, use a `sauce-storage:` URL for the file, in the following format: `"sauce-storage:your_file_name"`
* Sauce will find the file, download it through our fast internal network, and get your tests started right away.

Please note that our temporary storage retains files for only 24 hours. We recommend users of this service upload files via our REST API every time their tests are about to run, as part of their build process.

### Upload File

Upload a file to temporary storage.

URL: `https://saucelabs.com/rest/v1/storage/:username/:your_file_name`

Method: `POST`

**Example request:**
```bash
curl -u sauceUsername:sauceAccessKey \
     -X POST "http://saucelabs.com/rest/v1/storage/sauceUsername/your_file_name?overwrite=true" \
     -H "Content-Type: application/octet-stream" \
     --data-binary @/path/to/your_file_name
```

This can be scripted in any programming language. Just make sure the HTTP method being used is POST and the `Content-Type` header is correct.

**Optional Query Params:**

By default, the API prevents overwriting files already stored in Sauce temporary storage. The `overwrite=true` query parameter (shown in the example above) can be added to allow overwriting.

## JS Unit Testing

If you already have JS unit tests, running them on Sauce using the REST API is simple.

### Start JS Unit Tests

Start your JavaScript unit tests on as many browsers as you like with a single request.

URL: `https://saucelabs.com/rest/v1/:username/js-tests`

Method: `POST`

**Request Fields:**
* `platforms`(required): an array of platforms
* `url`(required): should point to the page that hosts your tests.
* `framework`(required): can be `"qunit"`, `"jasmine"`, `"YUI Test"`, `"mocha"`, or `"custom"`.

The `"custom"` framework allows you to display generic test information on the Sauce Labs website. Set `window.global_test_results` on the javascript on your unit test page to an object that looks like the following and Sauce will report any failing tests: `

**Example request:**
```bash
curl  -X POST https://saucelabs.com/rest/v1/sauceUsername/js-tests \
      -u sauceUsername:sauceAccessKey \
      -H 'Content-Type: application/json' \
      --data '{
          "platforms": [["Windows 7", "firefox", "27"],
                        ["Linux", "googlechrome", ""]],
          "url": "https://saucelabs.com/test_helpers/front_tests/index.html",
          "framework": "jasmine"}'
```

Hosting your tests on your LAN or your laptop? You'll need to run [Sauce Connect](/reference/sauce-connect/) to bridge Sauce Labs to your local network. Optional parameters related to Sauce Connect include:

* `tunnel_identifier`: specifies the ID of a specific tunnel when using multiple Sauce Connect tunnels.
* `parent_tunnel`: specifies the username of a parent account whose shared Sauce Connect tunnel your tests should use.

Any other parameters get passed on as [Optional Desired Capabilities][6] for the selenium server. This means you can set things like: `max-duration`

The default `max-duration` for all JS unit tests is 180 seconds.

**Example response:**
```json
{
  "js tests": [
    "064df78366ea4b25b32f88878c9d7aa4",
    "1e5ed949711545bd952456ac37479ada"
  ]
}
```

### Get JS Unit Test Status

Get the status of your JS unit tests

URL `https://saucelabs.com/rest/v1/:username/js-tests/status`

**Example request:**
```bash
curl -X POST https://saucelabs.com/rest/v1/sauceUsername/js-tests/status \
        -u sauceUsername:sauceAccessKey \
        -H 'Content-Type: application/json' \
        -d '{
              "js tests": [
                "064df78366ea4b25b32f88878c9d7aa4", 
                "1e5ed949711545bd952456ac37479ada"
              ]
            }'
```

Do that a few times as the tests run, waiting until the response contains `"completed": true` to get the final results.

**Example response:**
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
You can control the job attached to the JS unit test via the `job_id`.

## Bugs

Interacting with Jobs bug tracking system

### Get Bug Types

Get list of available bug types

URL: `https://saucelabs.com/rest/v1/bugs/types`

**Example request:**
```bash
curl https://saucelabs.com/rest/v1/bugs/types
```

**Example response:**
```json
[
  {
    "id": "bug-type-example-id",
    "name": "Bug-example-name",
    "description": "Bug example description"
  }
]
```

### Get Bug

Get description of each field for a particular bug type

URL: `https://saucelabs.com/rest/v1/bugs/types/:bug_id`

**Example request:**
```bash
curl https://saucelabs.com/rest/v1/bugs/types/bug-type-example-id-1234
```

**Example response:**
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

### Get Bug Details

Get detailed info for a particular bug

URL: `https://saucelabs.com/rest/v1/bugs/details/:bug_id`

**Example request:**
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/bugs/detail/YOUR_BUG_ID
```

**Example response:**
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

### Get Bugs Details

Get detailed info for a specified list of bugs

URL: `https://saucelabs.com/rest/v1/bugs/query/ids=[:id_1,:id_2]`

**Example request:**
```bash
curl -G https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/bugs/query/ --data-urlencode 'ids=[""YOUR_BUG_ID, "0123401234-example-id-12345"]'
```

**Example response:**
```json
[
  {"Description": "Description of really nasty bug",
   "Title": "What a nasty bug",
   "ScreenshotEmbedURL": "https://saucelabs.com/jobs//YOUR_JOB_ID0001screenshot.png?auth=secret-auth-token",
   "CreationTime": 1234567890,
   "VideoEmbedURL": "https://saucelabs.com/bugs/?YOUR_JOB_IDshow_video=1&auth=secret-auth-token",
   "Job": "https://saucelabs.com/jobs/?YOUR_JOB_IDauth=secret-auth-token",
   "BugEmbedURL": "https://saucelabs.com/bugs/?YOUR_JOB_IDauth=secret-auth-token",
   "OS": "Linux",
   "Browser": "iexplore 10."
   }
]
```

### Update Bug

Update bug id `:bug_id` with specified key-value pairs

URL: `https://saucelabs.com/rest/v1/bugs/update/:bug_id`

Method: `PUT`

**Example request:**
```bash
curl -G https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/bugs/update/YOUR_BUG_ID --data-urlencode 'update={"Property-name-1": "Property-Value-1", "Property-name-2": "Property-Value-2"}
```

**Valid keys:** Only the following bug properties can be modified with the API: `"Title"`, and `"Description"`.

**Example response:**
```json
{
  "status": "success"
}
```

   [1]: http://en.wikipedia.org/wiki/JSON
   [2]: http://en.wikipedia.org/wiki/Basic_access_authentication
   [3]: https://saucelabs.com/docs/additional-config#job-visibility
   [4]: /reference/sauce-connect/
   [5]: https://saucelabs.com/sub-accounts
   [6]: /reference/test-configuration/
