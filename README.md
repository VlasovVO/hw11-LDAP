# hw11-LDAP

- Запускаем подготовленные виртуалки в Vagrantfile

## Настройка freeipa server

- Запускаем команду по установки сервера:

```
ipa-server-install \
--setup-dns \
--no-host-dns \
--hostname=ipa.freeipa-hw.lan \
--domain=freeipa-hw.lan \
--realm=FREEIPA-HW.LAN \
--ds-password 8characterslong \
--admin-password adminpass \
--ssh-trust-dns \
--forwarder=8.8.8.8 \
--auto-forwarde
```
- В предложенных вопросах отвечаем:
  
```
Do you want to search for missing reverse zones? [yes]: no

Continue to configure the system with these values? [no]: yes
```

- Разрешаем создавать домашнюю директорию при логине:

```
authconfig --enablemkhomedir --update
```

- Разрешаем и открываем порты для FreeIPA

```
firewall-cmd --add-service=freeipa-ldap
firewall-cmd --add-service=freeipa-ldap --permanent
firewall-cmd --reload
```

- Создадим нового пользователя

```bash
[root@ipa ~]# kinit admin
Password for admin@FREEIPA-HW.LAN:
[root@ipa ~]# ipa config-mod --defaultshell=/bin/bash
  Maximum username length: 32
  Home directory base: /home
  Default shell: /bin/bash
  Default users group: ipausers
  Default e-mail domain: freeipa-hw.lan
  Search time limit: 2
  Search size limit: 100
  User search fields: uid,givenname,sn,telephonenumber,ou,title
  Group search fields: cn,description
  Enable migration mode: FALSE
  Certificate Subject base: O=FREEIPA-HW.LAN
  Password Expiration Notification (days): 4
  Password plugin features: AllowNThash, KDC:Disable Last Success
  SELinux user map order: guest_u:s0$xguest_u:s0$user_u:s0$staff_u:s0-s0:c0.c1023$unconfined_u:s0-s0:c0.c1023
  Default SELinux user: unconfined_u:s0-s0:c0.c1023
  Default PAC types: MS-PAC, nfs:NONE
  IPA masters: ipa.freeipa-hw.lan
  IPA CA servers: ipa.freeipa-hw.lan
  IPA NTP servers: ipa.freeipa-hw.lan
  IPA CA renewal master: ipa.freeipa-hw.lan
  IPA master capable of PKINIT: ipa.freeipa-hw.lan
[root@ipa ~]# ipa user-add sysadm --first=System --last=Admin --password
Password:
Enter Password again to verify:
-------------------
Added user "sysadm"
-------------------
  User login: sysadm
  First name: System
  Last name: Admin
  Full name: System Admin
  Display name: System Admin
  Initials: SA
  Home directory: /home/sysadm
  GECOS: System Admin
  Login shell: /bin/bash
  Principal name: sysadm@FREEIPA-HW.LAN
  Principal alias: sysadm@FREEIPA-HW.LAN
  User password expiration: 20190322130616Z
  Email address: sysadm@freeipa-hw.lan
  UID: 1185600001
  GID: 1185600001
  Password: True
  Member of groups: ipausers
  Kerberos keys available: True
```

- Добавим DNS запись о клиенте:

```
ipa dnsrecord-add freeipa-hw.lan client.freeipa-hw.lan --a-rec 192.168.11.102
```

## Настройка клиента

- Запустим настройку FREEIPA клиента:

```
ipa-client-install \
--hostname=`hostname -f` \
--mkhomedir \
--server=ipa.freeipa-hw.lan \
--domain freeipa-hw.lan \
--realm FREEIPA-HW.LAN
```
На интерактивные вопросы отвечаем:
```
Proceed with fixed values and no DNS discovery? [no]: yes

Continue to configure the system with these values? [no]: yes
```

В процессе установки авторизуемся под админом

```
User authorized to enroll computers: admin
Password for admin@FREEIPA-HW.LAN:
```

- Разрешаем создавать домашнюю директорию при логине:

```
authconfig --enablemkhomedir --update
```

- Заходим по ранее созданным пользователем и меняем ему пароль:
  
```
[root@client ~]# ssh sysadm@192.168.11.102
Password:
Password expired. Change your password now.
Current Password:
New password:
Retype new password:
Creating home directory for sysadm.
[sysadm@client ~]$
```
