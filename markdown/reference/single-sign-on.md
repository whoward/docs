{
  title: "Single Sign-On",
  description: "Configuring Single Sign-On for your Sauce Labs account",
  category: "Reference",
  index: 12
}

**Note:** SSO is only available to Enterprise users and is currently in beta. Please email [beta@saucelabs.com](mailto:beta@saucelabs.com) to request access.

Enterprise account owners are able to integrate their identity management solution with their Sauce Labs account. Single Sign-On (SSO) enables an organization to maintain a single access point which grants permission to all authorized applications with a single set of credentials. This enhances fine-grained control of application access and simplifies end user authentication. 

## Supported Integrations
The Sauce Labs SSO integration supports any configuration that complies with the [SAML 2.0 standard](http://en.wikipedia.org/wiki/SAML_2.0) such as Microsoft Active Directory Federation Service (ADFS) and PingIdentity's PingFederate. We have also partnered with the following identity-as-a-service providers for out-of-the-box integration with web-based portals. 

* [Okta](https://www.okta.com/product/identity-management/single-sign-on.html)
* [OneLogin](https://www.onelogin.com/product/sso)
* [PingIdentity (PingOne)](https://www.pingidentity.com/en/products/capabilities/sso-federated-identity.html)

## Downloading & Uploading Metadata
Depending on your provider or unique configuration you may need to upload SAML metadata to your identity provider to enable SAML assertions. 

### Sauce Labs Metadata
The Sauce Labs metadata can be obtained at the following URL. To download the xml file directly, right click the link and select your browser's "save as" function.  

[Sauce Labs SAML Metadata](https://saucelabs.com/sso/metadata)

### Obtaining Your Metadata
For your organization to be identified during an incoming assertion, you must upload the SAML metadata XML provided by your identity provider. If using an on-premise or custom solution, reference your internal documentation or consult with an SSO expert for help obtaining your metadata file. If using one of our partner service providers, see the references below for help configuring the integration.  

[PingIdentity Documentation](http://documentation.pingidentity.com/pingone/employeeSsoAdminGuide/#enableAppWithoutURL.html)  
[Okta Application Network](https://dev-989235-admin.oktapreview.com/admin/apps/add-app)  
[OneLogin Application Catalog](https://app.onelogin.com/apps/find)  

**Note:** When configuring an integration using a service provider's application catalog or index, you will typically be guided through step-by-step instructions for configuring metadata and other necessary information.

## Basic Configuration
The SSO integration relies heavily on convention to keep setup quick and simple. Configuring your assertion using the following specifications will ensure a smooth transition for your users.

### Assertion Consumer Service
The Assertion Consumer Service (ACS) URL is an endpoint that receives and processes SAML assertions from an identity provider (IdP). If using one of our partner web-based service providers, the ACS should be pre-configured for you or will be configured when uploading the Sauce Labs metadata. If using an on-premise or custom solution, point your assertions to **https://saucelabs.com/sso/acs**.

### Configuring Attributes
The following attributes *must* be included in your assertion with the expected values listed for the integration to work correctly.

**Required Attributes**  
| Attributes 	| Expected Value 	| Example    |
|----------	|-------------	|-------------    |
| saml:Issuer 	| URL identifying your organization 	| https://www.yourcompany.com/sso-prod  |
| saml:NameID 	| User's email address 	| john.smith@yourcompany.com |

**Note:** The required attributes listed above are those from the SAML 2.0 specification and must match the specified format, as in `saml:[attribute name]`. These values will not be honored if passed in as custom attributes, as in `saml:Attribute Name="[attribute name]"`.

**Optional Attributes**  
| Attributes 	| Expected Value 	| Example    |
|----------	|-------------	|-------------    |
| FirstName 	| User's first name 	| John  |
| LastName 	| User's last name 	| Smith |

**Note:** The optional attributes listed above are not yet implemented but may be passed with your assertion so they will automatically be utilized once these attributes have been enabled.

## Requiring SSO
To further control access to your account, you may optionally require your users to access the Sauce Labs web application through your SSO portal. When enabled, users attempting to access the service using their login credentials will be denied access and informed that their administrator requires the use of SSO.

## Just-In-Time (JIT) Provisioning
JIT provisioning allows new users accessing the Sauce Labs service for the first time to have an account instantly created for them using the information received in the SAML assertion. If disabled, users who do not have an existing Sauce Labs account will be denied access to the web application. You may optionally redirect these users to a URL of your choosing.

### Creating Users with JIT
To leverage JIT provisioning, you must select a domain to namespace your organization. This is a unique string identifier which prevents username collisions with other users during the automatic provisioning process. New users will be created using a combination of your organization's domain and the user's email username. For example, an organization with a domain of `acme` and an incoming SAML assertion with a `NameID` of `john.smith@acme.com` will generate a user with username `sso-acme-john.smith`. 
