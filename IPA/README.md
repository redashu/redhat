## Understanding and configuration of IPA in rhel 7 env 

### IDM -- to manage Identity we are going to use IPA 

### Hosts 

| IPA Server    | IPA Client    |
| ------------- | ------------- |
| idm.ashutoshh.india  | server79.ashutoshh.india  |
| 192.168.1.170  | 192.168.1.79  |

## Steps Configure and install IPA server 

### IPA 
<p> IPA server will create DNS,Kerberos,LDAP , NTP </p>

### setup hostname and ip statically 

```
hostnamectl set-hostname idm.ashutoshh.india 
```

### Installing software 
```
yum install ipa-server ipa-server-dns ipa-server-trust-ad bind bind-dyndb-ldap -y 
```

### COnfigure IPA server 

```
ipa-server-install  --setup-dns --no-forwarders --auto-reverse -p "Redhat@123" -a "Redhat@123" -n ashutoshh.india  -r ASHUTOSHH.INDIA --netbios-name=ashutoshh  --setup-adtrust --setup-kra --idstart=5000 --idmax=20000 --enable-compat --mkhomedir --no-host-dns --hostname=idm.ashutoshh.india
```

#### Note: this is take around 25 to 30 minutes 

### you can verify using 

```
[root@idm ~]# ipactl  status 
Directory Service: RUNNING
krb5kdc Service: RUNNING
kadmin Service: RUNNING
named Service: RUNNING
httpd Service: RUNNING
ipa-custodia Service: RUNNING
ntpd Service: RUNNING
pki-tomcatd Service: RUNNING
smb Service: RUNNING
winbind Service: RUNNING
ipa-otpd Service: RUNNING
ipa-dnskeysyncd Service: RUNNING
ipa: INFO: The ipactl command was successful
[root@idm ~]# 


```

### login with admin user using kerberos 

```
[root@idm ~]# kinit  admin
Password for admin@ASHUTOSHH.INDIA: 
[root@idm ~]# klist 
Ticket cache: KEYRING:persistent:0:0
Default principal: admin@ASHUTOSHH.INDIA

Valid starting     Expires            Service principal
01/12/23 17:41:46  01/13/23 17:41:42  krbtgt/ASHUTOSHH.INDIA@ASHUTOSHH.INDIA
[root@idm ~]# 


```

### you can search user also 

```
[root@idm ~]# ipa user-find admin
--------------
1 user matched
--------------
  User login: admin
  Last name: Administrator
  Home directory: /home/admin
  Login shell: /bin/bash
  Principal alias: admin@ASHUTOSHH.INDIA
  UID: 5000
  GID: 5000
  Account disabled: False
----------------------------
Number of entries returned 1
----------------------------

```

## TIme for IPA client configuration 

```
[root@server79 ~]# yum install ipa-client -y 
```

### connecting with IPa-server

```
[root@server79 ~]# ipa-client-install 
Discovery was successful!
Client hostname: server79.ashutoshh.india
Realm: ASHUTOSHH.INDIA
DNS Domain: ashutoshh.india
IPA Server: idm.ashutoshh.india
BaseDN: dc=ashutoshh,dc=india

Continue to configure the system with these values? [no]: yes
Synchronizing time with KDC...
Attempting to sync time using ntpd.  Will timeout after 15 seconds
User authorized to enroll computers: admin 
Password for admin@ASHUTOSHH.INDIA: 
Successfully retrieved CA cert
    Subject:     CN=Certificate Authority,O=ASHUTOSHH.INDIA
    Issuer:      CN=Certificate Authority,O=ASHUTOSHH.INDIA
    Valid From:  2023-01-11 09:38:39
    Valid Until: 2043-01-11 09:38:39


```

### Now testing IPA client 

```
[root@server79 ~]# kinit  admin
Password for admin@ASHUTOSHH.INDIA: 
[root@server79 ~]# 
[root@server79 ~]# klist 
Ticket cache: KEYRING:persistent:0:0
Default principal: admin@ASHUTOSHH.INDIA

Valid starting     Expires            Service principal
01/12/23 17:51:25  01/13/23 17:51:22  krbtgt/ASHUTOSHH.INDIA@ASHUTOSHH.INDIA
[root@server79 ~]# 
```

### try with su 

```
[root@server79 ~]# su - admin
Last login: Thu Jan 12 17:49:35 IST 2023 on pts/0
su: warning: cannot change directory to /home/admin: No such file or directory
-bash-4.2$ 
-bash-4.2$ whoami
admin
-bash-4.2$ id
uid=5000(admin) gid=5000(admins) groups=5000(admins) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
-bash-4.2$ 
```

### Note: there is no autofs configured so we are not getting home directory 

### Note: by default ssh with kerberos is also configured 

