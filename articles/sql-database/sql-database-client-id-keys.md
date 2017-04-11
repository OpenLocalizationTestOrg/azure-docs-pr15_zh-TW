<properties
   pageTitle="取得所需的值的驗證碼存取 SQL 資料庫應用程式 |Microsoft Azure"
   description="建立服務主要從程式碼存取 SQL 資料庫。"
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>驗證應用程式從 [程式碼存取 SQL 資料庫時，取得所需的值

建立及管理 SQL 資料庫的程式碼中，您必須註冊您的應用程式在訂閱中的 Azure Active Directory (AAD) 網域 Azure 資源所建立的位置。

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>建立服務本金存取資源從應用程式

您需要有最新的[PowerShell 的 Azure](https://msdn.microsoft.com/library/mt619274.aspx)安裝及執行。 詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

Active Directory (AD) 應用程式，且我們需要驗證我們 C# 應用程式服務原則，就會建立下列 PowerShell 指令碼。 指令碼輸出我們需要上述 C# 範例值。 如詳細資訊，請參閱[使用 Azure PowerShell 來建立主要存取資源的服務](../resource-group-authenticate-service-principal.md)。

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>另請參閱

- [使用 C# 建立 SQL 資料庫](sql-database-get-started-csharp.md)
- [使用 Azure Active Directory 驗證連線到 SQL 資料庫](sql-database-aad-authentication.md)


