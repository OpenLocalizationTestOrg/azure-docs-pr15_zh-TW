<properties
   pageTitle="新增與管理多個 Azure Active Directory 目錄 |Microsoft Azure"
   description="指示及新增與管理您的 Azure Active Directory 目錄、 說明完全獨立的資源為目錄的最佳作法"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>新增與管理多個 Azure Active Directory 目錄

Azure Active Directory (Azure AD)，在每個目錄為完全獨立資源︰ 完整功能，以邏輯方式不受影響您管理其他目錄的對等。 目錄之間有不父項與子項關聯性。 此獨立性目錄之間包含獨立性資源、 系統管理獨立性及同步處理獨立性。

##<a name="resource-independence"></a>資源獨立性

如果您建立或刪除一個目錄中的資源，其會造成任何影響與外部使用者，如下所述的部分的例外狀況的另一個目錄中的任何資源上。 如果您使用自訂網域 」 contoso.com 」 和一個目錄時，它不能與其他的目錄。

##<a name="administrative-independence"></a>管理獨立性

如果目錄 「 Contoso 」 的非系統管理員的使用者會建立測試目錄 '測試' 然後︰
- 根據預設，會建立目錄的使用者新增為該新的目錄，外部使用者，並指定該目錄中的全域管理員角色。
- 目錄 「 Contoso 」 的系統管理員擁有目錄 '測試' 沒有直接系統管理權限，除非 '測試 」 的系統管理員，特別是已授與這些權限。 「 Contoso 」 的系統管理員可以控制目錄 '測試 」 的存取權，是否他們控制的使用者帳戶，建立 [測試]。
- 如果您要變更 （新增或移除） 一個目錄中的使用者的管理員角色，變更並不會影響任何管理員角色的使用者可能必須在另一個目錄。

##<a name="synchronization-independence"></a>同步處理獨立性

您可以設定每個 Azure AD 目錄獨立取得從一個執行個體的 [同步處理的資料︰
  - 目錄同步處理 (DirSync) 工具，與單一 AD 樹系同步處理資料。
  - Azure Active Directory 連接器，Forefront 身分識別管理員，才能與一個或多個內部部署樹系，及/或非 Azure AD 資料來源同步處理資料。

##<a name="add-an-azure-ad-directory"></a>新增 Azure AD 目錄

若要新增 Azure AD 目錄 Azure 傳統入口網站中，選取左側的 Azure Active Directory 副檔名，然後點選 [**新增**。

> [AZURE.NOTE]   與其他 Azure 的資源，您的目錄不是 Azure 訂閱的子資源。 如果您取消或允許 Azure 訂閱到期時，您仍然可以存取您的目錄資料使用 PowerShell 的 Azure、 Azure Graph API 或其他介面，例如 Office 365 系統管理中心。 您也可以建立另一個訂閱關聯與目錄。

Azure AD 授權問題及最佳作法的概觀，請參閱[什麼 Azure Active Directory 授權？](active-directory-licensing-what-is.md)。
