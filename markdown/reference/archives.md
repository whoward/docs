{title: "Archives", description: "Using Sauce Labs Archive features", category: 'Reference', index: 13, }

##Archives

The **Archives** page includes a number of filters you can use to search through your tests and builds. You can search using a single filter, or you can use multiple filters to build a structured search.

##Basic Filtering

1.  Click **Archives**.
2.  Click the filter you want to use to open it.
3.  In the filter text field, enter the terms you want to search for.
    As you type, autocomplete will suggest existing results that match your search terms. If you want to select a date or date range, use the **Calendar** icon in the **Date** filter.
4.  When you find the items you want to use in your filter, select them.
5.  When you've finished selecting your filter criteria, click **Apply**.

**Note: Special Filter Criteria and Operators**

Some filters include additional options and the ability to use special operators. See **Search Fields and Operators** for more information.

### Creating Structured Searches

You have two options for creating structured searches.

####Building Structured Searches from Filters####

With this option, you would select filters and filter criteria as you would for basic filtering, but each time you make a selection, you will see it added to the **Search** field to build the query. Using this option, there is an implied AND between the different filters you select, and an implied OR between the values within a specific filter.

#### Writing Structured Searches Based on Filters

If you want to create a more complex search using AND, OR, or special operators associated with a specific filter, you can write your own structured search in the **Search** field. As you enter search criteria, the autocomplete will suggest values, operators, and filters to match your text. If your search query syntax is incorrect, your query text will turn orange, and you will see suggestions for how to correct it below the search field. When your syntax is correct, the text will turn blue.

**Note: Tips for Writing Structured Searches**

-   Strings must always be enclosed with quotation marks, but criteria values that are supplied by the application (for example, `status:failed`) do not.
-   If you want to search using text only, enter `text:` in the search field, and then enter the text you want to search for enclosed in quotation marks.
-   You can use \* as a wildcard in any of your filter criteria.
-   Use quotation marks around a string to search for that exact string. For example, "ruby test" will return "ruby test 1" and "ruby test 2," but not "ruby example test."
-   Use parentheses around a string to find those terms anywhere in the search field. For example, (ruby test) will return both "ruby test 1" and "ruby example test."

### Examples of Structured Searches


Find all of the tests that were tagged as `Selenium_194` or `Selenium_193` that have failed since 03/10/2015

`tag:(Selenium_194 Selenium_193) and status:failed and date:[2015-10-03 TO *]`

Find only the IE tests that have failed with the word `Main` in the name of the test.

`name:main AND browser:"Internet Explorer *"`

### Search Filters and Operators

You can use any of these filters singly or or combination to search through the tests and builds on your **Archive** page. The **Example** column shows how you could construct a search using a specific filter in the **Search** text field. Note that the name of the filter is always in all caps, and all text strings must be enclosed within quotation marks, while system derived values (**OS**, **Browser**, **Status**) are not. See **Searching for Test Results and Builds on Your Archive Page** for examples of how to build structured searches using multiple filters in the Search field.

Filed Name | Definition  | Example 
 ---------- | ----------- | ------- 
`TEXT`      | Used to search for any mention of the following string in across test details| `text: Appium` 
`NAME`      | Used to search for full or partial test name| `name: SauceTest` 
`PLATFORM`        | Search for test that ran on one or multipe OS. (Note: this field accepts only a predefined list of OS specified in our docs [here](https://saucelabs.com/platforms/))| ` platform:("OS X 10.10" "Windows 8.1"))` 
`BROWSER`   | Search for tests that ran on one or multipled browsers. Accepts only browsers that Sauce currently supports.  (A complete list of browsers is available [here](https://saucelabs.com/platforms/) ) | `browser:("Google Chrome 43" "Internet Explorer 11")`
`DATE`      | Search for tests that ran on a specific date or over a specified range. Field accepts dates in YYYY-MM-DD format.| `date:[2014-05-05 TO 2015-05-05] ``date:[2014-05-05 TO *] `
`STATUS`    | Search for tests based on their status. Currently there are 4 possible states: `failed, passed, complete, error`.| `status: failed`
`BUILD`     | Search for tests that belond to an individual build. | `build:main AND browser:"Internet Explorer 11" ` 
`TAG`       | Search for tests that have one or multiple tags. |`tag: Jenkins`
`OWNER`     | Search for tests that were created by a specific user|`owner: Admin`



Using Favorites to Save Your Searches
-------------------------------------

If you've created a search that you want to use in the future, you can save it by adding it to your favorites.

1.  After you've built your search from the filters or written it in the **Search** text field on the **Archives** page, click the **Star** icon next to the text field to save it.
2.  Click the expand icon next to the **Star** to view your favorited searches.
    You can select a favorite search to run it, or remove it by clicking the **Delete** icon.

Deleting Test and Build Results
-------------------------------

You can delete any of the tests or builds listed on your **Archives** page.

1.  Select the test or build result you want to delete.
    You can also select multiple test or build results for deletion.
2.  Click **Delete**.
3.  In the confirmation dialog, click **Yes**.

**Warning: Results are Permanently Deleted**

Once you delete a test or build result, it's gone forever and cannot be recovered. Be careful when you click **Yes**.

Changing the Layout of Your Archives Page
-----------------------------------------

You can change the layout of your Archives page by changing which columns are displayed, and how the content in those columns is sorted.

1.  On your **Archives** page, click **Show Columns**.
2.  Select the columns you want to display, or clear the selection of columns you want to remove.
3.  Click **Apply**.
4.  Click **Sort By** to change the display of your results based on search score or ascending or descending dates.

Your layout changes will be saved until you change them again.