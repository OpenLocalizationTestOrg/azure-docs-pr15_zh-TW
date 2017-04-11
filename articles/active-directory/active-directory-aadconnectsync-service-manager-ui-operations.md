<properties
    pageTitle="Azure AD Connect 同步處理︰ 同步處理服務管理員使用者介面 |Microsoft Azure"
    description="Azure AD Connect 瞭解在同步處理服務管理員中的 [作業] 索引標籤。"
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

![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

[作業] 索引標籤會顯示最新的作業的結果。 在此索引標籤是鍵以了解問題進行疑難排解。

## <a name="understand-the-information-visible-in-the-operations-tab"></a>瞭解在 [作業] 索引標籤中顯示的資訊
上半部顯示所有執行長期順序。 根據預設，作業記錄保留資訊過去七天，但可以變更此設定，以[排程器](active-directory-aadconnectsync-feature-scheduler.md)。 您想要尋找不會顯示成功的狀態任何執行。 您可以變更按一下標題來排序。

[**狀態**] 欄是最重要的資訊，並顯示為執行最嚴重的問題。 以下是快速摘要的順序的優先順序調查最常見的狀態 (位置 * 表示幾個可能的錯誤字串)。

狀態 | 註解
--- | ---
停止-* | 執行無法完成。 例如，如果遠端系統已關閉，且無法連絡。
停止錯誤限制 | 有超過 5000 名的錯誤。 由於錯誤大量自動停止執行。
完成-\*-錯誤 | 執行完成，但有錯誤 (不超過 5000)，則應該調查。
完成-\*-警告 | 完成後，執行，但某些資料不在預期的狀態。 如果您有錯誤，則此訊息為通常徵狀。 在您解決錯誤，直到您不應該調查警告。
成功 | 沒有問題。

當您選取列時，下方就會更新以顯示所執行的詳細資料。 以最左邊的底部，您可能需要拒絕**步驟 #**清單。 如果您樹系會以步驟來表示每個網域中有多個網域，只會出現此清單。 [**分割**] 標題下找的網域名稱。 在**同步處理統計資料**，您可以找到所處理的變更數目的詳細資訊。 您可以按一下連結以取得已變更的物件清單。 如果您有錯誤的物件，這些錯誤顯示下**的同步處理錯誤**。

## <a name="troubleshoot-errors-in-operations-tab"></a>疑難排解作業] 索引標籤中的錯誤
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
如果您有錯誤，錯誤與錯誤本身的物件就會是提供的詳細資訊的連結。

首先先按一下錯誤字串 （**同步處理規則-錯誤-函數-觸發**圖片中）。 您會先看到物件的概觀。 若要查看實際的錯誤，請按一下 [**堆疊追蹤**] 按鈕。 這項追蹤提供偵錯錯誤的層級的資訊。

**提示︰**您可以在**來電堆疊資訊**] 方塊中以滑鼠右鍵按一下，選擇 [**全選**]，然後**複製**]。 然後，您可以複製堆疊，並在 「 記事本 」 等您最愛編輯器中，查看錯誤。

- 如果是從**SyncRulesEngine**的錯誤，然後呼叫堆疊資訊第一次有之所有屬性的清單在物件上。 向下捲動，直到您看到標題**狀況 = >**。  
![同步處理服務管理員](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
之後會顯示錯誤。 在上方的圖片，錯誤是從建立自訂同步處理規則 Fabrikam。

如果錯誤本身不提供資訊不足，則若要查看資料本身的時間。 您可以按一下物件識別碼與[追蹤的物件，並透過系統其資料](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)的連結。

## <a name="next-steps"></a>後續步驟
進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
