<properties
    pageTitle="Azure AD Connect 同步處理︰ 同步處理服務管理員使用者介面 |Microsoft Azure"
    description="Azure AD Connect 瞭解在同步處理服務管理員中的 [連接器] 索引標籤。"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Azure AD Connect 同步處理︰ 同步處理服務管理員

[作業](active-directory-aadconnectsync-service-manager-ui-operations.md) | [連接器](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Metaverse 設計工具](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Metaverse 搜尋](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

[連接器] 索引標籤用來管理同步處理引擎已連線至的所有系統。

## <a name="connector-actions"></a>連接器動作

巨集指令 | 註解
--- | ---
建立 | 不要使用。 連線至其他 AD 樹系，請使用安裝精靈。
屬性 | 使用網域和 OU 篩選。
[刪除](#delete) | 用來刪除連接器空間中的資料，或若要刪除樹系連線。
[設定執行的設定檔](#configure-run-profiles) | 除了網域篩選，不用以下設定。 若要查看已設定執行的設定檔，您可以使用此動作。
執行 | 用來啟動設定檔的一次性執行。
停駐點 | 停止目前執行的設定檔的連接器。
匯出連接器 | 不要使用。
匯入連接器 | 不要使用。
更新連接器 | 不要使用。
重新整理結構描述 | 重新整理的快取的結構描述。 是喜好改用選項安裝精靈中，自的也更新同步處理規則。
[搜尋連接器空間](#search-connector-space) | 用來尋找物件及追蹤[的物件，並透過系統其資料](#follow-an-object-and-its-data-through-the-system)。

### <a name="delete"></a>刪除
[刪除] 動作用於兩個不同的項目。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

**刪除只連接器空間**的選項會移除所有資料，但保留設定。

資料和設定，則會移除**刪除連接器和連接器空間**的選項。 當您不想再連線至樹系時，會使用這個選項。

這兩個選項同步處理所有的物件，然後更新 metaverse 物件。 此動作是長時間執行的作業。

### <a name="configure-run-profiles"></a>設定執行的設定檔
這個選項可讓您查看執行設定連接器的設定檔。

![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>搜尋連接器空間
搜尋連接器空間動作很有用尋找物件及資料問題進行疑難排解。

![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

請先選取**範圍**。 您可以搜尋根據的資料 （RDN，DN，錨點] 子目錄），或狀態的物件 （所有其他選項）。  
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
如果您，例如子樹狀目錄中搜尋，您會收到一個 OU 中的所有物件。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)此格線中，您可以選取物件、 選取**屬性**和[追蹤其](#follow-an-object-and-its-data-through-the-system)來源連接器空間，透過 metaverse，以及目標連接器空間。

## <a name="follow-an-object-and-its-data-through-the-system"></a>請遵循的物件，並透過系統及其資料
當您在疑難排解資料的問題時，遵循 metaverse，從來源連接器空間，物件並目標連接器空間都索引鍵的程序，以瞭解為什麼資料沒有預期的值。

### <a name="connector-space-object-properties"></a>連接器空間物件屬性
**匯入**  
當您開啟 cs 物件時，請在頂端會數個索引標籤。 [**匯入**] 索引標籤會顯示的分段匯入資料。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png)**舊的值**會顯示目前儲存的內容在系統和**新值**什麼已從來源系統接收尚未套用。 在此情況下，由於同步處理錯誤，就無法套用變更。

**錯誤**  
錯誤頁面只會顯示物件問題時的選項。 如需有關如何[疑難排解同步處理錯誤](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab)的 [作業] 頁面上，請參閱詳細資料。

**歷程**  
[歷程] 索引標籤會顯示如何相關 metaverse 物件連接器空間物件。 您可以查看連接器上次匯入變更連線的系統，哪些規則套用至填入斷的資料。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png)在 [**動作**] 欄中，您可以看到一個**輸入**同步處理規則與**佈建**的動作。 表示此連接器空間物件為簡報，時 metaverse 物件仍然。 如果同步處理規則清單，請改為顯示方向**輸出****佈建**與同步處理規則，則表示刪除 metaverse 物件時，就會刪除此物件。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png)您也可以查看**PasswordSync**欄中的輸入的連接器空間可以參與變更密碼，因為一個同步處理規則有**，則為 True**的值。 此密碼然後傳送給 Azure AD 透過輸出規則。

歷程] 索引標籤上，您可以前往 metaverse [Metaverse 物件屬性](#metaverse-object-properties)] 即可。

在所有索引標籤底部的兩個按鈕︰**預覽**及**記錄**。

**預覽**  
預覽頁面用來同步處理單一物件。 如果您正在進行疑難排解的一些客戶同步處理規則，並想要在單一物件上查看變更的效果，很有用。 您可以選取**完整的同步處理**及**Delta 同步處理**。 您也可以選取之間**產生預覽**其中只保存在記憶體中的變更和**認可預覽**，所有的階段會變更為目標連接器空格。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png)的物件，以及哪些規則套用的特定屬性流程，您可以檢查。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**記錄檔**  
[記錄] 頁面用來查看 [密碼同步處理狀態] 和 [歷程記錄，請參閱[疑難排解密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)，如需詳細資訊。

### <a name="metaverse-object-properties"></a>屬性流程規則屬性
**屬性**  
在 [屬性] 索引標籤中，您可以看到的值，哪些連接器提供其。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**連接器**  
[連接器] 索引標籤會顯示所有的連接器空格的物件的表示。
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png)此索引標籤也可讓您瀏覽至[連接器空間物件](#connector-space-object-properties)。

## <a name="next-steps"></a>後續步驟
進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
