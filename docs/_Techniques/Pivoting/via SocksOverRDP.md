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
# exécuter en privilèges élevés (UAC)
regsvr32 /i SocksOverRDP-Plugin.dll

# Apres avoir lancé mstsc.exe et validé la connexion vers un serveur
# une boite de dialogue confirme que la DLL est bien fonctionnelle
```

### Sur le serveur RDP distant (pivot)

```
# Lancer proxifier en écoute sur le port 1080
# Lancer ensuite le serveur
```