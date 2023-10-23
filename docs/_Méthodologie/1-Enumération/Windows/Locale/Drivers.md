---
public: true 
#Tags: tag1, tag2
---

Rechercher des exploits sur des drivers listés ici (USBPcap par exemple)

```
driverquery /v
```

```powershell
driverquery.exe /v /fo csv | ConvertFrom-CSV | Select "Display Name", "Start Mode", "Path"
```

```powershell
Get-WmiObject Win32_PnPSignedDriver | Select DeviceName, DriverVersion, Manufacturer | ? {$_.DeviceName -like "*vmwa*"}
```
