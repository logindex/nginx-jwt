# Auth0 Module for Nginx

**nginx-auth0** is an authentication/authorization module for the [Nginx](http://nginx.org/) server.  It will allow you to use Nginx as a reverse proxy in front of your existing set of HTTP services or websites and secure them using Auth0, making little or no changes to the backing services themselves.

## Overview

[Auth0](http://www.auth0.com) provides an easy way for apps to incorporate user identities.  Integrating it into your mobile or [SPA](http://en.wikipedia.org/wiki/Single-page_application) web app is easy as Auth0 provides [libraries and guides](https://auth0.com/docs) for many of the most popular frontend frameworks.  Auth0 is also fairly easy to [integrate on the server-side](https://auth0.com/docs/quickstart/webapp).  However, often times there are additional barriers to integrating a new security layer into your backend services, especially if they already exist.  Some of these challenges may include:

* Your backend may not have been designed to easily incorporate a new security layer and making this change could potentially destabilize it for other existing apps
* Your backend codebase is maintained by another team and they told you it would take 6 months to make this kind of change, given their other priorities
* Your backend is a 3rd party service and it doesn't support token-based authentication
* Your app is a more traditional web application where there isn't a clean separation of frontend and backend, yet you still would like to secure it without having to alter any of the website code

Situations like the above are common, which is why many people turn to a product like **Nginx** as a [reverse proxy](http://en.wikipedia.org/wiki/Reverse_proxy) service, placing it in front of the various backend services required by their app.  Nginx allows a host of reverse proxy capabilities, including caching, doctoring responses, and redirecting.  Now with the **nginx-auth0** module, you can easily enforce Auth0 authentication and authorization, simply by configuring it with your Auth0 app's information.  Following are the specific scenarios that the **nginx-auth0** module is designed to support:

### Web API Scenario

The simplest **nginx-auth0** scenario is when you're building an app that has a separate frontend app (mobile or web) that gets all of its data from a set of existing backend HTTP services.  In this case the module can be configured to secure any number of necessary endpoints, all proxied through Nginx.  A secured endpoint can be configured to:

* Authenticate a user by ensuring that a valid JWT access token (one generated by Auth0 for a specific user) has been sent by the client app in the request via the `Authorization` HTTP request header
* Enforce that the authenticated user is also authorized to access the endpoint by requiring a specific set of claims found in the access token

Of course the client app will first need to obtain a valid access token, which can be done using one of the [Auth0 frontend libraries or widgets](https://auth0.com/docs).  If the client does not pass a valid token, the module will return an HTTP `401 Unauthorized` error.

### Website Scenario (Coming Soon)

If your app is a more traditional website, you may not have the luxury of modifying the frontend code since most of it is rendered server-side.  In this case, we can fall back to the standard mechanism used by web browsers to carry user identification: HTTP cookies.

Like the [Web API Scenario](#web-api-scenario), the **nginx-auth0** module can be configured to secure specific URL endpoints in your website.  If a user browses to one of those URL's and their browser is not passing a valid cookie, the module will redirect them to the appropriate Auth0 login page (ex: https://your-app.auth0.com/authorize/...).  When they successfully log in, they will be redirected back to the Nginx server where the module will extract the JWT access token, generate a valid cookie, and send it back to the browser with the originally requested website resource.  For subsequent requests to the server, the browser will continue to pass the cookie, which the module will validate and continue allow access to the backing service.
