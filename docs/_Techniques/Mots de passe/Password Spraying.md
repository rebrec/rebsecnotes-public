---
public: true # set to true to make the article publishable
---

```
# crowbar
crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'

# cme

# hydra
hydra -L usernames.txt -p 'password123' 192.168.2.143 rdp

```
