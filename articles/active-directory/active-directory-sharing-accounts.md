<properties
    pageTitle="共用帳戶使用 Azure AD | Microsoft Azure"
    description="Azure Active Directory 如何讓組織安全地共用的內部部署的應用程式與消費者雲端服務帳戶的描述。"
    services="active-directory"
    documentationCenter=""
    authors="msStevenPo"
    manager="femila"
    editor=""/>

 <tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"  
    ms.author="stevenpo"/>

# <a name="sharing-accounts-with-azure-ad"></a>Azure AD 與共用帳戶

## <a name="overview"></a>概觀
有時，組織必須多人使用以單一使用者名稱和密碼。 這種情況在兩種情況︰

- 當存取需要以唯一的登入和密碼，為每位使用者的應用程式時，是否內部部署的應用程式或消費者雲端服務 （例如公司社交媒體帳戶）。
- 當您建立多使用者環境。 您可能必須具有權限，並可用於執行核心設定、 管理及復原活動 （例如本機 「 全域系統管理員 」 的 Office 365） 或根帳戶 Salesforce 中的單一的本機帳戶。

一般而言，這些帳戶會被共用認證 （使用者名稱與密碼） 發送給正確的人，或將它們儲存在共用多個信任代理程式可以存取的位置。

傳統的共用模型具有多個缺點︰

- 啟用新的應用程式的功能需要您發佈的認證以每個人都需要存取。
- 每個共用的應用程式可能需要自己一組唯一的共用的認證，要求使用者請記住，多個認證集。 當使用者必須記住許多認證時，會增加風險，他們會採用高風險的作法。 （例如寫下密碼）。
- 您不知道有誰有權存取應用程式。
- 您不知道有哪些人擁有*存取*應用程式。
- 當您需要移除的應用程式的存取時，您需要更新的認證，並重新發佈至每個人都需要存取該應用程式。

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory 帳戶共用

Azure AD 提供的新方法使用共用的帳戶，可以排除這些缺點。

Azure AD 管理員設定讓使用者可以使用存取畫面，並選擇類型的單一登入的最佳存取哪些應用程式適用於該應用程式。 這些類型之一，*密碼以單一登入*，可讓 Azure AD 該應用程式的登入程序期間作為一種 [代理人]。

使用者登入以組織帳戶一次。 這是他們定期用來存取他們的桌面或電子郵件的同一個帳戶。 他們探索及存取指派給這些應用程式。 使用共用的帳戶，這份清單的應用程式可以包含共用任何的認證數字。 使用者不必記住或寫下可能會使用各種不同的帳戶。

共用的帳戶不只增加監督和改善合用性，他們也可以增強您的安全性。 使用認證的權限的使用者沒有看到共用的密碼，但而取得權限]，[密碼] 使用經過協調的驗證流程的一部分。 此外，某些密碼 SSO 應用程式中，您可以將 Azure AD 定期變換 （更新） 使用大型的複雜的密碼，提升安全性帳戶的密碼。 管理員可以輕鬆地授與或撤銷存取應用程式並又誰有權存取帳戶與人員存取它。

Azure AD 支援共用的帳戶的任何企業版行動性套件 (EMS)，進階版或基本授權的使用者，請在所有類型的密碼單一登入應用程式。 您可以共用千分位在 [應用程式] 圖庫中預先整合式應用程式的任何帳戶，而且可以新增[自訂的 SSO 應用程式](active-directory-sso-integrate-saas-apps.md)與您的密碼驗證應用程式。

啟用帳戶共用的 azure AD 功能包括︰

- [密碼單一登入](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
- 密碼單一登入代理程式
- [群組工作分派](active-directory-accessmanagement-self-service-group-management.md)
- 自訂密碼應用程式
- [儀表板報表方式應用程式使用方式](active-directory-passwords-get-insights.md)
- 使用者存取入口網站
- [應用程式 proxy](active-directory-application-proxy-get-started.md)
- [Active Directory 服務商場](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>共用帳戶
若要共用的帳戶，您必須使用 Azure AD:

- 新增應用程式[的應用程式庫](https://azure.microsoft.com/marketplace/active-directory/)或[自訂應用程式](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- 設定密碼單一登入 (SSO) 的應用程式
- 使用 [[群組依據工作分派](active-directory-accessmanagement-group-saasapps.md)並選取 [輸入共用的認證] 選項
- 可省略︰ 在某些應用程式，例如 Facebook、 Twitter 或 LinkedIn，您可以啟用[Azure AD 自動化的密碼變換](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)的選項

您也可以讓您共用的帳戶更安全的多重因素驗證 (MFA) （深入瞭解[保護 Azure AD 應用程式](../multi-factor-authentication/multi-factor-authentication-get-started.md)），您可以委派管理誰有權存取使用[Azure AD 自助](active-directory-accessmanagement-self-service-group-management.md)群組管理應用程式的能力。

## <a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [保護與條件的 access 應用程式](active-directory-conditional-access.md)
- [自助群組管理/SSAA](active-directory-accessmanagement-self-service-group-management.md)
