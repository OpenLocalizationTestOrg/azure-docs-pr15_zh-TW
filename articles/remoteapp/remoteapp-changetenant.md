
<properties
    pageTitle="變更租用戶的 Azure Active Directory 中 Azure RemoteApp |Microsoft Azure"
    description="瞭解如何變更 Azure RemoteApp 相關聯的 Azure Active Directory 租用戶"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>變更租用戶的 Azure Active Directory 中 Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 使用 Azure Active Directory (Azure AD)，以允許使用者存取權。 您可以使用中 Azure RemoteApp 只 Azure AD 租用戶是 Azure 訂閱相關聯的項目。 您可以在入口網站中的 [**設定**] 頁面上檢視相關聯的訂閱。 查看在 [**訂閱**] 索引標籤上的 [**目錄**] 欄。

> [AZURE.NOTE] 順利完成這項變更，第一次移除所有使用者從所有 Azure RemoteApp 集合現有的 Azure Active Directory 租用戶。 若要這麼做，請移至 [Azure 入口網站**Azure RemoteApp** ] 索引標籤，請開啟每個 Azure RemoteApp 集合。 移至 [**使用者**] 索引標籤，然後移除屬於您目前的 Azure Active Directory 租用戶的使用者。 對所有現有的 Azure RemoteApp 集合。 不這麼一來，您將無法建立或更新集合。

如果您想要使用不同的租用戶，使用這些步驟來變更您訂閱的關聯︰

1. 在入口網站中移除任何 Azure AD 使用者至您已指定存取 Azure RemoteApp 集合。 （請參閱上述的步驟記事如何執行此動作）。


2. 設定 Microsoft 帳戶 （之前稱為 Live ID） 服務系統管理員的身分。 （不知道是否您已是服務管理員？ 您可以找出按一下 [**設定]-> [系統管理員**。）現在，以下是您對的所做的變更︰
    1. 按一下右上角中的使用者，然後按一下 [**檢視帳單**。
    2. 按一下 [訂閱]。 在新頁面上，向下捲動，然後按一下 [在右側的 [**編輯訂閱詳細資料**。 （的中間右下方，如果該排序可協助您尋找。）
    3. 輸入應服務管理員使用者的 Microsoft 帳戶

3. 現在，登入入口網站，，然後返回以您在上一個步驟所指定的 Microsoft 帳戶登入。


4. 按一下 [**新增應用程式]-> [服務]-> [作用中的目錄]-> [目錄]-> [自訂建立**。
5. 在 [**目錄**] 底下，選擇 [**使用現有的目錄**]。 我們會讓您立即登入入口網站，因此選擇 [**我已經準備好現在已登出**。
6. 登回身為全域管理員，您想要新增的目錄的入口網站。 (如果您已經未全域管理員，您必須在後的登入，然後登出。)
7. 您會被要求您登入時是否您想要使用現有的 AD 租用戶與您的訂閱。 按一下 [**繼續**]，然後按一下 [**立即登出**。
5. 再次登入，並返回**設定]-> [訂閱]**。 選取您的訂閱，然後按一下 [**編輯目錄**。 選取您想要使用的 Azure AD 租用戶。



您可以立即使用新的 Azure AD 租用戶來控制 Azure 訂閱的存取權和 Azure RemoteApp 設定使用者存取權。
