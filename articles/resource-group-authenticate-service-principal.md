<properties
   pageTitle="使用 PowerShell 建立 Azure 服務本金 |Microsoft Azure"
   description="說明如何使用 PowerShell 的 Azure 建立 Active Directory 應用程式和服務原則，並透過角色型存取控制資源的存取權授與。 它會顯示如何驗證與密碼或憑證的應用程式。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>若要建立 access 資源服務主要使用 PowerShell 的 Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)

如果您有應用程式或需要存取資源的指令碼，您可能不要執行此程序，您自己的認證。 您可能會有不同的權限所要的應用程式，並不想讓應用程式繼續使用您的認證，如果您的職責變更。 不過，您可以建立包含驗證認證與角色指派的應用程式的身分識別。 每次應用程式執行時，它會驗證本身使用這些認證。 本主題說明如何使用[PowerShell 的 Azure](powershell-install-configure.md)設定執行自己的認證與身分識別] 下的應用程式，您需要的所有項目。

使用 PowerShell，您有兩個選項來驗證您的 AD 應用程式︰

 - 密碼
 - 憑證

本主題說明如何使用 PowerShell 中兩個選項。 如果您想要從程式設計架構 （例如 Python、 並列文字或 Node.js） 登入 Azure，密碼驗證可能是您最佳的選項。 之前決定是否要使用的密碼或憑證，請參閱範例驗證不同架構中的 [[範例應用程式](#sample-applications)] 的區段。

## <a name="active-directory-concepts"></a>Active Directory 概念

在本文中，您可以建立兩個物件的 Active Directory (AD) 應用程式與服務的本金。 AD 應用程式是您的應用程式的全域表示。 它所含的認證 （應用程式識別碼與密碼或憑證）。 主要服務是您的應用程式在 Active Directory 中的本機表示。 它所含的角色指派。 本主題著重於應用程式要執行只有一個組織內的單一租用戶應用程式。 通常會使用單一租用戶應用程式列的商務應用程式會在您的組織內。 在單一租用戶的應用程式，您有一個 AD 應用程式和服務主體。

您可能會好奇-我為什麼需要兩個物件？ 當您考慮多租用戶應用程式時，這種方法可讓您更有意義。 通常您使用多租用戶應用程式軟體為-的-服務 (SaaS) 應用程式，應用程式執行許多不同的訂閱中的位置。 多租用戶的應用程式，您有一個 AD 應用程式與多個服務原則 （每個授與存取權的應用程式的 Active Directory 中的其中一個）。 若要設定一個多租用戶應用程式，請參閱[開發人員指南和 Azure 資源管理員 API 的授權](resource-manager-api-authentication.md)。

## <a name="required-permissions"></a>必要的權限

若要完成此主題，您必須具備足夠的權限在您的 Azure Active Directory 和 Azure 訂閱。 具體來說，您必須能夠在 Active Directory 中建立應用程式，並將服務主要指派給角色。 

在您的 Active Directory 中您的帳戶必須以系統管理員 （例如**全域管理員**或**使用者管理**）。 如果您的帳戶已指派給**使用者**角色，您需要讓系統管理員提高權限。

在您的訂閱，您的帳戶必須有`Microsoft.Authorization/*/Write`授與透過[使用者存取系統管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色的[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色的存取。 如果您的帳戶已指派給**參與者**角色，當您嘗試將服務主要指派給角色時收到錯誤。 同樣地，您訂閱的管理員必須授與您足夠的存取權。

現在，前往 [[密碼](#create-service-principal-with-password)] 或 [[憑證](#create-service-principal-with-certificate)驗證] 區段。

## <a name="create-service-principal-with-password"></a>建立密碼的本金服務

在此區段中，您可以執行的步驟︰

- 使用密碼建立 AD 應用程式
- 建立服務原則
- 將讀取者角色指派給服務原則

若要快速地執行下列步驟，請參閱下列三個 cmdlet。 

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

我們幫助步驟更仔細地，請確定您瞭解程序。

1. 登入您的帳戶。

        Add-AzureRmAccount

1. 建立新的 Active Directory 應用程式提供的顯示名稱，將您的應用程式的說明 URI，找出您的應用程式和您的應用程式的身分識別的密碼 Uri。

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     單一租用戶的應用程式，不會驗證 Uri。
     
     如果您的帳戶沒有 Active Directory 的[必要的權限](#required-permissions)，您會看到錯誤訊息，表示 「 Authentication_Unauthorized 」 或者 「 內容中找到不到訂閱 」。

1. 檢查新的應用程式物件。 

        $app
        
     請注意特別**ApplicationId**屬性，所需的建立服務原則，角色指派]，並取得存取權杖。

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. 建立您的應用程式服務主要透過傳入的 Active Directory 應用程式的應用程式識別碼。

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. 授與您的訂閱服務本金權限。 在此範例中，您可以新增 [服務主要給**讀者**角色，授與讀取訂閱中的所有資源的權限。 其他的角色，請參閱[RBAC︰ 內建的角色](./active-directory/role-based-access-built-in-roles.md)。 **ServicePrincipalName**參數，提供建立應用程式時，您會使用**ApplicationId** 。 

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    如果您的帳戶沒有足夠的權限的角色指派，您會看到一則錯誤訊息。 訊息，指出您的帳戶**沒有執行動作對範圍 '/ 訂閱 / {guid}'' Microsoft.Authorization/roleAssignments/write 授權**。 

這樣就可以 ！ AD 應用程式與服務原則設定。 [下一步] 區段會顯示如何使用透過 PowerShell 認證登入。 如果您想要使用的程式碼應用程式中的認證，您可以跳到[範例應用程式](#sample-applications)。 

### <a name="provide-credentials-through-powershell"></a>提供透過 PowerShell 認證

現在，您需要執行作業的應用程式登入。

1. 建立**PSCredential**物件執行**取得認證**命令以包含您的認證。 您需要**ApplicationId**後再繼續執行此命令，因此請確定您有提供的貼上。

        $creds = Get-Credential

2. 系統會提示您要輸入您的認證。 使用者名稱] 中，使用**ApplicationId**您所建立的應用程式時。 輸入密碼，使用 [建立帳戶時，您所指定的項目。

     ![輸入認證](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. 每當您為服務主要登入，您需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Active Directory 執行個體。 如果您只有一個訂閱，您可以使用︰

        $tenant = (Get-AzureRmSubscription).TenantId
    
     如果您有多個訂閱，指定您 Active Directory 的所在位置的訂閱。 如需詳細資訊，請參閱[如何 Azure 訂閱 Azure Active Directory 與相關聯](./active-directory/active-directory-how-subscriptions-associated-directory.md)。

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. 以登入服務主要指定此帳戶的服務主要及提供認證物件。 

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     您現在已驗證 Active Directory 應用程式所建立的本金服務。

### <a name="save-access-token-to-simplify-log-in"></a>儲存存取權杖，以簡化登入

若要避免提供服務的本金認證，每次需要登入，您可以儲存存取權杖。

1. 若要在稍後的工作階段中使用目前的存取權杖，儲存設定檔。

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     開啟 [設定檔並檢視其內容。 請注意，它包含存取權杖。 
        
2. 而不是以手動方式一次登入，只要載入的設定檔。

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] 到期存取權杖，所以，只要權杖是有效使用儲存的設定檔只適用於。
        
## <a name="create-service-principal-with-certificate"></a>建立憑證的主體服務

在此區段中，您可以執行的步驟︰

- 建立自我簽署的憑證
- 使用憑證建立 AD 應用程式
- 建立服務原則
- 將讀取者角色指派給服務原則

若要快速在 Windows 10 或 Windows Server 2016 Technical Preview 中執行這些步驟來 Azure PowerShell 2.0，請參閱下列 cmdlet。 

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

我們幫助步驟更仔細地，請確定您瞭解程序。 本文也說明如何使用舊版的 PowerShell 的 Azure 或作業系統時，完成的工作。

### <a name="create-the-self-signed-certificate"></a>建立自我簽署的憑證

PowerShell 適用於 Windows 10 和 Windows Server 2016 Technical Preview 版本具有產生自我簽署的憑證更新的**新增 SelfSignedCertificate**指令程式。 舊版的作業系統有新增 SelfSignedCertificate 指令程式，但不提供此主題的所需的參數。 不過，您需要匯入模組來產生的憑證。 本主題說明產生根據作業系統的憑證，您有兩種方法。 

- 如果您有**Windows 10 或 Windows Server 2016 Technical Preview**中，執行下列命令以建立自我簽署的憑證︰ 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- 如果您**沒有 Windows 10 或 Windows Server 2016 Technical Preview**中，您必須下載[自我簽署憑證產生器](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/)從 Microsoft 指令碼 」。 展開其內容並匯入您需要指令程式。
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     然後，產生的憑證。
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

您擁有您的憑證，並且可以繼續建立 AD 應用程式。

### <a name="create-the-active-directory-app-and-service-principal"></a>建立 Active Directory 應用程式和服務的本金

1. 從憑證擷取的值。

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Azure 帳戶登入。

        Add-AzureRmAccount

3. 建立新的 Active Directory 應用程式提供的顯示名稱，將您的應用程式的說明 URI，找出您的應用程式和您的應用程式的身分識別的密碼 Uri。

     如果您有 Azure PowerShell 2.0 （八月 2016年或更新版本），使用下列 cmdlet:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    如果您有 Azure PowerShell 1.0，請使用下列 cmdlet:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    單一租用戶的應用程式，不會驗證 Uri。
    
    如果您的帳戶沒有 Active Directory 的[必要的權限](#required-permissions)，您會看到錯誤訊息，表示 「 Authentication_Unauthorized 」 或者 「 內容中找到不到訂閱 」。
        
    檢查新的應用程式物件。 

        $app

    請注意**ApplicationId**屬性，所需的建立服務原則，角色指派]，並取得存取權杖。

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. 建立您的應用程式服務主要透過傳入的 Active Directory 應用程式的應用程式識別碼。

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. 授與您的訂閱服務本金權限。 在此範例中，您可以新增 [服務主要給**讀者**角色，授與讀取訂閱中的所有資源的權限。 其他的角色，請參閱[RBAC︰ 內建的角色](./active-directory/role-based-access-built-in-roles.md)。 **ServicePrincipalName**參數，提供建立應用程式時，您會使用**ApplicationId** 。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    如果您的帳戶沒有足夠的權限的角色指派，您會看到一則錯誤訊息。 訊息，指出您的帳戶**沒有執行動作對範圍 '/ 訂閱 / {guid}'' Microsoft.Authorization/roleAssignments/write 授權**。

這樣就可以 ！ AD 應用程式與服務原則設定。 [下一步] 區段會顯示如何登入透過 PowerShell 的憑證。

### <a name="provide-certificate-through-automated-powershell-script"></a>提供透過自動化的 PowerShell 指令碼的憑證

每當您為服務主要登入，您需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Active Directory 執行個體。 如果您只有一個訂閱，您可以使用︰

    $tenant = (Get-AzureRmSubscription).TenantId
    
如果您有多個訂閱，指定您 Active Directory 的所在位置的訂閱。 如需詳細資訊，請參閱[管理您的 Azure AD 目錄](./active-directory/active-directory-administer.md)。

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

若要在您的指令碼進行驗證，指定的帳戶是主要的服務，提供的憑證指紋、 應用程式識別碼與租用戶識別碼。 若要自動化指令碼，您可以將這些值儲存為環境變數，並可在執行時，擷取或您可以將它們加入您的指令碼。

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

您現在已驗證 Active Directory 應用程式所建立的本金服務。

## <a name="sample-applications"></a>範例應用程式

下列範例應用程式顯示如何服務主要身分登入。

**.NET**

- [部署 SSH 啟用 VM.net 範本](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [管理 Azure 資源和.NET 資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [快速入門-部署使用 Azure 資源管理員範本-資源 java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [快速入門-管理資源群組-資源 java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [部署 SSH 啟用 VM Python 中的範本](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [管理 Azure 資源與資源群組 Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [部署 SSH 啟用 VM Node.js 中的範本](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [管理 Azure 資源與資源群組 Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**[注音標示**

- [部署 SSH 啟用 VM 與 [注音標示的範本](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [管理 Azure 資源與資源群組並列文字](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>後續步驟
  
- 整合 Azure 中的應用程式，來管理資源的詳細步驟，請參閱[開發人員指南和 Azure 資源管理員 API 的授權](resource-manager-api-authentication.md)。
- 應用程式與服務原則的詳細說明，請參閱[應用程式與服務主要物件](./active-directory/active-directory-application-objects.md)。 
- 如需有關 Active Directory 驗證的詳細資訊，請參閱[Azure ad 驗證案例](./active-directory/active-directory-authentication-scenarios.md)。



