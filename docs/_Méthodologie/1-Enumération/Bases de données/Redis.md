---
public: true 
#Tags: tag1, tag2
---

Port par défaut : 6379

Commandes utiles
```
redis-cli -h <HOSTNAME>

# afficher toutes les informations (version, etc)
> INFO

# afficher les information sur le serveur uniquement 
> INFO server

# Liste des DB
> CONFIG GET databases

# Info sur les clé des différentes DB
> INFO keyspace
# Keyspace
db0:keys=4,expires=0,avg_ttl=0

# Lister toutes les clés 
> KEYS *
1) "flag"
2) "stor"
3) "numb"
4) "temp"

# Récupérer la valeur d'une clé
> GET flag
"xxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

