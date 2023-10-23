---
public: true # set to true to make the article publishable
---

```shell-session
# installation
python3 -m pip install --user uploadserver

# creation du certificat
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

# lancement du service
python3 -m uploadserver 443 --server-certificate /root/server.pem
```
