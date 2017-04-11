<properties
    pageTitle="使用 Azure 堆疊鍵保存庫的憑證部署 VM |Microsoft Azure"
    description="瞭解如何部署 VM 及插入從 Azure 堆疊鍵保存庫的憑證"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="create-vms-and-include-certificates-retrieved-from-key-vault"></a>建立 Vm，並包含從金鑰保存庫擷取的憑證

在 Azure 堆疊 Vm 部署透過 Azure 資源管理員，您現在可以在 Azure 堆疊鍵保存庫來儲存憑證。 然後 Azure 堆疊 （Microsoft.Compute 資源特定的提供者） 將您的 Vm 部署 Vm 時。 憑證可以用在許多情況下，包括 SSL、 加密與基礎的憑證驗證]。

藉由使用這個方法，您可以將憑證安全。 現在無法在 VM 圖像，或應用程式的設定檔或其他不安全的位置。 藉由設定金鑰保存庫適當的存取原則，您也可以控制誰可以存取您的憑證。 另一個優點是，您可以管理 Azure 堆疊鍵保存庫中的一個位置中的所有您憑證。

以下是程序的快速概觀︰

-   您必須以.pfx 格式的憑證。

-   建立索引鍵的保存庫 （使用的範本或下列範例指令碼）。

-   請確定您已開啟 EnabledForDeployment 切換。

-   上傳為私人的憑證。

## <a name="deploying-vms"></a>部署 Vm

此範例指令碼建立索引鍵的保存庫，，然後會儲存在本機的目錄中，若要為私人金鑰保存庫.pfx 檔中儲存的憑證。

    $vaultName = "contosovault"
    $resourceGroup = "contosovaultrg"
    $location = "local"
    $secretName = "servicecert"
    $fileName = "keyvault.pfx"
    $certPassword = "abcd1234"

    # =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    $fileContentBytes = get-content \$fileName -Encoding Byte

    $fileContentEncoded =
    [System.Convert\]::ToBase64String(\$fileContentBytes)
    $jsonObject = @"
    {
    "data": "\$filecontentencoded",
    "dataType" :"pfx",
    "password": "\$certPassword"
    }
    @$jsonObjectBytes = [System.Text.Encoding\]::UTF8.GetBytes(\$jsonObject)
    $jsonEncoded = \[System.Convert\]::ToBase64String(\$jsonObjectBytes)
    Switch-AzureMode -Name AzureResourceManager
    New-AzureResourceGroup -Name \$resourceGroup -Location \$location
    New-AzureKeyVault -VaultName \$vaultName -ResourceGroupName
    $resourceGroup -Location \$location -sku standard -EnabledForDeployment
    $secret = ConvertTo-SecureString -String \$jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName \$vaultName -Name \$secretName -SecretValue \$secret

.Pfx 檔，然後儲存為 JSON 檔案內容 base64 編碼的物件，則會讀取指令碼的第一個部分。 然後 JSON 物件也是 base64 編碼。

接下來，它會建立新的資源群組，並且然後建立索引鍵保存庫。 請注意，[新增 AzureKeyVault] 命令，最後一個參數 '-EnabledForDeployment 」，其授與存取權 Azure （以便於 Microsoft.Compute 資源提供者） 讀取從索引鍵的機密資料地窖部署。

最後一個命令只會儲存在金鑰保存庫中 base64 編碼 JSON 物件為私人。

以下是從以上的指令碼輸出的範例︰

    VERBOSE:  – Created resource group 'contosovaultrg' in
    location
    'eastus'
    ResourceGroupName : contosovaultrg
    Location          : eastus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions NotActions
                        ======= ==========
                        \*
    ResourceId        :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56/re
    sourceGroups/contosovaultrg
    VaultUri             : https://contosovault.vault.azure.net
    TenantId             : xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
    TenantName           : xxxxxxxx
    Sku                  : standard
    EnabledForDeployment : True
    AccessPolicies       : {xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47}
    AccessPoliciesText   :
                           Tenant ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
                           Object ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-b092cebf0c80
                           Application ID         :
                           Display Name           : Derick Developer  (derick@contoso.com)
                           Permissions to Keys    : get, create, delete,
                           list, update, import, backup, restore
                           Permissions to Secrets : all
    OriginalVault        : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId           :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56                 
    /resourceGroups/contosovaultrg/providers/Microsoft.KeyV
                           ault/vaults/contosovault
    VaultName            : contosovault
    ResourceGroupName    : contosovaultrg
    Location             : eastus
    Tags                 : {}
    TagsTable            :
    SecretValue     : System.Security.SecureString
    SecretValueText :
    ew0KImRhdGEiOiAiTUlJSkN3SUJBekNDQ01jR0NTcUdTSWIzRFFFSEFh
    Q0NDTGdFZ2dpME1JSUlzRENDQmdnR0NTcUdTSWIzRFFFSEFhQ0NCZmtF           
    Z2dYMU1JSUY4VENDQmUwR0N5cUdTSWIzRFFFTUNnRUNvSUlFL2pDQ0JQ
    &lt;&lt;&lt; Output truncated… &gt;&gt;&gt;
    Attributes      :
    Microsoft.Azure.Commands.KeyVault.Models.SecretAttributes
    VaultName       : contosovault
    Name            : servicecert
    Version         : e3391a126b65414f93f6f9806743a1f7
    Id              :
    https://contosovault.vault.azure.net:443/secrets/servicecert
                      /e3391a126b65414f93f6f9806743a1f7

現在，我們已準備好部署 VM 範本。 請注意 URI （為上述的列印輸出以綠色醒目提示），輸出中的密碼。

您必須位於以下的範本。 （除了主要 VM 參數） 的特殊感興趣的參數是保存庫名稱、 保存庫資源] 群組中，與私人 URI。 當然，您也可以從 GitHub 下載，並視需要修改。

此 VM 部署時，Azure 會將憑證插入 VM。
在 Windows 上，不能匯出私密金鑰已新增.pfx 檔中的憑證。 憑證新增至 LocalMachine 憑證的位置，與使用者提供的憑證存放區。 憑證檔案放檔名 /var/lib/waagent 目錄下 linux， &lt;UppercaseThumbprint&gt;.crt 的 X509 憑證檔案和&lt;UppercaseThumbprint&gt;.prv 私密金鑰。
這些檔案都.pem 格式設定。

應用程式通常會使用指紋尋找憑證，而且不需要修改。

## <a name="retiring-certificates"></a>淘汰憑證


前一節中，我們示範如何推入您現有的 Vm 的 [新的憑證。 但您舊的憑證仍在 VM 中且無法移除。 增加安全性，您可以變更舊密碼的屬性 「 停用 」，以便即使舊的範本嘗試建立 VM 使用此舊版本的憑證，就會。 以下是您設定的特定的私人版本，才能停用︰

    Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0

## <a name="conclusion"></a>結束時


使用此新的方法，憑證可以保留分開 VM 圖像或應用程式內容。 讓我們已移除的曝光度的一個點。

憑證也會更新，而不必重新建立 VM 圖像或應用程式部署套件上傳至金鑰保存庫。 應用程式仍需要透過提供新 uri 為這個新的憑證版本。

從 VM 或應用程式內容分隔憑證，我們已現在縮減的人員都可以直接存取憑證的數目。

為一個優點，現在您可以在 [管理您的憑證，包括所有版本部署一段時間的金鑰保存庫同一個方便的位置。

## <a name="next-steps"></a>後續步驟

[部署 VM 使用金鑰保存庫密碼](azure-stack-kv-deploy-vm-with-secret.md)

[允許存取金鑰保存庫應用程式](azure-stack-kv-sample-app.md)