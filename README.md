# Sauce Labs Docs

This repo contains the markdown documentation for Sauce Labs. To learn about how the system works, check out [this](https://medium.com/code-stories/87c0317c56f7) blog post.

[![Build Status](https://travis-ci.org/saucelabs/docs.svg?branch=master)](https://travis-ci.org/saucelabs/docs)
[![Build status](https://ci.appveyor.com/api/projects/status/8f3q4ng044d748q6)](https://ci.appveyor.com/project/ChrisWren/docs)

## Editing Markdown

Every [markdown](http://daringfireball.net/projects/markdown/) document in the `markdown` folder of this repo has a required metadata section which should be valid a JavaScript object containing the `title`(used in the nav, title section, URL, search results, and as part of the [naming conventions](#naming-conventions) below), `description`(used in the meta description), `category`(used in the nav, URL and as part of the [naming conventions](#naming-conventions)), and the `index`(used to determine the document's position in the navigation relative to other documents in the same category). You can also optionally include an `image`(used in the nav, search results, and title section on the doc's page) or `libraries`(used to determine the list of libraries the specific doc page relies on). The supported version of markdown is GitHub flavored markdown as described [here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).

### Naming conventions

All markdown file names should be lowercase, dasherized, and be placed inside the corresponding category folder. For example a doc with the category `Tutorials` entitled `Sample Tutorial` would be located at `markdown/tutorials/sample-tutorial.md`. Images relating to docs are found in the same relative location as markdown files. A main doc `image` for `Sample Tutorial` would be located at `images/tutorials/sample-tutorial.png` while an image in the content of the doc would be located at `images/tutorials/sample-tutorial/content-image.png`.

## Custom Markdown processing

Any code block containing `sauceUsername` or `sauceAccessKey` will either get replaced by the logged-in user's credentials or be replaced by `YOUR_USERNAME`, and `YOUR_ACCESS_KEY` respectively. If the user is logged out, there will be a button present when hovering over code blocks prompting the user to login. Additionally, code blocks with a programming language defined will have a button present on hover to allow users to copy code to their clipboard.

### Linkable heading tags

Each heading tag is turned into a [fragment identifier](http://en.wikipedia.org/wiki/Fragment_identifier) so that users can link into sections within docs. Because of this, be wary of changing section headings because you might break linking between docs.

## Running tests locally

To run the tests locally, first clone the repo:
```bash
git clone https://github.com/saucelabs/docs.git && cd docs
```

Then run the following command to install the local node.js dependencies:
```bash
npm install
```
*Note: this assumes you have [node.js](http://nodejs.org/) installed.*

If you don't have Grunt installed, run the following to command to install it globally:
```bash
npm install -g grunt-cli
```

Now you can test your changes by running the following command:
```bash
npm test
```

## Non-dev contributors

Work should be done in branches so that it can be reviewed before being deployed. Follow the below instructions to improve the docs without using the command line:

1. Download [GitHub for Mac](https://mac.github.com/) or [Windows](https://windows.github.com/).
2. Click `Clone in Desktop` in the right nav of this repo above.
3. [Make a branch from master](https://help.github.com/articles/branching-out).
4. [Make changes](https://help.github.com/articles/making-changes).
5. [Push branch changes](https://help.github.com/articles/how-can-i-push-or-pull)

When you have a new set of changes, change back to the master branch, sync it, then start at #3 above. The goal is to have branches be a short-lived related set of changes. Feel free to make many small branches!
