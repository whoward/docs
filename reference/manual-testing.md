{
  title: "Manual Testing",
  description: "test",
  category: "Reference",
  index: 5
}

## Find bugs faster with manual testing

Design, debug, and test faster. Manual testing with Sauce gives you access to cloud-hosted browsers in less than 20 seconds. You control them with your keyboard and mouse  just like you would at your computer. You want to share what you found with your team, and we make that easy by automatically giving you videos and screenshots of your session.

Early users reported uses for Sauce beyond bug logging:

  1. By developers, to document that their submitted code works
  2. By graphic designers, to solve gnarly CSS issues in IE
  3. By documentation people, to show how a feature works

Can you think of other uses? Tell us! We offer a complimentary one-month "[Medium Spicy](https://saucelabs.com/pricing)" plan for all users we publish.

## Record a bug

After launching a browser, use it to navigate your site as you would any other computer. If you find anything out of place, click the **Bug** icon, then name and describe the issue. When you note a bug, we'll create a report on your [snapshots page](https://saucelabs.com/snapshots) containing the screenshot and video of the bug.

In addition, all Manual testing sessions are Sauce automated tests in the background. This isn't important for normal usage, but you will find a list of all manual test jobs on your [automated tests](https://saucelabs.com/tests) page, including all assets captured in that session.

You can record as many bugs as you want in one session, but note that sessions are limited to 20 minutes. This protects you from being charged for inadvertently leaving a manual session idle all day.

## Easy and secure testing - even behind a firewall

Is your web application internal? Sauce Connect works like a charm for Manual testing as well as Automated. Just start Sauce Connect for the account you want to use Manual testing with, then launch a manual browser and navigate to any server available inside your network.

For more details, see the [Sauce Connect documentation page](/reference/sauce-connect/).

## Seeing a Security Error message (Error #2048)

This error is displayed when the ports that Manual testing relies on are being blocked by a firewall on your end. This may also be caused by running applications such as avast! antivirus software.

Here are all the servers and ports that manual testing relies on; please check with your IT department that the following servers and ports are accessible from your end.

• If you are launching manual testing from within Internet Explorer on your local machine:


      tv1.saucelabs.com:843
      tv1.saucelabs.com:5901
      saucelabs.com:843


• If you are launching manual testing from any other locally-installed browser:


      charon.saucelabs.com:80


We recommend making all of these accessible if you plan on using several browsers locally.

## Trouble logging into Sauce Launcher

[Sauce Launcher](https://saucelabs.com/downloads) is a browser extension that allows you to instantly create manual testing sessions. When logging in, be sure to use your access key, rather than your saucelabs.com password. To copy your key, visit your Sauce Labs [Account](https://saucelabs.com/account) page, select the "Account" tab, then click "View my Access Key".

## Seeing a black screen / "Plugin Failure" error

If your test shows a black screen after starting the virtual machine, you may need to reinstall [Adobe Flash Player](http://get.adobe.com/flashplayer/) on your machine. This should only occur if you are using Internet Explorer to launch Sauce Manual Testing, which requires this software for our in-browser VNC player to function.

## Long load times or timing out

We've streamlined our service to provide the best possible load times. If you are seeing slow manual testing sessions, check our [status page](http://status.saucelabs.com/) \- or get instant updates by following [@sauceops](https://twitter.com/sauceops) on Twitter.

## All links open in new tabs

It's possible for the manual testing VNC client to have a modifier key "stuck" down, causing any clicked links to open in new tabs. This happens if the client loses focus while a key is held down - for example, when using Alt%2BTab to switch application windows. In this case, VNC never receives the keyUp event.

To prevent this from happening, every time you focus back on the manual testing window, first click in the middle of the page, then press and release all the modifier keys (like Alt, Control, Command, and Shift).

## Want more?

If you'd like to dig deeper, feel free to check out our [help forums](http://support.saucelabs.com/categories/20002728-sauce-scout) or email us directly at [help@saucelabs.com](mailto:help@saucelabs.com).
