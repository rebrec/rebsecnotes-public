---
public: true # set to true to make the article publishable
---

## Applications installées

```shell
wmic product get name, version, vendor
```

```powershell
gci "c:\Program Files","c:\Program Files (x86)" -Force
```

## Correctifs installés

```

wmic qfe get Caption, Description, HotFixID, InstalledOn
```

On peut ensuite voir si les dernières mises à jours de sécurités sont à jour ou anciennes.
