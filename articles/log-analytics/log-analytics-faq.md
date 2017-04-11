<properties
    pageTitle="登入分析常見問題集 |Microsoft Azure"
    description="記錄檔分析服務的相關常見問題的解答。"
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

# <a name="log-analytics-faq"></a>登入分析常見問題集

此 Microsoft 常見問題集是有關記錄分析中 Microsoft 作業管理套件 (OMS) 常見問題的清單。 如果您有任何其他記錄分析疑問，請移至 [[討論區](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)，並張貼您的問題。 從我們的社群幫助您取得您的答案。 如果經常會詢問問題，我們會將其新增至這份文件，讓它可以快速且輕鬆地找到。

## <a name="general"></a>一般

**問︰ 什麼檢查執行的廣告和 SQL 評估方案？**

答︰ 下列查詢會顯示目前執行的所有檢查的說明︰

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

結果供進一步檢閱然後匯出至 Excel。

**問︰ 為什麼看起來不同於*OMS*中是 SCOM Administration? * *

答︰ 根據哪些更新彙總套件的是 SCOM 在您可能會看到節點*系統管理中心顧問*、*操作的深入見解*或*記錄分析*。

文字字串更新*OMS*會包含在管理組件，必須手動匯入。 依照指示上的最新的是 SCOM 更新彙總套件知識庫文件並重新整理 OMS 主控台，若要查看*OMS*節點中最新的更新。

**問︰ 有*內部部署*版本 OMS? * *

A: [否]。 記錄檔分析程序，並將儲存有大量的資料。 雲端服務，記錄分析是調如有必要，並避免任何效能的影響您的環境。

此外，在雲端服務所代表的意義您不需要保留記錄分析基礎結構並執行常用的功能更新和修正可接收。

## <a name="configuration"></a>設定
**問︰ 可以變更用來讀取從 Azure 診斷 (WAD) 資料表/blob 容器的名稱？**  

答︰  否，這不目前可能，但未來的版本，計劃。

**問︰ 什麼 IP 位址進行 OMS 服務使用？如何確認我的防火牆，只讓流量 OMS 服務？**  

答︰ Azure 上方內建的記錄檔分析服務及結束點接收 IPs 中的[Microsoft Azure 資料中心 IP 範圍](http://www.microsoft.com/download/details.aspx?id=41653)。

進行服務部署時，變更 OMS 服務的實際 IP 位址。 DNS 名稱為允許透過您的防火牆會記錄在[proxy 和防火牆設定中記錄分析](log-analytics-proxy-firewall.md)。

**問︰ 我用於連線至 Azure ExpressRoute。我記錄分析的流量會使用我的 ExpressRoute 連線嗎？**  

答︰ 不同類型的 ExpressRoute 流量會描述[ExpressRoute 文件](./expressroute/expressroute-faqs.md#supported-services)中。

記錄檔分析流量導向使用公用外面 ExpressRoute 電路。

**問︰ 有個簡單又簡單方法，將現有的日誌分析工作區移到另一個記錄分析工作區/Azure 訂閱嗎？**  我們已我們測試的多個客戶的 OMS 工作區和嘗試我們 Azure 在訂閱中，並且生產中移動，因此我們要將其移至自己的 Azure/OMS 訂閱。  

答︰ `Move-AzureRmResource`指令程式可讓您將記錄分析工作區中，以及自動化帳戶從一個 Azure 訂閱移到另一個。 如需詳細資訊，請參閱[移動 AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx)。

Azure 入口網站中也可以進行這項變更。

您無法將資料從一個記錄分析工作區移到另一個，或變更記錄分析資料都會儲存在該區域。

**問︰ 如何將 OMS 新增至是 SCOM？**

答︰ 更新為最新的更新彙總套件及匯入管理組件會讓您將是 SCOM 連線到記錄分析。

請注意，是 SCOM 連線記錄分析只適用於是 SCOM 2012 SP1 及更新版本。

**問︰ 如何確認代理程式是能夠通訊記錄分析？**

A︰ 若要確保代理程式可以與 OMS 的通訊，請移至: [控制台]、 安全性與**Microsoft 監控代理程式**的設定。

在 [ **Azure 記錄分析 (OMS)** ] 索引標籤中，尋找綠色的核取記號。 綠色核取記號圖示確認代理程式都能與 OMS 服務通訊。

黃色警告圖示表示代理程式有問題通訊與 OMS。 常見的原因之一是 Microsoft 監控代理程式服務已停止，必須重新啟動。

**問︰ 如何停止與記錄分析通訊的代理程式？**

A︰ 中是 SCOM，會移除 OMS 受管理的清單中的電腦。 這會停止透過是 SCOM 代理程式的所有通訊。 直接連接到 OMS 代理程式，可以不讓其透過 OMS 與通訊: [控制台]、 安全性與**Microsoft 監控代理程式**的設定。
在**Azure 記錄分析 (OMS)**，移除所有列出的工作區。

## <a name="agent-data"></a>代理程式的資料

**問︰ 資料量可以傳送到代理程式給記錄分析？有最大的每位客戶的資料量嗎？**  
答︰ 免費的計劃每工作區設定每日的首字放大的 500 MB。 標準和付費的方案有無限制的上傳的資料量。 為雲端服務，記錄分析中 OMS 設計用來自動縮放控點音量最來自客戶 –，即使是每日 tb。

記錄檔分析代理程式的設計是以確保它的所需的小，而且沒有一些基本資料壓縮。 實際上，我們的客戶的其中一個寫部落格上針對我們代理程式和它們是如何印象他們所執行的測試。 資料量會視您的客戶可讓的解決方案。 您可以找到的資料量的詳細的資訊，並查看中斷的**使用方式**] 底下的 [OMS 概觀] 頁面中並排顯示。

如需詳細資訊，您可以閱讀有關低所需的 OMS 代理程式的[客戶部落格](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html)。

**問︰ 多少網路頻寬傳送資料至記錄分析時使用 Microsoft 管理代理程式 (MMA)？**

答︰ 頻寬已傳送的資料量函數。 資料壓縮時，會傳送到網路。

**問︰ 資料量會傳送每代理程式？**

答︰ 這是而定︰

- 您已啟用解決方案
- 記錄檔和正在收集到的數字
- 在記錄中的資料量

免費的價格層是內建的幾個伺服器及量測典型的資料量的好方法。 整體**使用**] 頁面上顯示使用方式。
能夠執行 WireData 代理程式的電腦上，您可以看到正在傳送資料量使用下列查詢︰

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>後續步驟

- [開始使用記錄分析](log-analytics-get-started.md)，以進一步了解記錄分析及取得啟動和執行以分鐘為單位。
