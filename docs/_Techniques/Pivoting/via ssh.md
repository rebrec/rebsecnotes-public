---
public: true # set to true to make the article publishable
---
## Client SSH

```
# Redirection simple : expose le service mysql de la Cible sur le port 1234 de l'attaquant
ssh -L 1234:localhost:3306 user@$TARGET_IP

# Redirection SOCKS : on pourra utiliser proxychain sur le port 1080 locale de l'attaquant pour transmettre tout notre traffic comme s'il était émis de la cible
ssh -D 1080 user@$TARGET_IP

# Redirection inverse : on expose notre listener local écoutant sur le port 1234 sur le port 8080 de la Cible
ssh -R $PIVOT_IP_IN_INTERNAL_NETWORK:8080:0.0.0.0:1234  user@$TARGET_IP -vN

# -v : verbeux, on verra les tunnels
# -N : on ne lance pas de shell
```

## Client Sshuttle

```
# disponible via les dépots apt
# on se connecte à un serveur SSH via l'outil
# l'outil crée des règles dans iptables pour rediriger le traffic réseau
sudo sshuttle -r ubuntu@10.129.202.64 192.168.1.0/24 -v 
```

```
# test de fonctionnement 
# pas besoin de proxychains !!
nmap -sV-p80 -Pn 192.168.1.123
```

**Remarque** : pour une raison inconnue, nmap fonctionne **seulement** lorsqu'il n'est pas lancé en tant qu'utilisateur *root*. Cela risque d'être de même pour d'autres outils...
