基本概念

CN 是一个具体的资源，

OU 是一个容器，里面可以包含各种CN

DC 是包含各种OU的，

一般会有一个或者多个DC，

部署

yum install openldap openldap-servers openldap-clients openldap-devel compat-openldap -y

配置

初始化配置

1、数据库生成

cp /usr/share/openldap-server/DB_CONFIG.example /var/lib/ldap/DB_CONFIG

chwon -R ldap:ldap /var/lib/ldap/

2、创建配置 /etc/openldap/slapd.conf 

\#include /etc/openldap/schema/collective.schema

include /etc/openldap/schema/corba.schema

include /etc/openldap/schema/core.schema

include /etc/openldap/schema/cosine.schema

include /etc/openldap/schema/duaconf.schema

include /etc/openldap/schema/dyngroup.schema

include /etc/openldap/schema/inetorgperson.schema

include /etc/openldap/schema/java.schema

include /etc/openldap/schema/misc.schema

include /etc/openldap/schema/nis.schema

include /etc/openldap/schema/openldap.schema

include /etc/openldap/schema/pmi.schema

include /etc/openldap/schema/ppolicy.schema

\#include /etc/openldap/schema/sudo.schema

allow bind_v2

pidfile /var/run/openldap/slapd.pid

argsfile /var/run/openldap/slapd.args

moduleload syncprov.la

modulepath /usr/lib64/openldap

database bdb

suffix "dc=wallace,dc=com"

rootdn "cn=Manager,dc=wallace,dc=com"

rootpw wallace

directory /var/lib/ldap

loglevel 256

3、配置日志

touch /var/log/slapd.log

mkdir /var/log/slapd

chmod 755 /var/log/slapd

chown ldap:ldap /var/log/slapd

vi /etc/rsyslog

local4.*                         /var/log/slapd/slapd.log

systemctl restart rsyslog

4、删除/etc/openldap/slapd.d/*

rm -rf /etc/openldap/slapd.d/*

5、启动ldap服务

systemctl start slapd

6、检查配置文件，并且根据配置生成/etc/openldap/slapd.d/下的内容

 slaptest -f /etc/openldap/slapd.conf 检查配置文件

 slaptest -f /etc/openldap/slapd.conf -F /etc/openldap/slapd.d/  生成配置

7、创建目录树组织结构

创建组织结构

cat << EOF | ldapadd -x -D cn=Manager,dc=wallace,dc=com -W

dn: dc=wallace,dc=com

dc: wallace

objectClass: top

objectClass: domain

 

dn: ou=people, dc=wallace, dc=com

ou: people

objectClass: top

objectClass: organizationalUnit

 

dn: ou=group, dc=wallace, dc=com

ou: ou=group

objectClass: top

objectClass: organizationalUnit

EOF

查看创建结构

ldapsearch -x -LLL

8、创建用户

创建用户

 cat << EOF | ldapadd -x -D cn=Manager,dc=wallace,dc=com -W 

dn: uid=wallace,ou=people,dc=wallace,dc=com

objectClass: posixAccount

objectClass: shadowAccount

objectClass: person

objectClass: inetOrgPerson

cn: System

sn: a

givenName: ning

displayName: aning

uid: wallace

userPassword: wallace 

uidNumber: 1001

gidNumber: 1001

gecos: System Manager

homeDirectory: /home/gdy

loginShell: /bin/nologin

shadowLastChange: 16020

shadowMin: 0

shadowMax: 9999999

shadowWarning: 7

shadowExpire: -1

employeeNumber: 1111111

homePhone: 1111111

mobile: 1111111111

mail: test@126.com

postalAddress: shanghai

initials: Test

EOF

 

userPassword: wallace 这个是设置用户密码

使用migrationtools 提取系统用户，而后通过ldapadd创建

1、配置migrationtools  vi /usr/share/migrationtools/migrate_common.ph

\# Default DNS domain

$DEFAULT_MAIL_DOMAIN = "wallace.com";

 

\# Default base

$DEFAULT_BASE = "dc=wallace,dc=com";

2、导出用户，添加进LDAP

先创建一个用户，设置密码，

tail -n 1 /etc/passwd > 1

/usr/share/migrationtools/migrate_passwd.pl 1 people.ldif

ldapadd -x -W -D "cn=Manager,dc=wallace,dc=com" -f people.ldif

需要输入密码 密码是在/etc/openldap/slapd.conf 中配置的 rootpw的值

查看创建的用户

ldapsearch -x -LLL

9、对接第三方

linux系统登陆接入ldap

* 安装软件

yum install openldap-clients nss-pam-ldapd -y

2、配置

vi /etc/nslcd.conf

```ini
ldap_version 3

uri ldap://192.168.137.130/

base dc=wallace,dc=com

ssl no

/etc/pam.d/system-auth

auth    required   pam_env.so

auth    required   pam_faildelay.so delay=2000000

auth    sufficient  pam_unix.so nullok try_first_pass

auth    requisite   pam_succeed_if.so uid >= 1000 quiet_success

auth    sufficient  pam_ldap.so use_first_pass

auth    required   pam_deny.so

 

account   required   pam_unix.so

account   sufficient  pam_localuser.so

account   sufficient  pam_succeed_if.so uid < 1000 quiet

account   [default=bad success=ok user_unknown=ignore] pam_ldap.so

account   required   pam_permit.so

 

password  requisite   pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=

password  sufficient  pam_unix.so sha512 shadow nullok try_first_pass use_authtok

password  sufficient  pam_ldap.so use_authtok

password  required   pam_deny.so

 

session   optional   pam_keyinit.so revoke

session   required   pam_limits.so

-session   optional   pam_systemd.so

session   [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid

session   required   pam_unix.so

session   optional   pam_ldap.so
cat /etc/pam.d/password-auth
auth    required   pam_env.so
auth    required   pam_faildelay.so delay=2000000
auth    sufficient  pam_unix.so nullok try_first_pass
auth    requisite   pam_succeed_if.so uid >= 1000 quiet_success
auth    sufficient  pam_ldap.so use_first_pass
auth    required   pam_deny.so
account   required   pam_unix.so
account   sufficient  pam_localuser.so
account   sufficient  pam_succeed_if.so uid < 1000 quiet
account   [default=bad success=ok user_unknown=ignore] pam_ldap.so
account   required   pam_permit.so
password  requisite   pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
password  sufficient  pam_unix.so sha512 shadow nullok try_first_pass use_authtok
password  sufficient  pam_ldap.so use_authtok
password  required   pam_deny.so
session   optional   pam_keyinit.so revoke
session   required   pam_limits.so
-session   optional   pam_systemd.so
session   [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
session   required   pam_unix.so
session   optional   pam_ldap.so
```

/etc/nsswitch.conf

![image-20191216075918696](C:\Users\wallace\OneDrive\笔记2\运维\images\未命名图片.png)

/etc/sysconfig/authconfig

USELDAP=yes

USELDAPAUTH=yes

USELOCAUTHORIZE=yes

USESHADOW=yes

启动nslcd进程

systemctl start nslcd

前台启动 nslcd -d 主要用于测试

而后进行登陆认证