<properties
    pageTitle="Azure AD 權限的身分識別管理 |Microsoft Azure"
    description="Azure AD 有權限的身分識別管理功能，以及如何使用 PIM 改善您的雲端安全性說明主題。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management"></a>Azure AD 權限的身分識別管理

有權限的身分識別管理 Azure Active Directory (AD)，您可以管理控制項，及監視存取您的組織內部。 包括中 Azure AD 的資源和 Office 365 或 Microsoft Intune 等其他 Microsoft 線上服務的存取權。  

> [AZURE.NOTE] 僅能使用的 Azure Active Directory 進階版 P2 版本有權限的身分識別管理。 如需詳細資訊，請參閱[Azure Active Directory 版本](active-directory-editions.md)。

組織想要有安全的資訊或資源的存取權的使用者數目最小化，因為，以減少惡意快速存取權的使用者的機率。 不過，使用者仍然需要執行 Azure、 Office 365 或 SaaS 應用程式中的權限作業。 組織授與使用者權限的存取 Azure AD 中不監控這些使用者以他們的管理員權限的執行。 Azure AD 權限的身分識別管理協助解決這類風險。  

Azure AD 權限的身分識別管理可協助您︰  

- 查看哪些使用者 Azure AD 管理員
- 視需要啟用，」 在 「 Office 365 和 Intune，例如 Microsoft Online Services 的管理權限
- 系統管理員工作分派中取得系統管理員存取歷程記錄 」 及 「 修訂的相關的報表
- 取得權限的角色的存取權的相關的警示

Azure AD 有權限的身分識別管理可以管理內建 Azure AD 組織角色，包括︰  

- 全域管理員
- 計費管理員
- 服務管理員  
- 使用者的管理員
- 密碼管理員

## <a name="just-in-time-administrator-access"></a>僅在時間系統管理員存取權

以往，您無法指派給使用者將透過 Windows PowerShell 的 Azure 傳統入口網站的管理員角色。 如此一來，該使用者會變成**永久性的管理員**，一律位於指派角色。 Azure AD 權限的身分識別管理介紹**符合資格的管理員**的概念。 符合資格的系統管理員應，但不是每天需要有權限的存取權的使用者。 使用者需要存取，然後完成啟動程序，並成為使用中管理員預先定義的一段時間之後，才非作用中的角色。

## <a name="enable-privileged-identity-management-for-your-directory"></a>啟用有權限的身分識別管理您的目錄

您可以開始[Azure 入口網站](https://portal.azure.com/)中使用 Azure AD 有權限的身分識別管理。

>[AZURE.NOTE] 您必須是全域管理員使用組織帳戶 (例如，@yourdomain.com),沒有 Microsoft 帳戶 (，例如@outlook.com),啟用 Azure AD 有權限的身分識別管理目錄。

1. 目錄的全域管理員的身分登入[Azure 入口網站](https://portal.azure.com/)。
2. 如果您的組織擁有多個目錄，選取您的使用者名稱中 Azure 入口網站的右上角。 選取您會在其中使用 Azure AD 有權限的身分識別管理的目錄。
3. 選取**更多服務**，然後使用 [篩選] 文字方塊**Azure AD 有權限的身分識別管理**搜尋。
4. 核取 [**固定至儀表板**，然後按一下 [**建立**]。 隨即會開啟 [權限的身分識別管理應用程式。

如果您是使用 Azure AD 有權限的身分識別管理您的目錄中的第一個人員，然後[安全性精靈](active-directory-privileged-identity-management-security-wizard.md)逐步引導您進行初始的工作分派體驗。 之後，您會自動變成的第一個**安全性系統管理員**和**權限的角色系統管理員**的目錄。

只要有權限的角色的系統管理員可以管理的其他系統管理員的存取。 您可以[授權讓其他使用者能夠管理 PIM 中](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)。

## <a name="privileged-identity-management-dashboard"></a>有權限的身分識別管理儀表板

Azure AD 權限的身分識別管理員提供儀表板，提供您重要的資訊，例如︰

- 警示，指出提升安全性的機會
- 指派給每個權限的角色的使用者數目  
- 符合資格和永久管理員的數字
- 檢閱的持續存取權

![PIM 儀表板的螢幕擷取畫面][2]

## <a name="privileged-role-management"></a>有權限的角色管理

Azure AD 有權限的身分識別管理] 中，您可以藉由新增或移除永久或符合資格的系統管理員，每個角色管理系統管理員。

![PIM 新增/移除管理員的螢幕擷取畫面][3]

## <a name="configure-the-role-activation-settings"></a>設定的角色啟動設定

您可以使用[角色設定](active-directory-privileged-identity-management-how-to-change-default-settings.md)來設定符合資格的角色啟動內容，包括︰

- 角色啟動期間的持續時間
- 角色啟動通知
- 使用者需要角色啟動程序時所提供的資訊  

![PIM 設定系統管理員啟用的螢幕擷取畫面][4]

請注意，在圖像中，已停用 [**多重因素**驗證] 按鈕。 針對特定、 高度的權限的角色，我們需要 MFA 增強的保護。

## <a name="role-activation"></a>角色啟動  

若要[啟動角色](active-directory-privileged-identity-management-how-to-activate-role.md)，符合資格的系統會要求時間繫結 「 啟動 」 的角色。 您可以使用 Azure AD 有權限的身分識別管理中的 [**啟動我的角色**] 選項會要求啟動。

要啟動角色的管理員必須初始化 Azure AD 有權限的身分識別管理 Azure 入口網站中。

角色啟動是可自訂的。 在 PIM 設定]，您可以決定啟動，以及長度管理員必須啟動該角色所提供的資訊。

![PIM 管理員要求角色啟動螢幕擷取畫面][5]

## <a name="review-role-activity"></a>檢閱角色活動

有兩種方式來追蹤您的員工及管理員使用有權限的角色。 [稽核記錄](active-directory-privileged-identity-management-how-to-use-audit-log.md)使用第一個選項。 稽核記錄中有權限的角色指派及角色啟動歷程記錄記錄追蹤修訂。

![PIM 啟動歷程記錄的螢幕擷取畫面][6]

設定一般[access 檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)為第二個選項。 這些存取評論可執行，並指派 （例如小組管理員） 的檢閱者或員工可以檢閱本身。 這是監視人員仍需要存取，以及誰不再負責的工作的最佳方式。


## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
