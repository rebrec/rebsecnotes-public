---
public: true # set to true to make the article publishable
---
## Liste des partages locaux

```powershell
# Liste des partages actifs sur la machine
PS > get-smbshare                                                                                  

Name     ScopeName Path                                        Description        
----     --------- ----                                        -----------        
ADMIN$   *         C:\Windows                                  Remote Admin       
C$       *         C:\                                         Default share       
IPC$     *                                                     Remote IPC         
NETLOGON *         C:\Windows\SYSVOL\sysvol\flight.htb\SCRIPTS Logon server share  
Shared   *         C:\Shared                                                       
SYSVOL   *         C:\Windows\SYSVOL\sysvol                    Logon server share 
Users    *         C:\Users                                                        

```

## Accès à un partage distant depuis Windows

```powershell
$username = 'someuser'
$password = 'somepass'
$secpassword = ConvertTo-SecureString $password -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
New-PSDrive -Name "S" -Root "\\192.168.10.10\Common" -PSProvider "FileSystem" -Credential $cred
```

## Liste des restrictions FSRM présentes sue un partage

```
# Liste des type de fichiers filtrés sur les partages (FSRM File Screening)
PS > Get-FsrmFileScreen| select -expandproperty IncludeGroup

Audio and Video Files                                      
Backup Files                                               
Compressed Files                                           
E-mail Files     
Executable Files                                 
Image Files                                     
Office Files                        
Share                                                      
System Files                                               
Temporary Files  
Text Files                                      
Web Page Files                                  

# Liste des extensions de fichiers associées aux "IncludeGroups" 
PS > Get-FsrmFileGroup | ? { $_.Name -eq "Share" } | Select -ExpandProperty IncludePattern

*.htm
*.inf
*.lnk
*.pdf
*.scf
*.url


```