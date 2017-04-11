<properties
    pageTitle="Azure AD 網域服務︰ 建立 AAD DC 管理員群組 |Microsoft Azure"
    description="快速入門 Azure Active Directory 網域服務"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="get-started-with-azure-ad-domain-services"></a>開始使用 Azure AD 網域服務

本文將引導啟用 Azure AD 租用戶的 Azure AD 網域服務所需的設定工作。

## <a name="task-1-create-the-aad-dc-administrators-group"></a>任務 1︰ 建立 ' AAD DC 系統管理員] 群組
第一個工作是在您的 Azure Active Directory 租用戶中建立系統管理群組。 此特殊的系統管理群組稱為**AAD DC 系統管理員**。 此群組的成員權限管理的網域為 Azure AD 網域服務受管理網域的電腦上。 加入網域在電腦上，此群組會新增至 [管理員] 群組。 此外，此群組的成員可以使用遠端桌面連線遠端網域的電腦。  

> [AZURE.NOTE] 您沒有建立使用 Azure AD 網域服務的受管理網域的網域管理員或企業系統管理員權限。 在受管理的網域，這些權限服務會保留，不會提供租用戶中的使用者]。 不過，您可以使用特殊的系統管理員群組建立在此設定任務中，執行某些權限的作業。 這些作業包括電腦加入網域，屬於管理員群組的網域在電腦上，設定群組原則等。

在此設定任務中，您可以建立系統管理群組，並在目錄中的一或多位使用者新增至群組。 執行下列步驟，以建立 Azure AD 網域服務的系統管理群組︰

1. 瀏覽至**Azure 傳統入口網站**([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. 選取在左窗格的 [ **Active Directory**節點。

3. 選取您要啟用 Azure AD 網域服務 Azure AD 租用戶 （目錄）。 您只可以建立一個網域的每個 Azure AD 目錄。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. 按一下 [**群組**] 索引標籤。

5. 若要新增至您的 Azure AD 租用戶的群組，請按一下 [從底部的頁面] 工作窗格的 [**新增群組**]。

    ![新增群組] 按鈕](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. 建立名為**AAD DC 系統管理員**的群組。 **安全性**設定**群組類型**。

    > [AZURE.WARNING] 若要啟用存取 Azure AD 網域服務中的受管理的網域，此確切的名稱建立群組。

    ![建立系統管理員群組](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. 新增此群組的描述，讓其他人瞭解此群組用於授與 Azure AD 網域服務中的系統管理員權限。

8. 已建立群組之後，請按一下 [群組的名稱，請參閱此群組的屬性。 若要新增使用者為此群組的成員，請按一下 [底部] 面板上的 [**新增成員**] 按鈕。

    ![新增群組成員] 按鈕](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. 在 [**新增成員**] 對話方塊中選取使用者，應該是此群組的成員完畢後，請選取核取方塊。

    ![將使用者新增至管理員群組](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>任務 2︰ 建立或選取 Azure 虛擬網路
[建立或選取 Azure 虛擬網路](active-directory-ds-getting-started-vnet.md)是下一個設定工作。
