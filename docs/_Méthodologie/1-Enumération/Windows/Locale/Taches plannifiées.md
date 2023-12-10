---
public: true # set to true to make the article publishable
---

```
schtasks /query /fo LIST /v
```

```powershell
# Donne quelques détails sur les tâches plannifiées à la racine ('\')
Get-ScheduledTask | ?{$_.TaskPath -eq "\"} | % { 
	Write-Host "###################### $_.TaskName  ######################"
	Write-Host "#"
	Write-Host "###### ACTIONS"
	Write-Host "#"
	$_.Actions | ConvertTo-Json -Depth 10
	Write-Host "#"
	Write-Host "###### TRIGGERS"
	Write-Host "#"
	$_.Triggers | ConvertTo-Json -Depth 10
	Write-Host "#"
	Write-Host "#"
	Write-Host "#"
	Write-Host "#"
	Write-Host "#"
	Write-Host "#"
	Write-Host "#"
	Write-Host "#"
}
```
