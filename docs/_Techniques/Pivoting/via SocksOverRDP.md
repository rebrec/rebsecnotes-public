---
public: true # set to true to make the article publishable
---

## Sources 
- https://github.com/nccgroup/SocksOverRDP/releases
- https://www.proxifier.com/download/ProxifierPE.zip

## Installation

### Sur le client RDP

```
# enregistrer la DLL de Socks Over RDP
# exécuter en privilèges élevés (UAC)  !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!   <=== ATTENTION
regsvr32 /i SocksOverRDP-Plugin.dll

# Apres avoir lancé mstsc.exe et validé la connexion vers un serveur
# une boite de dialogue confirme que la DLL est bien fonctionnelle

# Une fois que la partie serveur est réalisée (voir paragraphe plus bas), le serveur confirmera qu'il est bien connecté.
# A ce moment là, on peut voir localement sur le client que le port 1080 est bien en écoute
# On lance ensuite proxifier et on ajoute un serveur proxy de type socks 5 vers 127.0.0.1:80 
# ATTENTION : il faut lancer profixy en tant qu'administrateur (ATTENTION si on ferme l'application elle se minimise!)

# on peut ensuite lancer un nouveau MSTSC.exe et le traffic est acheminé au travers du proxy socks.
```

### Sur le serveur RDP distant (pivot)

```
# Lancer ensuite le serveur  (SocksOverRDP-Server.exe)
```