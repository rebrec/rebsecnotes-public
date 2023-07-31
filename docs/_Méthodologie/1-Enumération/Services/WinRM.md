---
public: true 
#Tags: tag1, tag2
---
Ports :
- 5985/tcp
- 5986/tcp

## Enumeration
```shell-session
$ nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
PORT     STATE SERVICE VERSION
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```


## Tester si une machine a WinRM d'actif 

### Sous windows
Utiliser le cmdlet `Test-WsMan` 

### Sous Linux

```shell-session
evil-winrm -i $TARGET_IP -u $AD_USER -p $AD_PASSWORD
```

