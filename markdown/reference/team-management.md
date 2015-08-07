{title: "Team Management", description: "Using Sauce Labs Team Management features", category: 'Reference', index: 3, }

##Team Management


With the Sauce Team Management feature you can easily create and manage group or individual accounts within your organization, allowing everyone to test from the same batch of usage minutes under a single plan.

##Team Management Plans


Sauce Labs offers both Enterprise and Subscription plans that provide you with different numbers of concurrent VMs, concurrent devices, and minutes to meet your specific testing needs. Enterprise plans are invoiced on a monthly basis, while Subscription plans will charge your credit card on a monthly basis for your current plan level.

### How Your Plan Minutes Are Used

Individual accounts within a team utilize the minutes from the root parent account. This way, all team members are able to use the same pool of minutes even if they are spread across different teams. Usage allocation for subaccounts is tied to the master account settings: if the main account is set to disallow tests when out of minutes, will also be unable to run tests when the main account runs out of minutes. If the master account is set to run into overages once the set number of minutes has been exceeded, the subaccount will also run into overages.

### Viewing Plan Details

You can view the number of concurrent VMs, concurrent devices, and minutes allowed under your plan.

1.  Click your account menu.
2.  Click **Team Management**.
3.  Your plan type (for example, Enterprise or Subscription) is displayed at the top of the **Team Management** page.
4.  Click **View Details** to see specific information about VMs, devices, and minutes allowed under your plan.

### Updating Plan Billing Information

You can update your plan billing information at any time on the **Team Management** page. If you have an Enterprise account, you can update the billing contact name and email address. If you have a subscription account, you can update your billing contact information and credit card.

1.  Click your account menu.
2.  Click **Team Management**.
3.  Your plan type (for example, Enterprise or Subscription) is displayed at the top of the **Team Management** page.
4.  Next to your plan type, click **View Details**.
5.  Next to **Billing Information**, click **Update**.
6.  Enter your new billing information, and then click **Save**.
7.  Click **Done** on the **Plan Details** page.

### Canceling Your Subscription Plan

You can cancel your subscription plan at any time from the **Team Management** page.

1.  Click your account menu.
2.  Click **Team Management**.
3.  Next to your plan description on the **Team Management** page, click **View Details**.
4.  Click the **Cancel Subscription** link at the bottom of the **Plan Details** page.
5.  Click **Yes** in the confirmation dialog to cancel your plan.

**Note: Canceling an Enterprise Plan**

If you want to cancel an Enterprise plan, contact your Sauce Labs account executive.

### Upgrading Your Subscription Plan

If you need more concurrent VMs, concurrent devices, or more minutes, you can upgrade your subscription plan on the **Team Management** page. You can also enter redemption codes for upgrades and free minutes on the same page.

1.  Click your account menu.
2.  Click **Team Management**.
3.  Next to your plan name, click **View Details**.
4.  Click **Upgrade Plan** and choose the new plan details.,/li\>

**Note: Using a Redemption Code**

If you have a redemption code, enter it in **Redeem Code** and then click **Upgrade Plan**.

**Note: Upgrading an Enteprise Plan**

If you want to upgrade an Enterprise plan, contact your Sauce Labs account executive.

### Viewing Plan Usage Details for Your Accounts

You can view usage information for your account, and all subaccounts under your account, on the **Team Management** page.

1.  Click your account menu.
2.  Click **Team Management**.
3.  Next to **Usage**, select a user to view their usage statistics for the current 30 days as compared with the previous 30 days.
    You can view usage statistics for your entire team, yourself only, or any team member with a subaccount below you.

Access Configuration
--------------------

On your Team Management page, you can configure the saucelabs.com domain for your organization, require that all users in your organization use Sauce Connect, and enable Single Sign-On (SSO) to manage user creation and authentication.

### Using Sauce Team Management Features to to Secure Access to Your Domain

Sauce offers several features that, when used together, create a very tight system to secure access to your domain.

1.  Setting a domain for your organization provides a single URL for distribution for your team to access Sauce, and is required for enabling access to Sauce through single sign-on (SSO).
2.  [Requiring SSO](https://docs.saucelabs.com/reference/single-sign-on/) ensures that the only way someone can access your domain is if their identity has already been validated through your own authentication system.
3.  Requiring the use of Sauce Connect ensures that the only access to your applications and tests is from sandboxed sessions that originate from specific Sauce Labs machines.

### Setting Your Domain

Your domain is used to configure settings such as a secure subdomain address and Single Sign-On (SSO) usernames. For example, if you entered a domain name of `acme`, it would create the subdomain `acme.protected.saucelabs.com`, and an SSO prefix of `sso:acme:mysampleusername`.

1.  Click your account menu.
2.  Click **Team Management**.
3.  At the top of the page, click the edit icon next to the domain name to edit it.

### Requiring Sauce Connect for Your Domain

You can require that all users in your domain use Sauce Connect for testing. Sauce Connect uses a secure tunnel protocol that gives specific Sauce machines access to your local network. Sauce Connect sessions are sandboxed from outside data flows, and are a convenient way to securely test apps that aren't ready for public deployment. See [the Sauce Connect documentation](https://docs.saucelabs.com/reference/sauce-connect/) for more information.

1.  Click your account menu.
2.  Click **Team Management**.
3.  Under the **Sauce Connect** heading, click **Configure** to set up Sauce Connect.

Managing Users for Your Plan
----------------------------

### Viewing Users Associated with Your Account

You can view the users associated with your account on the **Team Management** page.

1.  Click your account menu.
2.  Click **Team Management**.
3.  At the bottom of the **Team Management** page you will see a list of all user accounts associated with your account. If there are additional subaccounts associated with those accounts as their parents, you will see an indicator of how many users are associated with those subaccounts. Click the expand arrow next to the number indicator to view those subaccounts. The account listings also include the concurrency allocation for each account, and the account type.

### Adding Users

You can add users, set their concurrency limit, and associate them with a parent account on the **Team Management** page. You have the option to invite users, in which case they set their own user name and password, or you can set the username and password yourself.

1.  Click your account menu.
2.  Click **Team Management**.
3.  At the bottom of the page, next to **Users**, click **Add User**.
4.  Enter a **Username** and **Password** for the user, along with an optional **First Name** and **Last Name**.
5.  If you've configured Single Sign On (SSO) for your domain, select **SSO User** and usernames and passwords will be automatically generated by your identity provider.
6.  Under **Concurrency Limit**, enter the number of **VMs** you want to allocate to the user
7.  Select **Admin** if you want the user to be able to manage concurrency allocations for their peers and sub accounts.
    If you want users associated with your account to also be able to add users, you need to select this option under **Access Settings**, as explained in **Managing Access Settings for All Users**.
8.  Click **Done**.
    If you want to continue adding users, select **Continue Creating Users** and then click **Done**.

**Note: Concurrency Allocations and Parent Accounts**

The number of VMs you can allocate to a user is based on the total number of VMs that are allocated to the Parent account you associate with the user. For example, if the parent account has an allocation of 200 VMs and 25 devices, you could allocate 200 VMs and 25 devices to each subaccount.

**Note: Inviting Users by Email**

If you want to invite a user by email and allow them to set their own username and password, enter their email address and click **Invite.**

If you invite a user by email, you will need to wait until they accept your invitation before you can edit the concurrency settings for their account and associate them with other accounts.

### Deleting Users

You can delete users from your account on the **Team Management** page.

1.  Click your account menu.
2.  Click **Team Management**.
3.  Under **Users**, select the user you want to delete.
    The **Delete** button will be enabled.
4.  Click **Delete**.
5.  Click **Yes, Delete** in the dialog to confirm that you want to delete this user.

### Managing User Info and Settings

If you are an Admin user, you can manage the user info for any user account that is a sub account of yours. If you have invited a user via email, you will need to edit their concurrency limit and other account details after they have accepted your invitation and created an account.

1.  Click your account menu.
2.  Click **Team Management**.
3.  Under **Users**, click the username of the account that you want to edit.
4.  On the user account page, click **\>Edit User Info**.
5.  Edit the user information, and then click **Done**.

### Viewing and Managing Your Account Information

Your account information page includes your concurrency limits, charts of your usage, your Sauce Labs access key, your active Sauce Connect tunnels, and all the users associated with your account, including their concurrency allocations.

1.  Click your account menu.
2.  Click **My Account.**
3.  If you need to update your user information, click **Edit User Info**.
4.  If you need to manage any of your active tunnels, use the **Set Default** or **Stop** buttons for the tunnel.
5.  If you need to manage any of the users associated with you account, click their username, and then follow the instructions for **Managing User Info and Settings**.

### User Account Types

There are four types of accounts associated with an organization.

**Owner**

There is only one owner per Organization. This is most top-level account in the hierarchy.

**Parents**

Parents can manage any sub accounts they create.

**Admins**

Admins can manage both their peers (accounts that share the same parent) and any subaccounts below themselves or their peers.

**User**

Users can manage their own accounts, but no one else.

### Enabling Your Subaccounts to Add Users to Your Account

You can enable users associated with your parent account to add more users to your account.

1.  Click your account menu.
2.  Click **Team Management**.
3.  In the **Users** section of the **Team Management** page, click **Access Settings**.
4.  Select **Allow Users Within My Account to Create Additional Users.**
5.  Click **Save.**

### **Resetting User Access Tokens**

You can reset the user access keys for all of the users associated with your account.

1.  On your Sauce Labs home page, click your account menu.
2.  Click **Team Management**.
3.  In the **Users** section of the **Team Management** page, click **Access Settings**.
4.  Click **Reset All Access Tokens**.
5.  Click **Save**.