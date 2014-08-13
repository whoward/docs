{
  title: "REST API",
  description: "Documentation for the Sauce Labs REST API.",
  category: 'Reference',
  index: 2
}

The Sauce Labs REST API is accessed over HTTPS, with standard HTTP methods and authentication, and using [JSON](http://en.wikipedia.org/wiki/JSON) encoding for request and response data.

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

The Sauce Labs REST API uses [HTTP Basic Authentication](http://en.wikipedia.org/wiki/Basic_access_authentication). To authenticate, either include the Sauce username and access key in the request URL like so:
```bash
curl https://sauceUsername:sauceAccessKey@saucelabs.com/rest/v1/users/sauceUsername
```

Or add an `Authorization` header to the request like this:
```bash
curl https://saucelabs.com/rest/v1/users/sauceUsername \
-u sauceUsername:sauceAccessKey
```

Note that all below endpoints default to a `GET` request unless specified, and all `POST` requests **must** have the `Content-Type` header set to `application/json`.

## Account

These methods provide user account information and management.

### Get User

Access basic account information.

URL: `https://saucelabs.com/rest/v1/users/:username`

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/users/sauceUsername \
-u sauceUsername:sauceAccessKey
```

**Example Response:**
```json
{
    "access_key": "sauceAccessKey",
    "can_run_manual": true,
    "email": "YOUR_EMAIL",
    "id": "sauceUsername",
    "mac_manual_minutes": 30,
    "mac_minutes": 120,
    "manual_minutes": 30,
    "minutes": 913,
    "name": "YOUR_NAME",
    "subscribed": true,
    "user_type": "subscribed"
}
```

### Create User

Create a sub-account.

URL: `https://saucelabs.com/rest/v1/users/:username`

Method: `POST`

**Request Fields:**
* `username`(required)
* `password`(required)
* `name`(required): full name of sub-account user
* `email`(required)

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/users/sauceUsername \
-u sauceUsername:sauceAccessKey \
-H 'Content-Type: application/json' \
-d '{"username": "SUBACCOUNT_USERNAME", "password": "subaccount-password", "name": "subaccount-name", "email": "SUBACCOUNT"}'
```

**Example Response:**
```json
{
    "access_key": "SUBACCOUNT_ACCESS_KEY",
    "can_run_manual": true,
    "email": "SUBACCOUNT_EMAIL_ADDRESS",
    "id": "SUBACCOUNT_USERNAME",
    "mac_manual_minutes": "SUBACCOUNT_MAC_MANUAL_MINUTES",
    "mac_minutes": "SUBACCOUNT_MAC_MINUTES",
    "manual_minutes": "SUBACCOUNT_MANUAL_MINUTES",
    "minutes": "SUBACCOUNT_MINUTES",
    "name": "SUBACCOUNT_USERS_NAME",
    "subscribed": false,
    "user_type": "subaccount"
}
```

### Get User Concurrency

Check account concurrency limits.

URL: `https://saucelabs.com/rest/v1/users/:username/concurrency`

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/users/sauceUsername/concurrency \
-u sauceUsername:sauceAccessKey
```

**Example Response:**
```json
{
    "concurrency": {
        "sauceUsername": {
            "current": {
                "mac": 0,
                "manual": 1,
                "overall": 1
            },
            "remaining": {
                "mac": 3,
                "manual": 2,
                "overall": 3
            }
        },
        "YOUR_SUBACCOUNT_USERNAME": {
            "current": {
                "mac": 1,
                "manual": 0,
                "overall": 1
            },
            "remaining": {
                "mac": 0,
                "manual": 1,
                "overall": 1
            }
        }
    },
    "timestamp": 0
}
```

## Test Activity and Usage

### Get User Activity

URL: `https://saucelabs.com/rest/v1/:username/activity`

Get active job counts broken down by job status and sub-account.

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/sauceUsername/activity \
-u sauceUsername:sauceAccessKey
```

**Example Response:**
```json
{
    "subaccounts": {
        "sauceUsername": {
            "all": 1,
            "in progress": 0,
            "queued": 1
        },
        "YOUR_SUBACCOUNT_USERNAME": {
            "all": 1,
            "in progress": 1,
            "queued": 0
        }
    },
    "totals": {
        "all": 2,
        "in progress": 1,
        "queued": 1
    }
}
```

### Get User Account Usage
Access historical account usage data.

URL: `https://saucelabs.com/rest/v1/users/:username/usage`

**Optional Query Params:**
* `start` and `end` in YYYY-MM-DD format.

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/users/sauceUsername/usage \
-u sauceUsername:sauceAccessKey
```

**Example Response:**
```json
{
    "usage": [
        [
            "2014-3-18",
            [
                2,
                38
            ]
        ],
        [
            "2014-3-31",
            [
                5,
                5533
            ]
        ],
        [
            "2014-4-1",
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

### Get Jobs

List recent jobs belonging to a given user.

URL: `https://saucelabs.com/rest/v1/:username/jobs`

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/sauceUsername/jobs \
-u sauceUsername:sauceAccessKey
```

**Example Response:**
```json
[
    {
        "id": "f45fe215340b4ccead2e52e04ae7a98a"
    },
    {
        "id": "JOB_ID_2"
    }
]
```

**Optional query params:**

#### number of jobs
Specifies the number of jobs to be returned.

URL: `https://saucelabs.com/rest/v1/:username/jobs?limit=:number_of_jobs`

Default: `100`

**Example getting last 10 job IDs:**

```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/jobs?limit=10
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
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/jobs?full=true
```

**Example Response:**
```json
[
    {
        "breakpointed": null,
        "browser": "android",
        "browser_short_version": "4.3",
        "browser_version": "4.3.",
        "build": null,
        "commands_not_successful": 4,
        "creation_time": 1406237583,
        "custom-data": null,
        "end_time": 1406237603,
        "error": null,
        "id": "JOB_ID",
        "log_url": "https://assets.saucelabs.com/jobs/JOB_ID/selenium-server.log",
        "name": "Nexus4 AndroidDriver test",
        "os": "Linux",
        "owner": "sauceUsername",
        "passed": null,
        "proxied": false,
        "public": null,
        "start_time": 1406237584,
        "status": "complete",
        "tags": [],
        "video_url": "https://assets.saucelabs.com/jobs/JOB_ID/video.flv"
    }
]
```
#### skip jobs
Skips the specified number of jobs.

URL: `https://saucelabs.com/rest/v1/:username/jobs?skip=:number_of_jobs`

Default: `0`

**Example getting the last 100 job IDs, skipping 20 most recent jobs:**
```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/jobs?skip=20
```

#### jobs to and from time
Get jobs since/until the specified time (in epoch time, calculated from UTC).

URL: `https://saucelabs.com/rest/v1/:username/jobs?to=:time` and `https://saucelabs.com/rest/v1/:username/jobs?from=:time`

**Example Request(replace `EPOCH_TIME` with an [epoch time](http://en.wikipedia.org/wiki/Unix_time)):**
```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/jobs?from=EPOCH_TIME
```

#### format jobs
Get job info in the specified format. Currently we support `json` and `csv`.

URL: `https://saucelabs.com/rest/v1/:username/jobs?format=:job_format`

Default: `json`

**Example getting last 100 job IDs using the CSV format:**
```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/jobs?format=csv
```

**Example Response:**
```
id
8a9ed142f4854368a325afb10b7e19db
f45fe215340b4ccead2e52e04ae7a98a
0d29daf49a3b448e8a91f9ed31de97f3
5dc0aa8356f0417c83196c04bb232b25
a04e5c50eb7d4b3f8e5ef17df0784b65
...
```

### Get Job
Show the full information for a job given its ID.

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID
```

**Example Response:**
```json
{
    "breakpointed": null,
    "browser": "firefox",
    "browser_short_version": "30",
    "browser_version": "30.0.",
    "build": null,
    "commands_not_successful": 13,
    "creation_time": 1402525781,
    "custom-data": null,
    "end_time": 1402525814,
    "error": null,
    "id": "YOUR_JOB_ID",
    "log_url": "https://assets.saucelabs.com/jobs/YOUR_JOB_ID/selenium-server.log",
    "manual": false,
    "name": "Selenium Tests",
    "os": "Mac 10.9",
    "owner": "sauceUsername",
    "passed": null,
    "proxied": true,
    "public": null,
    "start_time": 1402525781,
    "status": "complete",
    "tags": [
        "master",
        "598baae9b3daf3faa3692f00027d7d25aa80f7df"
    ],
    "video_url": "https://assets.saucelabs.com/jobs/YOUR_JOB_ID/video.flv"
}
```

### Update Job
Edit an existing job

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id`

Method: `PUT`

**Request Fields:**
* `name`: [string] Change the job name
* `tags`: [array of strings] Change the job tags
* `public`: [string or boolean] Set [job visibility](/reference/test-configuration/#job-visibility) to "public", "public restricted", "share" (true), "team" (false) or "private"
* `passed`: [boolean] Set whether the job passed or not on the user end
* `build`: [int] The build number tested by this test
* `custom-data`: [JSON] a set of key-value pairs with any extra info that a user would like to add to the job. Note that the max data allowed is `64KB`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-X PUT \
-H "Content-Type: application/json" \
-d '{"tags": ["testing-rest-api"],
          "name": "REST API Test",
          "custom-data": {"source": "Testing REST API"}}' \
https://saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID
```

### Delete Job

Removes the job from the system with all the linked assets.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id`

Method: `DELETE`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-v \
-X DELETE \
https://saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID
```

### Stop Job

Terminates a running job.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id/stop`

Method: `PUT`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-X PUT \
-d '' \
https://saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID/stop
```

### Get Job Asset Names

Get details about the static assets collected for a specific job.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id/assets`

**Response Fields:** (each of these fields will be set to "null" if the specific asset isn't captured for a job):

* `sauce-log`: [string] Name of the Sauce log recorded for a job
* `screenshots`: [array of strings] List of screenshot names captured by a job
* `selenium-log`: [string] Name of the selenium Server log file produced by a job
* `video`: [string] Name of the video file name recorded for a job

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID/assets
```

**Example Response:**
```json
{
    "sauce-log": "log.json",
    "screenshots": [
        "0000screenshot.png",
        "0001screenshot.png"
    ],
    "selenium-log": "selenium-server.log",
    "video": "video.flv"
}
```

### Get Job Asset Files

Download job assets. After a job completes, all assets created during the job are available via this API. These include the screencast recording, logs, and screenshots taken on crucial steps.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id/assets/:file_name`

**Accepted Values for `:file_name`**:

* `selenium-server.log`
* `video.flv`
* `XXXXscreenshot.png` (where XXXX is a number between 0000 and 9999)
* `final_screenshot.png`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-O \
https://saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID/assets/final_screenshot.png
```

### Delete Job Assets

Delete all the assets captured during a test run. This includes the screencast recording, logs, and all screenshots.

URL: `https://saucelabs.com/rest/v1/:username/jobs/:job_id/assets`

Method: `DELETE`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-X DELETE \
https://saucelabs.com/rest/v1/sauceUsername/jobs/YOUR_JOB_ID/assets
```

## Tunnels

Tunnels are used by [Sauce Connect](/reference/sauce-connect/) to redirect traffic for a given domain to a server on your internal network. They are unique to users and will not affect any other users. For more information, please read our [Sauce Connect documentation](/reference/sauce-connect/)

### Get Tunnels

Retrieves all running tunnel IDs for a given user.

URL: `https://saucelabs.com/rest/v1/:username/tunnels`

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/sauceUsername/tunnels \
-u sauceUsername:sauceAccessKey
```

**Example Response:**

```json
["530c591c4666465aaf2e1097baff6324"]
```

### Get Tunnel
Get information for a tunnel given its ID.

URL: `https://saucelabs.com/rest/v1/:username/tunnels/:tunnel_id`

**Response Fields:**
* `id`: [string] Tunnel ID
* `owner`: [string] Tunnel owner
* `status`: [string] Tunnel status
* `host`: [string] Public address of the tunnel
* `creation_time`: [integer]

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/sauceUsername/tunnels/YOUR_TUNNEL_ID
```

**Example Response:**

```json
{
    "creation_time": 1406908737,
    "direct_domains": null,
    "domain_names": [
        "sauce-connect.proxy"
    ],
    "host": "maki77040.miso.saucelabs.com",
    "id": "YOUR_TUNNEL_ID",
    "metadata": {
        "Build": "35",
        "OwnerHost": "127.0.0.1",
        "OwnerPorts": [
            "60063"
        ],
        "Platform": "Java-1.6.0_65-Java_HotSpot-TM-_64-Bit_Server_VM,_20.65-b04-462,_Apple_Inc.-on-Mac_OS_X-10.9.3-x86_64",
        "Ports": [
            "80"
        ],
        "PythonVersion": "2.5.1",
        "Release": "3.0-r24",
        "ScriptName": "sauce_connect",
        "ScriptRelease": 35
    },
    "no_ssl_bump_domains": null,
    "owner": "SAUCE_USERNAME",
    "shared_tunnel": false,
    "shutdown_time": null,
    "ssh_port": 443,
    "status": "running",
    "tunnel_identifier": null,
    "use_caching_proxy": true,
    "use_kgp": true,
    "user_shutdown": null,
    "vm_version": null
}
```

### Delete Tunnel
Shuts down a tunnel given its ID.

URL: `https://saucelabs.com/rest/v1/:username/tunnels/:tunnel_id`

Method: `DELETE`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-X DELETE \
https://saucelabs.com/rest/v1/sauceUsername/tunnels/YOUR_TUNNEL_ID
```

## Information

Information resources are publicly available data about Sauce Lab's service.

### Get Sauce Labs Status

Get the current status of Sauce Labs services.

URL: `http://saucelabs.com/rest/v1/info/status`

**Example Request:**
```bash
curl http://saucelabs.com/rest/v1/info/status
```

**Example Response:**
```json
{
    "service_operational": true,
    "status_message": "Basic service status checks passed.",
    "wait_time": 0.5
}
```

### Get Supported Platforms

URL: `http://saucelabs.com/rest/v1/info/platforms/:automation_api`

Get a list of objects describing all the OS and browser platforms currently supported on Sauce Labs. Choose the automation API you need, bearing in mind that WebDriver and Selenium RC are each compatible with a different set of platforms.

**Accepted Values for `automation_api`:**

* `all`
* `appium`
* `webdriver`
* `selenium-rc`

**Example Request:**
```bash
curl http://saucelabs.com/rest/v1/info/platforms/appium
```

**Example Response:**
```json
[
    {
        "api_name": "ipad",
        "automation_backend": "appium",
        "device": "ipad",
        "latest_stable_version": "",
        "long_name": "iPad",
        "long_version": "7.0.",
        "os": "Mac 10.9",
        "short_version": "7.0"
    },
    {
        "api_name": "ipad",
        "automation_backend": "appium",
        "device": "ipad",
        "latest_stable_version": "",
        "long_name": "iPad",
        "long_version": "7.1.",
        "os": "Mac 10.9",
        "short_version": "7.1"
    },
    {
        "api_name": "android",
        "automation_backend": "appium",
        "device": "android",
        "latest_stable_version": "",
        "long_name": "Android",
        "long_version": "4.3.",
        "os": "Linux",
        "short_version": "4.3"
    }
]
```

## Partners

This section is for use only by Sauce Labs partner accounts. If you use these commands with a standard account, they may cause unexpected behavior. To add and manage sub-accounts, head to [your sub-accounts page](https://saucelabs.com/sub-accounts).

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

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/users/sauceUsername \
-H 'Content-Type: application/json' \
-d '{"username": "sauceUsername_subaccount",
        "password": "subaccount-password",
        "name": "sauceUsername_subaccount_name",
        "email": "subaccount-email-address",
        "plan": "free"}'
```

**Example Response:**
```json
{
    "access_key": "subaccount-api-key",
    "id": "new-subaccount-username",
    "minutes": 200,
    "plan": "free"
}
```

### Update Subaccount Plan

Update a subaccount Sauce Labs service plan.

URL: `https://saucelabs.com/rest/v1/:subaccount_username/subscription`

Method: `POST`

**Request Fields:**
* `plan`(required): Either 'free', 'small', 'team', 'com', or 'complus'

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-H 'Content-Type: application/json' \
-d '{"plan": "small"}' \
https://saucelabs.com/rest/v1/users/SUBACCOUNT_USERNAME/subscription
```

### Unsubscribe a Subaccount

Unsubscribe a subaccount from its Sauce Labs service plan.

URL: `https://saucelabs.com/rest/v1/:subaccount_username/subscription`

Method: `DELETE`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-X DELETE \
https://saucelabs.com/rest/v1/SUBACCOUNT_USERNAME/subscription
```

## Temporary Storage

Sauce Labs provides temporary storage inside our network for mobile apps, Selenium jars, prerun executables, and other assets required by your tests. Storing assets in our network can eliminate network latency problems when sending big files to Sauce. Here's how you use our storage:

* Before tests start, upload the file via our REST API as described below.
* During tests, use a `sauce-storage:` URL for the file, in the following format: `"sauce-storage:your_file_name"`
* Sauce will find the file, download it through our fast internal network, and get your tests started right away.

Please note that our temporary storage retains files for only seven days. We recommend users of this service upload files via our REST API every time their tests are about to run, as part of their build process.

### Upload File

Upload a file to temporary storage.

URL: `https://saucelabs.com/rest/v1/storage/:username/:your_file_name`

Method: `POST`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
-H "Content-Type: application/octet-stream" \
https://saucelabs.com/rest/v1/storage/sauceUsername/test_file_name?overwrite=true \
--data-binary @/path/to/your_file_name
```

This can be scripted in any programming language. Just make sure the HTTP method being used is POST and the `Content-Type` header is correct.

**Optional Query Params:**

By default, the API prevents overwriting files already stored in Sauce temporary storage. The `overwrite=true` query parameter (shown in the example above) can be added to allow overwriting.

### Get Stored Files

Check what files are in your temporary storage.

URL: `https://saucelabs.com/rest/v1/storage/:username`

**Example Request:**
```bash
curl -u sauceUsername:sauceAccessKey \
https://saucelabs.com/rest/v1/storage/sauceUsername
```

## JS Unit Testing

If you already have JS unit tests, running them on Sauce using the REST API is simple. If you are using Karma or want to setup a new test suite, check out our [JS Unit Testing Tutorial](/tutorials/js-unit-testing/).

### Start JS Unit Tests

Start your JavaScript unit tests on as many browsers as you like with a single request.

URL: `https://saucelabs.com/rest/v1/:username/js-tests`

Method: `POST`

**Request Fields:**
* `platforms`(required): an array of [platforms](https://saucelabs.com/platforms)
* `url`(required): should point to the page that hosts your tests.
* `framework`(required): can be `"qunit"`, `"jasmine"`, `"YUI Test"`, `"mocha"`, or `"custom"`.

The `"custom"` framework allows you to display generic test information on the Sauce Labs website. Set `window.global_test_results` in the following format on your unit test page and Sauce will report any failing tests:

```js
window.global_test_results = {
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

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/sauceUsername/js-tests \
-u sauceUsername:sauceAccessKey \
-H 'Content-Type: application/json' \
--data '{
    "platforms": [["Windows 7", "firefox", "30"],
                 ["Linux", "googlechrome", ""]],
    "url": "https://saucelabs.com/test_helpers/front_tests/index.html",
    "framework": "jasmine"}'
```

**Example Response:**
```json
{
    "js tests": [
        "064df78366ea4b25b32f88878c9d7aa4",
        "1e5ed949711545bd952456ac37479ada"
    ]
}
```

Hosting your tests on your LAN or your laptop? You'll need to run [Sauce Connect](/reference/sauce-connect/) to bridge Sauce Labs to your local network. Optional parameters related to Sauce Connect include:

* `tunnel_identifier`: specifies the ID of a specific tunnel when using multiple Sauce Connect tunnels.
* `parent_tunnel`: specifies the username of a parent account whose shared Sauce Connect tunnel your tests should use.

Any other parameters get passed on as [Optional Desired Capabilities](/reference/test-configuration/#webdriver-api) for the selenium server. This means you can set things like: `max-duration`

The default `max-duration` for all JS unit tests is 180 seconds.

### Get JS Unit Test Status

Get the status of your JS unit tests

URL `https://saucelabs.com/rest/v1/:username/js-tests/status`

Method: `POST`

**Request Fields:**
* `js tests`(required): an array of job ids which you would like the status of

**Example Request:**
```bash
curl https://saucelabs.com/rest/v1/sauceUsername/js-tests/status \
-u sauceUsername:sauceAccessKey \
-H 'Content-Type: application/json' \
-d '{"js tests": ["JOB_ID_1","JOB_ID_2"]}'
```

**Example Response:**
```json
{
    "completed": true,
    "js tests": [
        {
            "id": "f5ab14de2d2941dda402fefe79771968",
            "job_id": "4045ce18648a42718c738b55662ccbe0",
            "platform": [
                "Windows 7",
                "firefox",
                "27"
            ],
            "result": {
                "durationSec": 0.004,
                "passed": true,
                "suites": [
                    {
                        "description": "Player",
                        "durationSec": 0.004,
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
                            },
                            {
                                "description": "tells the current song if the user has made it a favorite",
                                "durationSec": 0.001,
                                "failedCount": 0,
                                "passed": true,
                                "passedCount": 1,
                                "skipped": false,
                                "totalCount": 1
                            }
                        ],
                        "suites": [
                            {
                                "description": "when song has been paused",
                                "durationSec": 0.001,
                                "passed": true,
                                "specs": [
                                    {
                                        "description": "should indicate that the song is currently paused",
                                        "durationSec": 0.001,
                                        "failedCount": 0,
                                        "passed": true,
                                        "passedCount": 2,
                                        "skipped": false,
                                        "totalCount": 2
                                    },
                                    {
                                        "description": "should be possible to resume",
                                        "durationSec": 0,
                                        "failedCount": 0,
                                        "passed": true,
                                        "passedCount": 2,
                                        "skipped": false,
                                        "totalCount": 2
                                    }
                                ],
                                "suites": []
                            },
                            {
                                "description": "#resume",
                                "durationSec": 0,
                                "passed": true,
                                "specs": [
                                    {
                                        "description": "should throw an exception if song is already playing",
                                        "durationSec": 0,
                                        "failedCount": 0,
                                        "passed": true,
                                        "passedCount": 1,
                                        "skipped": false,
                                        "totalCount": 1
                                    }
                                ],
                                "suites": []
                            }
                        ]
                    }
                ]
            },
            "url": "https://assets.saucelabs.com/jobs/4045ce18648a42718c738b55662ccbe0"
        }
    ]
}
```

Do that a few times as the tests run, waiting until the response contains `"completed": true` to get the final results.
