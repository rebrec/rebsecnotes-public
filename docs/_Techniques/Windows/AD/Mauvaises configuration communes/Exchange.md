---
public: true # set to true to make the article publishable
---
## Enumeration

Présence des groupes :

- Exchange Windows Permissions : les membres ont la permission Write DACL sur le domaine (DCSync)
- Organization Management :
	- accès aux boîtes mail
	- contrôle totale sur l'OU "Microsoft Exchange Security Groups" qui contient le groupe précédent (Exchange Windows Permissions)

Le dump des comptes en mémoire permet également de récupérer de nombreuses informations d'identifications (via OWA)

## Attaques

### PrivExchange

Vulnérabilité permettant a un utilisateur standard de forcer l'authentification du service (SYSTEM) vers une machine de son choix

<https://dirkjanm.io/abusing-exchange-one-api-call-away-from-domain-admin/>

PoC : <https://github.com/dirkjanm/PrivExchange>

Références :

- <https://github.com/gdedrouas/Exchange-AD-Privesc>
