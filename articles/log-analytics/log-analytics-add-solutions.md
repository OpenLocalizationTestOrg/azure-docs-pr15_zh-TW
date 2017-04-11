<properties
    pageTitle="從方案庫中新增記錄分析解決方案 |Microsoft Azure"
    description="記錄檔分析方案是邏輯、 視覺效果和資料擷取的集合規則，提供旋轉若需要處理的特定問題區域周圍的指標。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>從方案庫中新增記錄分析解決方案

記錄檔分析方案是**邏輯****視覺效果**與**資料擷取規則**提供指標旋轉若需要處理的特定問題區域周圍的集合。 此文件清單解決方案支援記錄分析，而且會告訴您如何新增及移除它們使用方案庫。

解決方案允許更深入探討以︰

- 協助調查與解決作業速度更快
- 收集並建立各種類型的機器資料的關聯
- 協助您主動例如容量規劃、 修補程式狀態報告及安全性稽核的活動。


>[AZURE.NOTE] 記錄分析會包含記錄搜尋功能，因此您不需要安裝的解決方案，將它啟用。 不過，您可以從方案庫新增解決方案取得資料視覺效果、 建議的搜尋和深入資訊。

您已新增解決方案之後，資料是在基礎結構伺服器從收集並傳送至 OMS 服務。 處理 OMS 服務通常需要幾分鐘為小時。 服務處理資料之後，您可以在 OMS 來進行檢視。

不再需要時，您可以輕鬆地移除解決方案。 當您移除解決方案時，並不會將其資料傳送給 OMS，如果您有可以減少的每日的配額] 中，使用的資料量。


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>支援由 Microsoft 監控代理程式的解決方案

此時，都能連線到 OMS 使用 Microsoft 監控代理程式的伺服器可以使用大部分的可用，包括解決方案︰

- Active Directory 評估
- 通知管理 （而不是 SCOM 通知）
- 反惡意程式碼
- 追蹤修訂
- 安全性
- SQL 評估
- 系統更新

不過，下列解決方案並*不*支援與 Microsoft 監控代理程式，並要求是系統管理中心的作業管理員 (SCOM) 代理程式。

- 通知管理 （包括是 SCOM 通知）
- 容量管理
- 設定評估

如需連線至記錄分析的是 SCOM 代理程式的相關資訊，請參閱[連線至記錄分析的 Operations Manager](log-analytics-om-agents.md) 。

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>若要新增使用方案庫的解決方案

1. 在 [OMS 中的 [概觀] 頁面中，按一下 [**方案庫**磚。    
    ![方案庫](./media/log-analytics-add-solutions/sol-gallery.png)
2. 在 OMS 方案庫頁面上，瞭解每一個可用的解決方案。 按一下您要新增至 OMS 的方案名稱。
3. 在頁面上您所選擇的方案，會顯示方案的詳細的資訊。 按一下 [**新增**]。
4. 新的方案新增概觀] 頁面的 OMS 您可以開始使用 OMS 服務處理您的資料後出現的方塊。

## <a name="to-configure-solutions"></a>若要設定解決方案
1. 您需要設定一些的解決方案。 例如，必須設定自動化、 Azure 網站復原和備份後，才能使用。
2. 任何這些解決方案中，按一下 [概觀] 頁面上的磚。  
    ![設定解決方案](./media/log-analytics-add-solutions/configure-additional.png)
3. 然後，設定的必要資訊的方案，然後按一下 [**儲存**。  
    ![設定解決方案](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>若要移除的方案，使用 [方案庫

1. 在 [OMS 中的 [概觀] 頁面上按一下 [**設定**] 方塊。
2. 在 [設定] 頁面上 [解決方案] 索引標籤下的 [**移除**您想要移除的方案。
3. 在 [確認] 對話方塊中，按一下 [****若要移除的解決方案。

## <a name="data-collection-details-for-oms-features-and-solutions"></a>OMS 功能的方案，以及資料集合詳細資料

下表顯示資料集合方法和其他詳細資料收集 OMS 功能和解決方案的方式。 直接代理程式和是 SCOM 代理程式為基本上相同，不過，直接代理程式包含允許連線至 OMS 工作區，並透過 proxy 路由傳送的其他功能。 如果您使用的是 SCOM 代理程式時，必須鎖定目標以進行通訊的 OMS OMS 代理程式。 此表格中的是 SCOM 代理程式是連線到是 SCOM OMS 代理程式。 連線至 OMS 的現有是 SCOM 環境的相關資訊，請參閱[連線至記錄分析的 Operations Manager](log-analytics-om-agents.md) 。

>[AZURE.NOTE] 您使用的代理程式類型會決定如何資料會傳送至 OMS，在下列情況︰

- 您使用直接代理程式或是 SCOM 附加 OMS 代理程式。
- 需要是 SCOM 時，是 SCOM 代理程式資料的方案會永遠傳送至 OMS 使用的是 SCOM 管理群組。 此外，是 SCOM 需要時，只是 SCOM 代理程式會使用解決方案。
- 當不是 SCOM 是下表顯示的是 SCOM 代理程式的資料傳送 OMS 使用 [管理] 群組中，然後是 SCOM 代理程式的資料永遠傳送至 OMS 使用管理群組。 直接代理程式略過管理群組，然後直接傳送 OMS 其資料。
- 當是 SCOM 代理程式的資料不會傳送使用管理群組時，然後資料直接傳送給 OMS，略過管理群組。


|資料類型| 平台 | 直接代理程式 | 是 SCOM 代理程式 | Azure 儲存體 | 必要時，是 SCOM 嗎？ | 透過管理群組傳送是 SCOM 代理程式的資料 | 集合頻率 |
|---|---|---|---|---|---|---|---|
|AD 評估|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 天|
|AD 複寫狀態|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 天|
|通知 (Nagios)|Linux|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|來到|
|通知 (Zabbix)|Linux|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 分鐘|
|通知 (Operations Manager)|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 分鐘|
|反惡意程式碼|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|容量管理|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|追蹤修訂|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)| 每小時|
|追蹤修訂|Linux|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|每小時|
|設定評估 （傳統顧問）|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)| 按兩次每日|
|ETW|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|IIS 記錄檔|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|索引鍵保存庫|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分鐘的時間|
|網路應用程式的閘道器|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分鐘的時間|
|網路安全性群組|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分鐘的時間|
|Office 365|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|通知|
|效能計數器|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|為排程，請 10 秒內的最小值|
|效能計數器|Linux|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|為排程，請 10 秒內的最小值|
|服務布料的轉印圖樣|Windows|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分鐘|
|SQL 評估|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 天|
|SurfaceHub|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|來到|
|系統|Linux|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|從 Azure 儲存空間︰ 10 分鐘的時間。從代理程式︰ 來到|
|系統更新|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)| 至少 2 乘以每日] 及 [安裝更新後的 15 分鐘|
|Windows 安全性事件記錄檔|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)| Azure 儲存空間︰ 10 個最小值;代理程式︰ 來到|
|在 Windows 防火牆記錄檔|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)| 來到|
|Windows 事件記錄檔|Windows|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)| Azure 儲存空間︰ 1 的最小值;代理程式︰ 來到|
|網路資料|Windows (2012 R2 / 8.1 或更新版本)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[是]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)|![無](./media/log-analytics-add-solutions/oms-bullet-red.png)| 每個 1 分鐘|

## <a name="log-analytics-preview-solutions-and-features"></a>登入分析預覽解決方案與功能

執行服務，然後遵循 devops 做法我們也能合作夥伴與客戶開發功能和解決方案。

私人預覽期間我們所提供的功能或取得意見反應，並改善解決方案的最早實作少數客戶存取。 此最早的實作具有最小的功能和作業能力。

我們的目標是快速嘗試的解決方法，讓我們可以尋找什麼，以及哪些功能無法運作。 我們會逐一此程序，直到私人預覽客戶的意見反應通知我們我們準備好公用預覽。

期間公用預覽中，我們提供功能或解決方案的所有使用者，以取得更多的意見反應和驗證我們縮放和效率。 在此階段︰

- 預覽功能會顯示 [設定] 索引標籤，可由使用者啟用
- 預覽解決方案可以透過庫新增或使用已發佈的指令碼

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>我應該預覽功能及解決方案的相關知道什麼？

我們很棒的新功能和解決方案，我們都歡迎您開發他們使用。

預覽功能和解決方案並未正確的每個人，因此之前要求加入私人的預覽] 或 [啟用公用預覽請先確認您確定使用的開發的項目。

啟用透過入口網站，您必須在 [預覽] 功能會顯示警告，提醒您此功能在預覽中。

#### <a name="for-both-private-and-public-preview"></a>*私人*及*公用*的預覽

下列適用於公用與私人預覽︰

- 項目可能無法總是正常運作。
  - 範圍的內容完全無法運作透過次要令中發生的問題
- 有潛在的預覽，系統會有負面影響 / 環境
  - 我們嘗試避免負數使用 OMS，但有時候非預期的項目會發生改變系統的項目
- 資料遺失 / 可能發生損毀
- 我們可能會要求您收集診斷記錄或其他資料來協助進行問題的疑難排解
- 功能或解決方案，將會移除 （暫時或永久）
  - 根據我們檢討期間我們可能決定要釋放功能或解決方案的預覽
- 預覽可能無法運作，或可能尚未經過測試所有的設定，使用，我們可能會限制︰
  - 可以使用的作業系統 （例如功能可能只會套用到 Linux 預覽中）
  - 代理程式 （MMA，是 SCOM），可以使用的類型 （例如功能可能無法正常運作是 SCOM 預覽中）  
- 預覽解決方案與功能未涵蓋的服務等級協定
- 預覽功能的使用狀況會對使用費用
- 您需要的功能，/ 有用的解決方案可能會遺失或不完整的功能或功能
- 功能 / 解決方案可能無法使用所有區域
- 功能 / 解決方案可能尚未本地化
- 功能 / 解決方案可能會有客戶或使用的裝置上的限制
- 您可能需要使用指令碼，來執行設定，並啟動解決方案/功能
- 使用者介面 (UI) 會在不完整，可能會變更一天日期
- 公用預覽可能不適合您生產 / 要徑系統

#### <a name="for-private-preview"></a>為*私人*的預覽

除了以上的項目，以下是私人預覽特定︰

- 我們預期您提供我們意見反應，在您的體驗，讓我們可以改善功能/方案
- 連絡您的意見反應使用問卷調查、 行動電話或電子郵件
- 項目不一定正確運作
- 我們可能需要參與保密合約 (NDA)，或可能包括機密內容
  - 部落格、 tweeting 或協力廠商否則通訊之前請檢查與專案經理負責預覽了解任何有提前洩漏的限制
- 無法執行生產 / 要徑系統


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>我要如何取得私人預覽功能和解決方案的存取權？

邀請客戶透過數種不同的方式，根據預覽私人預覽。

- 回答每月的客戶問卷，讓我們來追蹤您的權限可改善受邀參與私人預覽的機率。
- 您的 Microsoft 帳戶小組可以提名您。
- 您可以登入根據於 twitter [msopsmgmt](https://twitter.com/msopsmgmt)張貼的詳細資料
- 您可以登入詳細資料共用的社群事件 – 為基礎的我們查看開會 ups，會議和線上社群中。


## <a name="next-steps"></a>後續步驟

- 若要檢視詳細的資訊收集解決方案[搜尋記錄](log-analytics-log-searches.md)。
