<properties
    pageTitle="保護 Azure AD 中的 [權限的存取 |Microsoft Azure"
    description="主題說明確保安全 Azure Azure Active Directory 與 Microsoft 線上服務的存取權限的方法。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="mwahl"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="kgremban"/>


# <a name="securing-privileged-access-in-azure-ad"></a>Azure AD 保護有權限的存取

設定權限的存取的安全性是關鍵的第一個步驟，以協助保護現代化的組織中的商務資產。 有權限的帳戶是管理及管理 IT 系統。 網路攻擊目標存取組織的資料和系統這些帳戶。 以安全的權限的存取，您應該隔離帳戶與系統的公開給惡意使用者風險。

更多使用者開始到雲端服務的權限存取。 這可以包含的 office 365 的全域管理員、 Azure 訂閱管理員和 Vm 或 SaaS 應用程式上擁有系統管理權限的使用者。

Microsoft 建議您追蹤此藍圖[保護有權限](https://technet.microsoft.com/library/mt631194.aspx)存取。

使用管理存取 Azure、 Office 365 或其他 Microsoft 服務及應用程式的 Azure Active Directory 的客戶，這些原則會套用是否管理和驗證 Active Directory 或 Azure Active Directory 中的使用者帳戶。 下列各節提供支援保護有權限的存取的 Azure AD 功能的詳細資訊。

## <a name="multi-factor-authentication"></a>多重因素驗證

若要增加安全性的管理員驗證，您應該要求多重因素驗證 (MFA) 之前授與權限。 MFA 是一種方法驗證您的需要使用的不只是使用者名稱和密碼。 它會提供第二個使用者登增益集及交易的安全性層級。

Azure 多重因素驗證可協助保護存取資料和會議的簡單登入程序的使用者需求時的應用程式。 它提供強式驗證透過簡單的驗證選項包括 [行動電話、 文字訊息、 行動應用程式通知，或驗證碼及第 3 廠商誓言權杖範圍。

如需 Azure 多重因素驗證的運作方式的概觀，請參閱以下影片。

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

如需詳細資訊，請參閱[Office 365 和 Azure 的 MFA MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/)。

## <a name="time-bound-privileges"></a>時間繫結權限

在某些組織可能會發現他們在具有高權限的角色中有太多的使用者。 使用者可能已經新增的角色為特定的活動，例如註冊服務，但未之後經常使用這些權限。

若要降低揭露時間的權限，並增加您將使用，限制只需要執行工作時採取僅在時間 (JIT)，其權限的使用者。 如需 Azure Active Directory 與 Microsoft Online Services，您可以使用[Azure AD 有權限的身分識別管理 (PIM)](http://aka.ms/AzurePIM)。


![PIM 儀表板][2]


## <a name="attack-detection"></a>攻擊偵測

[Azure Active Directory 身分識別保護](../active-directory-identityprotection.md)可讓您合併的檢視風險事件與可能會影響您組織的身分識別的弱點。 根據風險事件，識別保護計算每個使用者，讓您設定自動保護貴組織的身分識別的風險原則使用者風險層級。 這些原則，以及 Azure Active Directory 和 EMS，提供的其他條件存取控制項可以自動封鎖使用者，或提供建議，包括密碼重設和多重因素驗證強制執行。

![Azure AD 身分識別保護][3]

## <a name="conditional-access"></a>條件的存取權

使用條件存取控制項，Azure Active Directory 檢查驗證的使用者，才能存取應用程式時，您選擇的特定條件。 一旦符合這些條件，使用者就是驗證，允許存取應用程式。


![設定與 MFA 條件存取規則][4]


## <a name="related-articles"></a>相關的文章

- 啟用[Azure 多重因素驗證](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- 啟用[Azure AD 權限的身分識別管理](../active-directory-privileged-identity-management-configure.md)
- 啟用[Azure AD 身分識別保護](../active-directory-identityprotection.md)
- 啟用[條件存取控制](../active-directory-conditional-access.md)


如需有關如何建立完成安全性藍圖的詳細資訊，請參閱[Microsoft 雲端安全性企業架構的](http://aka.ms/securecustomer)文件的 「 客戶的責任和藍圖 」 區段。 如需有關著讓 Microsoft 服務，協助這些主題的詳細資訊，請連絡您的 Microsoft 代表或造訪我們[Cybersecurity 方案] 頁面](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx)。

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png
