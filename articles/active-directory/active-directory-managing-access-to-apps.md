<properties
  pageTitle="管理存取應用程式使用 Azure AD | Microsoft Azure"
  description="說明如何 Azure Active Directory 可讓組織指定每位使用者具有存取權限的應用程式。"
  services="active-directory"
  documentationCenter=""
  authors="femila"
  manager="femila"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/13/2016"
  ms.author="femila"/>


# <a name="managing-access-to-apps"></a>管理存取應用程式

持續存取權管理、 使用評估，和報告繼續進行後貴組織的身分識別系統整合應用程式。 在許多情況下，IT 系統管理員或技術服務需要採取中管理存取您的應用程式進行中作用中的角色。 有時候，會執行一般或部門的 IT 小組工作分派。 工作分派決策，通常要委派給商業決策人員，要求其核准，才能 IT 會讓工作分派。  其他組織投資與現有自動化身分識別和存取管理系統整合，例如角色型存取控制 (RBAC) 或屬性型存取控制 (ABAC)。 整合和規則開發通常可以特殊且費時。 監控或 [管理方式報告為自己個別、 高，及複雜的投資。

## <a name="how-does-azure-active-directory-help"></a>Azure Active Directory 如何協助？

 Azure AD 支援更多的存取管理設定應用程式，讓組織輕鬆達成介於自動的屬性工作分派 （ABAC 或 RBAC 案例），透過委派，包括管理系統管理員的權限存取原則。 Azure AD 您可以輕鬆地達成複雜的原則，結合為單一應用程式的多個管理模型與偶數就可以重複使用管理規則以相同的對象的應用程式。

 - [新增新的或現有的應用程式](active-directory-sso-integrate-saas-apps.md)


 Azure AD 應用程式工作分派著重於兩個主要的工作分派模式︰

- **個別工作分派**IT 管理員目錄全域管理員權限可以選取個別的使用者帳戶，並授與存取應用程式。
- **群組為基礎的工作分派 （付款只 Azure AD）**IT 管理員目錄全域管理員權限可以指派至應用程式的群組。 無論是群組的成員次嘗試存取應用程式，取決於特定使用者的存取權。 換句話說，系統管理員可以有效率地建立指出 「 任何目前群組成員的已指派的應用程式存取 」 的工作分派規則。 使用此工作分派的選項，管理員可以可以從任何 Azure AD 群組管理選項，包括[屬性為基礎的動態群組](active-directory-accessmanagement-manage-groups.md)、 外部系統群組 （例如，內部部署的 Active Directory 或 Workday） 或管理員的管理或自行 service 管理群組。 一個群組，可以輕鬆地指定為多個應用程式，確保與工作分派相關性的應用程式，可以共用工作分派的規則，整體管理複雜性。 請注意巢狀的群組成員資格的群組指派的應用程式這次不支援。

系統管理員可以使用下列兩個工作分派模式，來達成任何必要的工作分派的管理方式。

Azure AD，與使用情況和工作分派報告已經完全整合，讓系統管理員輕鬆報告工作分派的狀態與工作分派的錯誤，即使使用方式。

## <a name="complex-application-assignment-with-azure-ad"></a>Azure AD 的複雜的應用程式工作分派

請考慮 Salesforce 等應用程式。 許多組織中 Salesforce 主要由行銷和銷售組織使用。 通常，行銷小組成員有高度的權限存取 Salesforce 時銷售小組成員的有限存取權。 在許多情況下，主要母體的資訊工作者有限制存取至應用程式。 很這些規則的例外狀況。 通常是行銷或銷售領導小組若要授與使用者存取權，或變更其角色亦這些一般的規則的 prerogative。

Azure AD，應用程式，例如 Salesforce 就能預先設定的單一登入 (SSO) 與自動化佈建。 設定應用程式之後, 系統管理員可以取得建立及指定適當的群組一次性動作。 在此範例中，以系統管理員可以執行下列作業︰

- [動態群組](active-directory-accessmanagement-manage-groups.md)可定義自動代表所有成員的行銷與銷售團隊使用屬性，例如部門或角色︰

    - 行銷群組的所有成員會都指派給 Salesforce 中 「 行銷 」 的角色

    - 所有的群組會指派給 Salesforce 「 銷售 」 角色的銷售團隊成員。 進一步精簡搜尋可能使用多個代表指派給不同的 Salesforce 角色的地區銷售團隊的群組。

- 若要啟用的例外機制，您可以建立自助群組，每一個角色。 例如，「 Salesforce 行銷例外狀況 」 可以建立群組自助群組。 群組可以指派給 Salesforce 行銷角色和行銷領導小組可以進行的擁有者。 行銷領導小組成員無法新增或移除使用者、 設定加入原則，或甚至核准或拒絕個別使用者的邀請加入。 支援此功能，透過資訊工作者適當的體驗，不需要特殊的訓練課程的擁有者] 或 [成員。

在此情況下，指定的所有使用者會自動都提供給 Salesforce，加入至想要在 Salesforce 更新其角色指派不同的群組。 使用者將能夠探索及存取 Salesforce 透過 Microsoft 應用程式存取面板，Office web 用戶端，或甚至瀏覽至其組織 Salesforce 登入頁面。 系統管理員能輕鬆地檢視使用情況和工作分派的狀態，使用 Azure AD 報告。

系統管理員可以採用[Azure AD 條件存取](active-directory-conditional-access.md)設定的特定角色的存取原則。 是否允許存取外部的公司的環境及更多因素驗證或裝置需求，以獲得存取在各種情況下，可以包含下列原則。

## <a name="how-can-i-get-started"></a>如何開始？

首先，如果您已不使用 Azure AD，而且您是 IT 管理員︰

 - [試試看 ！](https://azure.microsoft.com/trial/get-started-active-directory/) -您可以註冊免費 30 天的試用版今天，並將您的第一個雲端解決方案部署中底下 5 分鐘使用此連結

啟用帳戶共用的 azure AD 功能包括︰

- [群組工作分派](active-directory-accessmanagement-self-service-group-management.md)
- Azure AD 新增應用程式
- 工作分派快速入門
- 應用程式工作分派的常見問題集
- [儀表板報表方式應用程式使用方式](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>何處可以進一步？

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [保護與條件的 access 應用程式](active-directory-conditional-access.md)
- [管理/SSAA 自助] 群組](active-directory-accessmanagement-self-service-group-management.md)
