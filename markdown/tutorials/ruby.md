{
  title: "Ruby",
  description: "How to run Selenium tests on Sauce Labs using ruby",
  category: 'Tutorials',
  index: 1,
  image: "/images/tutorials/ruby.png"
}

In this tutorial we will write some Selenium tests in Ruby and run them on Sauce Labs' browser cloud. If you are using a Ruby test framework like Capybara or Rspec you can skip to the ["Ruby Selenium Testing with a Framework"](#ruby-selenium-testing-with-a-framework) section below. Otherwise follow along to get a simple Ruby Selenium test running on Sauce Labs.

## Getting Started

You will need to have Ruby installed on your system. It's assumed that you already have a text editor such as [Sublime](http://www.sublimetext.com), [TextMate](http://macromates.com/), [VIM](http://www.vim.org/), etc set up to run Ruby programs.

If you already have Ruby installed head down to the [Installing The Ruby Webdriver Bindings](#installing-the-ruby-webdriver-bindings) section, otherwise follow these steps:

### Installing Ruby

Install latest version of Ruby using the [Ruby Version Manager (RVM)](https://rvm.io/) by typing the command below in the terminal window:

```bash
curl -L https://get.rvm.io | bash -s stable
```

Now that RVM is installed, we're ready to install latest version of Ruby. Type,

```bash
rvm install 2.1.1
```

Your system may already come with a pre-installed version of Ruby. For the purposes of this guide, we will be using the Ruby 2.1.1 and it can be done by running this command:

```bash
rvm --default use 2.1.1
```

RVM creates a new completely separate gem directory of each version of Ruby. If you already have a previous installation of Ruby, you can update [Rubygems](https://rubygems.org/) by running the following command:

```bash
gem update --system
```

### Installing The Ruby Webdriver Bindings

The only dependency required to run a Ruby Selenium test is the [`selenium-webdriver`](http://docs.seleniumhq.org/projects/webdriver/) gem which can be installed by running the following command:

```bash
gem install selenium-webdriver
```

That's it! We are ready to run our first Selenium test!

### Simple Ruby Selenium Test Locally

Let's start by running a test on our local machine first. In the text editor of your choice copy the piece of code below into a file called `test.rb`. (Comments are optional, however they provide good reading to get reference to what the test is doing)

```ruby
# Importing the relevant classes to run our simple selenium test

require 'rubygems'
require 'selenium-webdriver'

# Passing the capabilities for the Selenium Remote Webdriver
# in order to run the test on firefox.

# We could pass chrome, internet explorer, opera, etc as well and the test 
# will run on that browser as long as it is installed on your local machine.

caps = Selenium::WebDriver::Remote::Capabilities.new
caps["browserName"] = "firefox"
caps["version"] = "35"
caps["name"] = "Local Selenium Test"

# Since we are running the test locally, we need to point 
# the driver object to the port of the Selenium Webdriver

driver = Selenium::WebDriver.for(:remote,
:url => "http://localhost:4444/wd/hub/",
:desired_capabilities => caps)

# Now to execute the test by navigating to www.google.com
# and searching for 'Sauce Labs'

driver.navigate.to "https://www.google.com/"
driver.find_element(:id, "gbqfq").click
driver.find_element(:id, "gbqfq").clear
driver.find_element(:id, "gbqfq").send_keys "Sauce Labs"
driver.quit

```

Then run the test with the following command:

```bash
ruby test.rb
```

You should see a Firefox window pop up with all the steps outlined above being executed. Done!

### Simple Ruby Selenium Test On Sauce Labs

Time to run our simple test on Sauce Labs! Also this time around, we will be able to execute the test on the browser & OS combinations of our choice, instead of being limited by the combinations we have locally.

Let's perform 2 small steps and you will be ready to test on Sauce!:

**1st Step** - Adding your Sauce Labs credentials, i.e. Username and Access key, as environment variables.

On Unix / Linux, open the system profile file by typing

```bash
echo "export SAUCE_USERNAME=sauceUsername
export SAUCE_ACCESS_KEY=sauceAccessKey" >> ~/.bash_profile && source ~/.bash_profile
```


On Windows, open your environment variables settings window (Instructions [here](http://www.itechtalk.com/thread3595.html)) and set the following variables

```
Name: SAUCE_USERNAME
Value: sauceUsername

Name: SAUCE_ACCESS_KEY
Value: sauceAccessKey
```

**2nd Step** - Previously we had pointed the Selenium Webdriver to our localhost. Since we want to implement the test on Sauce, we will make a slight change to the driver url in the `test.rb` file as shown in the code below:

```ruby
require 'rubygems'
require 'selenium-webdriver'

# Let's run our test on Internet Explorer 
# on the latest version of Windows and name our session too

caps = Selenium::WebDriver::Remote::Capabilities.new
caps["browserName"] = "internet explorer"
caps["version"] = "11"
caps["platform"] = "Windows 8.1"
caps["name"] = "Selenium on Sauce on IE11W8.1"

# Change to the driver url as mentioned

driver = Selenium::WebDriver.for(:remote,
:url => "http://sauceUsername:sauceAccesskey@ondemand.saucelabs.com:80/wd/hub",
:desired_capabilities => caps)

driver.navigate.to "http://www.google.com/"
driver.find_element(:id, "gbqfq").click
driver.find_element(:id, "gbqfq").clear
driver.find_element(:id, "gbqfq").send_keys "Sauce Labs"
driver.quit

```

Once you run the test, head over to the Sauce Labs [account page](https://saucelabs.com/account) and you will notice a new session, executed on a pristine new Virtual Machine, with the name 'Selenium on Sauce on IE11W8.1' and your chosen environment.

Voila!! You are up and running on Sauce Labs.


### Parallel Selenium Tests on Different Browser / OS Combinations - Sauce Labs

We have run our test on Sauce and are feeling accomplished! That's great! However, since Sauce Labs provides the world's largest grid for executing Selenium and Appium tests, we are able to harness it's abilities and run our test on various Browser/OS combinations at the same time.

Let's again perform 2 small steps and we will be rocking tests in parallel on Sauce:

**1st Step** - Lets install a rubygem called [Peach](https://github.com/schleyfox/peach) by running the following command in the terminal window:

```bash
gem install peach
```

**2nd Step** - Make the following changes to our test shown in the code below:

```ruby
# Import the newly installed gem to our test code

require 'peach'
require 'rubygems'
require 'selenium-webdriver'

# Let's add a couple more Browser/OS combinations to the existing one

caps1 = Selenium::WebDriver::Remote::Capabilities.new
caps1["browserName"] = "internet explorer"
caps1["version"] = "11"
caps1["platform"] = "Windows 8.1"
caps1["name"] = "Selenium on Sauce on IE11W8.1"

caps2 = Selenium::WebDriver::Remote::Capabilities.new
caps2["browserName"] = "Chrome"
caps2["version"] = "27"
caps2["platform"] = "Linux"
caps2["name"] = "Selenium on Sauce on C12L"

caps3 = Selenium::WebDriver::Remote::Capabilities.new
caps3["browserName"] = "Firefox"
caps3["version"] = "17"
caps3["platform"] = "OS X 10.6"
caps3["name"] = "Selenium on Sauce on F17OSX10"

# Create an array of the our chosen combos

caps = [caps1, caps2, caps3]

# With the slight modifications below, Sauce Labs creates a new VM instance for each of our combinations

caps.peach do |cap|
    driver = Selenium::WebDriver.for(:remote,
    :url => "http://sauceUsername:sauceAccesskey@ondemand.saucelabs.com:80/wd/hub",
    :desired_capabilities => cap)

    driver.navigate.to "http://www.google.com/"
    driver.find_element(:id, "gbqfq").click
    driver.find_element(:id, "gbqfq").clear
    driver.find_element(:id, "gbqfq").send_keys "saucelabs"
    driver.quit
end

```

Pat yourself on the back and grab a beer! You have **successfully** executed a Selenium test in parallel on Sauce.

## Ruby Selenium Testing with a Framework

The [sauce gem](https://github.com/saucelabs/sauce_ruby) makes it easy to run Selenium or Capybara tests against a wide range of browsers on Windows (XP, 7, 8), OS X, Linux, iOS and Android.

This example uses [Capybara](http://jnicklas.github.com/capybara/) and RSpec with Rails 3 and Ruby 1.9, but Sauce Labs also works great against any Ruby web stack, and with [Test::Unit](https://saucelabs.com/docs/ondemand/getting-started/env/ruby/se2/mac), [Cucumber](https://github.com/sauce-labs/sauce_ruby/wiki/Cucumber-and-Capybara), and most other testing frameworks... right down to vanilla [WebDriver](http://code.google.com/p/selenium/wiki/RubyBindings).

Your tests are run in real browsers on a real operating system, in a
dedicated, single-use VM.  Once they're complete, screenshots, video,
Selenium log and a log of passes and failures can be seen and shared.

## What You'll Need

In your Gemfile:

```ruby
group :test, :development do
  # These are the target gems of this tutorial
  gem 'rspec-rails', '~> 2.12'
  gem 'sauce', '~> 3.1.1'
  gem 'sauce-connect'
  gem 'capybara', '~> 2.0.3'
  gem 'parallel_tests'
end
```

## Setting up RSpec

From your `RAILS_ROOT`, generate a ./spec directory, a ./spec/spec_helper.rb file, and a warm, fuzzy feeling of productivity by executing:

```bash
rails generate rspec:install
```

Set up your `spec_helper` and create a template `sauce_helper`, by running:

```bash
rake sauce:install:spec
```

Now, open the new spec/sauce_helper.rb file, and just under the `require` statements, add requires for `capybara/rails` and `capybara/rspec`. Finally, set the default Capybara driver to be Sauce:

```ruby
Capybara.default_driver = :sauce
```

Once this is done, you can configure your desired test platforms. Here's an example:

```ruby
# Use Capybara integration
require "sauce"
require "sauce/capybara"
require "capybara/rails"
require "capybara/rspec"

Capybara.default_driver = :sauce

# Set up configuration
Sauce.config do |c|
  c[:browsers] = [
    ["Windows 8", "Internet Explorer", "10"],
    ["Windows 7", "Firefox", "20"],
    ["OS X 10.8", "Safari", "6"],
    ["Linux", "Chrome", nil]
  ]
end
```

Check out [our platforms page](http://saucelabs.com/docs/platforms) for available platforms (130+ and counting!).

## Setting up the Sauce Gem

Keep your Sauce Labs credentials out of your repositories and available to all your Sauce Labs tools using projects by adding them as environment variables.

Open `~/.bash_profile` and add the following lines:

```bash
export SAUCE_USERNAME=sauceUsername
export SAUCE_ACCESS_KEY=sauceAccessKey
```

You'll then need to re-load that profile with `source ~/.bash_profile`

Open your environment variables settings window (Instructions [here](http://www.itechtalk.com/thread3595.html)) and set the following variables:

    Name: SAUCE_USERNAME
    Value: sauceUsername

    Name: SAUCE_ACCESS_KEY
    Value: sauceAccessKey

## Writing your tests

Phew!  That's all your setup done.  You're ready to write your tests.

Because we want the Capybara DSL included, we're going to put our tests in
the spec/features directory.

Turn on the Sauce voodoo by tagging each describe block ('example group' in RSpec-lish)  with `:sauce => true` like this:

    $ mkdir ./spec/features
    $ vim ./spec/features/ramen_spec.rb

```ruby
require "spec_helper"

describe "Wikipedia's Ramen Page", :sauce => true do
  it "Should mention the inventor of instant Ramen" do
    visit "http://en.wikipedia.org/"
    fill_in 'search', :with => "Ramen"
    click_button "searchButton"
    page.should have_content "Momofuku Ando"
  end
end
```
That's one spec, how about another?

    $ vim ./spec/features/miso_spec.rb

```ruby
require "spec_helper"

describe "Wikipedia's Miso Page", :sauce => true do
  it "Should mention a favorite type of Miso" do
    visit "http://en.wikipedia.org/"
    fill_in 'search', :with => "Miso"
    click_button "searchButton"
    page.should have_content "Akamiso"
  end
end
```

And that's everything!

## Running your Ruby Selenium tests


```bash
rake sauce:spec
```

It's that simple (Thanks in part to the excellent [parallel_tests](https://github.com/grosser/parallel_tests) gem.)
Your tests will run once for every platform, taking advantage of Sauce Labs to run at the maximum concurrency for your
account. The sauce:spec rake command takes two optional parameters to let you control the directory you keep your specs
in and the level of concurrency at which you run them. For example, to run specs from the "spec" directory one at a time,
you'd run the command like so: 

```bash
rake sauce:spec[spec,1]
```

You should see output much like the following:

```
20 processes for 8 specs, ~ 0 specs per process
[Connecting to Sauce Labs...]
Sauce Connect 3.0-r25, build 38

[snip]A LOT OF TEST DATA[/snip]

8 examples, 0 failures

Took 196.657036 seconds
```

The `8 examples, 0 failures` line means your tests are running against each browser, and passing. Congratulations!

Running in parallel makes your build faster, so you can run more tests in more browsers in less total time.

Check out the results, including a command log, screenshots, and video of the browser executing the test, on your [account page](https://saucelabs.com/account).

Help, it didn't work!
---------------------

Make sure your example groups are tagged with `:sauce => true`.  Without this tag, the RSpec integration won't be used, and your tests will only run with the default Sauce configuration.

If you still can't get things going, drop us a line at help@saucelabs.com and we're happy to assist you.  Don't forget to include your Gemfile.lock!

What's Next?
------------
**Capybara Resources**

Now that you have an example to work with, it's time to write a test for your web app! For more info on how to write Capybara tests, we recommend the excellent [Capybara README](https://github.com/jnicklas/capybara).

**Testing against local servers with Sauce Connect**

If you need to test a staged site behind your firewall, that's no problem: you're already set up to use [Sauce Connect](http://saucelabs.com/docs/connect), which means local servers are available to your Sauce Labs tests.

If you don't need access to local servers, you can turn Sauce Connect off by adding this to your Sauce.config block:

```ruby
  config[:start_tunnel] = false
```

**parallel_tests considerations**

If you find that you run into problems with all your tests using the same db at once, or otherwise need some careful setup, the
[parallel_tests instructions](https://github.com/grosser/parallel_tests)
have useful info.

**The Sauce Gem**

The Sauce gem has its own [github repo](https://github.com/saucelabs/sauce_ruby) and a constantly evolving [wiki](https://github.com/saucelabs/sauce_ruby/wiki/_pages) you should check out!
