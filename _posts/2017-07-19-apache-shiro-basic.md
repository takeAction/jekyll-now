#### Terms

| Terms | Meaning |
| ----- | ------- |
| principal | user name |
| credential | password |
| authentication | can login or not |
| authorization | deciding what users are allowed to do(aka access control) |
| token | a key that you use to log in to a system, e.g. user name, password|
| subject | current user |
| SecurityManager | manages security operations for all users. there is almost always a single securitymanager instance per application. It is essentially an application singleton |

#### Session Management

1. container-independent
2. session data can be shared accross client technologies if desired, e.g. s swing desktop client can participate in the same web application session

#### Permission level

1. resource level : a user can edit customer records
2. instance level : a user can edit the customer record for ibm
3. attibute level : a user can edit the address of the ibm customer record

#### Authorization methods

1. programmatic authorization
2. annotation authorization, e.g. `@RequiresPermissions()`
3. jsp taglib authorization, e.g. `<shiro:hasPermission name="">...</shiro:hasPermission>
    
