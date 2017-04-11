<properties
    pageTitle="偵測到的 Azure Active Directory 身分識別保護弱點 |Microsoft Azure"
    description="偵測到的 Azure Active Directory 身分識別保護的弱點概觀。"
    services="active-directory"
    keywords="azure active directory 身分識別保護]，[管理應用程式、 安全性、 風險、 風險層級、 弱點、 安全性原則的雲端應用程式探索"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>偵測到的 Azure Active Directory 身分識別保護弱點 

弱點是您可以利用攻擊的環境中的弱點。 我們建議您地址來改善的安全性狀態，您的組織，這些弱點並防止攻擊者利用它們。
<br><br>
![弱點](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilities")
<br>

下列各節提供給您的身分識別保護報告的弱點概觀。

## <a name="multi-factor-authentication-registration-not-configured"></a>多重因素驗證註冊未設定 

這可協助您控制您組織中的 Azure 多重因素驗證部署。 

Azure 多重因素驗證提供第二個使用者驗證的安全性層級。 它可協助保護資料和應用程式的存取權時會議簡單登入程序的使用者需求。 它提供強式驗證透過一系列的簡單的驗證選項 — 電話、 文字訊息或行動應用程式通知或驗證碼和 3rd 廠商誓言權杖。

我們建議您在使用者登增益集需要 Azure 多重因素驗證。 多重因素驗證在風險條件存取原則可透過身分識別保護播放重要的角色。

如需詳細資訊，請參閱[什麼是 Azure 多重因素驗證？](../multi-factor-authentication/multi-factor-authentication.md)


## <a name="unmanaged-cloud-apps"></a>未受管理的雲端應用程式

這可協助您識別您組織中未受管理的雲端應用程式。
 
在新的企業，通常是不知道所有讓組織中的使用者使用其處理雲端應用程式的 IT 部門。 您可以輕鬆查看為什麼系統管理員就必須未經授權存取權企業資料、 可能資料外洩和其他安全性風險的相關問題。 

我們建議您的組織部署雲端應用程式探索以探索未受管理的雲端應用程式]，並管理使用 Azure Active Directory 這些應用程式。

如需詳細資訊，請參閱[尋找雲端應用程式探索未受管理的雲端應用程式](active-directory-cloudappdiscovery-whatis.md)。



##<a name="security-alerts-from-privileged-identity-management"></a>從有權限的身分識別管理的安全性警告

這可協助您找出並解決警告您組織中有權限的身分識別。  

若要讓使用者執行有權限的作業，組織必須將使用者暫時或永久權限的存取權授予中 Azure AD，Azure 或 Office 365 的資源或其他 SaaS 應用程式。 每個這些權限的使用者會增加攻擊貴組織。 這可協助您不必要的權限存取，請找出使用者並採取適當的動作，以減少或消除它們帶來的風險。 

我們建議您的組織使用 Azure AD 有權限的身分識別管理管理，控制項，，並監控小權限的身分識別與 Azure AD 中的資源其存取權，以及其他 Microsoft 線上服務，例如 Office 365 或 Microsoft Intune。

如需詳細資訊，請參閱[Azure AD 有權限的身分識別管理](active-directory-privileged-identity-management-configure.md)。 



## <a name="see-also"></a>另請參閱

 - [Azure Active Directory 身分識別保護](active-directory-identityprotection.md)
