<properties
    pageTitle="將使用者指派至 Azure Active Directory 中的自訂網域 |Microsoft Azure"
    description="如何填入 [使用者帳戶 Azure Active Directory 中的自訂網域。"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="assign-users-to-a-custom-domain"></a>將使用者指派至自訂網域

您已加入 Azure Active Directory 中的自訂網域之後，您必須新增此網域的使用者帳戶，讓您可以開始進行驗證。

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>從您的公司網路上的目錄同步處理中的使用者

如果您有已設定連線之間您的內部部署 Active Directory 與 Azure Active Directory，同步處理可以填入帳戶。 如需有關如何與您內部部署的 Active Directory 同步處理 Azure Active Directory 的詳細資訊，請參閱[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。

## <a name="users-added-and-managed-in-the-cloud"></a>新增和雲端中管理使用者

若要變更現有的使用者帳戶的網域︰

1.  開啟使用的是全域管理員和使用者管理員帳戶 Azure 傳統入口網站

2.  開啟您的目錄。

3.  選取 [**使用者**] 索引標籤。

4.  從清單中選取的使用者。

5.  變更使用者的網域，然後選取 [**儲存**]。

這也完成使用[Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)或[圖形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)。

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>建立新使用者時，選取 [將自訂網域名稱

1.  開啟使用的是全域管理員和使用者管理員帳戶 Azure 傳統入口網站

2.  開啟您的目錄。

3.  選取 [**使用者**] 索引標籤。

4.  命令列中，選取 [**新增]**。

5.  當您新增的使用者名稱時，請從 [網域] 清單中選擇自訂網域。

6.  選取 [**儲存**]。

## <a name="next-steps"></a>後續步驟

-   [使用自訂網域名稱，以簡化您的使用者的登入體驗](active-directory-add-domain.md)

-   [管理自訂網域名稱](active-directory-add-manage-domain-names.md)

-   [瞭解網域管理概念 Azure AD 中](active-directory-add-domain-concepts.md)
