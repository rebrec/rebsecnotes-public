---
public: true
---
## Linux ping

```bash
for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
```

## Powershell

```powershell
# slow

1..254 | % {"172.16.9.$($_): $(Test-Connection -count 1 -comp 172.15.9.$($_) -quiet)"}

# fast : Get-PingSweep -Subnet 1.2.3   
# ATTENTION : on perd parfois des paquets : relancer plusieurs fois pour être sûr
function Get-PingSweep {
    [CmdletBinding()]
    Param(
        [Parameter(Mandatory=$true)]
        [string]$SubNet,
        [switch]$ResolveName
    )
    $ips = 1..254 | ForEach-Object {"$($SubNet).$_"}
    $ps = foreach ($ip in $ips) {
        (New-Object Net.NetworkInformation.Ping).SendPingAsync($ip, 4000)
        #(New-Object Net.NetworkInformation.Ping).SendPingAsync($ip, 250)
        #[Net.NetworkInformation.Ping]::New().SendPingAsync($ip, 250) # or if on PowerShell v5
    }
    [Threading.Tasks.Task]::WaitAll($ps)
    $ps.Result | Where-Object -FilterScript {$_.Status -eq 'Success' -and $_.Address -like "$subnet*"} |
    Select-Object Address,Status,RoundtripTime -Unique |
    ForEach-Object {
        if ($_.Status -eq 'Success') {
            if (!$ResolveName) {
                Write-Output $_
            } else {
                $_ | Select-Object Address, @{Expression={ResolveIp($_.Address)};Label='Name'}, Status, RoundtripTime
            }
        }
    }
}
Get-PingSweep -Subnet 172.16.9  
```

## Nimscan

Pas vraiment utilisé pour du simple pingsweep, mais se scanner de port est portable est assez pratique pour énumérer depuis une machine Windows

```shell
PS C:\temp> .\nimscan.exe  172.16.9.0/24
    )              (
 ( /(              )\ )
 )\()) (      )   (()/(         )
((_)\  )\    (     /(_)) (   ( /(   (
 _((_)((_)   )\  '(_))   )\  )(_))  )\ )
| \| | (_) _((_)) / __| ((_)((_)_  _(_/(  
| .` | | || '  \()\__ \/ _| / _` || ' \))
|_|\_| |_||_|_|_| |___/\__| \__,_||_||_|

    Fast Port Scanner Written In Nim

==> 172.16.9.3:636 Open
==> 172.16.9.3:53 Open
==> 172.16.9.3:3269 Open
==> 172.16.9.3:445 Open
==> 172.16.9.3:139 Open
==> 172.16.9.3:593 Open
==> 172.16.9.3:3268 Open
==> 172.16.9.3:389 Open
==> 172.16.9.3:464 Open
==> 172.16.9.3:88 Open
==> 172.16.9.3:135 Open
==> 172.16.9.3:5985 Open
==> 172.16.9.3:9389 Open
==> 172.16.9.3:49677 Open
==> 172.16.9.3:49676 Open
==> 172.16.9.3:49664 Open
==> 172.16.9.3:47001 Open
==> 172.16.9.3:49698 Open
==> 172.16.9.3:49683 Open
==> 172.16.9.3:49668 Open
==> 172.16.9.3:49671 Open
==> 172.16.9.3:49665 Open
==> 172.16.9.3:49666 Open
==> 172.16.9.3:63953 Open
==> 172.16.9.25:22 Open

[*] NimScan finished in: 439 Seconds
```
