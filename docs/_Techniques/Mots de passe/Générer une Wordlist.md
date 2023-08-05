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
./username-anarchy John doe > .txt

## Créer une wordlist personnalisée

L'utilitaire `cupp` permet de générer une wordlist à partir d'informations concernant un utilisateur (date de naissance, noms des enfants, etc.).

## Filtrage d'une wordlist en fonction d'une politique de mot de passe

Lorsque l'on connait la politique de mot de passe, on peut "nettoyer" une wordlist existante afin de ne conserver que les mots de passe respectant la politique de mots de passe :

```shell
sed -ri '/^.{,7}$/d' wordlist.txt            # remove shorter than 8
sed -ri '/[!-/:-@\[-`\{-~]+/!d' wordlist.txt # remove no special chars
sed -ri '/[0-9]+/!d' wordlist.txt            # remove no numbers
sed -ri '/[A-Z]+/!d' wordlist.txt            # remove no uppercases
sed -ri '/[a-z]+/!d' wordlist.txt            # remove no lowercases

```