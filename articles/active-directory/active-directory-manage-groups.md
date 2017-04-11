<properties
    pageTitle="管理資源的存取權與 Azure Active Directory 群組 |Microsoft Azure"
    description="如何使用 Azure Active Directory 中的群組，若要管理內部部署和雲端應用程式與資源的使用者存取權。"
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


# <a name="managing-access-to-resources-with-azure-active-directory-groups"></a>管理資源的存取權與 Azure Active Directory 群組

Azure Active Directory (Azure AD) 是全面涵蓋所有內容身分識別和存取管理解決方案，提供強大的管理內部部署和雲端應用程式和資源，包括 Office 365 和非 Microsoft SaaS 全球等的 Microsoft 線上服務的存取權的功能。 本文提供的概觀，但如果您想要立即開始使用 Azure AD 群組權限，請依照下列[Azure AD 安全性管理] 群組](active-directory-accessmanagement-manage-groups.md)中的指示進行。 如果您想要瞭解如何使用 PowerShell 管理 Azure Active directory 中的群組，您可以瞭解更多的[Azure Active Directory 群組管理的預覽 cmdlet](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)。


> [AZURE.NOTE] 若要使用 Azure Active Directory，您需要 Azure 帳戶。 如果您沒有帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。


Azure AD 中, 主要功能是管理資源的存取權的能力。 下列資源可以是目錄，與管理物件的目錄或資源的外部的目錄，例如 SaaS 應用程式、 Azure 服務，以及 SharePoint 網站或內部部署資源中的角色的權限的大小寫的一部分。 有四種使用者可以指派資源的存取權限的方法︰


1. 直接工作分派

    使用者可以直接指派給資源的資源的擁有者。

2. 群組成員資格

    群組可以被指派到資源擁有者的資源，這樣做，授與存取資源該群組的成員。 群組的擁有者可以再管理群組的成員資格。 實際上，資源擁有者委派的權限指派給群組的擁有者其資源的使用者。

3. 規則為基礎

    資源擁有者可以使用規則來 express 使用者應存取分派給某個資源。 規則的結果取決於特定使用者的規則和其值中所使用的屬性及如此一來，資源擁有者有效委派管理規則中使用屬性的存取權的授權來源其資源的權限。 資源擁有者，仍會管理本身的規則，並決定哪些屬性和值提供其資源存取權。

4. 外部授權單位

    存取資源被衍生自外部來源。例如，從一個可靠的來源，例如內部部署目錄或 SaaS 應用程式，例如 WorkDay 同步處理的群組。 資源擁有人] 群組中提供的存取權的資源，並外部來源管理群組的成員。

  ![Access 管理圖表概觀](./media/active-directory-access-management-groups/access-management-overview.png)


## <a name="watch-a-video-that-explains-access-management"></a>觀看影片，說明存取管理

您可以觀看說明更多關於此簡短視訊︰

**動態群組的成員資格的 azure AD︰ 簡介**

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## <a name="how-does-access-management-in-azure-active-directory-work"></a>如何沒有存取 Azure Active Directory 中的管理？
Azure AD 的中央位置存取管理解決方案是安全性群組。 使用管理資源的存取權的安全性群組是已知的範例，可讓的彈性且容易瞭解方式提供資源的存取權的使用者想要的群組。 資源擁有者 （或目錄的系統管理員），可以指定要提供特定資源的存取權限他們擁有的群組。 群組的成員提供存取，請與資源擁有者可以委派管理的其他人，例如部門經理或技術服務管理員群組成員清單的權限。

![Azure Active Directory 存取管理圖表](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

群組的擁有者，也可以將該群組適用於自助要求。 如此一來，使用者可以搜尋和尋找群組，以加入的要求有效搜尋透過來存取您可管理的資源群組的權限。 群組的擁有者可以設定] 群組中，以便加入要求自動核准或群組的擁有者的核准。 當使用者要求加入群組，加入要求已轉寄給群組的擁有者。 如果擁有者的其中一個核准要求，要求使用者會收到通知，使用者加入群組。 如果其中一個擁有者拒絕要求，要求使用者會收到通知，但不是加入群組。


## <a name="getting-started-with-access-management"></a>Access management 快速入門
準備好開始使用？ 您應該嘗試一些 Azure AD 群組，您可以執行的基本工作。 使用這些功能提供您組織中的不同資源為不同群組的人員的特殊的存取權。 以下是基本的第一個步驟的清單。

* [建立簡單的規則，以設定動態群組的成員資格](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)

* [管理存取 SaaS 的應用程式中使用群組](active-directory-accessmanagement-group-saasapps.md)

* [群組可用讓使用者的自助](active-directory-accessmanagement-self-service-group-management.md)

* [同步處理 Azure 使用 Azure AD Connect 內部部署群組](active-directory-aadconnect.md)

* [管理群組的擁有者](active-directory-accessmanagement-managing-group-owners.md)


## <a name="next-steps-for-access-management"></a>接下來的步驟，來管理存取
現在，您必須瞭解存取管理的基本概念，以下是一些其他的進階的功能可用 Azure Active Directory 中管理您的應用程式和資源的存取權。

* [若要建立進階的規則使用屬性](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Azure AD 中管理安全性群組](active-directory-accessmanagement-manage-groups.md)

* [Azure AD 中設定專用的群組](active-directory-accessmanagement-dedicated-groups.md)

* [群組圖形 API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

* [Azure Active Directory cmdlet 設定群組設定](active-directory-accessmanagement-groups-settings-cmdlets.md)
