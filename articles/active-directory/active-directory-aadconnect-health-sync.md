
<properties
    pageTitle="使用同步處理的 Azure AD 連線狀況 |Microsoft Azure"
    description="這是將討論如何監控 Azure AD Connect 同步處理的 Azure AD 連線健康狀況] 頁面。"
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-for-sync"></a>使用同步處理 Azure AD 連線健康狀況
下列文件是特定監控 Azure AD Connect （同步處理） Azure AD 連線健康情況。  監控 AD FS Azure AD 連線健康情況的詳細資訊，請參閱[使用 Azure AD 連線健康狀況與 AD FS](active-directory-aadconnect-health-adfs.md)。 此外，如監控 Active Directory 網域服務 Azure AD 連線健康情況的詳細資訊請參閱[使用 Azure AD 連線健康狀況與 AD DS](active-directory-aadconnect-health-adds.md)。

![Azure AD Connect 健康狀況的同步處理](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Azure AD 連線狀況的同步處理通知
Azure AD 連線狀況的提醒同步處理的節會提供作用中的通知的清單。 每個通知包含相關資訊、 解決步驟和相關的文件的連結。 選取 [作用中或解決提醒中，您會看到新刀的其他相關資訊，以及您可以採取以解決的通知及連結至其他文件的步驟。 您也可以解決過去的通知上檢視歷程記錄的資料。

選取通知您將提供其他資訊，以及您在步驟，您可以採取以解決提醒和連結至其他文件。

![Azure AD Connect 同步處理錯誤](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>有限的評估的通知
如果 Azure AD Connect 未使用的預設設定 （例如，如果屬性篩選從預設的設定變更為自訂設定），然後 Azure AD 連線狀況代理程式會無法上傳 Azure AD Connect 的相關錯誤事件。

此服務限制通知的評估。 您將會看到橫幅指出此情況下您的服務 Azure 入口網站中。

![Azure AD Connect 狀況的同步處理](./media/active-directory-aadconnect-health-sync/banner.png)

您可以按一下 [設定]，並允許 Azure AD 連線狀況代理程式上傳錯誤記錄檔，所有變更。

![Azure AD Connect 狀況的同步處理](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>同步處理充分
經常管理員想要知道 Azure AD 同步處理變更並進行變更的時間。 此功能讓您輕鬆呈現使用下方圖形︰   

- 同步處理作業的延遲
- 物件變更趨勢

### <a name="sync-latency"></a>同步處理的延遲
此功能會提供圖形的連接器的同步處理作業 （匯入、 匯出等） 的延遲趨勢。  這會提供了解不僅的延遲 （如果您有大量進行的變更，此項會較大） 的作業的快速又簡單的方法，也會偵測異常中可能會需要進一步調查的延遲的方式。

![同步處理延遲](./media/active-directory-aadconnect-health-sync/synclatency02.png)

根據預設，會顯示的 [匯出] 作業 Azure AD 連接器的延遲時間。  若要查看更多作業的連接器，或若要檢視作業來自其他連接器，以滑鼠右鍵按一下圖表，選取 [編輯圖表或按一下 「 編輯延遲圖 」] 按鈕，然後選擇特定的作業與接點]。

### <a name="sync-object-changes"></a>同步處理物件變更
這項功能提供正在變更的數字的圖形化趨勢評估，和匯入至 Azure AD。  現在，嘗試同步處理記錄中收集這項資訊並不容易。  圖表可讓您，不僅更簡單的方法的監控變更您的環境中所發生的數目，也會發生失敗的視覺檢視。

![同步處理延遲](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>物件層級的同步處理錯誤報告 （預覽版本）
此功能會提供有關 Windows Server AD 和 Azure AD 使用 Azure AD Connect 之間同步處理的身分識別資料時，可能發生的同步處理錯誤的報表。

- 在報表所涵蓋錯誤記錄的同步處理用戶端 (Azure AD Connect 1.1.281.0 版本或更新版本)
- 其包含在同步處理引擎上次同步處理作業發生錯誤。 （」 上的 [匯出] Azure AD 連接器。）
- Azure AD 連線狀況代理程式的同步處理必須具有必要的結束點，以包含最新資料分析傳出連線能力。 [需求] 區段](active-directory-aadconnect-health-agent-install.md#Requirements)，如需詳細資訊，請參閱。
- 報表是**每 30 分鐘後更新**使用上傳的同步處理的 Azure AD 連線狀況代理程式的資料。
它會提供下列重要功能

    - 錯誤的分類
    - 物件，每個類別的錯誤清單
    - 在同一個地方錯誤相關的所有資料
    - 並排比較之後的物件，因為衝突錯誤
    - 下載錯誤報告為 CVS （即將推出）

### <a name="categorization-of-errors"></a>錯誤的分類
報表分類下列類別中現有的同步處理錯誤︰

| 類別 | 描述 |
| -------------- | ----------- |
| 重複的屬性 | 錯誤時 Azure AD Connect 嘗試建立或更新的一或多個屬性的重複值中 Azure AD 租用戶，例如 proxyAddresses，UserPrincipalName 中必須是唯一的物件。 |
| 資料不相符 | 柔相符項目無法以符合的物件會造成同步處理錯誤時的錯誤。 |
| 資料驗證失敗 | 無效的資料，例如不支援的字元，在 [要徑的屬性，例如 UserPrincipalName，因為錯誤格式化之前 Azure AD 正在撰寫的驗證失敗的錯誤。|
| 大型的屬性 | 大於允許的大小、 長度或計算的一或多個屬性時的錯誤。|
| 其他 | 所有其他錯誤不符合上述的類別。 根據意見反應，此類別會分割成子類別。

![同步處理錯誤報告摘要](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![同步處理錯誤報告類別](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>物件，每個類別的錯誤清單
切入至每個類別，其中提供物件都有該類別中的錯誤的清單。
![同步處理錯誤報告] 清單中](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>錯誤詳細資料
追蹤資料有每個錯誤的詳細檢視

- *AD 物件*所含的識別碼
- *Azure AD 物件*（視情況） 相關的識別碼
- 錯誤描述，以及如何修正問題
- 相關的文章

![同步處理錯誤的報表詳細資料](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>以 csv 格式下載錯誤報告
即將推出這項功能。 密切更多更新。



## <a name="related-links"></a>相關的連結
* [疑難排解同步處理期間的錯誤](active-directory-aadconnect-troubleshoot-sync-errors.md)
* [重複的屬性恢復功能](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect 健康狀況](active-directory-aadconnect-health.md)
* [Azure AD Connect 狀況代理程式的安裝](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect 狀況作業](active-directory-aadconnect-health-operations.md)
* [使用 Azure AD 連線健康狀況與 AD FS](active-directory-aadconnect-health-adfs.md)
* [使用 Azure AD 連線健康狀況與 AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect 狀況常見問題集](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect 狀況版本歷程記錄](active-directory-aadconnect-health-version-history.md)
