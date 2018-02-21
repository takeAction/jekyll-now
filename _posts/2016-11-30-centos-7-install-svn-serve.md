---
layout: post
title: centos 7 install svn server
categories: [linux]
---

1. install httpd(apache)
`sudo yum install httpd`
**Logs files are in /var/log/httpd**

2. install svn and apache module mod_dav_svn to make svn work with apache
`sudo yum install subversion mod_dav_svn`

3. modify svn configuration file
`sudo vi /etc/httpd/conf.modules.d/10-subversion.conf`
append the following content

```
<Location /svn>
DAV svn
SVNParentPath /svn
AuthName "SVN Repos"
AuthType Basic
AuthUserFile /etc/svn/svn-auth
AuthzSVNAccessFile /svn/authz
Require valid-user

</Location>

```

```
<Location> applies the enclosed directives only to matching URLs
Syntax: <Location URL-path|URL> ... </Location>

In the example below, requests to /private1, /private1/ and /private1/file.txt will have the enclosed directives applied, but /private1other would not.

<Location "/private1">
# ...
</Location>

While for following example, requests to /private2/ and /private2/file.txt will have the enclosed directives applied, but /private2 and /private2other would not.

<Location "/private2/">
# ...
</Location>

```

DAV svn
Must be included in any Directory or Location block for a Subversion repository. It tells httpd to use the Subversion backend for mod_dav to handle all requests.

SVNParentPath directory-path
Specifies the location in the filesystem of a parent directory whose child directories are Subversion repositories. In a configuration block for a Subversion repository, either this directive or SVNPath must be present, but not both.

SVNPath directory-path
Specifies the location in the filesystem for a Subversion repository's files. In a configuration block for a Subversion repository, either this directive or SVNParentPath must be present, but not both.

AuthName is an arbitrary name that you choose for the authentication domain. Most browsers display this name in the dialog box when prompting for username and password.

AuthType specifies the type of authentication to use.

AuthUserFile specifies the location of the password file to use.

AuthzSVNAccessFile specifies a file containing the permissions policy for paths within your repositories.</blockquote>

4. create svn repo
`sudo mkdir /svn`
`cd /svn`
`sudo svnadmin create test`
`sudo chown -R apache:apache test`

5. add svn user
`sudo mkdir /etc/svn`
`sudo htpasswd -cm /etc/svn/svn-auth admin`
`sudo chown root:apache /etc/svn/svn-auth`
`sudo chmod 640 /etc/svn/svn-auth</blockquote>`

if you want to add second user
`sudo htpasswd -m /etc/svn/svn-auth user1`
remove c flag, just use -m from second user

6. set permission for users

Following config called path-based authorization which used to restrict the access to svn files. You need to ask yourself that do you really need it before using it or it's just something that sounds good, because there are often invisible or visible costs associated with this feature.
`sudo cp /svn/repo1/conf/authz /svn/authz`
`sudo vi /svn/authz`

```
[groups]
admin=admin
repo1_user=user1,user2
[/]
@admin=rw

[test:/]
@repo1_user=rw
```

In this file, `[groups]` is used to defined user group, e.g. repo1_user is group name, user1 is its user.

`[/]` or `[test:/]` is svn repository path, its value can be `[repos-name:path]` or `[path]` when AuthzSVNAccessFile is used.

If you configured per repository access files via AuthzSVNReposRelativeAccessFile directive, you should always use `[path]` form only.

`[repos-name:path]` or `[path]` describe the permission of group or user for path, `@admin=rw` means admin is a group and it can read and write, while admin=r represents admin is a user and it only can read.

If the user is not mentioned at all, no access is allowed.

If you're using the SVNParentPath directive, it's important to specify the repository names in your sections. If you omit them, a section such as `[/some/dir]` will match the path /some/dir in every repository. If you're using the SVNPath directive, however, it's fine to provide only paths in your sections—after all, there's only one repository.

```
[/]
* = r
```

This makes all repositories world-readable to all users.

7. change selinux security context if selinux enabled

403 forbidden will be thrown withoud next modification.
`sudo chcon -Rv –type=httpd_sys_content_t /svn`

8. start appache<
`sudo systemctl start httpd.service`
if you want apache start automatically when system boot, then we can use

`sudo systemctl enable httpd.service`

9. modify firewall such that other computers can access the svn
`sudo firewall-cmd --zone=public --permanent --add-service=http`
`sudo firewall-cmd --reload`

Finally, we can access svn from browser by http://svn/test.

Reference websites:

	http://svnbook.red-bean.com/en/1.7/svn.ref.mod_dav_svn.conf.html
	http://svnbook.red-bean.com/en/1.8/svn.serverconfig.pathbasedauthz.html
	https://httpd.apache.org/docs/2.4/mod/core.html#location
	http://svnbook.red-bean.com/en/1.7/svn.serverconfig.httpd.html

