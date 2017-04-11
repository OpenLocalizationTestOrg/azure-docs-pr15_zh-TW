<properties
    pageTitle="Azure AD 和應用程式︰ 引導開發人員 |Microsoft Azure"
    description="為 IT 專業人員撰寫，這篇文章提供方針與 Active Directory 整合 Azure 應用程式。"
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
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-and-applications-develop-line-of-business-apps"></a>Azure AD 和應用程式︰ 開發的業務相關應用程式

本指南可提供開發線條的商務 (LoB) 應用程式的 Azure Active Directory (AD) 的概觀。預定對象是 Active Directory/Office 365 全域管理員。

## <a name="overview"></a>概觀

建置應用程式與 Azure AD 整合提供您的組織單一登入 Office 365 中的使用者。 Azure AD 可讓您控制驗證原則應用程式中，讓應用程式。 若要進一步瞭解條件存取及如何保護應用程式搭配使用多重因素驗證 (MFA)，請參閱[設定存取規則](active-directory-conditional-access-azuread-connected-apps.md)。

註冊您的應用程式使用 Azure Active Directory。 註冊應用程式，就表示您的開發人員，可以使用 Azure AD 驗證的使用者，並要求使用者資源，例如電子郵件、 行事曆及文件的存取權。

目錄 （而不是訪客） 的任何成員可以登錄應用程式，否則會以*建立應用程式物件*。

登錄應用程式可讓任何使用者執行下列動作︰

- 取得應用程式的 Azure AD 可辨識的身分識別
- 取得一或多個機密/索引鍵，應用程式可用來將 AD 自我驗證
- 建立自訂的名稱、 標誌、 等 Azure 入口網站中的應用程式。
- Azure AD 授權功能套用至其應用程式，包括︰
  - 角色型存取控制 (RBAC)
  - OAuth 授權伺服器的 azure Active Directory （安全應用程式提供的 API）

- 宣告必要的權限所需的函數的應用程式，如預期般，包括:-應用程式權限 （僅限全域管理員）。 例如︰ 另一個 Azure AD 應用程式或角色的成員資格相對於 Azure 資源，資源] 群組中或訂閱的委派權限 （任何使用者） 中的角色成員資格。 例如︰ Azure AD，登入和已讀取的設定檔


> [AZURE.NOTE]根據預設，任何成員可以登錄應用程式。 若要瞭解如何限制權限的註冊應用程式，以特定的成員，請參閱[Azure ad 如何新增應用程式](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

以下是全域管理員，您需要協助您進行準備生產其應用程式開發人員︰

- 設定存取規則 (存取原則/MFA)
- 設定應用程式要求使用者指派及指派的使用者
- 隱藏預設使用者同意體驗

## <a name="configure-access-rules"></a>設定存取規則

設定您 SaaS 應用程式的每個應用程式存取規則。 例如，您可以需要 MFA，或只允許在受信任的網路上的 [使用者的存取權。 這樣的詳細資訊，可在文件中[設定存取規則](active-directory-conditional-access-azuread-connected-apps.md)。

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>設定應用程式要求使用者指派及指派的使用者

根據預設，使用者可以存取應用程式，而不會被指派。 不過，如果應用程式公開角色，或者如果您想要顯示的使用者存取面板上的應用程式，您應該要求使用者指派。

[要求使用者指派](active-directory-applications-guiding-developers-requiring-user-assignment.md)

如果您是 Azure AD 進階版或企業版行動性套件 (EMS) 的訂閱，我們強烈建議使用群組。 群組指派的應用程式，可讓您委派的持續存取權管理] 以擁有者的群組。 您可以建立群組，或要求負責廠商組織建立 [使用您的群組管理功能] 群組中。

[將使用者指派至應用程式](active-directory-applications-guiding-developers-assigning-users.md)  
[群組指派的應用程式](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>隱藏使用者同意

根據預設，每位使用者經歷同意體驗︰ 登入。 同意體驗，要求使用者授與權限至應用程式，可以令人不安使用者熟悉這類決策。

信任的應用程式，您可以同意組織代表應用程式，以簡化的使用者體驗。

如需有關使用者同意和 Azure 同意體驗的詳細資訊，請參閱[Azure Active Directory 的整合應用程式](active-directory-integrating-applications.md)。

##<a name="related-articles"></a>相關的文章

- [啟用安全的遠端存取內部部署的應用程式與 Azure AD 應用程式 Proxy](active-directory-application-proxy-get-started.md)
- [Azure 條件存取預覽 SaaS 應用程式](active-directory-conditional-access-azuread-connected-apps.md)
- [管理應用程式搭配 Azure AD 的存取](active-directory-managing-access-to-apps.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
