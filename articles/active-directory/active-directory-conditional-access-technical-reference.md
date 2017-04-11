
<properties
    pageTitle="Azure Active Directory 條件的存取技術參照 |Microsoft Azure"
    description="使用條件存取控制項，Azure Active Directory 檢查您選擇 [驗證使用者時，才能存取應用程式的特定條件。 一旦符合這些條件，使用者就是驗證，允許存取應用程式。"
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory 條件的存取技術參照

## <a name="services-enabled-with-conditional-access"></a>啟用與條件 access services
Access 的條件規則支援過各種 Azure AD 應用程式類型。 這份清單，包括︰

- 從 Azure AD 應用程式組件庫的同盟應用程式
- 從 Azure AD 應用程式庫密碼 SSO 應用程式
- 註冊 Azure 應用程式 Proxy 的應用程式
- 開發的商業和註冊 Azure AD 租用戶多應用程式的一行
- Visual Studio 線上
- Azure Remote 應用程式
-   Dynamics CRM
- Microsoft Office 365 Yammer
- Microsoft Office 365 Exchange Online
- Microsoft Office 365 SharePoint Online （包括商務用 OneDrive）


## <a name="enable-access-rules"></a>啟用存取規則

每個規則可以啟用或停用上的每個應用程式基底。 **開啟**規則時它們會啟用並強制執行存取應用程式的使用者。 在**關閉**時，不會使用，並不會影響使用者登入體驗。

## <a name="applying-rules-to-specific-users"></a>將規則套用至特定的使用者
規則可以套用到特定的使用者設定**套用至**根據安全性群組集。 **套用至**可以設定為**所有使用者**或**群組**。 設定時，**所有使用者**規則會套用在任何使用者有權存取應用程式。 [**群組**] 選項可讓特定的安全性及通訊群組，若要選取，只會強制執行的規則這些群組。

在部署規則時，通常會先套用限制設定的使用者試驗群組的成員。 一旦完成規則可以套用至**所有使用者**。 這會導致在組織中的所有使用者強制執行規則。

使用 [**除了**] 選項的原則可能也會不選取的群組。 即使顯示在 [包括] 群組中，將會排除任何這些群組的成員。

## <a name="at-work-networks"></a>「 公司 」 網路


使用 「 公司 」 的網路的條件的存取規則依賴受信任的 IP 位址範圍已設定中 Azure AD，或使用 AD FS 從 「 內網卡 」 宣告。 包含這些規則︰

- 需要時不在公司的多因素驗證
- 封鎖時不在公司的存取權

指定選項 「 公司 」 網路

1. 設定[多重因素驗證設定] 頁面](../multi-factor-authentication/multi-factor-authentication-whats-next.md)中的信任的 IP 位址範圍。 條件存取原則會在每個驗證要求和權杖發行上使用的設定的範圍評估的規則。 
2. 設定使用內部網卡宣告，此選項可以搭配使用 AD FS 同盟目錄。 [深入了解內部 coronet 宣告](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)。
3. 設定公用 IP 位址範圍。 在 [設定] 索引標籤中，為您的目錄，您可以設定的公用 IP 位址。 為 「 公司 」 IP 位址，條件 Access 會使用這些，這個選項可讓其他設定，50 的 IP 位址限制強制執行 MFA 設定頁面上方的範圍。



## <a name="rules-based-on-application-sensitivity"></a>應用程式敏感度為基礎的規則

每個應用程式讓高值服務受到保護，而不影響其他服務的存取設定規則。 在 [應用程式的 [**設定**] 索引標籤上，可以設定條件存取規則。 

目前提供的規則︰

- **需要多重因素驗證**
 - 此原則套用至所有使用者都必須驗證透過多重因素驗證至少一次。
 
- **需要時不在公司的多因素驗證**
 - 如果您套用此原則，所有使用者都必須有執行多因素驗證至少一次，只要從非工作遠端位置存取服務。 如果他們移動工作從遠端位置時，它們都必須執行多因素驗證時存取服務。
 
- **封鎖時不在公司的存取權** 
 - 當使用者移動工作從遠端位置時，他們會遭到封鎖如果已套用的 「 封鎖時不在公司的 access 「 原則。  他們會重新允許在工作位置時的存取權。


## <a name="related-topics"></a>相關的主題

- [保護存取 Office 365 及其他應用程式連線至 Azure Active Directory](active-directory-conditional-access.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
