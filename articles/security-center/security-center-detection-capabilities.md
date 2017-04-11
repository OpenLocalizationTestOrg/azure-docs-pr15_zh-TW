<properties
   pageTitle="偵測功能 Azure 資訊安全中心 |Microsoft Azure"
   description="這份文件可協助您瞭解 Azure 資訊安全中心偵測功能的運作方式。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-detection-capabilities"></a>Azure 資訊安全中心偵測功能
這份文件討論 Azure 資訊安全中心的進階的偵測功能，可協助您識別作用中的潛在威脅針對 Microsoft Azure 資源，並提供您所需的快速回應深入資訊。

> [AZURE.NOTE] 進階的偵測可在 [標準層的 Azure 資訊安全中心。 使用的 90 天免費試用版。 您可以升級版價格層選取範圍中[的安全性原則](security-center-policies.md)。 請造訪若要進一步瞭解價格[資訊安全中心] 頁面](https://azure.microsoft.com/pricing/details/security-center/)。 


## <a name="responding-to-todays-threats"></a>回應威脅
中已有重大變更威脅橫向 20 最後一年。 過去公司通常只需要擔心網站損毀個別的攻擊者大多感興趣的文件都看到 「 他們無法做什麼 」。 今天攻擊更複雜的而且組織。 通常有特定的財務及策略性目標。 他們也有更多資源，為他們可能會依國家狀態或有組織的犯罪積累。

導致前所未有的專業攻擊在這種方法。 不再有其興趣 web 損毀。 現在有興趣竊取資訊、 財務帳戶，以及私人資料 – 均他們可以使用產生開啟市場上的現金流量，或使用特定的商業、 政治或軍事的位置。 更多關於比財務清這些能夠攻擊違反網路基礎結構，以及人員執行傷害。

回應，組織通常部署各種點解決方案，著重於由想找出防衛的企業周邊 」 或 「 結束點。 產生大量低逼真度通知，需要分類和調查安全性分析師似乎經常犯的這些解決方案。 大部分的企業組織缺少的時間和專業知識才能回應這些通知 – unaddressed 有這麼多移。  同時，攻擊有發展出其方法來破壞多個簽章型措施與[改寫雲端環境](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)。 新的方法，才能更快速地識別新威脅並偵測和回應項目。 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Azure 資訊安全中心偵測並回應潛在威脅的方式

Microsoft 安全性研究人員經常是潛在威脅的。 有權存取的內部部署與雲端中的 Microsoft 的全域目前狀態的遙測以免受到擴充組。 這個的資料集的整個到達的各種集合讓 Microsoft 其內部部署消費者版和企業版產品，以及其線上服務上探索新攻擊模式和趨勢。 如此一來，資訊安全中心可以迅速更新其偵測演算法攻擊放開新增及種逐漸複雜利用。 這種方法可協助您快速移動威脅環境跟。 

資訊安全中心威脅偵測的運作方式會自動從 Azure 資源、 網路和連線的合作夥伴解決方案收集安全性資訊。 分析這項資訊，通常相互關聯來識別威脅的多個來源中的資訊。 安全性警訊會在資訊安全中心排列優先順序，以及如何修補潛在威脅的建議。

![資訊安全中心資料收集與簡報](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

資訊安全中心採用了進階的安全性分析，您可以試試最簽章為基礎的方法。 在大型資料及[電腦學習](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)技術突破會運用過整個雲端布料的轉印圖樣 – 偵測威脅，就無法使用手動方式，以及預測攻擊的功用評估事件。 這些安全性分析包括︰ 

- **整合式的威脅智慧**︰ 透過運用全域威脅智慧從 Microsoft 產品和服務、 Microsoft 數位犯罪單位 (DCU)、 Microsoft 安全性回應中心 (MSRC)，以及外部的摘要尋找已知錯誤的動作項目。
- **行為的分析**︰ 適用於已知的圖樣探索惡意行為。 
- **異常偵測**︰ 使用統計分析建立歷史的比較基準。 它通知上既有的比較基準潛在攻擊的差。


### <a name="threat-intelligence"></a>威脅智慧
Microsoft 具有全域威脅智慧以量。 遙測接續在多個來源，例如 Azure、 Office 365、 Microsoft CRM online、 Microsoft Dynamics AX、 outlook.com，MSN.com、 Microsoft 數位犯罪單位 (DCU) 和 Microsoft 安全性回應中心 (MSRC)。 研究也會收到威脅智慧資訊的主要雲端服務提供者之間共用和協力廠商威脅智慧摘要而定。 Azure 資訊安全中心可以使用這項資訊來提醒您威脅已知錯誤的動作項目。 部分範例包括︰

- **輸出通訊惡意的 IP 位址**︰ 外寄流量的已知的 botnet 或 darknet 可能表示您的資源已洩露，攻擊，嘗試執行上的 [命令系統或 exfiltrate 資料。 Azure 資訊安全中心比較網路流量到 Microsoft 的全域威脅資料庫，並偵測到惡意的 IP 位址的通訊，請通知您。

## <a name="behavioral-analytics"></a>行為的狀況分析

行為分析是技術分析及比較集合已知的圖樣的資料。 不過，這些模式不是簡單的簽章。 取決於其複雜的電腦學習演算法的大量資料集就會套用到。 他們也由專家分析師取決於透過仔細分析惡意行為。 Azure 資訊安全中心可以使用行為的分析，找出洩漏的分析虛擬機器記錄、 虛擬網路裝置記錄、 布料的轉印圖樣記錄、 損毀傾印及其他來源為基礎的資源。 

此外，還有其他信號檢查支援廣泛的行銷活動的相互關聯。 這項關聯性，有助於識別與危害的既有的指標一致的事件。 部分範例包括︰

- **Suspicious [處理程序執行**︰ 攻擊者可以用來執行沒有偵測惡意軟體的幾項技巧。 例如攻擊可能會提供惡意程式碼相同的名稱為合法系統檔案，但這些檔案放置在另一個位置，使用檔案，非常類似的名稱或遮罩，則為 true 副檔名。 資訊安全中心模型程序行為並監視器處理偵測範數，例如這些執行。  
- **隱藏惡意程式碼和兒童嘗試**︰ 複雜惡意程式碼是能夠避開傳統反惡意程式碼的 [永遠不會寫入到磁碟] 或 [加密軟體元件儲存在磁碟上的產品。  不過，這類惡意程式碼可以偵測使用記憶體的分析，因為惡意程式碼必須保留在記憶體中追蹤才能運作。 如果軟體損毀，損毀傾印會擷取記憶體的部分時當機。  藉由分析中損毀傾印的記憶體，Azure 資訊安全中心偵測以利用軟體中的弱點、 存取機密資料，及暗中保存與增益集技術洩漏的電腦而不會影響您的電腦的效能。
- **橫向移動和內部偵察**︰ 若要保存洩漏網路和秋找出有價值資料，攻擊通常嘗試移動橫向洩漏電腦從給其他人在相同的網路。 資訊安全中心以探索嘗試展開攻擊的據點的網路，例如遠端命令執行網路探查，與帳戶列舉監視程序與登入的活動。
- **惡意的 PowerShell 指令碼**︰ PowerShell 用於攻擊的用途的目標虛擬機器上執行惡意程式碼。 資訊安全中心盡可能地壓縮 PowerShell 活動的跡象可疑的活動。 
- **外寄攻擊**︰ 攻擊通常目標的雲端裝載其他攻擊使用這些資源的目標資源。 洩漏的虛擬機器，例如，可能會用來啟動強制攻擊，對其他虛擬機器、 傳送垃圾郵件，或開啟連接埠和其他裝置在網際網路上的瀏覽。 將電腦學習套用至 [網路流量，以資訊安全中心可偵測輸出網路通訊超過一般。 若是垃圾郵件資訊安全中心也相互關聯異常的電子郵件流量使用從 Office 365，以判斷郵件是否有可能的智慧讓或合法的電子郵件行銷活動的結果。  

### <a name="anomaly-detection"></a>異常偵測

Azure 資訊安全中心也會用來識別潛在威脅的異常偵測。 在 [對比] 來行為分析 （其衍生自大型資料集的已知圖樣而定），異常偵測會更多 「 個人化]，然後著重於您的部署特定的比較基準。 電腦學習會套用到決定標準的活動，以便您部署，然後規則會產生以定義以下條件，可代表安全性事件。 以下是範例︰

- **連入 RDP/SSH 劇烈強制攻擊**︰ 您的部署可能會有忙碌的虛擬機器含有大量登入有極少每日和其他虛擬機器或任何登入。 Azure 資訊安全中心可以決定這些虛擬機器的比較基準登入活動，並使用電腦學習定義什麼是標準的登入活動以外。 如果明顯不同於比較基準的登入或登入，或從要求登入，位置的時間，或其他登入相關的特性，則可能會產生提醒。 同樣地，電腦學習會決定功能有效。

## <a name="continuous-threat-intelligence-monitoring"></a>連續威脅智慧監視

Azure 資訊安全中心的運作方式安全性研究及資料科學小組所持續監控威脅環境中的變更。 下列方案包括︰

- **威脅智慧監視**︰ 威脅智慧包含機制、 標記、 影響與現有或新的潛在威脅的相關當中的建議。 此資訊也會共用安全性社群和 Microsoft 持續監視內部和外部來源威脅智慧摘要。
- **訊號共用**︰ 獲得深入見解的雲端及內部部署的服務與伺服器、 用戶端端點裝置的安全性小組透過 Microsoft 的主要產品組合是共用和分析。 
- **Microsoft 安全性專家**︰ microsoft 使用特定的安全性功能變數，例如鑑識及網頁攻擊偵測的小組進行中的互動。
- **偵測調整**︰ 演算法會針對實際的客戶資料集執行與安全性研究人員使用客戶驗證結果。 True 和 false 測用來縮小電腦學習演算法。

這些合併的努力做為目標中新增及改良偵測，您可以立即可以從-您可以採取任何動作。

## <a name="see-also"></a>另請參閱
在此文件中，您學到 Azure 資訊安全中心偵測功能的運作方式。 若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [Azure 資訊安全中心規劃及作業指南](security-center-planning-and-operations-guide.md)
- [管理及回應 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)
- [依類型在 Azure 資訊安全中心的安全性警告](security-center-alerts-type.md)
- [監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)，瞭解如何監視 Azure 資源的狀況。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)，尋找相關 Azure 安全性和法規遵循的部落格文章。