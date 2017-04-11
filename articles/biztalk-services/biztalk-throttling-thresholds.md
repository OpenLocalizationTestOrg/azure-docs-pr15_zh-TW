<properties 
    pageTitle="瞭解在 Biztalk 節流 |Microsoft Azure" 
    description="進一步瞭解節流臨界值，並執行階段行為產生 BizTalk 服務。 節流根據記憶體使用量和訊息數。 MABS WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>





# <a name="biztalk-services-throttling"></a>Biztalk︰ 節流

Azure Biztalk 實作服務節流根據兩個條件︰ 記憶體使用量和同步處理的訊息數。 本主題列出節流臨界值，並說明執行階段行為，節流條件發生時。

## <a name="throttling-thresholds"></a>節流臨界值

下表列出節流來源和臨界值︰

||描述|低臨界值|高臨界值|
|---|---|---|---|
|記憶體|總系統 PageFileBytes 記憶體可用/的百分比。 <p><p>總可用 PageFileBytes 大約是系統的 RAM 2 倍。|60%|為 70%|
|郵件處理|同時處理的郵件數目|40 * 核心的數字|100 * 核心的數字|

達到高臨界值時，會開始 Azure Biztalk 節流。 達到低臨界值時，請節流停駐點。 例如，您的服務使用 65%系統記憶體。 在此情況下，不會節流服務。 使用 [70%系統記憶體啟動您的服務。 在此情況下，調整及持續直到服務使用 60%（低臨界值） 的系統記憶體節流的服務。

Azure Biztalk 追蹤節流狀態 （標準狀態與流速控制狀態） 和節流工期。


## <a name="runtime-behavior"></a>執行階段行為

當 Azure Biztalk 進入節流狀態時，會發生以下情況︰

- 節流設定為每個角色執行個體。 例如︰<br/>
RoleInstanceA 節流設定。 不節流 RoleInstanceB。 在此情況下，如預期般處理 RoleInstanceB 中的郵件。 捨棄 RoleInstanceA 中的郵件，並產生下列錯誤而失敗︰<br/><br/>
**忙碌伺服器。請再試一次。**<br/><br/>
- 任何擷取來源不投票或下載的郵件。 例如︰<br/>
管線外部來源的 FTP 擷取郵件。 將節流的狀態，取得執行擷取的角色執行個體。 在此情況下，管線停駐點，直到角色執行個體停駐點節流下載其他的郵件。
- 讓用戶端可以重新送出的郵件用戶端傳送回應。
- 您必須等到節流已解決。 具體來說，您必須等到達到低臨界值。

## <a name="important-notes"></a>重要附註
- 無法停用節流設定。
- 無法修改節流臨界值。
- 節流實作系統。
- Azure SQL 資料庫伺服器也有內建節流設定。

## <a name="additional-azure-biztalk-services-topics"></a>其他 Azure Biztalk 主題

-  [安裝 Azure Biztalk SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [教學課程︰ Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [我要如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>另請參閱
- [Biztalk︰ 開發人員、 基本、 標準和進階版圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Biztalk︰ 佈建使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Biztalk︰ 佈建狀態圖表](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Biztalk︰ 備份與還原](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Biztalk︰ 發行者名稱和發行者鍵](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 
