---
layout: post
title: struts2 no result defined for action and input result
date: 2016-08-11 22:40
author: 2freesky
comments: true
categories: [struts]
---
my struts2 action is :
<blockquote>public class TicketAction extends ActionSupport {

private String bookingNo;

private Integer bookingId;

private Integer customerId

//getter and setter

public String showTicket() {

logger.debug("Enter showTicket");

//get bookingNo, bookingId and customerId from url parameter

}

}</blockquote>
User will click a url to access action 'showTicket', the url parameters are encrypted and encoded in order to protect the attack, so the url looks like : http://192.169.0.1/showTicket?bookingNo=xxxx&amp;bookingId=xxxx&amp;customerId=xxxx

But when user click the url link, error : No result defined for action TicketAction and result input thrown.  And the log info "Enter showTicket" didn't show.

The reason is : there are bookingNo, bookingId and customerId fields in TicketAction, and their name are the same as parameter names in url, hence struts will populate the parameter values from url to TicketAction fields before executing action method : showTicket().

In action class, I defined bookingId and customerId as Integer, but they should be String if we use this approach to get their values, so this error happened.

The solution is : remove these fields from struts action class, and get their value in showTicket() with code : String bookingNo = ServletActionContext.getRequest().getParameter("bookingNo");

Or replace Integer with String.
