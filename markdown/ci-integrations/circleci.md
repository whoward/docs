{
  title: "CircleCI",
  description: "Run browser tests through Sauce Labs using CircleCI",
  category: "CI Integrations",
  index: 4,
  image: "/images/ci-integrations/circleci.png",
}

This article explains how to run your browser tests through Sauce Labs as part of your continuous
integration process on [CircleCI](https://circleci.com). CircleCI intelligently infers certain
test steps, such as running `npm test` for Node.js projects, but additional test steps such
as using Sauce Labs can be easily added with the addition of a `circle.yml` file. This article
will walk through the entries in a `circle.yml` file that are necessary to use Sauce Labs.

##Sauce Connect

You can run Selenium WebDriver tests with Sauce Labs on CircleCI using Sauce Labs' secure tunnel,
[Sauce Connect](https://docs.saucelabs.com/reference/sauce-connect/). Sauce Connect allows you
to run a test server within the CircleCI build container and expose it (using a URL like `localhost:8080`)
to Sauce Labs' browsers. If you run your browser tests after deploying to a publicly accessible
staging environment, then you can use Sauce Labs in the usual way without worrying about Sauce Connect.

###Downloading

The following custom `dependencies` entry in a `circle.yml` file will download Sauce Connect
onto the build container:

```yaml
dependencies:
  post:
    - wget https://saucelabs.com/downloads/sc-latest-linux.tar.gz
    - tar -xzf sc-latest-linux.tar.gz
```

###Running

The following test commands establish a secure tunnel with Sauce Connect. The `--readyfile/-f` option is
used to wait for the tunnel to be open before proceeding. Note that environment variables are used to
keep sensitive information out of the `circle.yml` file. See the [CircleCI documentation on environment
variables](https://circleci.com/docs/environment-variables) for more information.

```yaml
test:
  override:
    - ./bin/sc -u $SAUCE_USERNAME -k $SAUCE_ACCESS_KEY -f ~/sc_ready:
        background: true
        pwd: sc-*-linux
    # Wait for tunnel to be ready
    - while [ ! -e ~/sc_ready ]; do sleep 1; done
```

##Complete Example

Here is a complete example of a `circle.yml` file for a Python project that runs browser tests with
Sauce Labs. Note that in this example, the browser tests override any other tests CircleCI would
have run by default. If instead you want to run browser tests after CircleCI's inferred unit test
commands, then you can use the `post:` directive in the `test` section instead of `override:`.

```yaml
dependencies:
  post:
    - wget https://saucelabs.com/downloads/sc-latest-linux.tar.gz
    - tar -xzf sc-latest-linux.tar.gz

test:
  override:
    - ./bin/sc -u $SAUCE_USERNAME -k $SAUCE_ACCESS_KEY -f ~/sc_ready:
        background: true
        pwd: sc-*-linux
    # Wait for tunnel to be ready
    - while [ ! -e ~/sc_ready ]; do sleep 1; done
    - python -m hello.hello_app:
        background: true
    # Wait for app to be ready
    - curl --retry 10 --retry-delay 2 -v http://localhost:5000
    # Run selenium tests
    - nosetests
```

To see the complete example project that goes along with this example, see
[circleci/sauce-connect](https://github.com/circleci/sauce-connect) on GitHub. Also
see the [CircleCI docs](https://circleci.com/docs) for more information about working
with CircleCI.