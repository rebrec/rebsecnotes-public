---
public: true # set to true to make the article publishable
---
## Linux ping

```bash
for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
```

## Powershell

```powershell
# slow
1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}

# fast : GetPingSweep -Subnet 1.2.3   
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
        (New-Object Net.NetworkInformation.Ping).SendPingAsync($ip, 1000)
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

```
