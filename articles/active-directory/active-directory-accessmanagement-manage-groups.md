<properties
    pageTitle="管理 Azure Active Directory 中的群組 |Microsoft Azure"
    description="如何建立及管理群組，以管理 Azure 使用 Azure Active Directory 的使用者。"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/29/2016"
    ms.author="curtand"/>


# <a name="managing-groups-in-azure-active-directory"></a>Azure Active Directory 中的管理群組

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-groups-create-azure-portal.md)
- [Azure 傳統入口網站](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)


Azure Active Directory (Azure AD) 使用者管理的功能是建立的使用者群組的能力。 您可以使用群組來執行管理工作，例如指派授權] 或 [權限給使用者的數字，一次。 您也可以使用群組指派的存取權限

- 資源，例如目錄中的物件
- 資源外部的目錄，例如 SaaS 應用程式、 Azure 服務、 SharePoint 網站或者內部部署資源

此外，資源擁有者也可以指派存取至 Azure AD 群組擁有人的資源。 這項作業授與存取資源該群組的成員。 然後，群組的擁有者管理群組的成員資格。 實際上，資源擁有者委派給群組的擁有者權限指派給他們的資源的使用者。

## <a name="how-do-i-create-a-group"></a>我要如何建立群組？

根據貴組織已訂閱的服務，您可以建立群組，使用下列其中一項︰
- Azure 傳統入口網站
- Office 365 帳戶入口網站
- Windows Intune 帳戶入口網站

執行 Azure 傳統入口網站中，我們會說明工作。 如需有關如何使用非 Azure 入口網站管理您的 Azure AD 目錄的詳細資訊，請參閱[管理您的 Azure AD 目錄](active-directory-administer.md)。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，選取**Active Directory**，然後選取您的組織的目錄的名稱。

2. 選取 [**群組**] 索引標籤。

3. 選取 [**新增群組**]。

4. 在 [**新增群組**] 視窗中指定的名稱和群組的說明。


## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>如何新增或移除個別使用者的安全性群組中？

**將個別使用者新增至群組**

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，選取**Active Directory**，然後選取您的組織的目錄的名稱。

2. 選取 [**群組**] 索引標籤。

3. 開啟您要新增成員的群組。 開啟選取的群組**成員**] 索引標籤，如果它尚未顯示。

4. 選取 [**新增成員**]。

5. 在 [**新增成員**] 頁面選取使用者或您想要新增為此群組的成員的群組名稱。 請確定此名稱會新增到 [**選取**窗格。


**若要從群組移除個別的使用者**

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，選取**Active Directory**，然後選取您的組織的目錄的名稱。

2. 選取 [**群組**] 索引標籤。

3. 開啟您要移除成員的群組。

4. 選取 [**成員**] 索引標籤，選取您想要移除此群組的成員的名稱，再按一下 [**移除**。

6. 出現提示時確認您想要從群組中移除這個成員。


## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>如何動態管理群組的成員資格？

在中 Azure AD，您可以很容易設定簡單的規則決定哪些使用者所要群組的成員。 簡單的規則就是一個可讓您只是單一的比較。 例如，如果群組指派給 SaaS 應用程式，您可以設定規則來新增使用者職稱為 「 業務代表 」。 此規則然後授與存取權的職稱，在您的目錄中的所有使用者此 SaaS 應用程式。

當使用者變更的任何屬性，系統會評估，查看使用者的屬性變更是否想要觸發任何群組的目錄中的所有動態群組規則新增或移除。 如果使用者滿足群組中的規則，它們會為成員新增至該群組。 不再符合其所屬的群組的規則，它們會從該群組移除成員。

> [AZURE.NOTE] 您可以設定動態安全性群組或 Office 365 群組的成員資格的規則。 群組指派的應用程式目前不支援巢狀的群組的成員資格。
>
> 動態群組的成員資格需要 Azure AD 進階版授權指派給
>
> - 管理規則群組管理員
> - 所有群組的成員

**若要啟用動態群組的成員資格**

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，選取**Active Directory**，然後選取您的組織的目錄的名稱。

2. 選取 [**群組**] 索引標籤，並開啟您要編輯的群組。

3. 選取 [**設定**] 索引標籤，然後設定為 [**是]**的 [**啟用動態成員資格**。

4. 設定簡單單一規則來控制此群組函數如何動態成員資格的群組。 請確定**新增使用者位置**] 選項已選取，然後從清單 （例如部門、 jobTitle 等），按一下使用者的屬性

5. 接下來，請選取 [條件 （不等於等於、 不開始使用、 開始使用，不包含、 包含、 不相符，符合的項目）。

6. 指定給所選的使用者屬性比較值。

若要瞭解如何建立*進階*規則 （規則的最多可以包含多個比較） 的動態群組成員資格，請參閱[使用屬性建立進階的規則](active-directory-accessmanagement-groups-with-advanced-rules.md)。

## <a name="additional-information"></a>其他資訊

這些文章提供 Azure Active Directory 的其他資訊。

* [管理資源的存取權與 Azure Active Directory 群組](active-directory-manage-groups.md)

* [Azure Active Directory cmdlet 設定群組設定](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)

* [Azure Active Directory 是什麼？](active-directory-whatis.md)

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
