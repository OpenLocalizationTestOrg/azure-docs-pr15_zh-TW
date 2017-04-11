<properties
    pageTitle="變更主要保存庫租用戶識別碼訂閱移動後 |Microsoft Azure"
    description="瞭解如何切換重要保存庫的租用戶識別碼，訂閱會移至不同的租用戶之後"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>變更主要保存庫租用戶識別碼訂閱移動後
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>問︰ 我的訂閱移動的租用戶的租用戶 b。如何變更我現有的按鍵保存庫的租用戶識別碼，針對租用戶 B 中的原則設定正確的 Acl？

當您在訂閱中建立新的金鑰保存庫時，會自動與該訂閱的預設 Azure Active Directory 租用戶識別碼。 所有的 access 原則項目會連結到此租用戶識別碼。 當您移動 Azure 訂閱的租用戶的租用戶 B 時，您現有的按鍵保存庫都無法存取租用戶 b 中的原則 （使用者和應用程式）若要修正此問題，您需要︰

- 變更的租用戶識別碼相關聯的所有現有金鑰保存庫此訂閱中租用戶 b。
- 移除所有現有的存取原則項目。
- 新增新存取原則的項目相關聯的租用戶 b。

例如，如果您已從移訂閱中有金鑰保存庫 'myvault' 租用戶的租用戶 B，以下的如何變更此按鍵保存庫的租用戶識別碼，並移除舊的存取原則。

<pre>
$vaultResourceId = (取得 AzureRmKeyVault VaultName myvault)。預設 $vault = 取得 AzureRmResource – 預設 $vaultResourceId-ExpandProperties $vault。Properties.TenantId = (取得 AzureRmContext)。Tenant.TenantId $vault。Properties.AccessPolicies =@()設定 AzureRmResource 預設 $vaultResourceId-$vault 屬性。屬性
</pre>

由於此保存庫已在租用戶的前移動]，[ **$vault 的原始值。Properties.TenantId**就是租用戶 A、 時**(取得 AzureRmContext)。Tenant.TenantId**是租用戶 b。

現在您保存庫相關聯的正確的租用戶識別碼，並將會移除舊的項目存取原則，設定新存取原則[設定 AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx)項目。

## <a name="next-steps"></a>後續步驟

如果您有 Azure 金鑰保存庫的相關問題，請造訪[Azure 金鑰保存庫論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
