---
public: true # set to true to make the article publishable
---

Exemple pour un fichier VHD qui est chiffré par bitlocker :

```
bitlocker2john -i Backup.vhd > backup.hashes
grep "bitlocker\$0" backup.hashes > backup.hash
john backup.hash -w=resources/mutated.list

```

On peut ensuite monter directement le VHD en double cliquant sur le VHD dans l'explorateur Windows.
