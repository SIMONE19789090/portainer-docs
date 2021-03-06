# LDAP/AD Authentication

Portainer can be configured to accept Lightweight Directory Access Protocol (LDAP) authentication if your organization has implemented LDAP or Active Directory authentication. When users attempt to log into Portainer, the application will authenticate them against your LDAP directory or Active Directory. If authentication is successful, the user is allowed to log into Portainer.

In order to configure Portainer LDAP authentication, you first need to add a user to your directory service for the purpose of authenticating from Portainer to read the LDAP. The user should be a service account that needs read-only access to LDAP/Active Directory.

## Enabling LDAP

Log into Portainer as an administrator and navigate to Settings > Authentication. Select the ‘LDAP Authentication' option. You will see two options: <b>Custom</b> where you can define a custom/third party LDAP Server to use and <b>OpenLDAP</b> where you will capable to enter all the data needed to connect with OpenLDAP.

## Custom LDAP

![auth](assets/ldap_1.png)

Enter in the IP address/FQDN and the port number of your LDAP server, and then select to either connect anonymously (your LDAP server must support this), or enter a user account that has READ access to the directory. Click "Test Connectivity" to validate you can connect.

A configuration with Anonymous mode on (must be supported)

![auth](assets/ldap2.png)

A configuration defining <b>Reader DN</b> and <b>Password</b>.

![auth](assets/ldap3.png)

### Explanation of Settings

Here is an explanation of the above settings:

### LDAP Security

* Use StartTLS: After initial connection, elevate the insecure connection to secure.
* Use TLS: Initiate a connection to LDAP using TLS.
* Skip Verification of Certificate: If you do not have access to the certificate of the LDAP server, skipping verification enables encrypted communications, but you must manually ensure that you are talking to the intended LDAP server that you gave in your URL. If that gets maliciously redirected then you could be talking to a different server. Use with caution.
* TLS CA Certificate: Upload your CA Certificate for the secure connection.
* Connectivity Check:Validate successful connectivity before continuing.

### Automatic User Provisioning

* Automatic User Provisioning: Enabling this setting automatically creates users within Portainer once they are successfully authenticated by LDAP. If you do not enable this, you must manually create users with the same username as the corresponding LDAP directory.

### User Search Configurations

* Base DN: Enter <code>DC=MYDOMAIN,DC=com</code> to search your entire Directory for the username attempting to login. or <code>OU=<MYOU>,DC=<MYDOMAIN>,DC=<COM></code> to search for users only within the specified OU, or <code>CN=NAME,DC=<MYDOMAIN>,DC=<COM></code> if your users are only in a container (in AD, the default is that all users are in a container called users; CN=Users). If you have a large number of users in your Domain, you should narrow the scope Portainer searches by using OU's.

* Username Attribute: For Native LDAP, Enter <code>uid</code>; For Active Directory, either enter <code>userPrincipalName</code> if your usernames will be in the format of <code>user@mydomain.com</code> enter <code>SAMAccountName</code> if your usernames will be in the format <code>username</code>. Do not use uid with Active Directory as it will not work.

Note: These entries are case sensitive.

* Filter: Enter an criteria to pre-filter the results returned from LDAP to Portainer.

Example, to only allow users who are members of a group, which is a group defined within an OU to login, set the Filter to be: (the brackets are important, so copy the entire string below)...

<pre><code>(&(objectClass=user)(memberOf=CN=<GROUPNAME>,OU=<MYOU>,DC=<DOMAIN>,DC=<DOMAIN>))</code></pre>

The example below shows that in the domain portainer.local, we have an OU called "Groups" and within that OU is a group called "PortainerDevUsers". This search filter will only allow users to login to Portainer that are members of the PortainerDevUsers LDAP group.

![auth](assets/ldap_5.png)

### Team auto-population configurations

Portainer optionally allows you to set a Group Search as well as the User Search. If this is configured, if an LDAP user is a member of an LDAP group, and that LDAP Group corresponds to an identically named Portainer TEAM, then the LDAP user will automatically be placed into the Portainer Team based on their LDAP group membership. This is very useful for automatically granting access to Portainer endpoints via group membership.

* Group Base DN: Enter <code>DC=<MYDOMAIN>,DC=<COM></code> to search your entire Directory for the list of groups. or <code>OU=<MYOU>,DC=<MYDOMAIN>,DC=<COM></code> to search for groups only within the specified OU, or <code>CN=NAME,DC=<MYDOMAIN>,DC=<COM></code> if your groups are only in a container (in AD, the default is that all groups are in a container called users; CN=Users). If you have a large number of groups in your Domain, you should narrow the scope Portainer searches by using OU's.

![auth](assets/ldap_6.png)

* Group Membership Attribute: Enter "member" as the attribute that determines if a user is a member of a group.

* Group Filter: If you want to filter the list of groups returned, to say, only return groups that contain the string "Portainer" (eg PortainerDev, PortainerProd, PortainerUAT", you can set the filter as follows:

<pre><code>(&(objectclass=group)(cn=*Portainer*))</code></pre>

![auth](assets/ldap_7.png)

### Optional - if you are NOT enabling user Auto Provisioning - Creating LDAP Users

Navigate to User Management. Create a username that matches your LDAP source users with the format defined when enabling LDAP (either ‘username’ or ‘username@mydomain.com’).

![auth](assets/ldap_8.webp)

![auth](assets/ldap_9.webp)

## OpenLDAP

![auth](assets/openldap1.png)

Enter in the IP address/FQDN and the port number of your LDAP server, and then select to either connect anonymously (your LDAP server must support this), or enter a user account that has READ access to the directory. Click "Test Connectivity" to validate you can connect.

A configuration with Anonymous mode on (must be supported)

![auth](assets/ldap2.png)

A configuration defining <b>Reader DN</b> and <b>Password</b>.

![auth](assets/ldap3.png)

### Explanation of Settings

Here is an explanation of the above settings:

### LDAP Security

* Use StartTLS: After the initial connection, elevate the insecure connection to secure.
* Use TLS: Initiate a connection to LDAP using TLS.
* Skip Verification of Certificate: If you do not have access to the certificate of the LDAP server, skipping verification enables encrypted communications, but you must manually ensure that you are talking to the intended LDAP server that you gave in your URL. If that gets maliciously redirected then you could be talking to a different server. Use with caution.
* TLS CA Certificate: Upload your CA Certificate for the secure connection.
* Connectivity Check:Validate successful connectivity before continuing.

### Automatic User Provisioning

* Automatic User Provisioning: Enabling this setting automatically creates users within Portainer once they are successfully authenticated by LDAP. If you do not enable this, you must manually create users with the same username as the corresponding LDAP directory.

### User Search Configurations

* Root Domain: Will be used the DN used when you authenticate to the OpenLDAP Server.
* User Search Path (Optional): Here you can define differents OU or Folder.
* Allowed Groups (optional): You can specify another group and their path to the directory.
* User Filter: is filled by default according to OpenLDAP configuration. 

To check if everything works as expected, click <b>Display Users</b> and you will see a list with the names configured in the directory.

![auth](assets/openldap2.png)

### Team auto-population configurations

Portainer optionally allows you to set a Group Search as well as the User Search. If this is configured, if an LDAP user is a member of an LDAP group, and that LDAP Group corresponds to an identically named Portainer TEAM, then the LDAP user will automatically be placed into the Portainer Team based on their LDAP group membership. This is very useful for automatically granting access to Portainer endpoints via group membership.

* Group Search Path (optional): Here you can define differents OU or Folder.
* Group Base DN: Will be used the DN used when you authenticate to the OpenLDAP Server.
* Group Filter: Is filled default according to OpenLDAP configuration. 

To check if everything works as expected, click <b>Display Users</b> and <b>Groups</b> and you will see a list with the names configured in the directory.

![auth](assets/openldap3.png)

### Test Login

If you want to know if your configuration is valid, you can run a test login from the configuration of OpenLDAP settings. Scrolldown to <b>Test Login</b> Section, fill with a valid user and password and click <b>Test</b>. If everything works as expected, you will see a check beside of the button.

![auth](assets/openldap4.png)

### Optional - if you are NOT enabling user Auto Provisioning - Creating LDAP Users

Navigate to User Management. Create a username that matches your LDAP source users with the format defined when enabling LDAP (either ‘username’ or ‘username@mydomain.com’).

![auth](assets/ldap_8.webp)
![auth](assets/ldap_9.webp)


## Notes

[Contribute to these docs](https://github.com/portainer/portainer-docs/blob/master/contributing.md).