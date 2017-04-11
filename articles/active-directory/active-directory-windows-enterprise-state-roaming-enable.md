<properties
    pageTitle="啟用企業狀態漫遊 Azure Active Directory 中 |Microsoft Azure"
    description="常見問題集在 Windows 裝置中的企業狀態漫遊設定。 企業狀態漫遊提供使用者在他們的 Windows 裝置上統一的體驗，並減少設定新的裝置所需的時間。"
    services="active-directory"
    keywords="如何啟用企業狀態漫遊，windows 雲端漫遊企業狀態"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>



# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Azure Active Directory 中漫遊啟用企業狀態

進階版 Azure Active Directory (Azure AD) 訂閱的組織使用企業狀態漫遊。 如需如何取得 Azure AD 訂閱的詳細資訊，請參閱[Azure AD 產品頁面](https://azure.microsoft.com/services/active-directory)。

當您啟用企業狀態漫遊時，您的組織會自動授與免費、 限制使用訂閱的授權 Azure 版權管理。 這個免費訂閱只限加密和解密企業版設定及企業狀態漫遊服務; 同步處理應用程式資料您必須使用完整功能的 Azure 版權管理的付費的訂閱。

取得之後 Azure AD 付費訂閱，請遵循下列步驟以啟用企業狀態漫遊︰

1. Azure 傳統入口網站登入。
2. 在左側，選取**ACTIVE DIRECTORY**，然後選取您要啟用企業狀態漫遊的目錄。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. 移至 [**設定**] 索引標籤上的頂端。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.  頁面上向下捲動並選取**[使用者設定與企業應用程式資料可能會同步處理**，然後按一下 [**儲存**。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Windows 10 裝置漫遊設定與企業狀態漫遊服務，使用的裝置必須驗證使用 Azure AD 身分識別。 Azure AD 加入的裝置，使用者的主要登入是 Azure AD 身分識別，因此不不需要任何其他設定。 若為使用傳統的內部部署的 Active Directory 的裝置，IT 管理員必須[連接至 Windows 10 體驗 Azure AD 網域的裝置](active-directory-azureadjoin-devices-group-policy.md)。

## <a name="sync-data-storage"></a>同步處理資料儲存區
企業狀態漫遊資料裝載於一或多個[Azure 的地區](https://azure.microsoft.com/regions/ )設定中的 Azure Active Directory 執行個體的國家/地區值最適合對齊。 企業狀態漫遊資料分割根據三個主要的地理區域︰ 北美地區、 EMEA 及 APAC。 企業狀態漫遊資料的租用戶本機所在的地理區域中，並不會跨區域複寫。  例如客戶的國家/地區值會設定為 EMEA 國家/地區的 「 法國 」 或者 「 幾內亞 」 必須裝載於其中一種或 Azure 區域歐洲內的資料。  其國家/地區中設定的值至北美地區的國家/地區的其中一個 Azure AD 等 「 美國 」 或 「 加拿大 」 必須裝載於一或多個 Azure 區域內美國其資料的客戶。  其國家/地區中設定的值至其中一個 APAC 國家/地區的 Azure AD 等 「 澳大利亞 」 或 「 紐西蘭 」 有裝載於一或多個 Azure 區域內亞洲其資料的客戶。  南美洲國家/地區和時區資料會裝載在美國內的一個或多個 Azure 區域內。  國家/地區值設為 Azure AD 目錄建立程序的一部分，而且無法後續修改。 

如果您需要更多詳細資料上資料的儲存位置，請檔案與[Azure 支援](https://azure.microsoft.com/support/options/)票證。

## <a name="manage-enterprise-state-roaming"></a>管理企業狀態漫遊
Azure AD 全域管理員可以啟用及停用 [Azure 傳統入口網站中的 [企業狀態漫遊。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

全域管理員可以限制特定安全性群組的設定同步處理。

全域管理員也可以選取特定使用者 Active Directory 執行個體**的使用者**清單中，按一下 [**裝置**] 索引標籤上，選取檢視**裝置同步處理設定與企業應用程式的資料**檢視的每位使用者的裝置同步處理狀態報表。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

##<a name="data-retention"></a>資料保留資料
同步處理到透過企業狀態漫遊 Azure 資料將無限期保留，除非執行手動刪除作業或問題資料判斷為過期。 

**明確刪除︰**Azure 管理員會刪除使用者或目錄或系統管理員明確要求資料會被刪除時，會刪除資料。

- **刪除使用者**︰ Azure AD 中刪除使用者時，漫遊資料的使用者帳戶會標示為刪除，並會刪除 90 至 180 天之間。 
- **目錄刪除**︰ 刪除整個目錄中 Azure AD 是即時運算。 設定的所有資料與相關都聯的目錄會標示為刪除會刪除 90 至 180 天之間。 
- **在邀請中刪除**︰ 如果 Azure AD 管理員想要手動刪除特定使用者的資料或設定的資料時，管理員可以檔案與[Azure 支援](https://azure.microsoft.com/support/)票證。 

**過期資料刪除**︰ 一年 （「 保留期間 」） 會被視為過時，可能會被刪除 azure 尚未存取的資料。 保留期間會有所變更，但不是會小於 90 天。 資料失效可能是一組特定的 Windows/應用程式設定] 或 [所有使用者的設定。 例如︰
 
- 如果沒有任何裝置存取特定設定集合 （例如，從裝置移除應用程式或設定] 群組，例如 「 佈景主題 」 會停用所有使用者的裝置），然後該集合會變成保留時間後過期，而且會遭到刪除。 
- 如果使用者已關閉所有是在自己的裝置上的設定同步處理，然後設定資料的存取，並為該使用者的所有設定資料就會失效保留期間之後可能會被刪除。 
- 如果 Azure AD 目錄管理員關閉企業狀態漫遊針對整個目錄，然後所有使用者的目錄會停止同步處理設定]，並為所有使用者的所有設定資料就會失效保留期間之後可能會被刪除。 

**刪除資料修復**︰ 資料保留原則是無法設定。 一旦將會永久刪除資料，它會無法復原。 不過，很重要的設定資料從 Azure 使用者裝置將只會刪除。 如果任何裝置稍後再重新連線至企業狀態漫遊服務，設定一次會同步處理並儲存在 Azure。


## <a name="related-topics"></a>相關的主題
- [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
- [設定和資料漫遊常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md)
- [群組原則] 和 [MDM 設定同步處理設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [在 Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
