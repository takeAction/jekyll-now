<style>
.tablelines table, .tablelines td, .tablelines th {
    border: 1px solid black;
}
</style>

### Terms

|Terms|Meaning|
|-----|-------|
|principal|user name|
|credential|password|
|authentication|can login or not|
|authorization|deciding what users are allowed to do(aka access control)|
|token|a key that you use to log in to a system, e.g. user name, password|
|subject|current user|
|SecurityManager|manages security operations for all users. there is almost always a single securitymanager instance per application. It is essentially an application singleton|
{:.tablelines}

### Session Management

1. container-independent
2. session data can be shared accross client technologies if desired, e.g. s swing desktop client can participate in the same web application session

### Permission

Permission check has two approaches :

1. base on permission class(implements Permission interface)
    e.g. Permission printPermission = new PrinterPermission("laserjet3000n","print");
         if (currentUser.isPermitted(printPermission)) { ....}
         
2. base on string(aka WildcardPermission)

    A string can contains three section, one is domation, second is action, the last is instance.  Each level can have multiple values.  Also, each part can use * to be represented.
    
    - string "newsletter:edit" means a user can edit the newsletter, it's called resource level
    
      In this example, the first token is the domain that is being operated on and the second token is the action being performed, for "newsletter:new,edit,delete", subject.isPermitted("newsletter:edit") will return true
     
     - for "newsletter:edit:12,13,18", it is instance level.  Assume 12,13 and 18 are the system's ID of the newsletter, that would allow the user to edit the newsletter 12, 13 and 18.
     
     user:view = user:view:*
     
     *:view cannot match system:user:view while *:*:view can match

### Authorization methods

1. programmatic authorization
2. annotation authorization, e.g. `@RequiresPermissions()`
3. jsp taglib authorization, e.g. `<shiro:hasPermission name="">...</shiro:hasPermission>
    
