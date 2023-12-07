---
public: true # set to true to make the article publishable
---

Cet outil utilise des requêtes ICMP echo et reply pour établir un tunnel entre le client et le serveur.

Il expose un port local du serveur au travers du tunnel.

Si l'on souhaite disposer d'un proxy socks, il faudra d'abord exposer un service ssh par exemple, puis s'y connecter au travers du tunnel avec l'option `-D`

## Installation

```
git clone https://github.com/utoni/ptunnel-ng.git
cd ptunnel-ng
sudo ./autogen.sh 

```

## Utilisation

### Etablissement du tunnel ICMP

On exécute le serveur sur la cible et le client sur la machine de l'attaquant.

On commence par exposer le service SSH (port 22) s'exécutant sur la cible dont l'IP est `$PIVOT_IP`

```
# exécution du serveur sur la cible

sudo ./ptunnel-ng -r -R22 
# ou
sudo ./ptunnel-ng --remote-addr --remote-port22 
```

On se connecte ensuite au serveur depuis la machine de l'attaquant. On crée un listener sur le port `1234` qui redirigera le traffic vers le port `22` (ssh) distant via le tunnel ICMP.

```
# exécution serveur sur la machine de l'attaquant (écoute sur le port 1234 de l'attaquant et redirige le traffic sur le port 22 de la cible exécutant le serveur)

sudo ptunnel-ng -p$PIVOT_IP -l1234 -R22
#ou 
sudo ptunnel-ng --proxy$PIVOT_IP -l1234 --remote-port22
```

### Connexion au service SSH et activation d'un proxy socks

Il ne reste ensuite plus qu'à se connecter au service SSH et créer un tunnel socks :

```
ssh ubuntu@$PIVOT_IP -D 1080
```
