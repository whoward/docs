{
  title: "Team Management",
  description: "How to create and manage subaccounts on Sauce Labs",
  category: "Reference",
  index: 3
}

Sauce is great for teams with multiple users. By using the sub-account feature, you can easily create and manage group or individual accounts within your organization, allowing everyone to test from the same batch of usage. Our team management tool allows you to create a tree-like structure within your account to better understand testing usage across multiple accounts.

The typical sub-account structure looks as follows:

!["Sauce Labs sub-accounts feature"](/images/reference/team-management/02-basic2.png "Sauce Labs sub-accounts feature")

You can easily navigate through multiple levels of the structure using our web user interface:

!["Sub-accounts multiple levels"](/images/reference/team-management/03-embedded.png "Sub-accounts multiple levels")

Minutes and Usage
-------
Individual accounts within a team utilize the minutes from the root parent account. This way, all team members are able to use the same minutes pool even if they are assigned to various groups. Subaccount usage allocation is tied to the master account's settings - if the main account is set to disallow tests when out of minutes, subaccounts will also be unable to run tests when the main account runs out of minutes. If the master account is set to run into overages once out of minutes, the subaccount will also run into overages.

Available concurrency is shared amongst all accounts.

Account types
-------------
There are two types of sub-accounts and each has a different function.


### Admin account
This is a special type of account intended to be used only by highly-trusted team members to hand off responsibility from the management staff. Admin accounts can add and delete other sub-accounts of their parents. This is a powerful feature so give admin privileges only to a limited number of people.


### Standard account
Creating standard sub-accounts allows the user to utilize minutes of the parent user. Usage of minutes is completely transparent - each minute the sub-account consumes is taken from the parent account. Standard accounts can create subaccounts, but they can only manage subaccounts lower than them in the hierarchy, while admin accounts can manage all accounts.


Rollup Mode
-----------
![Rollup Mode for Sauce Labs sub-accounts](/images/reference/team-management/04a-rollup-mode-300x96.png "Rollup Mode for Sauce Labs sub-accounts")

If you want to see the total account usage for your team, switch into rollup mode. This is a convenient way to compare usage for different subsections of your team.


Creating sub-accounts
-------------------
Setting up a hierarchy is easy with our team management tool. Simply click "Add" on the sub-accounts page, or "Edit" on an existing user. You'll now be able to say which account should own the account you are editing or creating.
![Creating sub-accounts - step 1](/images/reference/team-management/06-create-new-300x257.png "Creating sub-accounts - step 1")

If you're the main account, you can also designate which sub-accounts should be admins by checking the "Is admin?" box in the account creation or edit forms:
![Creating sub-accounts - step 2](/images/reference/team-management/07-edit-admin-300x285.png "Creating sub-accounts - step 2")

Then, when your admins log in, they'll be able to see the whole team and manage other sub-accounts for you, and they'll be notified about why they're seeing all these accounts:

![Creating sub-accounts - step 3](/images/reference/team-management/08-as-admin-300x212.png "Creating sub-accounts - step 3")


Sending login links to sub-accounts
--------------------------------
If you're in charge of your Sauce admin account, you may want to create sub-accounts for other team members. Sending the login link will allow you to send password reset emails to users so they can set their own passwords. If you create an account using the **Add** button, you will need to create a placeholder password. You can click the **send login link** button and the user will be able to reset his or her password.
![sub-account login link](/images/reference/team-management/send_login_link.png "sub-account login link")


Converting normal accounts to sub-accounts
-----------------------------------------
![Invite](/images/reference/team-management/invite.png "Invite")

You can convert an existing account to a sub-account of another account (for example, team members at your company can have multiple Sauce accounts but you want them all of them to use the same parent account minutes).

To convert a normal account to a sub-account, the user who will be the parent account should click on the **Invite** button at the top of the sub-accounts page. This will bring up a dialog box where you can enter the email address(es) of the users you with to convert to sub-accounts. Those users will receive an email with a link. Clicking the link will allow the user to convert the currently logged in account to a sub-account of the user who sent the invite. You can also use the invite button to invite team members to create new accounts that are automatically created as sub-accounts of the user who sent the invite.
