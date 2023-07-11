---
public: true 
---

`https://sitereport.netcraft.com`

## Wayback Machine
Les archives de site présentes sur http://web.archive.org/ peuvent permettre de trouver des versions anciennes d'une application Web, pouvant révéler des informations qui ont depuis été nettoyées (commentaires, etc.)


## WaybackUrls

```
# installation 
go install github.com/tomnomnom/waybackurls@latest

# utilisation
waybackurls -dates https://facebook.com > waybackurls.txt

```