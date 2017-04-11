<properties
    pageTitle="專用的 Azure Active Directory 中的群組 |Microsoft Azure"
    description="在 Azure Active Directory，以及如何建立這些工作如何專屬群組的概觀。"
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
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="dedicated-groups-in-azure-active-directory"></a>Azure Active Directory 中的專用的群組

Azure Active Directory (Azure AD)，在專用的群組功能會自動建立並填入 Azure AD 預先定義的群組的成員資格。 專屬群組的成員無法新增或移除使用 Azure 傳統入口網站、 Windows PowerShell cmdlet，或以程式設計方式。

>[AZURE.NOTE] 專用的群組會需要的 Azure AD 進階版授權指派給
>- 管理規則群組管理員
>- 所有使用者都是群組的成員，已選取的規則

**若要啟用專用的群組**

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，選取 [ **Active Directory**中，然後再開啟您組織的目錄。

2. 選取 [**群組**] 索引標籤，然後開啟 [您要編輯的群組。

3. 選取 [**設定**] 索引標籤，然後設定為 [**是]**的 [**啟用專用的群組**。

一旦啟用專用的群組切換設定為 [**是**，您可以進一步啟用自動設定建立所有使用者專屬的群組的目錄**啟用 」 的所有使用者 」 群組**切換為 [**是]**。 您再也可以編輯此專屬群組的名稱來輸入**」 的所有使用者 」 的顯示名稱] 群組**欄位。

[所有使用者群組可用於將相同的權限指派給您的目錄中的所有使用者。 例如，您可以授與所有使用者在您的目錄存取 SaaS 應用程式中指派給此應用程式的 [所有使用者專屬群組的存取權。

專用的所有使用者群組包含在目錄中，包括來賓與外部使用者的所有使用者。 如果您需要群組的排除外部使用者，然後您可以藉由建立群組屬性型動態規則，例如下列完成︰

                (user.userPrincipalName -notContains "#EXT#@")

使用排除所有來賓一個群組，例如下列規則︰

                (user.userType -ne "Guest")

若要瞭解如何建立*進階*規則 （規則的最多可以包含多個比較） 的動態群組成員資格，請參閱[使用屬性建立進階的規則](active-directory-accessmanagement-groups-with-advanced-rules.md)。


這些文章提供 Azure Active Directory 的其他資訊。

* [管理資源的存取權與 Azure Active Directory 群組](active-directory-manage-groups.md)
* [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
* [Azure Active Directory 是什麼？](active-directory-whatis.md)
* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
