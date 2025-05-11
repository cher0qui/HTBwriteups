

---

## Nmap Summary
| Port | Software    | Version                                 | Status  |
| ---- | ----------- | --------------------------------------- | ------- |
| 53   | domain      | Simple DNS                              | open    |
| 80   | http        | Apache http 2.4.29                      | open    |
| 139  | netbios-sec | Microsfot Windows netbios-ssn           | open    |
| 389  | ldap        | Microsoft Windows Active Directory LDAP | open    |


## Information Recon

Ports tcp open in nmap format

```bash

```

Ports services and versions nmap format

```bash

```

Ports UDP nmap format

```bash

```

---

## Enumeration

## Port 80 - HTTP (Apache)



![[Pasted image 20250125191214.png]]

``` java
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:104::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:104:105:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
pollinate:x:105:1::/var/cache/pollinate:/bin/false
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
syslog:x:107:113::/home/syslog:/usr/sbin/nologin
uuidd:x:108:114::/run/uuidd:/usr/sbin/nologin
tcpdump:x:109:115::/nonexistent:/usr/sbin/nologin
tss:x:110:116:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:111:117::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:112:118:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
usbmux:x:113:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
ralph:x:1000:1000:ralph:/home/ralph:/bin/bash
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false
avahi:x:114:120:Avahi mDNS daemon,,,:/run/avahi-daemon:/usr/sbin/nologin
geoclue:x:115:121::/var/lib/geoclue:/usr/sbin/nologin
postgres:x:116:123:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
_laurel:x:998:998::/var/log/laurel:/bin/false
ron:x:1001:1001:,,,:/home/ron:/bin/bash
```

Database.yml:

![[Pasted image 20250125192941.png]]

Cogemos los archivos "test" y "development";

![[Pasted image 20250125200410.png]]

encontramos el  hash de ralph , lo pasamos a john:
```sqlite3
1|ralph@heal.htb|$2a$12$dUZ/O7KJT3.zE4TOK8p4RuxH3t.Bz45DSr7A94VLvY9SWx1GCSZnG|2024-09-27 07:49:31.614858|2024-09-27 07:49:31.614858|Administrator|ralph|1
2|cher0@mail.com|$2a$12$V5gIpHzGI1aIbV4WD81bxO4VZzIHNgkCSnnqPmREgKjDq2zN6i4De|2025-01-25 18:24:48.597549|2025-01-25 18:24:48.597549|cher0 del rio |cher0|0

```


```bash
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 4096 for all loaded hashes
Will run 8 OpenMP threads
Proceeding with wordlist:/usr/share/john/password.lst
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:01 6.71% (ETA: 20:10:11) 0g/s 47.05p/s 47.05c/s 47.05C/s asdfgh..zephyr
147258369        (?)     
1g 0:00:00:47 DONE (2025-01-25 20:10) 0.02107g/s 47.04p/s 47.04c/s 47.04C/s 333333..iverson
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

Tenemos la password de ralph
	ralph:147258369


No funciona en ssh pero podemos acceder desde la url
http://take-survey.heal.htb/index.php/admin/authentication/sa/login

![[Pasted image 20250125201341.png]]

Podemos entrar! 
![[Pasted image 20250125201412.png]]



---

