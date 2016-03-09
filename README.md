# Code-snippets - Azure
Import-Module Azure

try
{
    &Echo "Logging in to Azure RM Account ......."

    $username = '%AzureUsername%'
    $securePassword = ConvertTo-SecureString `
            -String '%AzurePassword%' -AsPlainText -Force
    $subscriptionId='%AzureSubscriptionID%'
    $cred = New-Object System.Management.Automation.PSCredential ($username, $securePassword)

    Login-AzureRmAccount -Credential $cred -SubscriptionId $subscriptionId -TenantId xxxx-xxxx-xxxx-xxxx
    Add-AzureAccount -Credential $cred -Tenant xxx-xxxxx-xxxxx

    if("%Configuration%" -eq "Live")
    {
        &Echo "Stopping website ......."
        Stop-AzureRmWebApp -v -Name yourWebSite -ResourceGroupName ResourceGroupName-NorthEurope

        &Echo "publishing website ......."
        Publish-AzureWebsiteProject -v  -Name yourWebSite  -Package %teamcity.build.checkoutDir%\yourWebSite_extract.zip -ErrorAction Stop

        &Echo "starting website ......."
        Start-AzureRmWebApp -v -Name yourWebSite  -ResourceGroupName ResourceGroupName-NorthEurope
    }
    else
    {
        &Echo "Stopping website ......."
        Stop-AzureRmWebAppSlot -v -Name yourWebSite -Slot %Configuration% -ResourceGroupName ResourceGroupName-NorthEurope

        &Echo "publishing website ......."
        Publish-AzureWebsiteProject -Name yourWebSite -slot %Configuration% -Package %teamcity.build.checkoutDir%\yourWebSite_extract.zip -verbose -ErrorAction Stop

        &Echo "starting website ......."
        Start-AzureRmWebAppSlot -Name yourWebSite -Slot %Configuration% -ResourceGroupName ResourceGroupName-NorthEurope
    }
}
catch
{
    write-host $_.Exception.Message -foregroundcolor Red
    [Environment]::Exit("1")
}
