---
public: true # set to true to make the article publishable
---

Si on dispose d'une session PSRemote on peut utiliser le cmdlet `Copy-Item` :

```powershell
$Session = New-PSSession -ComputerName $SomeHost

# envoie le fichier locale c:\file.txt vers $SomeHost
Copy-Item -Path C:\file.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\

# télécharge le fichier distant C:\Users\Administrator\Desktop\file.txt depuis $SomeHost
Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```