<properties
    pageTitle="Azure Active Directory 常見問題集 |Microsoft Azure"
    description="Azure Active Directory 常見問題集提供搭配問題的答案存取 Azure 和 Azure Active Directory 管理和應用程式存取的密碼。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>

# <a name="azure-active-directory-faq"></a>Azure Active Directory 常見問題集

Azure Active Directory 做為合併身分識別，存取管理與安全性的所有層面的服務 (IDaaS) 解決方案全面涵蓋所有內容的身分識別。


如需詳細資訊，請參閱[什麼是 Azure Active Directory？](active-directory-whatis.md)。



## <a name="accessing-azure-and-azure-active-directory"></a>存取 Azure 和 Azure Active Directory


**問︰ 為什麼會出現 「 沒有訂閱找到 「 當我嘗試存取 Azure 傳統入口網站 (https://manage.windowsazure.com) 中的 Azure AD？**

**A:**存取 Azure 傳統入口網站需要每位使用者擁有的權限 Azure 的訂閱。 如果您在付費的 Office 365 或 Azure AD 瀏覽至[http://aka.ms/accessAAD](http://aka.ms/accessAAD)一次啟動步驟，否則您將需要啟動完整的[Azure 試用](https://azure.microsoft.com/pricing/free-trial/)或付費的訂閱。 

如需詳細資訊，請參閱︰

- [Azure 的訂閱是 Azure Active Directory 與相關聯的方式](active-directory-how-subscriptions-associated-directory.md)

- [管理您的 Office 365 訂閱，Azure 中的目錄](active-directory-manage-o365-subscription.md)

---

**問︰ 什麼是 Azure AD，之間的關聯性 Office 365 和 Azure？**

**A:**Azure Active Directory 為您提供所有 Microsoft online services 常見的身分識別和存取功能。 您使用 Office 365、 Microsoft Azure、 Intune 或其他人，您是否已啟用登入和存取的所有這些服務管理使用 Azure AD。 

事實上，您已啟用 Microsoft Online services 的所有使用者都定義為一或多個 Azure AD 執行個體中的使用者帳戶。 您可以啟用這些帳戶免費 Azure AD 功能，例如雲端應用程式存取。
 
此外，Azure AD 支付服務 (例如︰ Azure AD 基本、 進階版，EM 等) 互補其他線上服務，例如 Office 365 與 Microsoft Azure 使用完整的企業色階管理和安全性解決方案。


---



## <a name="getting-started-with-hybrid-azure-ad"></a>混合式 Azure AD 快速入門


**問︰ 如何將我的內部部署目錄連線至 Azure AD**

**A:**您可以使用**Azure AD Connect**Azure ad 連線您內部部署的目錄。 

如需詳細資訊，請參閱[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。


---

**問︰ 如何設定 SSO 我內部部署目錄和之間我雲端應用程式？**

**A:**您只需要設定您的內部部署目錄和 Azure AD 之間的 SSO。 您可以透過 Azure AD 存取您的雲端應用程式，如服務自動磁碟機其內部部署認證的正確驗證使用者。

從內部部署實作 SSO 就能輕鬆達成與同盟解決方案，例如 ADFS 或設定密碼雜湊同步處理。 您可以輕鬆地部署使用 Azure AD Connect 設定精靈這兩個選項。
  

如需詳細資訊，請參閱[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
  

---

**問︰ Azure Active Directory 會針對組織中的使用者提供的自助入口網站？**

**A:**是的 Azure Active Directory 可讓您與使用者的自助] 和 [應用程式存取[Azure AD 存取面板](http://myapps.microsoft.com)。 如果您是 Office 365 客戶，您可以在 Office 365 入口網站中找到許多相同的能力。 

如需詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 



---

**問︰ 沒有 Azure AD 有助於管理我的內部部署基礎結構？**

**A:**是的它會。 Azure AD 進階版可讓您**連線**的健康情況。 Azure AD 連線狀況可協助您監視及掌握您的內部部署識別基礎架構與同步處理服務。  

如需詳細資訊，請參閱[監控您的內部部署識別基礎結構及同步處理服務雲端中](active-directory-aadconnect-health.md)。  

---

## <a name="password-management"></a>密碼管理

**問︰ 使用 Azure AD 密碼回寫功能不密碼同步處理嗎？（又稱，我想要使用密碼回寫 Azure AD SSPR，但不想儲存在 cloud? 我密碼）**

**A:**您不需要同步處理您 AD 密碼 Azure ad 才能啟用回寫功能。 在同盟環境中，Azure AD SSO 依賴驗證使用者的內部部署目錄。 這種情況時，不需要在 Azure AD 中追蹤的內部部署密碼。

---

**問︰ 如何時間寫回 AD 內部部署的密碼？**

**A:**密碼回寫功能的運作方式即時。 

如需詳細資訊，請參閱[快速入門密碼管理](active-directory-passwords-getting-started.md) 


---

**問︰ 可以使用密碼回寫所管理的系統管理員的密碼嗎？**

**A:**是的如果您有啟用回寫的密碼，請執行由系統管理員的密碼作業會重新寫入到您的內部部署環境。  

如需更多解答密碼相關的問題，請參閱[密碼管理常見問題集](active-directory-passwords-faq.md)。

---

## <a name="application-access"></a>應用程式存取


**問︰ 到哪裡尋找預先整合 Azure AD 使用的應用程式的清單及其功能？**

**A:**Azure AD 有超過 2600 Microsoft、 應用程式服務提供者，或協力廠商的預先整合式應用程式。 所有預先整合式應用程式支援 SSO。 SSO 可讓您使用您的組織認證來存取您的應用程式。 有些應用程式也支援自動化佈建和不支援

預先整合式應用程式的完整清單，請參閱[使用中的目錄服務商場](https://azure.microsoft.com/marketplace/active-directory/)。


---

**問︰ 如果我需要的應用程式不是 Azure AD 服務商場？**

**A:**Azure AD 進階版，請使用您可以新增並設定您想要的任何應用程式。 視您的應用程式功能與您的喜好設定，而定，您可以設定 SSO 和自動化佈建。  

如需詳細資訊，請參閱︰

- [設定單一登入 Azure Active Directory 應用程式組件庫中的應用程式](active-directory-saas-custom-apps.md)
- [若要啟用自動提供 [使用者和群組的 Azure Active Directory 從應用程式使用 SCIM](active-directory-scim-provisioning.md) 


---

**問︰ 使用者如何登入使用 Azure Active Directory 的應用程式？**
 
**A:**Azure Active directory 提供數種方式，讓使用者檢視及存取他們的應用程式，例如︰

- Azure AD 存取面板

- 在 Office 365 應用程式啟動器

- 直接登入同盟應用程式

- [深層連結同盟的密碼或現有的應用程式

如需詳細資訊，請參閱[部署 Azure AD 整合應用程式的使用者](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)。


---

**問︰ 什麼是 Azure Active Directory 的不同方式，可讓驗證和單一登入應用程式？**
 
**A:**Azure Active Directory 驗證和授權，例如 SAML 2.0、 OpenID 連線、 OAuth 2.0 和 WS 同盟支援許多標準化的通訊協定。 Azure AD 也支援密碼儲存庫存和自動的登入功能僅支援表單型驗證的應用程式。  

如需詳細資訊，請參閱︰

- [Azure ad 驗證案例](active-directory-authentication-scenarios.md)

- [Active Directory 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [如何沒有單一登入與 Azure Active Directory 的工作？](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**問︰ 可以新增我執行內部部署的應用程式？**

**A:**Azure AD 應用程式 Proxy 可讓您輕鬆地與安全存取您選擇的內部部署 web 應用程式。 您可以存取您的 SaaS 應用程式，Azure Active Directory 中的相同方式來存取這些應用程式。 有不需要 VPN，或變更您的網路基礎結構。  

如需詳細資訊，請參閱[如何提供安全的遠端存取內部部署的應用程式](active-directory-application-proxy-get-started.md)。


--- 

**問︰ 如何是否需要 MFA 使用者存取特定的應用程式？**

**A:**透過 Azure AD 條件存取時，您可以指定每個應用程式的唯一存取原則。 在您的原則，您可以要求 MFA，在所有的時間，或使用者未連線到本機的網路。  

如需詳細資訊，請參閱[安全存取 Office 365 和其他應用程式連線至 Azure Active Directory](active-directory-conditional-access.md)。


---

**問︰ 什麼是自動化使用者佈建 SaaS 應用程式？**

**A:**Azure Active Directory 可讓您可以自動化建立、 維護及移除許多熱門的雲端 (SaaS) 應用程式中的使用者身分識別。 

如需詳細資訊，請參閱[自動化使用者佈建和 Deprovisioning 與 Azure Active Directory SaaS 應用程式](active-directory-saas-app-provisioning.md)

---



