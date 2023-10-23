---
public: true # set to true to make the article publishable
---

```
# configuration d'une redirection de port
C:\Windows\system32> netsh.exe interface portproxy add v4tov4 listenport=1234 listenaddress=10.10.15.2 connectport=3389 connectaddress=192.168.2.100

# vérification
netsh.exe interface portproxy show v4tov4
```
