---
public: true # set to true to make the article publishable
---
## Fichier de règles de permutations (Word Mangling)

Les symboles suivants peuvent être utilisés pour réaliser des modifications à partir d'un mot initialement présent dans un dictionnaire (wordlist).

### Fonctions de transformation
- `:` : ne fait rien (gènère le mot tel qu'il est présent dans le dictionnaire
- `l` : met en minuscule toutes les lettres (lowercase)
- `u` : met en majuscule toutes les lettres (uppercase)
- `c` : met en majuscule le premier caractère et en minuscule le reste (capitalize)
- `sXY` : remplace X par Y (exemple : sa@)
- `$!` : ajoute un point d'exclamation à la fin du mot

### Exemple de règles

```
:               <=== LeMotDorigine
c               <=== Lemotdorigine
so0             <=== LeM0tD0rigine
c so0           <=== Lem0td0rigine
$!              <=== LeMotDorigine!
$! c            <=== Lemotdorigine!
$! so0          <=== Lem0td0rigine!
```

### Utilisation des règles pour générer une nouvelle wordlist

```
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

## Cassages de hash communs

```
# Hash NT
hashcat -m 1000 -a 0 hashes rockyou.txt

# Hash SHA-512 ($6$ commun dans /etc/passwd)
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked

# Hash NetNTLMv2
hashcat -m 5600 hash.txt rockyou.txt

# Hash de TGS    $krb5tgs$23$*user1$DOMAIN...
hashcat -m 13100 hash.txt rockyou.txt
```

La liste des hash est disponible ici : <https://hashcat.net/wiki/doku.php?id=example_hashes>
