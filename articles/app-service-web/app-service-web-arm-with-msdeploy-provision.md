<properties
    pageTitle="部署 MSDeploy 使用 [主機名稱和 ssl 憑證的 web 應用程式"
    description="使用 Azure 資源管理員範本來部署 web 應用程式使用 MSDeploy 及設定自訂主機名稱和 SSL 憑證"
    services="app-service\web"
    manager="erikre"
    documentationCenter=""
    authors="jodehavi"
    />

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="john.dehavilland"/>

# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>部署 MSDeploy、 自訂主機名稱與 SSL 憑證的 web 應用程式

本指南逐步解說 Azure Web 應用程式建立的端對端部署、 運用 MSDeploy，以及將自訂主機名稱和 SSL 憑證新增至 ARM 範本。

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

###<a name="create-sample-application"></a>建立範例應用程式

您部署 ASP.NET web 應用程式。 第一個步驟是，建立簡單的 web 應用程式 （或您無法選擇使用現有-在這種情況下可以略過此步驟中）。

開啟 Visual Studio 2015，然後選擇 [檔案 > 新專案。 在出現的對話方塊中，選擇 [Web > ASP.NET Web 應用程式。 在 [範本選擇 [網頁]，並選擇 MVC。 選取_驗證類型] 變更_為 [_無驗證_。 這只是讓盡範例應用程式。

此時，您必須在基本 ASP.Net web 應用程式可供您部署程序的一部分使用。

###<a name="create-msdeploy-package"></a>建立 MSDeploy 套件

下一步是建立部署 Azure web 應用程式套件。 若要這麼做，將專案，然後執行下列命令︰

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

這會建立 PackageLocation 資料夾中的 [壓縮的套件。 應用程式現在已準備就緒部署，其中您現在可以建置 Azure 資源管理員範本，以執行這項作業。

###<a name="create-arm-template"></a>建立 ARM 範本
首先，讓我們來啟動基本 ARM 範本，會建立 web 應用程式和主機服務方案 （請注意，參數和變數不會顯示贅述）。

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

接下來，您會需要修改才能巢狀的 MSDeploy 資源的 web 應用程式資源。 這可讓您套件先前建立並告知以用於部署套件至 Azure WebApp MSDeploy Azure 資源管理員的參考。 下列範例顯示與巢狀 MSDeploy 資源 Microsoft.Web/sites 資源︰

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

現在，您會注意到 MSDeploy 資源採用**packageUri**屬性的定義如下︰

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

此**packageUri**會儲存帳戶 uri 指向儲存帳戶，您將上傳至您套件 zip。 Azure 資源管理員會運用[共用 Access 簽章](../storage/storage-dotnet-shared-access-signature-part-1.md)下拉式功能表套件本機從儲存的帳戶時部署範本。 此程序就會自動透過 PowerShell 指令碼來上傳的套件，並呼叫 Azure 管理 API 建立索引鍵的必要，傳遞到範本的那些做為參數 （*_artifactsLocation*和*_artifactsLocationSasToken*）。 您必須定義資料夾的參數和檔名套件上載到下存放容器。

您需要新增另一個安裝程式使用自訂網域的主機名稱繫結的巢狀資源中的下一步。 您將必須先確定您擁有主機名稱，並將其設定為驗證 Azure 由您擁有該-請參閱[設定 Azure 應用程式服務中的自訂網域名稱](web-sites-custom-domain-name.md)。 一旦完成後，您可以新增下列 Microsoft.Web/sites 資源之下範本︰

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

最後，您需要新增另一個上方的撫平資源，Microsoft.Web/certificates。 此資源會包含您的 SSL 憑證，且會為您的 web 應用程式和代管計劃的同一層級存在。

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

您必須具備有效的 SSL 憑證，才能設定此資源。 一旦您擁有該有效的憑證您需要為 base64 字串中擷取 pfx 位元組。 使用下列 PowerShell 命令可擷取以下其中一個選項︰

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

您可以再通過這樣做為參數 ARM 部署範本。

此時也可以將 ARM 範本。

###<a name="deploy-template"></a>部署範本

最後一個步驟是拼湊這所有將完整端對端部署。 若要讓您更容易，您可以利用**部署 AzureResourceGroup.ps1** PowerShell 指令碼加入當您建立的 Azure 資源群組專案以協助進行的任何範本中所需的成品上傳的 Visual Studio 中部署。 您需要建立您想要使用提前儲存帳戶。 此範例中，針對我在自己建立上傳 package.zip 共用儲存體帳戶。 指令碼會運用 AzCopy 至套件上傳至儲存帳戶。 您在您的成品資料夾位置中傳送和指令碼會自動將上傳該目錄內的所有檔案命名的存放容器。 呼叫部署 AzureResourceGroup.ps1 後，您必須然後更新 [對應自訂主機名稱與您的 SSL 憑證的 SSL 繫結。

下列 PowerShell 顯示呼叫部署完成部署-AzureResourceGroup.ps1:

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

現在您的應用程式應該已部署，您應該可以透過 https://www.yourcustomdomain.com 瀏覽至
