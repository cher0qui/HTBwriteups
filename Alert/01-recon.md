{TPC SynScan}
```bash
sudo nmap -sS --min-rate 5000 -Pn -n -vvv alert.htb -oN reconsS.txt 
[sudo] contraseña para kali: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-26 16:19 CET
Initiating SYN Stealth Scan at 16:19
Scanning alert.htb (10.10.11.44) [1000 ports]
Discovered open port 80/tcp on 10.10.11.44
Discovered open port 22/tcp on 10.10.11.44
Completed SYN Stealth Scan at 16:19, 0.25s elapsed (1000 total ports)
Nmap scan report for alert.htb (10.10.11.44)
Host is up, received user-set (0.037s latency).
Scanned at 2024-11-26 16:19:50 CET for 0s
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.37 seconds
           Raw packets sent: 1000 (44.000KB) | Rcvd: 1000 (40.008KB)

```
{TCP SCV}
```bash
 sudo nmap -sCV --min-rate 5000 -Pn -n -vvv alert.htb  -p 22,80 -oN reconsCV.txt
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-26 16:20 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
Initiating SYN Stealth Scan at 16:20
Scanning alert.htb (10.10.11.44) [2 ports]
Discovered open port 22/tcp on 10.10.11.44
Discovered open port 80/tcp on 10.10.11.44
Completed SYN Stealth Scan at 16:20, 0.06s elapsed (2 total ports)
Initiating Service scan at 16:20
Scanning 2 services on alert.htb (10.10.11.44)
Completed Service scan at 16:20, 6.17s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.11.44.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 1.31s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.15s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
Nmap scan report for alert.htb (10.10.11.44)
Host is up, received user-set (0.037s latency).
Scanned at 2024-11-26 16:20:42 CET for 8s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 7e:46:2c:46:6e:e6:d1:eb:2d:9d:34:25:e6:36:14:a7 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDSrBVJEKTgtUohrzoK9i67CgzqLAxnhEsPmW8hS5CFFGYikUduAcNkKsmmgQI09Q+6pa+7YHsnxcerBnW0taI//IYB5TI/LSE3yUxyk/ROkKLXPNiNGUhC6QiCj3ZTvThyHrFD9ZTxWfZKEQTcOiPs15+HRPCZepPouRtREGwmJcvDal1ix8p/2/C8X57ekouEEpIk1wzDTG5AM2/D08gXXe0TP+KYEaZEzAKM/mQUAqNTxfjc9x5rlfPYW+50kTDwtyKta57tBkkRCnnns0YRnPNtt0AH374ZkYLcqpzxwN8iTNXaeVT/dGfF4mA1uW89hSMarmiRgRh20Y1KIaInHjv9YcvSlbWz+2sz3ev725d4IExQTvDR4sfUAdysIX/q1iNpleyRgM4cvDMjxD6lEKpvQYSWVlRoJwbUUnJqnmZXboRwzRl+V3XCUaABJrA/1K1gvJfsPcU5LX303CV6LDwvLJIcgXlEbtjhkcxz7b7CS78BEW9hPifCUDGKfUs=
|   256 45:7b:20:95:ec:17:c5:b4:d8:86:50:81:e0:8c:e8:b8 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHYLF+puo27gFRX69GBeZJqCeHN3ps2BScsUhKoDV66yEPMOo/Sn588F/wqBnJxsPB3KSFH+kbYW2M6erFI3U5k=
|   256 cb:92:ad:6b:fc:c8:8e:5e:9f:8c:a2:69:1b:6d:d0:f7 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIG/QUl3gapBOWCGEHplsOKe2NlWjlrb5vTTLjg6gMuGl
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-title: Alert - Markdown Viewer
|_Requested resource was index.php?page=alert
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.35 seconds
           Raw packets sent: 2 (88B) | Rcvd: 2 (88B)

```
---

## Nmap Summary
| Port | Software | Version                        | Status |
| ---- | -------- | ------------------------------ | ------ |
| 22   | openssh  | OpenSSH 8.2p1                  | open   |
| 80   | http     | Apache httpd 2.4.41 ((Ubuntu)) | open   |

---

## Enumeration

## Port 80 - HTTP (Apache)
## Fuzz
```bash
wfuzz --sc 400,200,300 -w /usr/share/wordlists/wfuzz/general/common.txt http://alert.htb/FUZZ.php 
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://alert.htb/FUZZ.php
Total requests: 951

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                    
=====================================================================

000000203:   200        1 L      3 W        24 Ch       "contact"                                                                                                  
000000521:   200        1 L      0 W        1 Ch        "messages"                                                                                                 

Total time: 0
Processed Requests: 951
Filtered Requests: 949
Requests/sec.: 0


```
### About us..
![[Pasted image 20241126162421.png]]
### Main

![[Pasted image 20241126162252.png]]
#### code

##### view-source:http://alert.htb/index.php?page=alert
```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="[css/style.css](view-source:http://alert.htb/css/style.css)">
    <title>Alert - Markdown Viewer</title>
</head>
<body>
    <nav>
        <a href="[index.php?page=alert](view-source:http://alert.htb/index.php?page=alert)">Markdown Viewer</a>
        <a href="[index.php?page=contact](view-source:http://alert.htb/index.php?page=contact)">Contact Us</a>
        <a href="[index.php?page=about](view-source:http://alert.htb/index.php?page=about)">About Us</a>
        <a href="[index.php?page=donate](view-source:http://alert.htb/index.php?page=donate)">Donate</a>
            </nav>
    <div class="container">
        <h1>Markdown Viewer</h1><div class="form-container">
            <form action="[visualizer.php](view-source:http://alert.htb/visualizer.php)" method="post" enctype="multipart/form-data">
                <input type="file" name="file" accept=".md" required>
                <input type="submit" value="View Markdown">
            </form>
          </div>    </div>
    <footer>
        <p style="color: black;">© 2024 Alert. All rights reserved.</p>
    </footer>
</body>
</html>
```
##### http://alert.htb/visualizer.php
```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alert - Markdown Viewer</title>
    <link rel="stylesheet" href="[css/style.css](view-source:http://alert.htb/css/style.css)">
    <style>
        .share-button {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: rgb(100, 100, 100);
            color: #fff;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    Please upload a Markdown file.</body>
</html>
```

Intentamos subir un archivo markdown ..
![[Pasted image 20241126163005.png]]

Vamos  a intentar un **XSS** en el archivo markdown, para ello:


```html
<!-- XSS with regular tags -->
<script>alert(1)</script>
<img src=x onerror=alert(1) />
```

![[Pasted image 20241126165646.png]]

El servidor es vulnerable a **XSS**

---

# Subdominio 

* statistics.alert.htb