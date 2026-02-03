# Microsoft-fabric-resume-pause-v2
microsoft-fabric-resume-pause Public Forked from https://github.com/jagdish0909/microsoft-fabric-resume-pause PowerShell script to automate Microsoft Fabric capacity start (resume) and stop (suspend) using Azure Automation Account. Helps optimize costs by pausing unused capacity based on a schedule.

```powershell
Param(
    [Parameter(Mandatory=$true)] [string]$operation,  
             # Options are "suspend" or "resume"    
    [string]$SubID = "1111111-0000-1111-0000-333333ee0d0d",  #Your Azure subscription
    [string]$ResGrp = "Fabric-RG",   #Resource Group Name
    [string]$CapacityName = "CapacityName"    
)
#Example: $PartialURL = "/subscriptions/1111111-0000-1111-0000-333333ee0d0d/resourceGroups/Fabric-rg/providers/Microsoft.Fabric/capacities/CapacityName"
$PartialURL = "/subscriptions/"+ $SubID +"/resourceGroups/"+ $ResGrp +"/providers/Microsoft.Fabric/capacities/"+ $CapacityName
Write-Output "PartialURL= " $PartialURL  #debug statment for testing

Connect-AzAccount -Identity
$tokenObject = Get-AzAccessToken -ResourceUrl "https://management.azure.com/"
$token = $tokenObject.Token

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$tokenObject = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$token = $tokenObject.AccessToken

$url = "https://management.azure.com/$PartialURL/$operation" + "?api-version=2022-07-01-preview"
Write-Output "URL= " $url    #debug statment for testing

$headers = @{
    'Content-Type' = 'application/json'
    'Authorization' = "Bearer $token"
}

$response = Invoke-RestMethod -Uri $url -Method Post -Headers $headers

$response```

