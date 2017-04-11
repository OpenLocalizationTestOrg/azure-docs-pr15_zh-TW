<properties
    pageTitle="防火牆存取鍵保存庫 |Microsoft Azure"
    description="了解如何從應用程式在防火牆存取鍵保存庫"
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

# <a name="accessing-key-vault-behind-firewall"></a>防火牆存取鍵保存庫
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-portshostsip-addresses-should-i-open-to-enable-access-to-key-vault"></a>問︰ 我的主要保存庫用戶端應用程式需要有防火牆，哪些連接埠/主機 IP 位址應該開啟以存取金鑰保存庫？

若要存取金鑰保存庫金鑰保存庫用戶端應用程式必須能夠存取的各種功能的多個結束點。

- 驗證 （透過 Azure Active Directory)
- 管理的金鑰保存庫 （包括建立/已讀取/更新/刪除，同時設定存取原則） 透過 Azure 資源管理員
- 存取和管理物件 （索引鍵和機密） 儲存在本身的金鑰保存庫中，會經歷金鑰保存庫特定結束點 (例如[https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net))。  

根據您的設定和環境，有一些變化。   

## <a name="ports"></a>連接埠

所有流量導向所有 3 函數 （驗證、 管理及資料平面存取） 鍵保存庫介紹 HTTPS︰ 連接埠 443。 不過的 CRL，會有有時候 HTTP （連接埠 80） 的流量。 支援 OCSP 用戶端不應達到 CRL，但有時候可能會碰到[http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)。  

## <a name="authentication"></a>驗證

需要金鑰保存庫用戶端應用程式存取 Azure Active Directory 驗證的結束點。 使用的結束點取決於 AAD 租用戶設定]，然後輸入的本金-使用者主體、 服務主要和帳戶類型，例如 Microsoft 帳戶或組織的識別碼。  

| 主體類型 | 端點︰ 連接埠 |
|----------------|---------------|
| 使用 Microsoft 帳戶的使用者<br> （例如︰user@hotmail.com) | **全域管理員︰**<br> login.microsoftonline.com:443<br><br> **Azure china （中國):**<br> login.chinacloudapi.cn:443<br><br>**Azure 美國政府︰**<br> 登入 us.microsoftonline.com:443<br><br>**Azure 德國︰**<br> login.microsoftonline.de:443<br><br> 與 <br>login.live.com:443   |
| 使用 AAD 組織識別碼 （例如使用者/服務本金user@contoso.com) | **全域管理員︰**<br> login.microsoftonline.com:443<br><br> **Azure china （中國):**<br> login.chinacloudapi.cn:443<br><br>**Azure 美國政府︰**<br> 登入 us.microsoftonline.com:443<br><br>**Azure 德國︰**<br> login.microsoftonline.de:443 |
| 使用組織識別碼 + ADFS 或其他同盟的結束點 （例如使用者/服務本金user@contoso.com) | 組織識別碼以及 ADFS 上述的結束點或其他同盟的端點 |

有其他可能的複雜案例。 請參閱[Azure Active Directory 驗證流程](/documentation/articles/active-directory-authentication-scenarios/)、[整合應用程式與 Azure Active Directory](/documentation/articles/active-directory-integrating-applications/)和[Active Directory 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn151124.aspx)的其他資訊。  

## <a name="key-vault-management"></a>索引鍵保存庫管理

索引鍵保存庫的 [管理] （CRUD 並設定存取原則），金鑰保存庫用戶端應用程式需要存取 Azure 資源管理員結束點。  

| 作業類型 | 端點︰ 連接埠 |
|----------------|---------------|
| 索引鍵保存庫控制項平面作業<br> 透過 Azure 資源管理員 | **全域管理員︰**<br> management.azure.com:443<br><br> **Azure china （中國):**<br> management.chinacloudapi.cn:443<br><br> **Azure 美國政府︰**<br> management.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> management.microsoftazure.de:443 |
| Azure Active Directory 圖表 API | **全域管理員︰**<br> graph.windows.net:443<br><br> **Azure china （中國):**<br> graph.chinacloudapi.cn:443<br><br> **Azure 美國政府︰**<br> graph.windows.net:443<br><br> **Azure 德國︰**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>索引鍵保存庫作業

所有的金鑰保存庫物件 （索引鍵和機密） 管理以及加密作業，金鑰保存庫用戶端需要存取金鑰保存庫結束點。 根據您的金鑰保存庫的位置，端點 DNS 尾碼是不同的。 索引鍵保存庫結束點是格式︰ < 保存庫名稱 >。 < 地區-特定--尾碼 > 下表所述。  

| 作業類型 | 端點︰ 連接埠 |
|----------------|---------------|
| 鍵保存庫作業，例如密碼編譯作業按鍵，建立/已讀取/更新/刪除索引鍵和密碼，設定/取得標記及其他屬性的金鑰保存庫物件 （按鍵/密碼）     | **全域管理員︰**<br> &lt;保存庫名稱&gt;。 vault.azure.net:443<br><br> **Azure china （中國):**<br> &lt;保存庫名稱&gt;。 vault.azure.cn:443<br><br> **Azure 美國政府︰**<br> &lt;保存庫名稱&gt;。 vault.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> &lt;保存庫名稱&gt;。 vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP 位址範圍

依序鍵保存庫服務使用的其他 Azure 的資源，例如 PaaS 基礎結構，因此不能提供特定的 IP 位址範圍的重要保存庫服務端點必須在任何時間。 但是如果您的防火牆僅支援 IP 位址範圍然後，請參閱[Microsoft Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)文件。   驗證與身分識別 (Azure Active Directory)，您的應用程式必須連接至[驗證與身分識別的地址](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)所述的端點。

## <a name="next-steps"></a>後續步驟

- 如果您有金鑰保存庫的相關問題，請造訪[Azure 金鑰保存庫論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
