---
public: true # set to true to make the article publishable
---

## Applications installées

```shell
wmic product get name, version, vendor
```

```powershell
Get-WmiObject -Class Win32_Product |  select Name, Version

gci "c:\Program Files","c:\Program Files (x86)" -Force

$INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |  Select-Object DisplayName, DisplayVersion, InstallLocation
$INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, InstallLocation
$INSTALLED | ?{ $_.DisplayName -ne $null } | sort-object -Property DisplayName -Unique | Format-Table -AutoSize
```

On peut ensuite voir si les versions sont récentes ou non, (indice de maturité de l'entreprise en terme de sécurité) et cherchercher si les version sont anciennes si des CVE existent.
