---
layout : post
title : Redirect Page Not Shown
comments : true
categories : [javascript]
---

I am using `Apache Shiro` to do authentication and authorization.

There were two tabs for one web application, I did `logout` in one tab, then clicked a button to do something in another tab.

In this case, web application should redirect to login page because I have been logout.

However, the login page not shown, browser still display original page.

I checked the developer tool and found that there were two requests, one was click button request and it response `302` and 
another was redirect to login page request which response `200`. It seems that `redirect` works, just login page not shown.

When user click this button, system will call a structs action via `ajax`. Therefore, the problem is caused by `ajax`.

**Ajax call cannot show redirect page**.

Following is a jQuery ajax call:

```javascript
	$.ajax({
        url: '/shiro/account-info',
        			
        error: function() {
        		alert(111);
        },
        			
        statusCode: {
        		
        		302: function() {
          			alert( "302" );
          	},
        		error: function() {
        			   alert(222);
        		}
        },
        			
        success: function(data) {
        				
        },
        type: 'POST'
   })
```

According to the jQuery documentation,we can catch the status by `statusCode`, 
hence I tried to add `302` code in `statusCode` and call `window.location.reload()` in it if user logout in other tab. 
But it cannot be catched.

The reason is that ajax call only catch the redirect request response, that is browser request a resource and server return
`302` to browser to redirect to login page, then browser transparently follow the redirect, 
finally ajax callback catch the response of login page which is `200`.

There are some solutions, one is check in ajax call like 

```javascript
$.ajax(...).done(function(data, statusText, xhr){
    var contentType = xhr.getResponseHeader("Content-Type");
    if (xhr.status === 200 && contentType.toLowerCase().indexOf("text/html") >= 0) { 
        	location.reload();
     }else{
        		            
     }
});
```

With the help of this approach, if it is redirected to login page by ajax, current page will be reload. Because current page
is authenticated, and user has been logout in other tab, as a consequence, reload current page will redirect to login page,
finally the login page can be shown.

### Reference

[jQuery Ajax 302](https://www.cnblogs.com/wonyun/p/5696838.html)

[detecting-location-redirects](https://dzone.com/articles/detecting-location-redirects)

[how-to-manage-a-redirect-request-after-a-jquery-ajax-call](https://stackoverflow.com/questions/199099/how-to-manage-a-redirect-request-after-a-jquery-ajax-call)

[jquery-ajax-issue-with-statuscode](https://stackoverflow.com/questions/50300558/jquery-ajax-issue-with-statuscode)

[statusCode parameter to $.ajax doesn't work with 302 responses](https://github.com/jquery/jquery/issues/3467)

[prevent-redirection-of-xmlhttprequest](https://stackoverflow.com/questions/228225/prevent-redirection-of-xmlhttprequest/343359#343359)
