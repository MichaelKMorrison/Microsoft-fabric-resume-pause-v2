# Microsoft-fabric-resume-pause-v2
microsoft-fabric-resume-pause Public Forked from jagdish0909/microsoft-fabric-resume-pause PowerShell script to automate Microsoft Fabric capacity start (resume) and stop (suspend) using Azure Automation Account. Helps optimize costs by pausing unused capacity based on a schedule.


Param(
    [Parameter(Mandatory=$true)] [string]$operation,  
             # Options are "suspend" or "resume"    
    [string]$SubID = "98fcf920-1280-4e34-8920-92179ea0d7cf",  #Your Azure subscription
    [string]$ResGrp = "FabricPaid",   #Resource Group Name
    [string]$CapacityName = "fabric4eastusf2capacity"    
)
#Example: $PartialURL = "/subscriptions/12345678-1234-1234-1234-123a12b12d1c/resourceGroups/fabric-rg/providers/Microsoft.Fabric/capacities/myf2capacity"
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

$response
