{
  title: "REST API",
  description: "Documentation for the Sauce Labs REST API.",
  category: 'Reference',
  index: 2
}

**API Version 1.1**

## Introduction
The Sauce Labs REST API allows customers to retrieve information about Sauce Labs resources programmatically over HTTP using JSON. Customers can retrieve job information, video replays of tests, Selenium logs of tests, start and stop SauceTunnels, and so on. 

All headers **must** have `Content-Type` set to `application/json`.

### Sauce API libraries

<ul class="list-inline inline-container">
  <li><a href="https://github.com/saucelabs/saucerest-java"><img src="/images/tutorials/Java.png" alt="Java"></a></li>
  <li><a href="https://github.com/holidayextras/node-saucelabs"><img src="/images/tutorials/nodejs.png" alt="Node.js"></a></li>
  <li><a href="https://github.com/saucelabs/sauce_whisk"><img src="/images/tutorials/Ruby_logo.png" alt=""></a></li>
  <li><a href="https://github.com/jlipps/sausage"><img src="/images/tutorials/PHP-logo.png" alt=""></a></li>
  
</ul>

## Authentication

#### Option One:

Sauce [REST][1] uses [HTTP Basic Authentication][5] base64 encoded _Username_ and _API Access Key_. Send the 'Authorization: Basic ...' header to authenticate your requests.

Example:


    $ curl --basic -u 'demo-user:access-key' https://saucelabs.com/rest/v1/demo-user/jobs

#### Option Two:

Alternatively, you can place the user name and password directly in the URLs.

Example:


    $ curl https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/jobs

Login a subaccount to saucelabs.com and retrieve login cookies:


    $ curl -D - -X POST https://demo-user:access-key@saucelabs.com/rest/v1/users/subaccount-username/login -H 'Content-Type: application/json' -d '{"password":"subaccount-password"}'

Result:


    HTTP/1.1 200 OK
      Content-Type: application/json
      Set-cookie: saucelabs-login=...
      Set-cookie: ...

      {"cookies": ["saucelabs-login=...", ...]}

## Provisioning

Access account details.

(Includes the number of minutes available.)


    $ curl https://demo-user:access-key@saucelabs.com/rest/v1/users/demo-user

Result:


    {"access_key": "access-key", "minutes": "200", "manual_minutes": "infinite", "mac_minutes": "100", "mac_manual_minutes": "infinite", id": "demo-user"}

Check account limits.


    $ curl https://demo-user:access-key@saucelabs.com/rest/users/v1/demo-user/concurrency

Result:


    {
      "timestamp": 1397657955659,
      "concurrency": 10
    }

Create a new subaccount.


    $ curl -X POST https://demo-user:access-key@saucelabs.com/rest/v1/users/demo-user -H 'Content-Type: application/json' -d '{"username":"subaccount-username", "password":"subaccount-password", "name":"subaccount-name", "email":"subaccount-email-address"}'

Result:


    {"access_key": "subaccount-api-key", "minutes": 200, "id": "new-subaccount-username"}

## Usage

Access current account activity.

Returns active job counts broken down by job status and subaccount.


    $ curl https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/activity

Result:


    {"subaccounts": {"demo-user": {"in progress": 25, "all": 30, "queued": 5}, "demo-subaccount": {"in progress": 5, "all": 5, "queued"0}}, "totals": {"in progress": 30, "all": 35, "queued": 5}}

Access historical account usage data.

Optional query parameters 'start' and 'end' in YYYY-MM-DD format. Returns array of ['YYYY-MM-DD', [, ]] pairs for each day that had activity )


    $ curl https://demo-user:access-key@saucelabs.com/rest/v1/users/demo-user/usage

Result:


    {"usage": [["2011-3-18", [2, 38]], ["2011-3-31", [5, 5533]], ["2011-4-1", [7, 5076]]], "username": "demo-user"}

## Jobs

All URL's listed below are assumed to be against the base url of


    https://username:access-key@saucelabs.com

Where _username_ is your Sauce Labs username, _access-key_ is the Sauce Labs access key you can find on your accounts page.

### Jobs

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
      * [GET] limit -&gt; displays the specified number of jobs, instead of truncating the list at the default 100.
      * [GET] full -&gt; forces full job information to be returned, rather than just IDs.
      * [GET] skip -&gt; skips the specified number of jobs.
      * [GET] from -&gt; returns jobs since the specified time (in epoch time, calculated from UTC).
      * [GET] to -&gt; returns jobs up until the specified time (in epoch time, calculated from UTC).
      * [GET] format -&gt; returns jobs is specified format. Currently we support 'json' and 'csv' and the default one is 'json'
    * Examples:

                $ curl https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/jobs
        $ curl https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/jobs?limit=200
        $ curl https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/jobs?full=true
        $ curl https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/jobs?skip=20
        $ curl https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/jobs?format=csv
        $ curl https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/jobs?from=1357747500&amp;to=1357748700

  * **Show** \- Show the full information for a job given its ID.
  * **Create** \- Jobs cannot be created via API. They must be created/started via the selenium client, such as SauceIDE, or the Selenium bindings for the language of your choice.
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

                $ curl -X PUT https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/jobs/01230123-example-id-1234 -H "Content-Type: application/json" -d '{"tags":["test","example","taggable"],"public":true,"name":"changed-job-name","passed": false, "custom-data":{"error":"step 17 failed"}}'

  * **Delete** \- Removes the job from the system with all the linked assets.
  * **Stop** \- Terminates a running job.
  * **List Job Assets** \- Get a details about the static assets collected for a specific job.
  * **Download Job Assets** \- You can download every asset created after your test runs on Sauce through our REST API. These include the video recording, Selenium log, and screenshots taken on crucial steps.
  * **Remove Job Assets** \- You can delete all the data gathered during test run from our servers. That includes the video recording, Selenium log, and all the screenshots.

## Tunnels

Tunnels are used by [Sauce Connect][7] to redirect traffic for a given domain to a server on your internal network. They are unique to users and will not affect any other users. For more information, please read our [Sauce Connect documentation][7]

  * **Attributes:**
    * 'id': [string] Tunnel ID
    * 'owner': [string] Tunnel owner
    * 'status': [string] Tunnel status
    * 'host': [string] Public address of the tunnel
    * 'creation_time': [integer]
  * **List** \- Retrieves all running tunnels for a given user.
  * **Show** \- Show the full information for a tunnel given its ID.
  * **Delete** \- Shuts down a tunnel given its ID.

## Information

Informational REST commands _do not_ require the username to be in the base URL. They are publicly available resources.

  * **Status** \- Returns the current status of Sauce Labs' services.
    * Relative URL: /info/status
    * Method: GET
    * Authentication: none
    * Response fields: none
    * Parameters: none
    * Examples:

                $ curl -X GET http://saucelabs.com/rest/v1/info/status

Result:

                {"wait_time": 0.5, "service_operational": true, "status_message": "Basic service status checks passed."}

  * **Browsers** \- Returns an array of strings corresponding to all the browsers currently supported on Sauce Labs. (Choose the termination that defines which list you need, bearing in mind that Selenium 1 [RC] and 2 [WebDriver] are compatible with different browser/OS combinations.)
  * **Counter** \- Returns the number of test executed so far on Sauce Labs.

## Partners

This section is for use only by Sauce Labs partner accounts. If you use these commands with a standard account, they may cause unexpected behavior. To add and manage sub-accounts, head to [your sub-accounts page][8].

Create a new subaccount, specifying a Sauce Labs service plan.


    $ curl -X POST https://demo-user:access-key@saucelabs.com/rest/v1/users/demo-user -H 'Content-Type: application/json' -d '{"username":"subaccount-username", "password":"subaccount-password", "name":"sub-account-name", "email":"subaccount-email-address", "plan":"free"}'

**Available plans: ** 'free', 'small', 'team', 'com', 'complus'

Result:


    {"access_key": "subaccount-api-key", "minutes": 200, "id": "new-subaccount-username", "plan": "free"}

Update a subaccount Sauce Labs service plan.


    $ curl -X POST https://demo-user:access-key@saucelabs.com/rest/v1/users/demo-user/subscription -H 'Content-Type: application/json' -d '{"plan":"small"}'

Unsubscribe a subaccount from it's Sauce Labs service plan.


    $ curl -X DELETE https://demo-user:access-key@saucelabs.com/rest/v1/demo-user/subscription

## Temporary Storage

Sauce Labs provides temporary storage inside our network for mobile apps, Selenium jars, prerun executables, and other assets required by your tests. Storing assets in our network can eliminate network latency problems when sending big files to Sauce. Here's how you use our storage:

  * \- Before tests start, upload the file via our REST API as described below.
  * \- During tests, use a special URL for the file, in the following format: `"sauce-storage:your_file_name"`
  * \- Sauce will find the file, download it through our fast internal network and get your tests started right away

To upload the file via our REST API:


    curl -u demo-user:access-key -X POST "http://saucelabs.com/rest/v1/storage/demo-user/your_file_name?overwrite=true" -H "Content-Type: application/octet-stream" --data-binary @/path/to/your_file_name

The overwrite=true URL parameter allows files already stored in the Sauce network to be overwritten. It can be removed if you prefer to prevent overwriting.

This can be scripted in any programming language. Just make sure the HTTP method being used is POST and the Content-Type header is correct.

Please note that our temporary storage retains files for only 24 hours. We recommend users of this service upload files via our REST API every time their tests are about to run, as part of their build process.

## JS Unit Testing

If you already have JS unit tests, running them on Sauce using the REST API is simple.

Start your tests on as many browsers as you like with a single request:


    curl -X POST https://saucelabs.com/rest/v1/$SAUCE_USERNAME/js-tests \
        -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY \
        -H 'Content-Type: application/json' \
        --data '{
            "platforms": [["Windows 7", "firefox", "20"],
                          ["Linux", "googlechrome", ""]],
            "url": "https://saucelabs.com/test_helpers/front_tests/index.html",
            "framework": "jasmine"}'


`url` should point to the page that hosts your tests.
`framework` can be `"qunit"`, `"jasmine"`, `"YUI Test"`, `"mocha"`, or `"custom"`.

The `"custom"` framework allows you to display generic test information on the Sauce Labs website. Set `window.global_test_results` on the javascript on your unit test page to an object that looks like the following and Sauce will report any failing tests: `


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


`

Hosting your tests on your LAN or your laptop? You'll need to run [Sauce Connect][9] to bridge Sauce Labs to your local network. Optional parameters related to Sauce Connect include:

`tunnel_identifier` \- specifies the ID of a specific tunnel when using multiple Sauce Connect tunnels. `parent_tunnel` \- specifies the username of a parent account whose shared Sauce Connect tunnel your tests should use.

The response will look something like this:


    {"js tests": ["064df78366ea4b25b32f88878c9d7aa4", "1e5ed949711545bd952456ac37479ada"]}

To check the result, POST that response back to `.../js-tests/status` verbatim, like so:


    curl -X POST https://saucelabs.com/rest/v1/$SAUCE_USERNAME/js-tests/status \
        -u $SAUCE_USERNAME:$SAUCE_ACCESS_KEY \
        -H 'Content-Type: application/json' \
        --data '{"js tests": ["064df78366ea4b25b32f88878c9d7aa4", "1e5ed949711545bd952456ac37479ada"]}'

Do that a few times as the tests run, waiting until the response contains `"completed": true`.

Once the tests are completed, the result will look something like this:


    {"completed": true,
     "js tests": [
      {"id": "064df78366ea4b25b32f88878c9d7aa4",
       "platform": ["Windows 8", "internet explorer", "10"],
       "url": "http://saucelabs.com/jobs/ff737d47e03e47bfb45100a45e4b5ca5",
       "result": {"durationSec": 0.005,
                  "passed": true,
                  "suites": [
                    {"description": "Player",
                     "durationSec": 0.005,
                     "passed": true,
                     "specs": [
                       {"description": "should be able to play a Song",
                        "durationSec": 0.002,
                        "failedCount": 0,
                        "passed": true,
                        "passedCount": 2,
                        "skipped": false,
                        "totalCount": 2},
    ...

## Bugs

Interacting with Jobs bug tracking system

Get list of available bug types


    $ curl https://saucelabs.com/rest/v1/bugs/types

Result:


    [{"id": "bug-type-example-id", "name": "Bug-example-name", "description": "Bug example description"}]

Get description of each field for a particular bug type


    $ curl https://saucelabs.com/rest/v1/bugs/types/bug-type-example-id-1234

Result:


    [{"type": "string", "name": "Field-name", "id": "Field-id"}, {"type": "int", "name": "Field-name", "id": "Field-id"}, ...]

Get detailed info for a particular bug


    $ curl https://demo-user:access-key@saucelabs.com/rest/v1/bugs/detail/01230123-example-id-1234

Result:


    {"Description": "Description of really nasty bug", "Title": "What a nasty bug", "ScreenshotEmbedURL": "https://saucelabs.com/jobs/01230123-example-id-1234/0001screenshot.png?auth=secret-auth-token", "CreationTime": 1234567890, "VideoEmbedURL": "https://saucelabs.com/bugs/01230123-example-id-1234?show_video=1&amp;auth=secret-auth-token", "Job": "https://saucelabs.com/jobs/01230123-example-id-1234?auth=secret-auth-token", "BugEmbedURL": "https://saucelabs.com/bugs/01230123-example-id-1234?auth=secret-auth-token", "OS": "Linux", "Browser": "android 4."}

Get detailed info for a specified list of bugs


    $ curl -G https://demo-user:access-key@saucelabs.com/rest/v1/bugs/query/ --data-urlencode 'ids=["01230123-example-id-1234", "0123401234-example-id-12345"]'

Result:


    List of JSON objects containing detailed info on each queried bug id

Update bug id '01230123-example-id-1234' with specified key-value pairs


    $ curl -G https://demo-user:access-key@saucelabs.com/rest/v1/bugs/update/01230123-example-id-1234 --data-urlencode 'update={"Property-name-1": "Property-Value-1", "Property-name-2": "Property-Value-2"}

**Valid keys: **Only following bug properties can be modified with the API: 'Title', 'Description'.

Result:


    After successful command execution API will return {"status": "success"}

   [1]: http://en.wikipedia.org/wiki/Representational_State_Transfer
   [2]: http://en.wikipedia.org/wiki/HTTP
   [3]: http://en.wikipedia.org/wiki/JSON
   [4]: http://en.wikipedia.org/wiki/Content-Type
   [5]: http://en.wikipedia.org/wiki/Basic_access_authentication
   [6]: https://saucelabs.com/docs/additional-config#public-jobs
   [7]: https://saucelabs.com/docs/sauce-connect
   [8]: https://saucelabs.com/sub-accounts
   [9]: https://saucelabs.com/docs/connect
  