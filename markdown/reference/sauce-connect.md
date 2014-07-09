{
  title: "Sauce Connect",
  description: "Documentation for Sauce Connect",
  category: "Reference",
  index: 0
}

Sauce Connect is a secure tunneling app which allows you to execute tests securely when testing behind firewalls via a secure connection between Sauce Labs’ client cloud and your environment.

##  When Should I Use Sauce Connect?

You should use Sauce Connect whenever you’re testing an app behind a firewall. Sauce Connect is **not** required to execute scripts on Sauce.

You can also use Sauce Connect:

- as an alternative to whitelisting
- as a means of filtering traffic in your records (e.g. for Google Analytics)
- as a means of monitoring network traffic
- as a way to stabilize network connections (detecting/re-sending dropped packets)

##  Basic Setup

1. Get the latest Sauce Connect:
<ul>
<li><a href="https://saucelabs.com/downloads/sc-4.3-osx.zip"><i class="fa fa-apple"></i> Download Sauce Connect v4.3 for OS X</a> SHA1 checksum: ae017d7d2bfe63989b22f8f913ee4de010b221d7<br>
</li><li><a href="https://saucelabs.com/downloads/sc-4.3-win32.zip"><i class="fa fa-windows"></i> Download Sauce Connect v4.3 for Windows</a> SHA1 checksum: ca0d7a636df22d3191a7d71bf2707e09767a35ed<br>
</li><li><a href="https://saucelabs.com/downloads/sc-4.3-linux.tar.gz"><i class="fa fa-linux"></i> Download Sauce Connect v4.3 for Linux</a> SHA1 checksum: 3bd70442329a240869eb1e41bc0ab2a9d885face<br>
</li><li>(If you're looking for Sauce Connect v3, you can download it <a href="https://saucelabs.com/downloads/Sauce-Connect-3.1-r32.zip">here</a>.)<br>
</li>
</ul>
2. Open outbound port 443 (or configure Sauce Connect with a proxy that can reach saucelabs.com, using the `--proxy` or `--pac` [command line options](#advanced-configuration)).
3. After extracting, go to the install directory and run:

```bash
bin/sc -u sauceUsername -k sauceAccessKey
```
When you see "connected", you are ready to go!  We also recommend reading our take on [security best practices](http://sauceio.com/index.php/2011/09/security-through-purity/).

## About Sauce Connect

###  System Requirements

System requirements vary depending on the number of parallel tests you plan to run. Here are some samples based on simultaneous test volume:

| Machine Type | RAM  | Processor | Parallel Tests |
| ------------ | :-------------:       | :-------------: | :-------------: |
| Local | 4GB | 4GHz | 10 |
| Dedicated | 8GB  | 4GHz | 100 |

For increased reliability and security, use a dedicated server. You may need to up your open file limit if your parallel test count is high (`ulimit -n 8192`).

###  How is Sauce Connect Secured?

Though starting up a tunnel using Sauce Connect may take a few seconds, our tunneling method allows for the highest possible security. We spin up a secure tunnel sandbox environment for each tunnel connection in order to provide greater tunnel security and isolation from other customers.

Data transmitted by Sauce Connect is encrypted through industry-standard TLS, using the AES-256 cipher. Sauce Connect also uses a caching web proxy to minimize data transfer. To read more about security on Sauce, read our [security white paper](http://info.saucelabs.com/SecurityWhitepaperDownload_SecurityWhitepaperLP.html).

Within your infrastructure, Sauce Connect needs access to the application under test, but can be firewalled from the rest of your internal network. We recommend running Sauce Connect in a firewall DMZ, on a dedicated machine, and setting up firewall rules to restrict access from that DMZ to your internal network.


![How is Sauce Secured](/images/reference/sauce-connect/sc.png)

###  Setup Process

During startup, Sauce Connect issues a series of HTTPS requests to the Sauce Labs REST API. These are outbound connections to saucelabs.com on port 443. Using the REST API, Sauce Connect checks for updates and other running Sauce Connect sessions, and ultimately launches a remote tunnel endpoint VM. Once the VM is started, a tunnel connection is established to a makiXXXXX.miso.saucelabs.com address on port 443, and all traffic between Sauce Labs and Sauce Connect is then multiplexed over this single encrypted TLS connection.

![SetupProcess](/images/reference/sauce-connect/sc-setup-process.png)

1. Sauce Connect makes HTTPS REST API calls to saucelabs.com:443 using the username and access key provided when starting Sauce Connect.
2. Sauce Labs creates a dedicated virtual machine which will serve as the endpoint of the tunnel connection created by Sauce Connect.
3. Sauce Labs responds with the unique ID of the virtual machine created in step 2.
4. Sauce Connect establishes a TLS connection directly to the dedicated virtual machine created in step 2. (makiXXXXX.miso.saucelabs.com).
5. All test traffic is multiplexed over the tunnel connection established in step 4.

###  Teardown Process

Once Sauce Connect is terminated (typically via ctrl-c), a call will be made from Sauce Connect to the REST API with instructions to terminate the tunnel VM. Sauce Connect will continue to poll the REST API until the tunnel VM has been halted and deleted.

###  System Requirements

These vary depending on the number of parallel tests you plan to run. Here are some samples based on simultaneous test volume:

| Machine Type | RAM  | Processor | Parallel Tests |
| ------------ | :-------------:       | :-------------: | :-------------: |
| Local | 4GB | 4GHz | 10 |
| Dedicated | 8GB  | 4GHz | 100 |

For increased reliability and security, use a dedicated server. You may need to up your open file limit if your parallel test count is high (`ulimit -n 8192`).

##  Advanced Configuration

### Command-line Options

The `sc` command line program accepts the following parameters:



    Usage: ./sc
    -u, --user <username>           The environment variable
                                    SAUCE_USERNAME can also be used.
    -k, --api-key <api-key>         The environment variable
                                    SAUCE_ACCESS_KEY can also be used.
    -B, --no-ssl-bump-domains       Comma-separated list of domains.
                                    Requests whose host matches one of
                                    these will not be SSL re-encrypted.
    -D, --direct-domains <...>      Comma-separated list of domains.
                                    Requests whose host matches one of
                                    these will be relayed directly
                                    through the internet, instead of
                                    through the tunnel.
    -t, --tunnel-domains <...>      Inverse of '--direct-domains'.
                                    Only requests for domains in this 
                                    list will be sent through the 
                                    tunnel.
                                    Overrides '--direct-domains'.
    -v, --verbose                   Enable verbose debugging.
    -F, --fast-fail-regexps         Comma-separated list of regular
                                    expressions. Requests with URLs 
                                    matching one of these will get 
                                    dropped instantly and will not go 
                                    through the tunnel.
    -i, --tunnel-identifier <id>    Assign <id> to this Sauce Connect 
                                    instance. Future jobs will use this 
                                    tunnel only when explicitly 
                                    specified by the 'tunnel-
                                    identifier' desired capability in a 
                                    Selenium client.
    -l, --logfile <file>
    -P, --se-port <port>            Port on which Sauce Connect's
                                    Selenium relay will listen for
                                    requests. Selenium commands
                                    reaching Sauce Connect on this port
                                    will be relayed to Sauce Labs 
                                    securely and reliably through Sauce 
                                    Connect's tunnel. Defaults to 4445.
    -p, --proxy <host:port>         Proxy host and port that Sauce
                                    Connect should use to connect
                                    to the Sauce Labs cloud.
    -w, --proxy-userpwd <user:pwd>  Username and password required to
                                    access the proxy configured with -p.
        --pac <url>                 Proxy autoconfiguration. Can be a
                                    http(s) or local file:// URL.
    -T, --proxy-tunnel              Use the proxy configured with -p
                                    for the tunnel connection.
    -s, --shared-tunnel             Let sub-accounts of the tunnel
                                    owner use the tunnel if requested.
    -x, --rest-url <arg>            Advanced feature: Connect to Sauce
                                    REST API at alternative URL. Use
                                    only if directed to do so by Sauce
                                    Labs support.
    -f, --readyfile                 File that will be touched to signal
                                    when tunnel is ready.
    -a, --auth <host:port:user:pwd> Perform basic authentication when
                                    a URL on <host:port> asks for a
                                    username and password. This option
                                    can be used multiple times.
    -z, --log-stats <seconds>       Log statistics about HTTP traffic
                                    every <seconds>. Information 
                                    includes bytes transmitted, 
                                    requests made, and responses 
                                    received.
        --max-logsize <bytes>       Rotate logfile after reaching
                                    <bytes> size. Disabled by default.
        --doctor                    Perform checks to detect possible
                                    misconfiguration or problems.
    -h, --help                      Display this help text.

### Proxy Configuration

#### Automatic

As of Sauce Connect 4.4, proxies will be autoconfigured based on the running system's settings.

On **Windows**, Internet Explorer proxy settings will be checked as well as system-wide proxy settings set via Control Panel.

On **Mac OS X**, Sauce Connect will use the proxy set in Preferences / Network.  We support both the proxy and the PAC settings.

On **Linux**, Sauce Connect looks for the following variables, in order: `http_proxy`, `HTTP_PROXY`, `all_proxy`, and `ALL_PROXY`. They can be in the form `http://host.name:port` or just `host.name:port`.

#### Manual

If auto-configuration fails, or the settings need to be overridden, there are a few command-line options that can be used to [configure proxies manually](#command-line-options): `-p, --proxy <host:port>`, `-w, --proxy-userpwd <user:pwd>`, `-T, --proxy-tunnel`, and `--pac <url>`.

###  Managing Multiple Tunnels

In its default mode of execution, one Sauce Connect instance will suffice all your needs and will require no efforts to make cloud browsers driven by your tests navigate through the tunnel.

After starting Sauce Connect, all traffic from jobs under the same account will use the tunnel automatically and transparently. After the tunnel is stopped, jobs will simply attempt to find your servers through the open internet.

#### Using Tunnel Identifiers

If you still believe you need multiple tunnels, you will need tunnel identifiers.

Using identified tunnels, you can start multiple instances of Sauce Connect that will not collide with each other and will not make your tests' traffic automatically tunnel through.  This allows you to test different localhost servers or access different networks from different tests (a common requirement when running tests on TravisCI.)

To use this feature, simply start Sauce Connect using the `--tunnel-identifier` flag (or `-i`) and provide your own unique identifier string. Once the tunnel is up and running, any tests that you want going through this tunnel will need to provide the correct identifier using the `tunnel-identifier` desired capability.

#### On the Same Machine

Please note that in order to run multiple Sauce Connect instances on the same machine, it's necessary to provide additional flags to configure independent log files, pid files, and ports for each instant. Here's an example of how to configure all of these settings for a second instance:


```bash
sc --pidfile /tmp/sc2.pid --logfile /tmp/sc2.log --scproxy-port 29999 --se-port 4446 -i my-tun2
```

##  FAQs

###  Can I reuse a tunnel between multiple accounts?

Tunnels started by an account can be reused by its sub-accounts. To reuse a tunnel, start Sauce Connect with the special --shared-tunnel parameter from the main account in your account tree. For example:

```bash
sc --shared-tunnel parentAccount parentAccountsAccessKey
```
Once the tunnel is running, provide the special "parent-tunnel" desired capability on a per-job basis. The value of this capability should be the username of the parent account that owns the shared Sauce Connect tunnel as a string. Here's an example (this test should can run using Auth credentials for any sub-account of "parentAccount"):

```python
capabilities['parent-tunnel'] = "parentAccount"
```
That's it! We'll take care of the rest by making the jobs that request this capability route all their traffic through the tunnel created using your parent account (parentAccount, following our example).

### What firewall rules do I need? 

Sauce Connect needs to make outbound connections to saucelabs.com and \*.miso.saucelabs.com on port 443 for the REST API and the primary tunnel connection to the Sauce cloud. It can also optionally make these connections through a web proxy; see the `--proxy`, `--pac`, and `--proxy-tunnel` command line options.

### I have verbose logging on, but I'm not seeing anything in stdout.  What gives?

Output from the `-v` flag is sent to the Sauce Connect [log file](#logging) rather than stdout.

### How can I periodically restart Sauce Connect?

Sauce Connect handles a lot of traffic for heavy testers. Here is one way to keep it 'fresh' to avoid leakages and freezes.
First write a loop that will restart Sauce Connect every time it gets killed or crashes:

```bash
while :; do killall sc; sleep 30; sc -u $SAUCE_USERNAME -k $SAUCE_ACCESS_KEY; done
```
Then, write a cron task that will kill Sauce Connect on a regular basis:

```bash
crontab -e 00 00 * * * killall sc
```

This will kill Sauce Connect every day at 12am, but can be modified to behave differently depending on your requirements.  

### Can I access applications on localhost?

When using Sauce Connect, local web apps running on commonly-used ports are available to test at localhost URLs, just as if the Sauce Labs cloud were your local machine. Easy!

However, because an additional proxy is required for localhost URLs, tests may perform better when using a locally-defined domain name (which can be set in your [hosts file](http://en.wikipedia.org/wiki/Hosts_(file))) rather than localhost. Using a locally-defined domain name also allows access to applications on any port.

Sauce Connect proxies these commonly-used localhost ports:

> 80, 443, 888, 2000, 2001, 2020, 2109, 2222, 2310, 3000, 3001, 3030, 3210, 3333, 4000, 4001, 4040, 4321, 4502, 4503, 4567, 5000, 5001, 5050, 5555, 5432, 6000, 6001, 6060, 6666, 6543, 7000, 7070, 7774, 7777, 8000, 8001, 8003, 8031, 8080, 8081, 8765, 8777, 8888, 9000, 9001, 9080, 9090, 9876, 9877, 9999, 49221, 55001

Do you need a different port? [Please let us know!](http://support.saucelabs.com/anonymous_requests/new) We do our best to support ports that will be useful for many customers, such as those used by popular frameworks.

### How can I improve performance?

There are a few Sauce Connect specific [command line options](#command-line-options) that can help.  These include `-D, --direct-domains`, `-t, --tunnel-domains`, and `-F, --fast-fail-regexps`.  These allow for careful curating of which traffic will go through the tunnel and which will go directly to the internet.

A common use case for this is for users who only need their requests to their local development environment to go through Sauce Connect, with external resources being pulled as usual.

##	Troubleshooting Sauce Connect

### Logging

By default, Sauce Connect generates log messages to your local operating system's temporary folder.  On **Linux** / **Mac OS X** this is usually `/tmp`; on **Windows**, it varies by individual release.  You can also specify a specific location for the output using the `-l` [command line option](#command-line-options). 

You can enable verbose logging with the `-v` flag. Verbose output will be sent to the Sauce Connect log file rather than standard out.

###	Connectivity Considerations
- Is there a firewall in place between the machine running Sauce Connect and Sauce Labs (\*.saucelabs.com:443)? You may need to allow access in your firewall rules, or configure Sauce Connect to use a proxy. Sauce Connect needs to establish outbound connections to saucelabs.com (67.23.20.87) on port 443, and to one of many hosts maikiXXXXX.miso.saucelabs.com IPs (162.222.76.0/21), also on port 443. It can make these connections directly, or can be configured to use an HTTP proxy with the `--proxy`, `--pac` and `--proxy-tunnel` command line options.
- Is a proxy server required to connect to route traffic from saucelabs.com to an internal site? If so you may need to configure Sauce Connect with the `--proxy` or `--pac` command line options.

###	Checking Network Connectivity to Sauce Labs
Make sure that saucelabs.com is accessible from the machine running Sauce Connect. This can be tested issuing a ping, telnet or cURL command to sacuelabs.com from the machine's command line interface. If any of these commands fail please work with your internal network team to resolve them.
```bash
ping saucelabs.com
```
This command should return an IP address of 67.23.20.87
```bash
telnet saucelabs.com 443
```
This command should return a status message of "connected to saucelabs.com"
```bash
curl -v https://saucelabs.com/
```

###	For More Help

If you need additional help, get in touch at help@saucelabs.com. To provide our support team with additional information, please add the `-v` and `-l sc.log` options to your Sauce Connect command line, reproduce the problem, and attach the resulting log file (called `sc.log`) to your support request.

For more advance troubleshooting steps please refer to http://support.saucelabs.com/entries/22485469-Sauce-Connect-Troubleshooting-Tips
  
