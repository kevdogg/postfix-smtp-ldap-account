# Postfix SMTP ldap account
## Ansible Role which allows ansible script to provision ou=mail,dc=ldap,dc=gohilton,dc=com accounts when postfix is installed to local server


This role is used in conjunction with [postfix-setup](https://github.com/kevdogg/postfix-setup) ansible role to standup a postfix localhost relay on server<br><br>
The localhost postfix relay will either forward mail to smtp-prospect.gohilton.com or smtp-quincy.gohilton.com depending on ldap configuration.<br><br>
Mail is sent over an encrypted TLS 1.3 connection using a post-quantum algorithm <br><br>
The local postfix server needs to authenticate to either smtp-prospect.gohilton.com or smtp-quincy.gohilton.com.  These servers will authenticate using ldap (ldap-prospect.gohilton.com or ldap-quincy.gohilton.com ((ldap-active.gohilton.com))) in via cyrus/ldap plugin. <br><br>
<br>
When provisioning the localhost postfix simple relay server, the ansible role will create the localhost username/password which will be used for the postfix server authentication.  The role will add this localhost username/password to the ldap database. The username will be smtp-<short hostname> and the password will be an argon2 password. <br>
<br>
The ultimate source of truth will be stored within an ansible encrypted vault which is specify the smtp-<short hostname> with unencrypted password. The model will look like the following: <br><br>
```
ONE vault.yml
│
├── ldap_provisioner_password       shared
├── ansible_become_password         shared (if applicable)
└── postfix_sasl_passwords
      ├── arch160.gohilton.com      unique
      ├── server2.gohilton.com      unique
      └── ...
```
<br>
This provisioner will first check if account exists.  If account exists it will check ldap password vs vault password and a flag will designate whether to change password to vault password if they differ, and it not, the playwill end. If account does not exist, the account will be created with the username/argon2 encrypted password
