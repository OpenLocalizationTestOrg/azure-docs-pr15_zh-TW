<properties
   pageTitle="重要保存庫.NET 2.x API 版本資訊 |Microsoft Azure"
   description=".NET 開發人員使用這個 API 的程式碼的 Azure 金鑰保存庫"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="CSharp"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/07/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure 保存庫.NET 2.0-發行備忘稿和移轉指南

追蹤的備忘稿和指引，也適用於開發人員使用 Azure 金鑰保存庫.NET / C# 文件庫。 在從 1.0 版到 2.0 版本的變更，更新的數字所做的就是需要在程式碼中，您才能受益的功能改良的功能與功能加入項目，例如金鑰保存庫憑證支援中的移轉工作。

索引鍵保存庫憑證支援提供管理您的 x509 憑證和下列問題︰  

-   可讓認證擁有者，若要建立的憑證，透過金鑰保存庫建立程序或匯入現有的憑證。 這包含兩自我簽署和憑證授權單位產生的憑證。

- 可讓金鑰保存庫認證擁有者實作安全儲存和管理的 X509 憑證不需與私密金鑰資料互動。  

-   允許建立原則的指示來管理憑證生命週期的金鑰保存庫認證擁有者。  

-   可讓認證的擁有者，若要提供連絡人的資訊告知生命週期事件的到期日及更新憑證。  

-   支援的自動更新與所選的發行者-鍵保存庫合作夥伴 X509 憑證提供者 / 憑證授權單位。
    - 請注意-非建立合作關係提供者/授權會也可以，但不是支援的自動更新功能。


## <a name="net-support"></a>.NET 支援
- Azure 鍵保存庫.NET 2.0 版本不支援**.NET 4.0** / C# 文件庫
- Azure 鍵保存庫.NET 2.0 版本支援**.NET 核心**/ C# 文件庫

## <a name="namespaces"></a>命名空間
- **模型**的命名空間從**Microsoft.Azure.KeyVault**變更為**Microsoft.Azure.KeyVault.Models**。
- **Microsoft.Azure.KeyVault.Internal**命名空間會遭到捨棄。
- Azure SDK 相依性命名空間變更**Hyak.Common**和**Hyak.Common.Internals** **Microsoft.Rest**和**Microsoft.Rest.Serialization**


## <a name="type-changes"></a>變更類型
- *私人*變更為*SecretBundle*
- 變更為*IDictionary* *字典*
- *清單<T>，字串 [*變更為*IList<T> *
- *NextList*變更為*NextPageLink*


## <a name="return-types"></a>傳回類型
- 會傳回**KeyList**和**SecretList** *IPage<T> * ，而不是*ListKeysResponseMessage*
- 產生的**BackupKeyAsync**會傳回*BackupKeyResult*包含*值*(備份 blob)。 包裝的方法是先並傳回的值。

## <a name="exceptions"></a>例外狀況
- *KeyVaultClientException*會變更為*KeyVaultErrorException*
- 服務錯誤，即會變更*例外狀況。錯誤**例外狀況。Body.Error.Message*。
- 移除錯誤訊息的其他資訊的**[JsonExtensionData]**。

## <a name="constructors"></a>建構函式
- 接受*HttpClient*做為建構函式引數，而不是建構函式只接受*HttpClientHandler*或*DelegatingHandler []*。



## <a name="downloaded-packages"></a>下載的套件  
當用戶端處理時鍵保存庫之間的相依性下列下載
#### <a name="previous-package-list"></a>上一個套件清單
- 封裝 id="Hyak.Common 」 版本 = 「 1.0.2 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Azure.Common 」 版本 = 「 2.0.4 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Azure.Common.Dependencies 」 版本 = 」 1.0.0 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Azure.KeyVault 」 版本 = 」 1.0.0 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Bcl 」 版本 = 「 1.1.9 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Bcl.Async 」 版本 = 「 1.0.168 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Bcl.Build 」 版本 = 「 1.0.14 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Net.Http 」 版本 = 「 2.2.22 」 targetFramework = 「 net45 」

#### <a name="current-package-list"></a>目前的套件清單
- 封裝 id="Microsoft.Azure.KeyVault 」 版本 = 「 2.0.0-preview 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Rest.ClientRuntime 」 版本 = 「 2.2.0 」 targetFramework = 「 net45 」
- 封裝 id="Microsoft.Rest.ClientRuntime.Azure 」 版本 = 「 3.2.0 」 targetFramework = 「 net45 」


## <a name="class-changes"></a>變更類別

- 已移除**UnixEpoch**類別
- **Base64UrlConverter**類別是重新命名為**Base64UrlJsonConverter**

## <a name="other-changes"></a>其他變更

- 支援的暫時性失敗 KV 作業重試原則設定已新增至這個版本的 API。



## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
- 傳回*保存庫*的作業，則傳回類型為包含保存庫屬性的類別。 傳回類型現在*保存庫*。
- *PermissionsToKeys*及*PermissionsToSecrets*是現在*Permissions.Keys* ， *Permissions.Secrets*
- 傳回類型變更套用至控制項平面以及。

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
- 封裝是分解**Microsoft.Azure.KeyVault.Extensions**和**Microsoft.Azure.KeyVault.Cryptography**為加密作業。
