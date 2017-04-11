<properties
    pageTitle="快速入門 Azure 堆疊金鑰保存庫 |Microsoft Azure"
    description="開始使用 Azure 堆疊鍵保存庫"
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
    ms.date="10/18/2016"
    ms.author="ricardom"/>


# <a name="getting-started-with-key-vault"></a>開始使用金鑰保存庫

本節說明建立保存庫、 管理索引鍵和機密資料，以及授權的使用者或應用程式叫用作業 Azure 堆疊的保存庫中的步驟。 下列步驟假設租用戶訂閱存在，而且 KeyVault 服務已註冊的訂閱中。 所有的範例命令為基礎的 KeyVault 指令程式可用 Azure PowerShell SDK 的一部分。

## <a name="enabling-the-tenant-subscription-for-vault-operations"></a>啟用保存庫作業的租用戶訂閱 

您可以發行任何保存庫作業之前，必須先確定您的訂閱已啟用為保存庫作業。 您可以確認的發出下列 PowerShell 命令︰

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -AutoSize

上述命令的輸出應該報告 」 已註冊 」 的每個資料列的 [註冊] 狀態。

    ProviderNamespace RegistrationState ResourceTypes Locations
    Microsoft.KeyVault Registered {operations} {local}
    Microsoft.KeyVault Registered {vaults} {local}
    Microsoft.KeyVault Registered {vaults/secrets} {local}
    

 如果不是這樣，您應該叫用下列命令以註冊您的訂閱中的 KeyVault 服務︰

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault

以下為命令的輸出︰
    
    ProviderNamespace : Microsoft.KeyVault
    RegistrationState : Registered
    ResourceTypes : {operations, vaults, vaults/secrets}
    Locations : {local}


>[AZURE.NOTE] 如果您收到錯誤: 「*訂閱未註冊 Azure 金鑰保存庫為*「 叫用 KeyVault cmdlet 時請確認您已啟用 KeyVault 資源提供者，依照上述的指示。


## <a name="creating-a-hardened-container-a-vault-in-azure-stack-to-store-and-manage-cryptographic-keys-and-secrets"></a>儲存和管理加密金鑰和機密 Azure 堆疊中建立強化的容器 （保存庫）

若要建立保存庫，租用戶應該先建立資源群組。 下列的 PowerShell 命令建立該資源] 群組中的資源群組，然後保存庫。 範例也包含該指令程式的一般輸出。

### <a name="creating-a-resource-group"></a>建立資源群組︰

    New-AzureRmResourceGroup -Name vaultrg010 -Location local -Verbose -Force

輸出︰

    VERBOSE: Performing the operation "Replacing resource group ..." on target "".
    VERBOSE: 12:52:51 PM - Created resource group 'vaultrg010' in location 'local'
    ResourceGroupName : vaultrg010
    Location : local
    ProvisioningState : Succeeded
    Tags :
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010
    

### <a name="creating-a-vault"></a>建立保存庫︰


    New-AzureRmKeyVault -VaultName vault010 -ResourceGroupName vaultrg010 -Location local -Verbose

輸出︰

    VaultUri : https://vault010.vault.azurestack.local
    TenantId : 5454420b-2e38-4b9e-8b56-1712d321cf33
    TenantName : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Sku : Standard
    EnabledForDeployment : False
    EnabledForTemplateDeployment : False
    EnabledForDiskEncryption : False
    AccessPolicies : {5454420b-2e38-4b9e-8b56-1712d321cf33}
    AccessPoliciesText :
    Tenant ID : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Object ID : ca342e90-f6aa-435b-a11c-dfe5ef0bfeeb
    Application ID :
    Display Name : Tenant Admin (tenantadmin1@msazurestack.onmicrosoft.com)
    Permissions to Keys : get, create, delete, list, update, import, backup, restore
    Permissions to Secrets : all
    OriginalVault : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010/providers/Microsoft.KeyVault/vaults/vault010
    VaultName : vault010
    ResourceGroupName : vaultrg010
    Location : local
    Tags : {}
    TagsTable :
    
這個 cmdlet 輸出會顯示您剛剛建立的金鑰保存庫的屬性。 兩個最重要的內容是︰

-   **保存庫名稱**︰ 在範例中，這是**vault010**。 您會使用其他鍵保存庫 cmdlet 此名稱。

-   **保存庫 URI**︰ 範例中，這是 https://vault010.vault.azurestack.local。 使用您透過其 REST API 的保存庫應用程式，必須使用這個 URI。

Azure 帳戶立即執行此按鍵保存庫上的任何作業的權限。 尚未，其他人一律是。


## <a name="operating-on-keys-and-secrets"></a>索引鍵和機密資料上作業

建立之後保存庫，請遵循下列步驟來建立管理索引鍵和機密資料︰

### <a name="creating-a-key"></a>建立索引鍵

若要建立索引鍵，使用每個下面的範例中**新增 AzureKeyVaultKey** 。 成功的關鍵建立之後指令程式會輸出新建立的重要詳細資料。

    Add-AzureKeyVaultKey -VaultName \$vaultName -Name\$keyVaultKeyName -Verbose -Destination Software

以下是*新增 AzureKeyVaultKey* cmdlet 輸出︰

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff
    
您現在可以參考的您建立或使用其 URI 上傳至 Azure 金鑰保存庫，此按鍵。 使用**https://vault010.vault.azurestack.local:443/鍵/keyVaultKeyName001**永遠取得最新版本。並使用**https://vault010.vault.azurestack.local:443/鍵/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff**取得此特定的版本。

### <a name="retrieving-a-key"></a>擷取索引鍵

您可以使用**取得 AzureKeyVaultKey**來擷取索引鍵和每個下列範例及其詳細資料︰

    Get-AzureKeyVaultKey -VaultName vault010 -Name keyVaultKeyName001

下列是取得 AzureKeyVaultKey 的輸出

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff

### <a name="setting-a-secret"></a>設定密碼

    $secretvalue = ConvertTo-SecureString 'User@123' -AsPlainText -Force
    Set-AzureKeyVaultSecret -Name MySecret-VaultName vault010 -SecretValue $secretvalue
    
輸出

    Vault Name : vault010
    Name : MySecret
    Version : 65a387f2ed4a416180e852b970846f5b
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret/65a387f2ed4a416180e852b970846f5b
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags : 

### <a name="retrieving-a-secret"></a>擷取密碼

    Get-AzureKeyVaultSecret -VaultName vault010

輸出

    Vault Name : vault010
    Name : MySecret
    Version :
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags :

現在您的主要保存庫和鍵或私人可以準備好要使用的應用程式。
您必須授權應用程式的使用方式。

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>授權使用金鑰或私人應用程式 

若要授權存取鍵或私人保存庫中的應用程式，使用設定-**AzureRmKeyVaultAccessPolicy**指令程式。

例如，如果您保存庫的名稱為*ContosoKeyVault*及您想要授權的應用程式的*用戶端識別碼*是*8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*，且您想要授權解密並與您保存庫鍵登入應用程式，執行下列動作︰

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

如果您要授權閱讀您保存庫中的機密的同一個應用程式，請執行下列動作︰

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get


## <a name="next-steps"></a>後續步驟
[部署 VM 使用金鑰保存庫密碼](azure-stack-kv-deploy-vm-with-secret.md)

[部署 VM 鍵保存庫憑證](azure-stack-kv-push-secret-into-vm.md)