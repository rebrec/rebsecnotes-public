---
public: true 
#Tags: tag1, tag2
---

Ports

- 161 (agents)
- 162 (traps)

## Brute force de la communauté SNMP

```
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt $TARGET

onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp-onesixtyone.txt $TARGET

```

## Parcours d'une partie de l'arborescence

### snmpwalk

```shell-session
snmpwalk -v 2c -c public $TARGET 1.3.6.1.2.1.1.5.0
```

### braa

```
braa public@"$TARGET":.1.3.6.*
```
