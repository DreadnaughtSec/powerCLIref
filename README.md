# powerCLIref
Misc powerCLI notes

## Install PowerCLI
`Install-Module -Name VMware.PowerCLI`

## PShell Execution Policy
`Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Unrestricted -Force`

## Ignore PowerCLI Certificate Error
`Set-PowerCLIConfiguration -InvalidCertificateAction Ignore`

## Connect to VCSA
`Connect-VIServer <VCSA address> -User <user> -Password '<pass>'`
*single quotes will escape characters for password

## Change VM RAM/CPU
`Get-VM -Name <VM name> | Stop-VM -Confirm:$false | Set-VM -MemoryGB <#> -Confirm:$false | Set-VM -NumCpu <#> -Confirm:$false | Start-VM`
  
## Get VM Creation Dates
`(get-vm).ExtensionData.Config.createDate`

## How Long Have VMs Been Powered Off
`$hosts=@(Get-VM | ?{$_.PowerState -eq "PoweredOff"} | Select -Property Name); foreach($i in $hosts){ $last=@(Get-VM -Name $i.Name | Get-VIEvent | Where-Object {$_.GetType().Name -eq "VmPoweredOnEvent"} | findstr -i Created | Select-Object -First 1); $trimlast=@($last -replace "CreatedTime          : ", ""); Write-Host $i.Name - $trimlast}`

## Get VM Location In Datastore
`(Get-VM <VM Name>).ExtensionData.Config.Files`

## Get DataCenter Info (Cluster, VMHost, VM Names)
*won't return DataCenters with 0 VMs
`foreach($dc in Get-Datacenter){
    foreach($cluster in Get-Cluster -Location $dc){
        foreach($esx in Get-VMHost -Location $cluster){
            Get-VM -Location $esx |
            Select @{N='Datacenter';E={$dc.Name}},
                @{N='Cluster';E={$cluster.Name}},
                @{N='VMhost';E={$esx.Name}},Name
        }
    }
}`

## Get A Log File From A Host In Searchable Dialog
`(Get-VMHost | Select -Index 3 | Get-Log -Key * ).Entries | Where-Object -FilterScript {$_ -like "*warning*"} | out-gridview`

## Get All Logs From A VMHost And Show In Descending Order In Searchable Dialog
`(Get-VMHost -Name <VMHost Name> | Get-Log -Key * ).Entries | Sort-Object -Property string -Descending |out-gridview`
