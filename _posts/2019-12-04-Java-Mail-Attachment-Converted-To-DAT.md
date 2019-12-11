---
layout : post
title : Java Mail Attachment Converted To DAT
comments: true
categories : [Java]
---

Java mail `1.6.2`

Send an email with an attachment whose file name has 70 characters to my company email account.

When open this email from outlook client(which is c/s app), this attachment is shown as `.dat`.

However, send the same attachment to others(not my company account), this attachment is normal.

If we decrease the length of file name to 56, then this attachment is normal when sent to my company email account and open it from outlook
client.

If we login my company email from browser and open this email, this attachment is normal.

So this problem is exist in outlook client which installed in our pc, it cannot recognize this attachment sent from java mail program
with java mail 1.6.2.
(it can recognize this attachment sent from other's outlook client or sent from java mail 1.4.7)

The solution is : add `System.setProperty("mail.mime.encodeparameters", "false");` if java mail 1.6.2 is used.

### Reference

[The attached file name is converted to .dat in outlook](https://community.oracle.com/thread/4052611)

[javamail faq](https://javaee.github.io/javamail/FAQ)

[javamail package summary](https://javaee.github.io/javamail/docs/api/com/sun/mail/smtp/package-summary.html)
