
<properties 
    pageTitle="如何使用 Office 365 使用者帳戶的 Azure RemoteApp |Microsoft Azure"
    description="瞭解如何使用我的 Office 365 使用者帳戶的 Azure RemoteApp"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>如何使用 Office 365 使用者帳戶的 Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

如果您有 Office 365 訂閱可以儲存您的使用者名稱和密碼，用來存取 Office 365 服務的 Azure Active Directory。 例如，當使用者啟動 Office 365 專業增強版時它們驗證針對要檢查授權 Azure AD。 大部分的客戶想要使用 Azure RemoteApp 相同的目錄。

如果您部署 Azure RemoteApp 您很可能會使用不同的 Azure AD 與相關聯的 Azure 訂閱。 若要使用您的 Office 365 目錄，您必須將 Azure 訂閱移到該目錄。

如何部署 Office 365 用戶端應用程式的資訊，請參閱[如何使用 Azure RemoteApp 與 Office 365 訂閱](remoteapp-officesubscription.md)。
 
## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>階段 1︰ 註冊您的免費 Office 365 Azure Active Directory 訂閱
如果您使用的 Azure 傳統入口網站，使用[註冊免費的 Azure Active Directory 訂閱](https://technet.microsoft.com/library/dn832618.aspx)中的步驟，以取得您 Azure AD 透過 Azure 管理入口網站的管理權限。 此程序的結果為您應該可以登入 Azure 入口網站，並查看您的目錄 – 此時您就不會看到更之後您要使用的 Azure RemoteApp 完整 Azure 訂閱位於不同的目錄。

記住的名稱與您在此步驟中建立的系統管理員帳戶的密碼，他們會需要階段 2。

如果您使用的 Azure 入口網站，請參閱[如何註冊和啟動使用 Office 365 入口網站免費 Azure Active Directory](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/)。

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>階段 2︰ 變更 Azure AD Azure 訂閱相關聯。
我們會從目前的目錄中變更您 Azure 的訂閱，我們階段 1 中使用 Office 365 目錄。

依照指示[變更租用戶的 Azure Active Directory Azure RemoteApp 中](remoteapp-changetenant.md)所述。 注意下列步驟︰

- 步驟 1︰ 如果您已經部署 Azure RemoteApp 其在此訂閱，請確定您移除所有 Azure AD 使用者帳戶任何其集合首先，嘗試其他項目之前。 或者，您可以考慮刪除任何現有的集合。
- 步驟 2︰ 這是重要的步驟。 您需要使用 Microsoft 帳戶 (例如@outlook.com)服務管理員身分訂閱; 這是因為我們無法將所有的使用者帳戶，從現有的 Azure AD 附加至訂閱 – 如果，我們無法將它移到不同的 Azure AD。
- 步驟 4︰ 新增現有的目錄，系統會要求您使用的系統管理員帳戶登入，該目錄。 請務必使用階段 1 的系統管理員帳戶。
- 步驟 5︰ 變更您的 Office 365 目錄訂閱的父目錄。 最後的結果應該是，在 [設定]-> [訂閱您的訂閱清單的 Office 365 目錄。 
![變更訂閱的父目錄](./media/remoteapp-o365user/settings.png)
 

此時 Azure RemoteApp 訂閱已與您的 Office 365 Azure AD; 相關聯您可以使用現有的 Office 365 使用者帳戶與 Azure RemoteApp ！




