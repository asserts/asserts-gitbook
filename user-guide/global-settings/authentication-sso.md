# Authentication (SSO)

Asserts uses OAuth2 and OIDC for Single Sign On (SSO). For users with the Owner role, this configuration can be found in the Asserts Settings page. Note that SSO is not available with a Freemium license

<figure><img src="../../.gitbook/assets/auth_setting_menu_smaller.jpg" alt=""><figcaption><p>Auth config from the Settings menu</p></figcaption></figure>

### OAuth2 Config Screen

Once on the OAuth2 Config screen, we can set the configuration properties.&#x20;

<figure><img src="../../.gitbook/assets/OAuth2_Config_Small.png" alt=""><figcaption></figcaption></figure>

The properties include:

* Provider Name - The name of the provider used for reference
* Provider ID - The unique identifier of this IDP
* Client ID - Unique ID created within the IDP
* Client Secret - Unique secret created within the IDP
* Authorize Endpoint - IDP authorization endpoint
* Token Endpoint - IDP token endpoint
* User Info Endpoint - Optional IDP endpoint for user profile information
* Logout Endpoint - An optional logout template URL following the [OpenID logout spec](https://openid.net/specs/openid-connect-rpinitiated-1\_0.html#RPLogout)
  * The URL template you provide must include a value for the id\_token_\__hint parameter defined in the spec. This is done through the template binding of __ {id_\__token}
* Grant Type - The grant type(s) for this IDP (generally authorization\_code)
* Scope - Space separated scope fields defined by the IDP to be able to access the user profile as well as name & email
* Endpoint Auth Scheme - Either POST or BASIC depending on the IDP
* Parameters - the parameters required by the IDP to support an id auth & refresh token
  * Includes `response_type: code` plus any other IDP specific parameters

### User Profile Fields

<figure><img src="../../.gitbook/assets/OAuth2_User_Profile_Small.jpg" alt=""><figcaption></figcaption></figure>

When available, Asserts will query the IDP user info endpoint to build the user profile. There are several user profile fields in Asserts and these properties may be referenced with different key names in the IDP. Therefore, we have built a mapping mechanism for these fields. For the following properties, enter the corresponding key value for the user info response.

The Asserts properties are as follows:

* Name - The user's full name (required)
* Email - the user's email address (required)
* Picture - The user's profile picture
* Provider ID - The unique ID of the user in the IDP
* Locale - The user's locale
* Hosted Domain - The hosted domain to which this user belongs

### Verified IDPs

At Asserts we have verified the SSO flow for the following IDPs. Also included are the proper properties for Grant Type, Scope, Parameters and User Profile mappings

* **Apple**
  * grant\_type: authorization\_code
  * scope: openid
  * params:
    * response\_type: code
  * User Profile
    * name: name
    * email: email
* **Auth0**
  * grant\_type: authorization\_code
  * scope: openid email profile offline\_access name picture
  * params
    * response\_type: code
  * User Profile
    * name: name
    * email: email:
    * locale: locale
    * picture: picture
    * providerUserId: sub
* **Google**
  * grant\_type: authorization\_code
  * scope: openid email profile
  * params
    * prompt: consent
    * access\_type: offline
    * response\_type: code
  * User Profile
    * name: name
    * email: email
    * locale: locale
    * picture: picture
    * hostedDomain: hd
    * providerUserId: sub
* **Okta**
  * grant\_type: authorization\_code
  * scope: openid email profile offline\_access
  * params:
    * response\_type: code
  * User Profile
    * name: name
    * email: email
    * locale: locale
    * picture: profile
    * providerUserId: preferred\_username
* **OneLogin**
  * grant\_type: authorization\_code
  * scope: openid name profile groups email params phone
  * params:
    * response\_type: code
  * User Profile
    * name: name
    * email: email
    * locale: locale
    * picture: profile
    * providerUserId: preferred\_username
* **PingOne**
  * grant\_type: authorization\_code
  * scope: openid name profile email phone address
  * params:
    * response\_type: code
  * User Profile
    * name: name
    * email: email
    * locale:locale
    * picture: profile
    * providerUserId: preferred\_username

### IDP Configuration

To allow Asserts access to your IDP you must configure an authorized origin and redirect URI at a minimum. In addition, your IDP may support direction to a login and/or logout URI.&#x20;

The values you should use are as follows:

* authorized origin - `http(s)://<your Asserts hostname>`
* redirect URI - `http(s)://<your Asserts hostname>/authorized`
* login URI - `http(s)://<your Asserts hostname>/login`
* logout URI - `http(s)://<your Asserts hostname>/logout`

### Sample Google IDP Configuration

<figure><img src="../../.gitbook/assets/Sample_Google_Auth_Config.png" alt=""><figcaption></figcaption></figure>
