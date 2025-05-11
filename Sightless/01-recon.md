
/// Correos 

	sales@sightless.htb
	john@sightless.htb
	admin@sightless.htb
	
/// Usuarios
	michael@sightless.htb
		

/// Passwords

---

## Nmap Summary
| Port | Software | Version               | Status |     |
| ---- | -------- | --------------------- | ------ | --- |
| 21   | ftp      | ProFTPD Server        | open   |     |
| 22   | ssh      | OpenSSH 8.9p1 Ubuntu  | open   |     |
| 80   | http     | nginx/1.18.0 (Ubuntu) | open   |     |


## Nmap  Recon

Ports tcp open in nmap format

```bash
# Nmap 7.94SVN scan initiated Mon Nov 18 16:42:57 2024 as: nmap -sS -vvv -n -Pn -p- --min-rate 5000 -oN sscan sightl>
PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 63
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63
Read data files from: /usr/bin/../share/nmap
# Nmap done at Mon Nov 18 16:43:12 2024 -- 1 IP address (1 host up) scanned in 15.07 seconds
```

Ports services and versions nmap format

```bash
# Nmap 7.94SVN scan initiated Mon Nov 18 16:57:22 2024 as: nmap -sCV -vvv -n -Pn -p 21,22,80 --min-rate 5000 -oN Rec>
Nmap scan report for sightless.htb (10.10.11.32)
Host is up, received user-set (0.039s latency).
Scanned at 2024-11-18 16:57:22 CET for 68s

PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63
| fingerprint-strings:
|   GenericLines:
|     220 ProFTPD Server (sightless.htb FTP Server) [::ffff:10.10.11.32]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 c9:6e:3b:8f:c6:03:29:05:e5:a0:ca:00:90:c9:5c:52 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGoivagBalUNqQKPAE2WFpkFMj+vKwO9D3RiUUxsnk>
|   256 9b:de:3a:27:77:3b:1b:e1:19:5f:16:11:be:70:e0:56 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIA4BBc5R8qY5gFPDOqODeLBteW5rxF+qR5j36q9mO+bu
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods:
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Sightless.htb
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerpr>
SF-Port21-TCP:V=7.94SVN%I=7%D=11/18%Time=673B63ED%P=x86_64-pc-linux-gnu%r(
SF:GenericLines,A0,"220\x20ProFTPD\x20Server\x20\(sightless\.htb\x20FTP\x2
SF:0Server\)\x20\[::ffff:10\.10\.11\.32\]\r\n500\x20Invalid\x20command:\x2
SF:0try\x20being\x20more\x20creative\r\n500\x20Invalid\x20command:\x20try\
SF:x20being\x20more\x20creative\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Nov 18 16:58:30 2024 -- 1 IP address (1 host up) scanned in 68.48 seconds

```

Ports UDP nmap format

```bash

```

---

## Enumeration

## Port 80 - HTTP (Apache)

![[Pasted image 20241118170222.png]]



### wfuzz

``` bash
wfuzz -w /usr/share/wordlists/wfuzz/general/big.txt --hc 404 http://sightless.htb/FUZZ
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://sightless.htb/FUZZ
Total requests: 3024

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                             
=====================================================================

000001337:   301        7 L      12 W       178 Ch      "images"                                            

Total time: 12.00577
Processed Requests: 3024
Filtered Requests: 3023
Requests/sec.: 251.8787

```
---

###  SQLPad

Descubrimos subdominio  gracias a un redirect en un botón de la pagina web:
// http://sqlpad.sightless.htb/ //

```bash
echo "10.10.11.32 sqlpad.sightless.htb " >> /etc/hosts 
```

![[Pasted image 20241118171259.png]]

SQLPad is a web app for writing and running SQL queries and visualizing the results. Supports Postgres, MySQL, SQL Server, ClickHouse, Crate, Vertica, Trino, Presto, Pinot, Drill, SAP HANA, BigQuery, SQLite, TiDB and many others via ODBC.

En la opcion "New unsaved query " descubrimos dos correos extra guardados en la pagina
![[Pasted image 20241118173418.png]]

john@sightless.htb
admin@sightless.htb

En la parte superior derecha encontramos la opcion "about" 
Observamos la version del SqlPad, es vulnerave al [CVE-2022-0944](obsidian://open?vault=HTB&file=Machines%2FSightless%2FCVES%2FCVE-2022-0944)

![[Pasted image 20241118183714.png]]
