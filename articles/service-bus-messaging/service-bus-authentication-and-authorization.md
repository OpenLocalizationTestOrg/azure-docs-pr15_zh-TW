<properties 
    pageTitle="服務匯流排驗證與授權 |Microsoft Azure"
    description="共用 Access 簽章 (SA) 驗證概觀。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="service-bus-authentication-and-authorization"></a>服務匯流排驗證和授權

Azure 服務匯流排使用其中一個共用 Access 簽章 (SA) 驗證]，或透過 Azure Active Directory 存取控制 （也稱為存取控制服務或 ACS），可以驗證應用程式。 共用 Access 簽章驗證可讓應用程式，以驗證服務匯流排使用設定命名空間，或使用的特定權限的相關聯的實體便捷鍵。 然後可以使用此按鍵來產生用戶端可以使用驗證服務匯流排共用 Access 簽章權杖。

>AZURE。重要 SA 建議 ACS，因為它提供簡單、 有彈性，及方便使用的驗證配置的服務。 應用程式可以使用 SA 案例中，不需要管理授權 」 的使用者。 」 的概念

## <a name="shared-access-signature-authentication"></a>共用的 Access 簽章驗證

[SA 驗證](service-bus-sas-overview.md)可讓您授與使用者存取服務匯流排資源具備特定權限。 在服務匯流排 SA 驗證涉及加密金鑰以服務匯流排資源相關聯的權限設定。 用戶端可以再存取該資源的簡報 SA 權杖組成的資源 URI 存取並到期登入以設定金鑰。

您可以設定按鍵 sa 上服務匯流排命名空間。 索引鍵適用於該命名空間中的所有郵件項目。 您也可以設定服務匯流排佇列 」 和 「 主題上的按鍵。 SA 也支援服務匯流排轉接。

若要使用 SA，您可以在命名空間、 佇列中或包含下列的主題設定[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)物件︰

- *KeyName*可識別規則。

- *PrimaryKey*是用來登/驗證 SA 權杖的加密金鑰。

- *SecondaryKey*是用來登/驗證 SA 權杖的加密金鑰。

- 代表接聽、 傳送] 或管理授與的權限的集合*權限*。

命名空間層級設定授權規則可以授與存取權命名空間中的所有項目用戶端的權杖簽署使用的對應鍵。 最多 12 這類授權規則可以設定服務匯流排命名空間、 佇列中，或主題。 根據預設，所有的權限的[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)設定為每個命名空間時第一次佈建。

若要存取實體，用戶端需要使用特定的[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)所產生的 SA 權杖。 SA 權杖，就會產生的資源和的字串，包含要宣告的存取權，資源 URI 到期 HMAC SHA256 使用授權規則相關聯的加密金鑰。

服務匯流排 SA 驗證支援是包含於 Azure.NET SDK 版本 2.0 及更新版本。 SA 包含[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)支援。 接受做為參數的連線字串的所有 Api 都包含支援 SA 連接字串。

## <a name="acs-authentication"></a>ACS 驗證

透過 ACS 服務匯流排驗證透過小幫手] 來管理 」-sb 」 ACS 命名空間。 如果您想要建立服務匯流排命名空間的小幫手 ACS 命名空間，您無法建立使用傳統 [Azure 入口網站; 您服務匯流排命名空間您必須建立使用[新增 AzureSBNamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx) PowerShell 指令程式。 例如︰

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

若要避免建立 ACS 命名空間，請輸入以下命令︰

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

例如，如果您建立名為**contoso.servicebus.windows.net**服務匯流排命名空間，稱為 「 **contoso sb.accesscontrol.windows.net**小幫手 ACS 命名空間是佈建後自動。 2014 年 8 月之前所建立的所有命名空間，也被建立隨附的 ACS 命名空間。

預設服務身分識別 「 擁有者，「 所有的權限，是根據預設，這個小幫手 ACS 命名空間中提供。 您可以取得 ACS 透過任何服務匯流排實體精密的控制，藉由設定適當的信任關係。 您可以設定其他服務身分識別管理服務匯流排實體的存取權。

若要存取實體，用戶端要求 SWT 權杖從 ACS 適當的宣告提供其認證。 SWT 權杖必須然後傳送邀請的一部分服務匯流排啟用的實體 access 用戶端的授權。

ACS 驗證支援服務匯流排是包含於 Azure.NET SDK 版本 2.0 及更新版本。 此驗證包含[SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx)支援。 接受做為參數的連線字串的所有 Api 都包含支援 ACS 連接字串。

## <a name="next-steps"></a>後續步驟

繼續閱讀[與服務匯流排共用 Access 簽章驗證](service-bus-shared-access-signature-authentication.md)，如需詳細資訊 SA。

在服務匯流排 SA 概觀，請參閱[共用 Access 簽章](service-bus-sas-overview.md)。

您可以找到有關 ACS 權杖中[如何︰ 從 ACS 透過 OAuth 自動換行通訊協定要求的憑證](https://msdn.microsoft.com/library/hh674475.aspx)。



