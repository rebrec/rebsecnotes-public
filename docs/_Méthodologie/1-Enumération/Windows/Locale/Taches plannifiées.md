---
public: true # set to true to make the article publishable
---

```
schtasks /query /fo LIST /v
```

```powershell
# Donne quelques détails sur les tâches plannifiées à la racine ('\')
Get-ScheduledTask | ?{$_.TaskPath -eq "\"} | % { 
	Write-Host $_.TaskName
	$_.Actions | fl *
	$_.Triggers | fl *
}
```