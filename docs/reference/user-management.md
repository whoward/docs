{
  title: "User Management",
  description: "How to create and manage subaccounts on Sauce Labs",
  category: "Reference",
  index: 3
}

Sauce is great for teams with multiple users. By using the subaccount feature, you can easily create and manage group or individual accounts within your organization, allowing everyone to test from the same batch of usage. Our team management tool allows you to create a tree-like structure within your account to better understand testing usage across multiple accounts.

The typical subaccount structure looks as follows:

!["Sauce Labs sub-accounts feature"](/images/reference/user-management/02-basic2.png "Sauce Labs sub-accounts feature")

You can easily navigate through multiple levels of the structure using our web user interface:

!["Sub-accounts multiple levels"](/images/reference/user-management/03-embedded.png "Sub-accounts multiple levels")

Minutes and Usage
-------
Individual accounts within a team utilize the minutes from the root parent account. This way, all team members are able to use the same minutes pool even if they are assigned to various groups. Keep in mind that when the root parent account is out of minutes, subaccounts will be unable to run tests.

Account types
-------------
There are two types of subaccounts and each has a different function.


### Admin account
This is a special type of account intended to be used only by highly-trusted team members to hand off responsibility from the management staff. Admin accounts can add and delete other subaccounts of their parents. This is a powerful feature so give admin privileges only to a limited number of people.


### Standard account
Creating standard subaccounts allows the user to utilize minutes of the parent user. Usage of minutes is completely transparent - each minute the subaccount consumes is taken from the parent account.


Rollup Mode
-----------
![Rollup Mode for Sauce Labs sub-accounts](/images/reference/user-management/04a-rollup-mode-300x96.png "Rollup Mode for Sauce Labs sub-accounts")

If you want to see the total account usage for your team, switch into rollup mode. This is a convenient way to compare usage for different subsections of your team.


Creating subaccounts
-------------------
Setting up a hierarchy is easy with our team management tool. Simply click "Add" on the subaccounts page, or "Edit" on an existing user. You'll now be able to say which account should own the account you are editing or creating.
![Creating subaccounts - step 1](/images/reference/user-management/06-create-new-300x257.png "Creating subaccounts - step 1")

If you're the main account, you can also designate which subaccounts should be admins by checking the "Is admin?" box in the account creation or edit forms:
![Creating subaccounts - step 2](/images/reference/user-management/07-edit-admin-300x285.png "Creating subaccounts - step 2")

Then, when your admins log in, they'll be able to see the whole team and manage other subaccounts for you, and they'll be notified about why they're seeing all these accounts:

![Creating subaccounts - step 3](/images/reference/user-management/08-as-admin-300x212.png "Creating subaccounts - step 3")


Sending login links to subaccounts
--------------------------------
If you're in charge of your Sauce admin account, you may want to create subaccounts for other team members. Sending the login link will allow you to send password reset emails to users. This way, you don't have to set their passwords manually. You can click the **send login link** button and the user will be able to set his or her password.
![Subaccount login link](/images/reference/user-management/send_login_link.png "Subaccount login link")


Converting normal accounts to subaccounts
-----------------------------------------
![Invite](/images/reference/user-management/invite.png "Invite")
You can convert an existing account to a subaccount of another account (for example, team members at your company can have multiple Sauce accounts but you want them all of them to use the same parent account minutes). 

To convert a normal account to a subaccount, the user who will be the parent account should click on the **Invite** button at the top of the Subaccounts page. This will bring up a dialog box where you can enter the email address(es) of the users you with to convert to subaccounts. Those users will receive an email with a link. Clicking the link will allow the user to convert the currently logged in account to a subaccount of the user who sent the invite. You can also use the invite button to invite team members to create new accounts that are automatically created as subaccounts of the user who sent the invite.
