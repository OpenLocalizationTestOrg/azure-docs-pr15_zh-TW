<properties
   pageTitle="建立服務本金 Azure CLI |Microsoft Azure"
   description="說明如何使用 Azure CLI 建立 Active Directory 應用程式和服務原則，並透過角色型存取控制資源的存取權授與。 它會顯示如何驗證與密碼或憑證的應用程式。"
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
   ms.date="09/30/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>使用 Azure CLI 建立服務主體存取資源

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)


如果您有應用程式或需要存取資源的指令碼，您可能不要執行此程序，您自己的認證。 您可能會有不同的權限所要的應用程式，並不想讓應用程式繼續使用您的認證，如果您的職責變更。 不過，您可以建立包含驗證認證與角色指派的應用程式的身分識別。 每次應用程式執行時，它會驗證本身使用這些認證。 本主題說明如何使用[For Mac、 Linux 和 Windows Azure CLI](xplat-cli-install.md)設定執行自己的認證與身分識別] 下的應用程式。

Azure CLI 與您有兩個選項來驗證您的 AD 應用程式︰

 - 密碼
 - 憑證

本主題說明如何使用 Azure CLI 中兩個選項。 如果您想要從程式設計架構 （例如 Python、 並列文字或 Node.js） 登入 Azure，密碼驗證可能是您最佳的選項。 之前決定是否要使用的密碼或憑證，請參閱範例驗證不同架構中的 [[範例應用程式](#sample-applications)] 的區段。

## <a name="active-directory-concepts"></a>Active Directory 概念

在本文中，您可以建立兩個物件的 Active Directory (AD) 應用程式與服務的本金。 AD 應用程式是您的應用程式的全域表示。 它所含的認證 （應用程式識別碼與密碼或憑證）。 主要服務是您的應用程式在 Active Directory 中的本機表示。 它所含的角色指派。 本主題著重於應用程式要執行只有一個組織內的單一租用戶應用程式。 通常會使用單一租用戶應用程式列的商務應用程式會在您的組織內。 在單一租用戶的應用程式，您有一個 AD 應用程式和服務主體。

您可能會好奇-我為什麼需要兩個物件？ 當您考慮多租用戶應用程式時，這種方法可讓您更有意義。 通常您使用多租用戶應用程式軟體為-的-服務 (SaaS) 應用程式，應用程式執行許多不同的訂閱中的位置。 多租用戶的應用程式，您有一個 AD 應用程式與多個服務原則 （每個授與存取權的應用程式的 Active Directory 中的其中一個）。 若要設定一個多租用戶應用程式，請參閱[開發人員指南和 Azure 資源管理員 API 的授權](resource-manager-api-authentication.md)。

## <a name="required-permissions"></a>必要的權限

若要完成此主題，您必須具備足夠的權限在您的 Azure Active Directory 和 Azure 訂閱。 具體來說，您必須能夠在 Active Directory 中建立應用程式，並將服務主要指派給角色。 

在您的 Active Directory 中您的帳戶必須以系統管理員 （例如**全域管理員**或**使用者管理**）。 如果您的帳戶已指派給**使用者**角色，您需要讓系統管理員提高權限。

在您的訂閱，您的帳戶必須有`Microsoft.Authorization/*/Write`授與透過[使用者存取系統管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色的[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色的存取。 如果您的帳戶已指派給**參與者**角色，當您嘗試將服務主要指派給角色時收到錯誤。 同樣地，您訂閱的管理員必須授與您足夠的存取權。

現在，前往 [[密碼](#create-service-principal-with-password)] 或 [[憑證](#create-service-principal-with-certificate)驗證] 區段。

## <a name="create-service-principal-with-password"></a>建立密碼的本金服務

在此區段中，您可以執行的步驟，建立 AD 應用程式的密碼，並閱讀程式角色指派原則服務。

我們幫助這些步驟。

1. 登入您的帳戶。

        azure login

1. 您可以建立 AD 應用程式的兩個選項。 您可以建立 AD 應用程式和服務的本金在一個步驟中，或建立獨立。 如果您不需要指定的首頁] 頁面和應用程式識別碼 Uri 請建立這些一個步驟。 建立獨立如果您要設定這些值的 web 應用程式。 在此步驟中會顯示兩個選項。

     - 若要建立 AD 應用程式和服務的本金單步驟中，提供應用程式和密碼，的名稱，如下列命令中所示︰
     
            azure ad sp create -n exampleapp -p {your-password}     
     
     - 若要分別建立 AD 應用程式，提供應用程式、 首頁 URI、 Uri、 識別碼和密碼的名稱，如下所示下列命令︰
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>

         之前的命令會傳回 AppId 值。 若要建立服務主要，提供的值做為參數，在以下命令︰
     
            azure ad sp create -a <AppId>
     
     如果您的帳戶沒有 Active Directory 的[必要的權限](#required-permissions)，您會看到錯誤訊息，表示 「 Authentication_Unauthorized 」 或者 「 內容中找到不到訂閱 」。
    
     兩個選項，則會傳回新的服務主要。 授與權限時，需要**物件識別碼**。 登入時需要 guid 列**服務主要名稱**。 此 guid 是應用程式識別碼值相同。 在範例應用程式中，此值被指**用戶端識別碼**。 
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. 授與您的訂閱服務本金權限。 在此範例中，您可以新增 [服務主要給**讀者**角色，授與讀取訂閱中的所有資源的權限。 其他的角色，請參閱[RBAC︰ 內建的角色](./active-directory/role-based-access-built-in-roles.md)。 **ServicePrincipalName**引數，請提供**ObjectId**您所建立的應用程式時。 

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     如果您的帳戶沒有足夠的權限的角色指派，您會看到一則錯誤訊息。 訊息，指出您的帳戶**沒有執行動作對範圍 '/ 訂閱 / {guid}'' Microsoft.Authorization/roleAssignments/write 授權**。 

這樣就可以 ！ AD 應用程式與服務原則設定。 [下一步] 區段會顯示如何使用 [透過 Azure CLI 認證登入。 如果您想要使用的程式碼應用程式中的認證，您不需要繼續執行本主題。 您可以跳到[範例應用程式](#sample-applications)的登入您的應用程式識別碼和密碼的範例。 

### <a name="provide-credentials-through-azure-cli"></a>提供透過 Azure CLI 的認證

現在，您需要執行作業的應用程式登入。

1. 每當您為服務主要登入，您需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Active Directory 執行個體。 若要擷取您目前已驗證的訂閱的租用戶識別碼，請使用︰

        azure account show

     傳回的︰

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     如果您需要取得租用戶識別碼的另一個訂閱，請使用下列命令︰

        azure account show -s {subscription-id}

2. 如果您要擷取的用戶端識別碼，使用於登入，請使用︰

        azure ad sp show -c exampleapp --json

     若要使用的登入的值是 guid 列在服務主要名稱。

        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]

3. 以服務主要登入。

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}

    系統會提示您輸入密碼。 提供建立 AD 應用程式時，您所指定的密碼。

        info:    Executing command login
        Password: ********

您現在會為您建立的服務主體的服務主體驗證。

## <a name="create-service-principal-with-certificate"></a>建立憑證的主體服務

在此區段中，您可以執行的步驟︰

- 建立自我簽署的憑證
- 使用憑證，以及服務主要建立 AD 應用程式
- 將讀取者角色指派給服務原則

若要完成這些步驟，您必須安裝[OpenSSL](http://www.openssl.org/) 。

1. 建立自我簽署的憑證。

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. 合併公開及私密金鑰鍵。

        cat privkey.pem cert.pem > examplecert.pem

3. 開啟**examplecert.pem**檔案，並尋找字元之間**---開始憑證---** **---結束憑證---**長順序。 複製憑證資料。 建立主要服務時，您可以傳送做為參數的這個資料。

1. 登入您的帳戶。

        azure login

1. 您可以建立 AD 應用程式的兩個選項。 您可以建立 AD 應用程式和服務的本金在一個步驟中，或建立獨立。 如果您不需要指定的首頁] 頁面和應用程式識別碼 Uri 請建立這些一個步驟。 建立獨立如果您要設定這些值的 web 應用程式。 在此步驟中會顯示兩個選項。

     - 若要建立 AD 應用程式和服務的本金單步驟中，提供的應用程式和憑證資料，名稱，如下列命令中所示︰
     
            azure ad sp create -n exampleapp --cert-value <certificate data>
     
     - 若要分別建立 AD 應用程式，提供應用程式與首頁 URI、 識別碼 Uri、 憑證資料的名稱，如下所示下列命令︰
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>

         之前的命令會傳回 AppId 值。 若要建立服務主要，提供的值做為參數，在以下命令︰
     
            azure ad sp create -a <AppId>
  
     如果您的帳戶沒有 Active Directory 的[必要的權限](#required-permissions)，您會看到錯誤訊息，表示 「 Authentication_Unauthorized 」 或者 「 內容中找到不到訂閱 」。
    
     兩個選項，則會傳回新的服務主要。 授與權限時，需要物件識別碼。 登入時需要 guid 列**服務主要名稱**。 此 guid 是應用程式識別碼值相同。 在範例應用程式中，此值被指**用戶端識別碼**。 
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. 授與您的訂閱服務本金權限。 在此範例中，您可以新增 [服務主要給**讀者**角色，授與讀取訂閱中的所有資源的權限。 其他的角色，請參閱[RBAC︰ 內建的角色](./active-directory/role-based-access-built-in-roles.md)。 **ServicePrincipalName**引數，請提供**ObjectId**您所建立的應用程式時。 

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     如果您的帳戶沒有足夠的權限的角色指派，您會看到一則錯誤訊息。 訊息，指出您的帳戶**沒有執行動作對範圍 '/ 訂閱 / {guid}'' Microsoft.Authorization/roleAssignments/write 授權**。 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>提供透過自動化 Azure CLI 指令碼的憑證

現在，您需要執行作業的應用程式登入。

1. 每當您為服務主要登入，您需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Active Directory 執行個體。 若要擷取您目前已驗證的訂閱的租用戶識別碼，請使用︰

        azure account show

     傳回的︰

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     如果您需要取得租用戶識別碼的另一個訂閱，請使用下列命令︰

        azure account show -s {subscription-id}

1. 若要擷取的憑證指紋並移除不需要的字元，請使用︰

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     這會傳回類似指紋值︰

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. 如果您要擷取的用戶端識別碼，使用於登入，請使用︰

        azure ad sp show -c exampleapp

     若要使用的登入的值是 guid 列在服務主要名稱。

        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]

1. 以服務主要登入。

        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

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
- 若要取得關於使用憑證和 Azure CLI 的詳細資訊，請參閱[Azure 服務原則從 Linux 命令列使用的憑證驗證](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)。 
