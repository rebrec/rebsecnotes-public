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

### VRFY
```
telnet $TARGET_IP 25
[...]
VRFY root

252 2.0.0 root

VRFY rebrec

550 5.1.1 <rebrec>: Recipient address rejected: User unknown in local recipient table
```

### EXPN

Peut afficher la liste des membres d'une liste de distribution.

```
telnet  $TARGET_IP 25
[...]
EXPN existinguser

250 2.1.0 existinguser@superdomain.com


EXPN company-members

250 2.0.0 existinguser@superdomain.com
250 2.1.5 existinguser1@superdomain.com
```

### RCPT TO
```
telnet $TARGET_IP 25
[...]
MAIL FROM:test@mydomain.com

250 2.1.0 test@mydomain.com... Sender ok


RCPT TO:baduser1

550 5.1.1 baduser1... User unknown


RCPT TO:baduser2

550 5.1.1 baduser2... User unknown


RCPT TO:existinguser

250 2.1.5 existinguser... Recipient ok
```