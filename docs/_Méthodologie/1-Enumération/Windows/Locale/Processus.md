---
public: true
---

Liste des processus avec le nom du service associé lorsqu'il est disponible

```
tasklist /SVC
```


```shell
Get-WmiObject Win32_Process |  
>> Select Name, @{Name="UserName";Expression={$_.GetOwner().Domain+"\"+$_.GetOwner().User}},@{Name="Path";E={$_.Path}} |
>> Sort-Object UserName, Name
```
