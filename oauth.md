# OAuth


The Fuel Rats API supports user authentication via OAuth tokens only.  
The OAuth token is provided in REST requests via the HTTP Authorization header formatted as :
`Authorization: Bearer yourtokenhere`. The OAuth token is provided in websocket requests as a 'bearer' query parameter in the URL you connect to the server with. `https://api.fuelrats.com?bearer=yourtokenhere`.   

If you are for some reason in a situation where you need to make authenticated REST requests but are unable to set HTTP headers yourself, you may also provide the bearer query parameter to authenticate.

## Create a Client  
To be allowed to create an OAuth client you must have the relevant permission enabled on your Fuel Rats account, this is automatically given to any tech rat or administrator, if you are not a tech rat or administrator you may request the permission with a [Fuel Rats Help desk ticket](https://t.fuelr.at/help).

Once you have the required permission, navigate to the [developer area on your Fuel Rats profile](https://fuelrats.com/profile/developer), provide the name of your client and your redirect uri (for information on the redirect uri read the Authorisation section below). 
When you create your client you will receive a unique client identifier and a client secret code, you **MUST** store these somewhere safely as they cannot be recovered if lost, **if you lose your client credentials and request to have them reset all existing copies of your application will stop working.**




## Supported Grants  

The Fuel Rats API supports 3 different OAuth grant types, Authorization Code, Implicit Grant, and Resource Owner Password Credentials (ROPC). Client grants are not supported as there are no endpoints on the API that requires client authorisation but no user authentication.

**Authorization Code** is the prefered and most secure method of OAuth authentication and can be used when you are in a position to hide your clients secret key, for example in a native desktop application or in a server-side script.
It cannot be used in client-side web applications since anyone would be able to view the secret key in the source code.   
   
**Implicit Grant** is an alternative method of OAuth authentication for third party developers, it depends on being able to redirect to a static URL to verify the developer instead of using a secret key. It is most often used in client-side web applications and its use is discouraged by security experts. Please use Authorization Code instead if you can help it.   
   
**Resource Owner Password Credentials** is traditional password based login, except instead of a cookie, the server provides you with an OAuth token. ROPC authentication gives unlimited scope-less access to the user's account and is only available to first-party Fuel Rats applications, attempting to request an ROPC grant using a client-id that has not been whitelisted will result in a 403.

### Authorization Code

    +----------+
    | Resource |
    |   Owner  |
    |          |
    +----------+
        ^
        |
        (B)
    +----|-----+          Client Identifier      +---------------+
    |         -+----(A)-- & Redirection URI ---->|               |
    |  User-   |                                 | Authorization |
    |  Agent  -+----(B)-- User authenticates --->|     Server    |
    |          |                                 |               |
    |         -+----(C)-- Authorization Code ---<|               |
    +-|----|---+                                 +---------------+
      |    |                                         ^      v
    (A)  (C)                                         |      |
      |    |                                         |      |
      ^    v                                         |      |
    +---------+                                      |      |
    |         |>---(D)-- Authorization Code ---------'      |
    |  Client |          & Redirection URI                  |
    |         |                                             |
    |         |<---(E)----- Access Token -------------------'
    +---------+       (w/ Optional Refresh Token)

Authorization Code grant can be summarised in the following steps:
* Application opens the fuelrats.com authorisation endpoint in the browser listing the name of the application and what it wants access to.
* The user either approves or rejects the request.
* The request comes back to the application with an **authorization code**. 
* The application makes a **token exchange** request to the API together with its **client ID** and **client secret** to authenticate itself and receive an access token.
  
#### Authorisation
The authorisation stage consists of displaying the fuelrats.com authorisation page to the user, having them approve it and receive an authorisation code back. Depending on the kind of application you are writing this can be acheived in different ways.

* If you are a website within a web browser, you may simply redirect to the fuelrats.com authorisation page and have the user be redirected back to your website.
* If you are a native application with permission to register URL schemes with the operating system you may choose to open the authorisation page in the user's browser and have them be redirected to your app via a URL scheme such as `myapplication://authorization`.
* If you are a native application without permission to register URL schemes with the operating system you may opt to tempoarily run a local web server on the device, open the authorisation page in the user's browser, and have them be redirected back to your webserver via a url such as `http://localhost:8080/authorisation`.
* If you are a GUI application running natively on the device you may choose to display the authorisation page within a hosted web view and retrieve the authorisation key by intercepting the redirect event when the user authorises your application.
* If you are an application where none of these options are possible, you may set the redirect_uri query parameter in the authorisation url to `DISPLAY`, this will display the authorisation code on the page instead of redirecting, the user must then copy it into your application. This option is not very user friendly and should only be used when there is no alternative.

The authorisation URL is formatted as follows:  

https\://fuelrats.com/authorization?**response_type**=code&**client_id**=your_client_id&**scope**=your_scope_list&**redirect_uri**=your_redirect_uri&**state**=your_state_variable

Parameters:
* **response_type**: The response type, for Authorization grants this **must** have the value "code".
* **client_id**: The unique identifier of your OAuth client application.
* **scope**: Space seperated list of permissions required by your application, for a detailed lists of them see [scopes](scopes.md).
* **redirect_uri**: The uri that should be redirected to after the user has approved or rejected it. This can be any valid URL but it **CANNOT** contain a fragment component. If your application requires the user to manually copy and paste the authorisation code you may set this value to `DISPLAY`.
* **state**: A state value that will be returned to your application in the redirect so you can identify this request. This value can be whatever you want, but it cannot be empty.

Parameters should be encoded according to the application/x-www-form-urlencoded" format.

#### Authorisation Callback

The authorisation response will be a redirect to your defined redirect uri. 

---
This is the format for a successful grant:

https:\//yourexampleredirect.com/examplepage?**code**=authorisation_code_here&state=your_state_here.
* **code**: This is the authorisation code you will use in the next stage to retrieve your access token.
* **state**: This is the state value you provided in the authorisation page url.
   
---
This is the format for an sunsuccessful grant:  

https:\//yourexampleredirect.com/examplepage?**error**=error_code_here&**error_description**=error_description_here&**state**=your_state_here
* **error**: An ASCII lower case error identifier code signifying the type of error that caused the grant to fail, for a list of possible options see [The OAuth2 Specification](https://tools.ietf.org/html/rfc6749#section-4.1.2.1).
* **error_description**: A human readable explanation of the error that occured.
* **state**: This is the state value you provided in the authorisation page url.

---

#### Token Exchange
In the next step you will be making a request to the Fuel Rats API with your authorisation code to verify your client credentials. 

The token exchange shall be made with an HTTP POST request to `https://api.fuelrats.com/oauth2/token`.
The request needs to be authenticated with your OAuth client credentials using [HTTP Basic authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) in the header of the request.
This is a base 64 encoded string consisting of a username and password seperated by a colon (:).
In this instance the username will be the **OAuth Client ID** and the password will be the **OAuth Client Secret**.

`Authorization: Basic QWxhZGRpbjpPcGVuU2VzYW1l`

The body of the request should be in a application/x-www-form-urlencoded format and have the following values:


Key | Value | 
---------|----------|
 grant_type | This is the grant type of the request, for a token exchange this value **must** be `authorization_code`.    |
 code | This is the authorisation code you retrieved in the previous step of the OAuth flow. |
 redirect_uri | This value must be the same as the redirect URI you used in the authorisation stage of the flow, this is to help protect against man-in-the-middle attacks. |

----

IF successful, the response to the token exchange request will be an HTTP 200 with a body in UTF-8 encoded application/json and the following values will be returned in the top level JSON object.

Key | Value |
---------|----------|
access_token | This is the bearer access token that your application can use in future requests to make authenticated calls to the Fuel Rats API |
token_type | the type of token issued, in this case the value will be `bearer`. |

---

If the token exchange is not successful, the response will be an `HTTP 400 Bad Request` with a body in UTF-8 encoded application/json and the following values will be returned in the top level JSON object.

Key | Value |
---------|----------|
error | An ASCII lower case error identifier code signifying the type of error that caused the grant to fail, for a list of possible options see [The OAuth2 Specification](https://tools.ietf.org/html/rfc6749#section-5.2). |
error_description | A human readable explanation of the error that occured. |

### Implicit Grant

     +----------+
     | Resource |
     |  Owner   |
     |          |
     +----------+
          ^
          |
         (B)
     +----|-----+          Client Identifier     +---------------+
     |         -+----(A)-- & Redirection URI --->|               |
     |  User-   |                                | Authorization |
     |  Agent  -|----(B)-- User authenticates -->|     Server    |
     |          |                                |               |
     |          |<---(C)--- Redirection URI ----<|               |
     |          |          with Access Token     +---------------+
     |          |            in Fragment
     |          |                                +---------------+
     |          |----(D)--- Redirection URI ---->|   Web-Hosted  |
     |          |          without Fragment      |     Client    |
     |          |                                |    Resource   |
     |     (F)  |<---(E)------- Script ---------<|               |
     |          |                                +---------------+
     +-|--------+
       |    |
      (A)  (G) Access Token
       |    |
       ^    v
     +---------+
     |         |
     |  Client |
     |         |
     +---------+

Implicit grant can be summarised in the following steps:
* Client-side web application redirects to the fuelrats.com authorisation endpoint listing the name of the application and what it wants access to.
* The user either approves or rejects the request.
* The authorisation page redirects back to the URL provided on OAuth Client creation with either a rejection, or an access token.

Implicit Grant follows the same authorisation step as the Authorization Code grant above with a few differences: 
* Implicit grant can **only** be performed with redirects to web services, not localhost or application URL schemes. Attempting to make an implicit grant request with a redirect_uri that is not an HTTP/HTTPS link to a valid web domain will result in an error. 
* The value of response_type should be `token` **not** code. 
* the redirect_uri value **MUST** be the same as the one you provided when registering your OAuth client, unlike Authorization Grants, you cannot define a different redirect uri for implicit grant requests.

#### Authorisation Callback

The authorisation response will be a redirect to your defined redirect uri. 

---
This is the format for a successful grant:

https:\//yourexampleredirect.com/examplepage?**access_token**=access_token_here&**token_type**=bearer&**scope**=scope&**state**=your_state_Here.
* **access_token**: This is the bearer access token that your application can use in future requests to make authenticated calls to the Fuel Rats API 
* **token_type**: the type of token issued, in this case the value will be `bearer`.
* **scope**: The scopes you have been granted, may be different than the scopes you requested if you could not be granted all the scopes for some reason, or you were granted an additional scope out of necessity. 
* **state**: This is the state value you provided in the authorisation page url.
   
---
This is the format for an sunsuccessful grant:  

https:\//yourexampleredirect.com/examplepage?**error**=error_code_here&**error_description**=error_description_here&**state**=your_state_here
* **error**: An ASCII lower case error identifier code signifying the type of error that caused the grant to fail, for a list of possible options see [The OAuth2 Specification](https://tools.ietf.org/html/rfc6749#section-4.2.2.1).
* **error_description**: A human readable explanation of the error that occured.
* **state**: This is the state value you provided in the authorisation page url.

---

### Resource Owner Password Credentials

    +----------+
    | Resource |
    |  Owner   |
    |          |
    +----------+
        v
        |    Resource Owner
        (A) Password Credentials
        |
        v
    +---------+                                  +---------------+
    |         |>--(B)---- Resource Owner ------->|               |
    |         |         Password Credentials     | Authorization |
    | Client  |                                  |     Server    |
    |         |<--(C)---- Access Token ---------<|               |
    |         |    (w/ Optional Refresh Token)   |               |
    +---------+                                  +---------------+

#### **IMPORTANT**
Resource Owner Password Credentials grant is only available to first-party Fuel Rats applications. In practice it is only available to the main fuelrats.com website itself, even first party applications are encouraged to use Authorization Code instead. If you attempt to make an ROPC grant request with a non-whitelisted client it will be rejected.


ROPC can be summarised in the following steps:
* Application makes a request to the API's OAuth endpoint with its client id, client secret, the user's email, and the user's password.
* The API provides an OAuth token on successful authentication or rejects with an error.

The authorization request shall be made with an HTTP POST request to `https://api.fuelrats.com/oauth2/token`.
The request needs to be authenticated with your OAuth client credentials using [HTTP Basic authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) in the header of the request.
This is a base 64 encoded string consisting of a username and password seperated by a colon (:).
In this instance the username will be the **OAuth Client ID** and the password will be the **OAuth Client Secret**.

`Authorization: Basic QWxhZGRpbjpPcGVuU2VzYW1l`

These additional headers are also required in the request:   

Header | Description
---------|----------|
X-Fwd-IP | The user's IP address, if not provided the IP of the client making the request is used.
User-Agent | A string describing the user agent making this request
X-Fingerprint | A unique fingerprint of the user's client generated using [Fingerprint.js](https://github.com/Valve/fingerprintjs2)

The body of the request should be in a application/x-www-form-urlencoded format and have the following values:


Key | Value | 
---------|----------|
 grant_type | This is the grant type of the request, for an ROPC grant this value **must** be `password`.    |
 username | The email address of the Fuel Rats account |
 password | The password for the Fuel Rats account |

 #### Response

If successful, the response to the request will be an HTTP 200 with a body in UTF-8 encoded application/json and the following values will be returned in the top level JSON object.

Key | Value |
---------|----------|
access_token | This is the bearer access token that your application can use in future requests to make authenticated calls to the Fuel Rats API |
token_type | the type of token issued, in this case the value will be `bearer`. |

----

If the request is unsuccessful because the user agent is an unverified client, a verification email will be sent to the user's email address and the API will respond with an HTTP 403 JSONAPI verification_required error. 

----

If the token exchange is not successful, the response will be an `HTTP 400 Bad Request` with a body in UTF-8 encoded application/json and the following values will be returned in the top level JSON object.

Key | Value |
---------|----------|
error | An ASCII lower case error identifier code signifying the type of error that caused the grant to fail, for a list of possible options see [The OAuth2 Specification](https://tools.ietf.org/html/rfc6749#section-5.2). |
error_description | A human readable explanation of the error that occured. |
