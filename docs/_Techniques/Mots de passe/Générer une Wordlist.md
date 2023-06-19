---
public: true # set to true to make the article publishable
---
## Générer un dictionnaire à partir de permutations d'une dictionnaire existant.
On pourra utiliser les règles de Hashcat pour cela : [[docs/_Techniques/Mots de passe/Casser un mot de passe/Hashcat]]

On pourra également utiliser l'utilitaire https://github.com/digininja/CeWL

```
# -d : profondeur de liens (depth) réalisé par le scan du bot
# -m : longueur minimale des mots extraits des pages
cewl https://www.company.com -d 2 -m 7 --lowercase -w inlane.wordlist
``` 

## Générer une liste d'utilisateurs
On pourra générer, à partir de nom / prénoms collectés une liste de compte utilisateurs potentiels.

L'outil [Username Anarchy](https://github.com/urbanadventurer/username-anarchy) offre notamment cette possibilité