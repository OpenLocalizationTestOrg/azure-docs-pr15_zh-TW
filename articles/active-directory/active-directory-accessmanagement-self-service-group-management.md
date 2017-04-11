<properties
    pageTitle="設定來管理自我服務應用程式存取的 Azure Active Directory |Microsoft Azure"
    description="自助群組管理可讓使用者建立及管理安全性群組或 Azure Active Directory 中的 Office 365 群組，並提供使用者可能會要求安全性群組或 Office 365 群組的成員資格"
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>Azure Active Directory 設定自助群組管理

自助群組管理可讓使用者建立及管理安全性群組或 Azure Active Directory (Azure AD) 中的 Office 365 群組。 安全性群組或 Office 365 群組的成員資格，也可以要求使用者，然後群組的擁有者可以核准或拒絕其成員資格。 如此一來，每日的控制權群組成員資格可以委派給瞭解的成員資格的商務內容的人員。 可使用自助群組管理功能僅適用於安全性群組和 Office 365 群組，而不是用於郵件功能的安全性群組群組或通訊群組清單。

自助群組管理目前包含兩個基本案例︰ 委派的管理群組及自助群組管理。

- **群組管理作業委派**
  範例是負責管理存取 SaaS 應用程式使用公司的系統管理員。 管理這些存取權限變得麻煩，因此此系統管理員要求商務擁有者，若要建立新的群組。 系統管理員會指派的應用程式，[新增] 群組中，以存取，並將已存取應用程式的所有人都新增至群組。 商務擁有者，然後可以新增更多使用者，且這些使用者會自動佈建到應用程式。 商務擁有者不需要的管理員，才能管理使用者的存取，請等候。 如果系統管理員，將相同的權限授與不同的商務] 群組中的 [管理員]，然後該人員也可以管理自己的使用者存取。 商務擁有人或管理員都不可以檢視或管理其他人的使用者。 系統管理員仍可看到有權存取的應用程式及封鎖的存取權限如有需要的所有使用者。

- **自助群組管理**
  這種情況的範例是兩個使用者同時設定獨立的 SharePoint Online 網站。 他們想要授與其他人小組存取其網站。 若要這麼做，他們可以建立一個群組中 Azure AD，和在 SharePoint Online 中每個選取該群組，以提供存取其網站。 當其他人想要的存取時，請他們要求從 Access] 面板中，並核准後，存取這兩個 SharePoint Online 網站會自動。 更新版本，其中會決定存取網站的所有人員也應該都取得存取特定的 SaaS 應用程式。 SaaS 應用程式的系統管理員可以新增至 SharePoint Online 網站的應用程式的存取權限。 之後，請於任何要求取得核准的兩個 SharePoint Online 網站，也可以這個 SaaS 應用程式可讓存取。

## <a name="making-a-group-available-for-end-user-self-service"></a>群組可用讓使用者的自助

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，開啟您 Azure AD 的目錄。

2. 在 [**設定**] 索引標籤中，設定**委派群組管理**為啟用。

3. 設定為 [已啟用的**使用者可以建立安全性群組**，或者**使用者也可以建立 Office 群組**。

啟用**的使用者可以建立安全性群組**時，您的目錄中的所有使用者都可以建立新的安全性群組及將成員新增至這些群組。 這些新群組就會顯示在所有其他使用者存取面板中。 在群組原則設定允許，如果其他使用者可以建立加入這些群組的要求。 如果已停用**使用者可以建立安全性群組**，使用者就會無法建立群組，並無法變更現有群組的擁有者。 不過，他們仍管理這些群組的成員資格及核准要求其他使用者加入的群組。

您也可以使用**可以使用自助安全性群組的使用者**，以獲得更細緻的存取控制，透過自助群組管理您的使用者。 啟用**使用者可以建立群組**時，您的目錄中的所有使用者都可以建立新群組及將成員新增至這些群組。 藉由也某些設定**可以使用自助安全性群組的使用者**，您會限制群組管理] 以只有有限的一組使用者。 當此參數設為部分時，您必須新增使用者群組 SSGMSecurityGroupsUsers 他們可以建立新的群組，並將成員新增至其之前。 所有設定**可以使用自助安全性群組的使用者**，您可以啟用所有使用者在您的目錄，來建立新群組。

您也可以使用的**群組，可以使用自助安全性群組的**方塊來指定的群組的成員可以使用自助的自訂名稱。

## <a name="additional-information"></a>其他資訊

這些文章提供 Azure Active Directory 的其他資訊。

* [管理資源的存取權與 Azure Active Directory 群組](active-directory-manage-groups.md)

* [Azure Active Directory cmdlet 設定群組設定](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)

* [Azure Active Directory 是什麼？](active-directory-whatis.md)

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
