---
public: true 
#Tags: tag1, tag2
---
Ports : 
- 25 (non chiffré)
- 465 (chiffré)

## Enumeration

```
sudo nmap -Pn -sV -sC -p25,465 $TARGET_IP
```

## Exploitation

Peut être utilisé pour énumérer des comptes utilisateurs valides via les paramètres `VRFY`, `EXPN` et `RCPT TO` 

```
telnet $TARGET_IP 25
[...]
VRFY root

252 2.0.0 root

VRFY rebrec

550 5.1.1 <rebrec>: Recipient address rejected: User unknown in local recipient table
```