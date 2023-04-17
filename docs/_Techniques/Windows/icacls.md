---
public: true # set to true to make the article publishable
---

```shell
# Ajout d'une ACE autorisant tout utilisateur à un contrôle total d'un dossier :
icacls c:\rebrec /grant "Everyone:(OI)(CI)F"
```


icacls /grant rebrec:F /T



