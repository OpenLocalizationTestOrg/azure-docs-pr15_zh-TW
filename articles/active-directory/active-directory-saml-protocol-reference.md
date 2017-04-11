<properties
    pageTitle="Azure AD SAML 通訊協定參照 |Microsoft Azure"
    description="本文提供 Azure Active Directory 中的單一登入與單一 Sign-Out SAML 設定檔的概觀。"
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/23/2016"
    ms.author="priyamo"/>


# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Azure Active Directory 如何使用 SAML 通訊協定

Azure Active Directory (Azure AD) 使用 SAML 2.0 的通訊協定，讓應用程式為使用者提供單一登入體驗。 [單一登入](active-directory-single-sign-on-protocol-reference.md)及[單一教具借出](active-directory-single-sign-out-protocol-reference.md)SAML 設定檔的 Azure AD 會說明如何在身分識別提供者服務中使用 SAML 判斷提示，通訊協定和連結。

SAML 通訊協定需要身分識別提供者 (Azure AD) 與服務提供者 （應用程式） 至 exchange 本身的相關資訊。

當與 Azure AD 註冊應用程式時，應用程式開發人員註冊 Azure AD 同盟的相關資訊。 包括**重新導向 URI**和**中繼資料 URI**的應用程式。

Azure AD 使用雲端服務的**中繼資料 URI**擷取簽署索引鍵和登出 URI 雲端服務。 開發人員如果應用程式並不支援中繼資料 URI，必須連絡 Microsoft 支援服務，提供登出 URI 和簽署鍵。

Azure Active Directory 公開租用戶專屬和一般 （租用戶無關） 單一登入和單一教具借出端點。 這些 Url 代表定址位置--不是只識別碼-，因此您可以前往閱讀中繼資料端點。

 - 租用戶專屬端點位於`https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`。  <TenantDomainName>版面配置區代表已註冊的網域名稱或 TenantID GUID Azure AD 租用戶。 例如，contoso.com 租用戶的同盟中繼資料是在︰ https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- 租用戶獨立端點位於`https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`。在此端點位址，**一般**便會出現，而不是租用戶的網域名稱或識別碼。

Azure AD 發佈同盟中繼資料文件的相關資訊，請參閱[同盟中繼資料](active-directory-federation-metadata.md)。
