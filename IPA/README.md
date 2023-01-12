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

```
