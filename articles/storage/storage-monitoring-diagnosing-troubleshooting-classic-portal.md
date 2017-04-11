<properties
    pageTitle="監控、 診斷，及疑難排解儲存 |Microsoft Azure"
    description="使用功能，例如儲存分析、 用戶端記錄及其他協力廠商工具來識別，診斷，而且 Azure 儲存體相關的問題進行疑難排解。"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>監控、 診斷，及疑難排解 Microsoft Azure 儲存體

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>概觀

可比傳統的環境中更複雜診斷和疑難排解裝載雲端環境中的分散式應用程式的問題。 應用程式，可以部署在 PaaS 或 IaaS 基礎結構，內部部署，在行動裝置上，或在這些位置的組合。 一般而言，應用程式的網路流量可能往返公開及私密金鑰的網路和應用程式可能會使用多個儲存技術，例如 Microsoft Azure 儲存資料表、 二進位大型物件、 佇列，或除了其他資料檔案會儲存例如關聯式和文件的資料庫。

成功管理這類應用程式，您應該主動監視它們，並瞭解如何診斷和疑難排解以及其相關技術的所有部分。 Azure 儲存體服務的使用者，請持續監控儲存服務應用程式使用任何非預期中的變更行為 （例如低於一般回應時間），並使用記錄來收集詳細的資料以及分析深度問題。 您從同時監視與記錄取得的診斷資訊可協助您判斷您的應用程式發生的問題的根本原因。 然後，可以疑難排解問題並判斷適合補救它，您可以採取的步驟。 Azure 儲存為核心 Azure 服務，，而且表單，大部分的客戶部署至 Azure 基礎結構的解決方案的重要部分。 Azure 儲存包含功能，以簡化監控、 診斷並儲存在雲端應用程式中的問題的疑難排解。

> [AZURE.NOTE] 儲存與複寫類型區域多餘儲存空間 (ZRS) 的帳戶沒有 [公制] 或 [在此時間啟用記錄功能。 

疑難排解 Azure 儲存體應用程式中的端對端的實際操作指南，請參閱[結束端對端疑難排解使用 Azure 儲存指標和記錄、 AzCopy 及郵件分析](storage-e2e-troubleshooting.md)。

+ [簡介]
    + [本指南的組織方式]
+ [監控您儲存服務]
    + [監視服務健康狀況]
    + [監控容量]
    + [監視可用性]
    + [監控效能]
+ [診斷儲存問題]
    + [服務健康狀況的問題]
    + [效能問題]
    + [診斷錯誤]
    + [儲存模擬器問題]
    + [儲存記錄工具]
    + [使用網路記錄工具]
+ [端對端追蹤]
    + [相互關聯記錄資料]
    + [要求的用戶端識別碼]
    + [伺服器要求識別碼]
    + [加上時間戳記]
+ [疑難排解指南]
    + [指標顯示 AverageE2ELatency 高和低 AverageServerLatency]
    + [指標顯示低 AverageE2ELatency 和低 AverageServerLatency，但在用戶端發生在高延遲]
    + [指標顯示高 AverageServerLatency]
    + [發生無法預期的延遲郵件傳遞的佇列中]
    + [指標顯示 PercentThrottlingError 增加]
    + [指標顯示 PercentTimeoutError 增加]
    + [指標顯示 PercentNetworkError 增加]
    + [用戶端收到 HTTP 403 （禁止） 郵件]
    + [用戶端收到 HTTP 404 （找不到） 郵件]
    + [用戶端收到 HTTP 409 （衝突） 郵件]
    + [指標顯示低 PercentSuccess 或分析記錄項目有 ClientOtherErrors 交易狀態作業]
    + [容量指標顯示非預期的增加儲存空間容量使用量]
    + [發生意外重新啟動有大量的附加 Vhd 的虛擬機器]
    + [您的問題發生時使用的儲存空間模擬器開發或測試]
    + [您所遇到的.NET 安裝 Azure SDK 的問題]
    + [您有儲存服務的其他問題]
+ [附錄]
    + [附錄 1︰ 使用 Fiddler 擷取 HTTP 和 HTTPS 的資料傳輸]
    + [附錄 2︰ 使用 Wireshark 擷取網路流量]
    + [附錄 3︰ 使用 Microsoft 訊息分析來擷取網路流量]
    + [附錄 4︰ 使用 Excel 檢視標準及資料記錄]
    + [Visual Studio 小組服務附錄 5︰ 監控與應用程式的深入見解]

## <a name="introduction"></a>簡介

本指南示範如何使用功能，例如 Azure 儲存體分析，用戶端登入 Azure 儲存用戶端文件庫及其他協力廠商工具來識別、 診斷，及疑難排解 Azure 儲存體相關的問題。

![][1]

*圖 1 監控診斷與疑難排解*

本指南是主要由開發人員使用 Azure 儲存服務和 IT 專業人員負責管理這類線上服務的線上服務的讀取。 本指南的目標是︰

- 若要協助您維護健康狀況和 Azure 儲存體帳戶的效能。
- 若要提供給您的必要的程序及工具，可協助您決定議題或問題的應用程式中是否與 Azure 儲存體。
- 可讓您可採取動作的指導方針解決 Azure 儲存體的相關問題。

### <a name="how-this-guide-is-organized"></a>本指南的組織方式

「[監控您儲存的服務]] 區段會說明如何監視健康狀況和 Azure 儲存體服務使用 Azure 儲存分析指標 （儲存指標） 的效能。

「[Diagnosing 儲存問題]」 的一節說明如何診斷使用 Azure 儲存體分析記錄 （儲存記錄） 的問題。 它也會說明如何啟用的功能的用戶端文件庫的其中一種例如儲存空間的用戶端程式庫.NET 或使用 java Azure SDK 的用戶端記錄。

區段 」[端對端追蹤]」 會說明如何建立關聯各種記錄檔和指標資料中所包含的資訊。

區段 」[疑難排解指南]」 提供一些您可能會遇到的常見的儲存空間相關的問題的疑難排解指南。

「[附錄]」 包括使用 Netmon Wireshark 等其他工具來分析網路封包資料、 Fiddler 相互關聯記錄資料的分析 HTTP/HTTPS 訊息和 Microsoft 訊息分析程式的相關資訊。


## <a name="monitoring-your-storage-service"></a>監控您儲存服務

如果您熟悉 Windows 效能監視，您可以將儲存指標為 Azure 儲存體的同等方式 Windows 效能監視器。 在儲存指標您會看到一組完整的指標 （Windows 效能監視器術語中計數器） 等服務可用性總數的服務要求或成功服務要求的百分比 （如可用的指標的完整清單，請參閱<a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">儲存分析指標表格的結構描述</a>MSDN 上）。 您可以指定是否要儲存服務，來收集及彙總每小時] 或 [每分鐘的指標。 如需有關如何啟用指標，並監控您儲存的帳戶的詳細資訊，請參閱 MSDN 上的<a href="http://go.microsoft.com/fwlink/?LinkId=510865" target="_blank">啟用儲存指標</a>。

您可以選擇您想要顯示在 Azure 傳統入口網站及設定通知的系統管理員的規則的每小時指標電子郵件的每小時公制超過特定閾值時 (如需詳細資訊，請參閱頁面<a href="http://msdn.microsoft.com/library/azure/dn306638.aspx" target="_blank">如何︰ 收到通知的通知和 Azure 中管理通知規則</a>)。 儲存服務收集使用最佳的效果，但可能無法錄製每次儲存作業。

圖 2，下方會顯示 [監視器] 頁面，您可以在此檢視例如可用性、 總要求和平均延遲數字，儲存帳戶的指標 Azure 傳統入口網站中。 通知規則也設定，提醒系統管理員，如果可用性低於特定的層級。 檢視此資料，一個可能的區域，針對調查為表格服務成功百分比正下方 100%（如需詳細資訊，請參閱 「[指標顯示低 PercentSuccess 或分析日誌項目有作業 ClientOtherErrors 交易狀態]」 一節）。

![][2]

*圖 2 Azure 傳統入口網站中檢視儲存指標*

您應該持續監視 Azure 以確保這些狀況良好和執行如預期般的應用程式︰

- 建立可讓您的應用程式的一些基準公制比較目前的資料，並識別 Azure 儲存與您的應用程式的行為，任何重大變更。 您的比較基準準則的值，在許多情況下，會在特定的應用程式，您應該建立時的效能測試您的應用程式。
- 錄製 minute 指標，以及使用它們來監控主動發生意外的錯誤和異常，例如特殊圖文集錯誤的計算，或要求費率。
- 錄製下限指標監控平均的值，例如使用平均錯誤計數，並要求費率。
- 調查可能發生的問題使用診斷工具，討論區段 」[Diagnosing 儲存問題]」。

圖 3 下圖說明如何下限眼就會發生的平均可以隱藏波峰上活動。 每小時的指標出現指標顯示真正進行變動分鐘時顯示的要求]，以穩定比率。

![][3]

本節的其餘部分說明您應該監視什麼度量及原因。

### <a name="monitoring-service-health"></a>監視服務健康狀況

若要檢視儲存服務 （及其他 Azure 服務） 的健康情況世界各地的所有 Azure 區域，您可以使用[Azure 傳統入口網站](https://manage.windowsazure.com)。 這可讓您立即看到是否外部控制項問題會影響您使用應用程式的區域中的儲存空間服務。

Azure 傳統入口網站，也可以提供的事件會影響各種 Azure 服務的通知。
注意︰ 這項資訊的先前可用，以及在<a href="http://status.azure.com" target="_blank">http://status.azure.com</a>Azure 服務儀表板上的歷史資料。

時 Azure 傳統入口網站收集狀況資訊 （內部出監控） Azure 資料中心內，您可能也會考慮採用組織外部的方式，來產生定期從多個位置存取 Azure 裝載 web 應用程式的模擬交易。 Visual Studio 小組服務所<a href="http://www.keynote.com/solutions/monitoring/web-monitoring" target="_blank">「 主題演說</a> <a href="https://www.gomeznetworks.com/?g=1" target="_blank">Gomez</a>，與應用程式的深入見解提供的服務是這種外中的範例。 Visual Studio 小組服務的應用程式的深入見解的相關詳細資訊，請參閱附錄 」[附錄 5︰ 使用 Visual Studio 小組服務應用程式獲得深入見解監控]。 」

### <a name="monitoring-capacity"></a>監控容量

儲存指標只會儲存容量計量 blob 服務，因為 blob 通常是帳戶的最大比例儲存的資料 （在撰寫時，不能使用儲存指標監控的資料表及佇列容量）。 如果您已啟用監控 Blob 服務，您可以找到這項資料**$MetricsCapacityBlob**資料表中。 儲存指標記錄一天，一次此資料，您可以使用**RowKey**的值，來判斷列是否包含與使用者資料 （數值**資料**） 或分析資料 （值**分析**） 相關的實體。 已儲存的每個實體使用中儲存帳戶包含的儲存空間使用 （**容量**以位元組為單位） 量與目前的容器 (**ContainerCount**) 及二進位大型物件 (**ObjectCount**) 數的相關資訊。 如需有關**$MetricsCapacityBlob**資料表中儲存容量指標的詳細資訊，請參閱 MSDN 上的<a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">儲存空間分析指標資料表結構描述</a>。

> [AZURE.NOTE] 您應該監控早期警告的完容量儲存帳戶的值。 在 Azure 傳統入口網站中，在儲存帳戶的 [**監視器**] 頁面上您可以新增提醒通知您是否使用彙總的儲存空間超過，或低於您指定的臨界值的規則。

說明估計各種不同的儲存空間物件，例如二進位大型物件的大小，請參閱的部落格文章<a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx" target="_blank">瞭解 Azure 儲存體計費 – 頻寬、 交易和容量</a>。

### <a name="monitoring-availability"></a>監視可用性

您應該在您儲存帳戶監視的儲存空間服務可用性來監控每小時或分鐘指標資料表中的 [**顯示狀態**] 欄中的值 — **$MetricsHourPrimaryTransactionsBlob** **$MetricsHourPrimaryTransactionsTable**、 **$MetricsHourPrimaryTransactionsQueue**、 **$MetricsMinutePrimaryTransactionsBlob**、 **$MetricsMinutePrimaryTransactionsTable**、 **$MetricsMinutePrimaryTransactionsQueue**、 **$MetricsCapacityBlob**。 **顯示狀態**欄包含一個百分比值，指出服務或列 （ **RowKey**顯示的資料列包含指標整個服務或特定的 API 作業如果） 代表 API 作業。

任何值小於 100%表示某些儲存要求會失敗。 您可以看到他們會失敗的原因檢視中顯示的數字的不同錯誤類型，例如**ServerTimeoutError**要求指標資料的欄。 您應該會看到低於暫時 100%的原因，例如暫時性伺服器逾時時服務移到更佳的負載平衡邀請; 的磁碟分割區的**可用性**重試邏輯用戶端應用程式中的應處理這類間歇性發生的情況。 頁面<a href="http://msdn.microsoft.com/library/azure/hh343260.aspx" target="_blank"></a>列出其**可用性**計算中包含的儲存指標的交易類型。

在 Azure 傳統入口網站中，在儲存帳戶的 [**監視器**] 頁面上您可以新增提醒時通知您的服務的**可用性**低於您指定的臨界值的規則。

本指南 」[疑難排解指南]」 一節說明可用性一些常見儲存服務問題。

### <a name="monitoring-performance"></a>監控效能

若要監控效能的儲存空間服務，您可以使用以下評估每小時和分鐘指標的資料表。

- **AverageE2ELatency**和**AverageServerLatency**中的值的平均時間顯示儲存服務或 API 作業類型花費程序的要求。 **AverageE2ELatency**是包含讀取回條，並傳送邀請 [處理程序所花的時間除了回應所花的時間的端對端延遲 （因此包含網路延遲一旦邀請達到儲存服務）。**AverageServerLatency**是只在處理時，因此排除任何相關通訊與用戶端的網路延遲。 請參閱 「[指標顯示 AverageE2ELatency 高和低 AverageServerLatency]」 的討論本指南稍後的原因可能有下列兩個值之間的顯著差異。
- **TotalIngress**和**TotalEgress**欄中的值在位元組，寄至以及不在您儲存的服務，或透過特定的 API 作業類型顯示資料的總數量。
- **TotalRequests**欄中的值會顯示所收到的 API 作業的儲存空間服務要求的總數。 **TotalRequests**是儲存服務會收到要求的總數。

一般而言，您會監控這些值意外變更為您有問題需要調查的標記。

在 Azure 傳統入口網站中，在儲存帳戶的 [**監視器**] 頁面上您可以新增提醒時通知您，如果這項服務效能指標的任何低於或超過您指定的臨界值的規則。

本指南 」[疑難排解指南]」 一節說明一些常見儲存服務的問題與效能。


## <a name="diagnosing-storage-issues"></a>診斷儲存問題

有數種方式，您可能會注意問題或問題的應用程式中，其中包括︰

- 主要失敗，導致應用程式當機或停止運作。
- 從 [度量基準值的重大變更您在監視 「[監控您的儲存空間服務]」 的前一節所述
- 報告您的應用程式的使用者未完成一些特定的作業，如預期般或的部分功能無法運作。
- 在應用程式中產生的會出現錯誤記錄檔中，或透過其他告知方法。

一般而言，Azure 儲存服務的相關問題分為四種主要類型︰

- 您的應用程式有效能問題，報告您的使用者，或顯示效能指標中的變更。
- 有一或多個區域中的 Azure 儲存體基礎結構的問題。
- 您的應用程式發生錯誤，報告您的使用者，或是顯示您監視錯誤計數指標的其中一種增加。
- 期間開發和測試，您可能會使用本機存放區模擬器。您可能會遇到的儲存空間模擬器的使用狀況相關的一些問題。

下列各節應該先按照步驟診斷和疑難排解問題，在每一個這四種類別。 本指南稍後的 「[疑難排解指南]」 一節會提供更多詳細資料，您可能會遇到一些常見的問題。

### <a name="service-health-issues"></a>服務健康狀況的問題

服務健康狀況的問題通常是您的控制項之外。 Azure 傳統入口網站提供 Azure 服務，包括儲存服務進行中的任何問題的相關資訊。 如果您選擇讀取權限地理多餘的儲存空間，您建立您儲存的帳戶時，然後當您在主要的位置，無法使用的資料應用程式可能會暫時切換唯讀複本中的第二個位置。 若要這麼做，必須能夠使用主要和次要儲存位置，切換應用程式，並將其無法使用唯讀資料精簡的功能模式中。 Azure 儲存用戶端文件庫可讓您定義重試原則，以避免從主要的儲存空間讀取失敗，可以讀取次要儲存空間。 您的應用程式也必須注意的第二個位置中的資料是最後一致。 如需詳細資訊，請參閱部落格文章<a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx" target="_blank">Azure 儲存體重複選項，讀取權限地理多餘的儲存空間</a>。

### <a name="performance-issues"></a>效能問題

應用程式的效能可主觀，尤其是從使用者的觀點。 因此，請務必有比較基準公制可用來協助您識別可能是效能問題的位置。 許多因素可能會影響從用戶端應用程式的觀點 Azure 儲存服務的效能。 這些因素可能操作儲存服務、 用戶端，或網路基礎結構。因此務必識別效能問題的策略。

您找出可能的原因從計量效能問題的位置之後，您就可以尋找診斷並進一步疑難排解問題的詳細的資訊，然後使用記錄檔。

[] 區段中稍後本指南中的 「[疑難排解指南]」 提供深入瞭解相關的一些常見效能問題，您可能會遇到。

### <a name="diagnosing-errors"></a>診斷錯誤

應用程式的使用者可能會通知您的報告的用戶端應用程式錯誤。 儲存指標也會記錄從您的儲存空間服務，例如**NetworkError**、 **ClientTimeoutError**或**AuthorizationError**不同錯誤類型的計數。 時儲存指標只會記錄不同錯誤類型的數量，您可以取得關於個別的邀請更多詳細資料的檢查伺服器端、 用戶端和網路的記錄。 一般而言，儲存服務所傳回的 HTTP 狀態碼可讓表示要求失敗的原因。

> [AZURE.NOTE] 請記住，您應該會看到某些間歇性發生錯誤︰ 例如，[暫時網路條件，因為錯誤] 或 [應用程式錯誤。

MSDN 上的下列資源可以讓您瞭解儲存相關狀態和錯誤代碼︰

- <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">常見的 REST API 出現錯誤碼</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179439.aspx" target="_blank">Blob 服務錯誤碼</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179446.aspx" target="_blank">服務錯誤碼佇列中</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179438.aspx" target="_blank">表格服務錯誤碼</a>

### <a name="storage-emulator-issues"></a>儲存模擬器問題

Azure SDK 包含您可以在開發工作站執行的儲存空間模擬器。 這個模擬器模擬大部分的 Azure 儲存服務的行為，且有助於期間開發和測試]，讓您可以執行使用而不需要使用 Azure 的訂閱和 Azure 儲存體帳戶 Azure 儲存服務應用程式。

本指南 」[疑難排解指南]」 一節說明一些常見的問題使用儲存空間模擬器。

### <a name="storage-logging-tools"></a>儲存記錄工具

儲存記錄提供 Azure 儲存體帳戶中的儲存空間要求的伺服器端記錄。 如需有關如何啟用伺服器端記錄與存取權的記錄資料，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=510867" target="_blank">使用伺服器端記錄 」</a> MSDN 上。

.NET 儲存用戶端文件庫，可讓您，收集在用戶端記錄與的資料儲存應用程式所執行的作業。 如需有關如何啟用用戶端記錄與存取權的記錄資料，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=510868" target="_blank">使用儲存空間的用戶端文件庫的用戶端記錄</a>MSDN 上。

> [AZURE.NOTE] 在某些情況下 （例如 SA 授權失敗） 中，使用者可能報告的錯誤，您可以找到沒有要求資料伺服器端儲存記錄檔中。 您可以使用的儲存空間用戶端文件庫的記錄功能調查的問題導致用戶端上時，或使用網路監視工具調查網路。

### <a name="using-network-logging-tools"></a>使用網路記錄工具

您可以擷取之間提供資料交換用戶端和伺服器和基礎網路條件的詳細的資訊用戶端和伺服器的流量。 實用的網路記錄工具包括︰

- Fiddler (<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>) 是免費的 web 偵錯時，可讓您檢查標題和內容資料 HTTP 和 HTTPS 的邀請及回覆郵件的 proxy。 如需詳細資訊，請參閱 「[附錄 1︰ 使用 Fiddler 擷取 HTTP 和 HTTPS 的資料傳輸]」。
- Microsoft 網路監視器 (Netmon) (<a href="http://www.microsoft.com/download/details.aspx?id=4865" target="_blank">http://www.microsoft.com/download/details.aspx?id=4865</a>) 和 Wireshark (<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>) 是免費的網路通訊協定分析，可讓您檢視的各種網路通訊協定的封包詳細的資訊。 如需有關 Wireshark 的詳細資訊，請參閱 「[附錄 2︰ 使用 Wireshark 擷取網路流量]」。
- Microsoft 訊息分析器是 Microsoft 取代 Netmon 且除了擷取網路封包資料，可協助您檢視及分析從其他工具擷取的記錄檔資料的工具。 如需詳細資訊，請參閱 「[附錄 3︰ 使用 Microsoft 訊息分析器擷取網路流量]」。
- 如果您想要執行基本連線測試，以檢查您的用戶端電腦可以透過網路連線至 Azure 儲存服務，您無法在用戶端上使用標準**偵測 （ping)**工具執行此。 不過，您可以使用**tcping**工具來檢查連線。 **Tcping**適用於在<a href="http://www.elifulkerson.com/projects/tcping.php" target="_blank">http://www.elifulkerson.com/projects/tcping.php</a>下載。

在許多情況下，從儲存記錄和儲存用戶端文件庫的記錄資料會足以診斷問題，但在某些情況下，您可能需要這些網路記錄工具可提供的詳細的資訊。 例如，使用 Fiddler 檢視 HTTP 和 HTTPS 的郵件可讓您檢視標題和內容的資料傳送的儲存空間服務，讓您檢查如何用戶端應用程式重試儲存作業。 通訊協定分析，例如 Wireshark 操作層級封包讓您能夠檢視 TCP 資料，讓您遺失的封包及連線問題的疑難排解。 郵件分析器操作 HTTP 和 TCP 層級。

## <a name="end-to-end-tracing"></a>端對端追蹤

使用各種不同的記錄檔的端對端追蹤是很有用的技巧調查可能的問題。 您可以使用從您的指標資料的日期/時間資訊為何時開始尋找可協助您解決問題的詳細資訊的記錄檔中的指示。

### <a name="correlating-log-data"></a>相互關聯記錄資料

當您檢視從用戶端應用程式的記錄，則網路追蹤，以及很重要，才能建立關聯的伺服器端儲存記錄要求跨不同的記錄檔。 記錄檔包含不同的欄位很有用做為相互關聯識別碼的數字。 用戶端要求 id 是最有用的功能變數，用來建立不同的記錄檔中的項目之間的關係。 然而有時候，可使用伺服器要求 id 或加上時間戳記。 下列各節提供更多詳細資料，瞭解這些選項。

### <a name="client-request-id"></a>要求的用戶端識別碼

儲存用戶端文件庫會自動產生唯一的用戶端要求 id 針對每個要求。

- 在用戶端記錄中的儲存空間的用戶端程式庫所建立，用戶端要求識別碼會出現在每個記錄項目，要求的相關的 [**用戶端要求識別碼**] 欄位。
- 例如 Fiddler 所擷取的其中一個網路追蹤中的用戶端要求識別碼做為邀請郵件中看到的**x-ms-用戶端-要求-識別碼**HTTP 標頭值。
- 伺服器端記錄儲存記錄檔中的用戶端要求識別碼會出現在用戶端要求 [識別碼] 資料行。

> [AZURE.NOTE]若要共用相同的用戶端要求識別碼，因為 （雖然儲存用戶端文件庫會自動指派新的值），用戶端可以指派此值的多個要求的可能是。 若是從用戶端重試，所有嘗試都共用相同的用戶端要求識別碼。 若是從用戶端傳送以批次，批次會有單一用戶端要求 id。


### <a name="server-request-id"></a>伺服器要求識別碼

儲存服務會自動產生伺服器要求識別碼。

- 伺服器端記錄儲存記錄檔中的伺服器要求識別碼會出現**要求識別碼標題**欄。
- 例如 Fiddler 所擷取的其中一個網路追蹤中的伺服器要求識別碼回應的郵件中顯示為**x ms-要求 id** HTTP 標頭值。
- 在用戶端記錄中的儲存空間的用戶端程式庫所建立，伺服器要求識別碼會出現在 [顯示詳細資料的伺服器回應的記錄項目**作業文字**欄中。

> [AZURE.NOTE] 儲存服務一律會指定唯一的伺服器要求 id 收到，每個要求，讓每個重試的嘗試從用戶端與每一項作業以批次包含具有唯一的伺服器要求 id。

如果儲存空間的用戶端程式庫擲回**StorageException** ，在用戶端， **RequestInformation**屬性會包含**RequestResult**物件包含**ServiceRequestID**屬性。 您也可以從**OperationContext**執行個體存取**RequestResult**物件。

下列程式碼範例會示範如何設定自訂**ClientRequestId**值**OperationContext**物件要求附加至儲存服務。 也會顯示如何回應訊息從擷取**ServerRequestId**值。

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>加上時間戳記

您也可以使用找出相關的記錄項目，但小心的用戶端和伺服器可能存在於之間任何時鐘扭曲的時間戳記。 您應該搜尋加號或減號 15 分鐘，比對根據用戶端上的時間戳記伺服器端項目。 請記住，包含度量 blob 的 blob 中繼資料，表示儲存 blob; 在標準的時間範圍這是如果您有許多指標 blob 相同的分鐘或小時。

## <a name="troubleshooting-guidance"></a>疑難排解指南

本節將協助您使用的診斷與疑難排解的一些常見的問題，因此您的應用程式可能會遇到的使用 Azure 儲存服務時。 您可以使用下列清單，找出您的特定問題的相關資訊。

**疑難排解決策樹**

----------

您的問題會與其中一個存放服務的效能？

- [指標顯示 AverageE2ELatency 高和低 AverageServerLatency]
- [指標顯示低 AverageE2ELatency 和低 AverageServerLatency，但在用戶端發生在高延遲]
- [指標顯示高 AverageServerLatency]
- [發生無法預期的延遲郵件傳遞的佇列中]

----------

要建立您的問題關聯的其中一個儲存服務可用性嗎？

- [指標顯示 PercentThrottlingError 增加]
- [指標顯示 PercentTimeoutError 增加]
- [指標顯示 PercentNetworkError 增加]

----------

您的用戶端應用程式 HTTP （例如 404) 4XX 回應從接收儲存服務嗎？

- [用戶端收到 HTTP 403 （禁止） 郵件]
- [用戶端收到 HTTP 404 （找不到） 郵件]
- [用戶端收到 HTTP 409 （衝突） 郵件]

----------

[指標顯示低 PercentSuccess 或分析記錄項目有 ClientOtherErrors 交易狀態作業]

----------

[容量指標顯示非預期的增加儲存空間容量使用量]

----------

[發生意外重新啟動有大量的附加 Vhd 的虛擬機器]

----------

[您的問題發生時使用的儲存空間模擬器開發或測試]

----------

[您所遇到的.NET 安裝 Azure SDK 的問題]

----------

[您有儲存服務的其他問題]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>指標顯示 AverageE2ELatency 高和低 AverageServerLatency

從 Azure 傳統入口網站的監控工具圖例攻擊的效果會顯示範例大幅高於**AverageServerLatency** **AverageE2ELatency**位於何處。

![][4]

請注意，儲存服務只計算成功要求的公制**AverageE2ELatency** ，跟**AverageServerLatency**，包括 [用戶端資料從傳送和接收認可儲存服務所需的時間。 因此，因為正在緩慢回應，用戶端應用程式，或因為網路上的條件，可能是**AverageE2ELatency**與**AverageServerLatency**之間的差異。

> [AZURE.NOTE] 您也可以檢視**E2ELatency**和**ServerLatency**儲存記錄日誌資料中的個別的儲存空間作業。

#### <a name="investigating-client-performance-issues"></a>調查用戶端效能問題

回應速度緩慢的用戶端的可能原因包括有有限的可用連線或執行緒。 您可能可以解決問題，修改用戶端程式碼，更有效率 （例如使用非同步呼叫儲存服務），或使用較大的虛擬機器 （使用更多核心和更多記憶體）。

資料表和佇列服務，Nagle 演算法可能會導致高**AverageE2ELatency**與**AverageServerLatency**︰ 如需詳細資訊請參閱<a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx" target="_blank">Nagle 的演算法會根據小型要求不易記</a>張貼在 Microsoft Azure 儲存體小組部落格上。 您可以使用**ServicePointManager**類別**System.Net**命名空間中，以停用 Nagle 演算法程式碼。 您撥打任何資料表或佇列服務，因為這不會影響連線已使用的應用程式中開啟，您應該執行此動作。 下列範例是來自工作者角色中的**Application_Start**方法。

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

您應該檢查用戶端記錄，以便查看多少要求會提交您的用戶端應用程式，並檢查一般.NET 相關效能瓶頸，在您的用戶端，例如 CPU、.NET 回收、 網路使用率或記憶體 （疑難排解.NET 用戶端應用程式的起點，請參閱<a href="http://msdn.microsoft.com/library/7fe0dd2y(v=vs.110).aspx" target="_blank">偵錯，追蹤和分析</a>MSDN 上）。

#### <a name="investigating-network-latency-issues"></a>調查網路延遲問題

一般而言，高因為網路的端對端延遲是因為暫時性的狀況。 若要檢閱這兩個暫時性和永久性網路問題，例如捨棄封包，您可以使用例如 Wireshark 或 Microsoft 訊息分析程式工具。

如需有關如何使用 Wireshark 疑難排解網路問題的詳細資訊，請參閱 「[附錄 2︰ 使用 Wireshark 擷取網路流量]。 」

如需有關如何使用 Microsoft 訊息分析程式疑難排解網路問題的詳細資訊，請參閱 「[附錄 3︰ 使用 Microsoft 訊息分析器擷取網路流量]。 」

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>指標顯示低 AverageE2ELatency 和低 AverageServerLatency，但在用戶端發生在高延遲

在此案例中，最常見的原因會是在儲存邀請達到儲存服務中的延遲。 您應該調查為什麼從用戶端的要求都不讓透過 blob 服務。

延遲傳送要求的用戶端的可能原因包括有有限的可用連線或往來。 您也應該如果用戶端正在執行多個重試，請核取和調查原因，如果這種情況。 您可以進行此以程式設計方式**OperationContext**物件要求相關聯，以及擷取**ServerRequestId**值中尋找。 如需詳細資訊，請參閱 「[伺服器要求識別碼]」。] 區段中的程式碼範例

如果在用戶端中不有任何問題，您應該調查可能的網路問題，例如封包遺失。 您可以使用例如 Wireshark 或 Microsoft 訊息分析程式工具調查網路問題。

如需有關如何使用 Wireshark 疑難排解網路問題的詳細資訊，請參閱 「[附錄 2︰ 使用 Wireshark 擷取網路流量]。 」

如需有關如何使用 Microsoft 訊息分析程式疑難排解網路問題的詳細資訊，請參閱 「[附錄 3︰ 使用 Microsoft 訊息分析器擷取網路流量]。 」

### <a name="metrics-show-high-AverageServerLatency"></a>指標顯示高 AverageServerLatency

若是 blob 下載要求的高**AverageServerLatency** ，您應該使用記錄儲存記錄檔若要查看是否有重複相同的 blob （或 blob 一組） 的要求。 Blob 的上傳的要求，您應該調查大小的用戶端使用何種區塊 （例如，會封鎖小於 64k 大小可能會導致負荷除非讀取也中小於 64k 區塊），而且多個用戶端上載至相同的 blob 平行區塊。 您也應該檢查每分鐘的指標特殊圖文集的要求導致出現與超出的數字的每個第二個延展性目標︰ 也會看到 「[指標顯示 PercentTimeoutError 增加]」。

如果您看到高**AverageServerLatency** blob 的下載有重複的要求時相同 blob 或一組二進位大型物件的要求，您應該考慮快取這些使用 Azure 快取或 Azure 內容傳遞網路 (CDN) 的二進位大型物件。 上傳要求，您可以使用較大的區塊改善處理量。 若是資料表查詢，則也實作用戶端的用戶端的執行相同的查詢作業及資料不會經常變更快取。

最高**AverageServerLatency**值也可以設計不良的資料表或查詢中掃描作業的結果，或依照在前面加上新增/反模式的症狀。 如需詳細資訊，請參閱 「[計量顯示 PercentThrottlingError 增加]」。

> [AZURE.NOTE] 您可以找到的全面涵蓋所有內容的檢查清單效能檢查清單︰ [Microsoft Azure 儲存體效能與延展性檢查清單](storage-performance-checklist.md)。

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>發生無法預期的延遲郵件傳遞的佇列中

如果您發生延遲時間應用程式新增至佇列郵件的時間和有空讀取佇列中的時間，您應該採取下列步驟，以診斷問題︰

- 驗證應用程式成功新增至佇列中的郵件。 核取的應用程式不重試**AddMessage**方法數次成功之前。 用戶端文件存放庫記錄會顯示任何重複的重試的儲存空間作業。
- 確認有無時鐘 skew 之間新增訊息佇列中的工作者角色和佇列中，讓您可以從讀取郵件的工作者角色會出現在處理中為有延遲。
- 核取 [是否失敗從佇列中讀取郵件的工作者角色]。 如果佇列中的用戶端呼叫**GetMessage**方法，但無法回應的通知，郵件仍會維持在佇列中看不見直到**invisibilityTimeout**期間逾時。 此時，將會變成適用於處理一次。
- 核取一段時間是否成長佇列長度。 如果您沒有足夠的同事處理的所有郵件的其他工作人員會放在佇列中，會發生這項目。 您也應該檢查即可刪除如果要求會失敗，且可能表示的郵件佇列計數重複指標失敗嘗試刪除的郵件。
- 檢查儲存記錄，比預期的**E2ELatency**和**ServerLatency**值更高的較長的時間與平常不同任何佇列中作業的記錄。


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>指標顯示 PercentThrottlingError 增加

當您在超出延展性的目標儲存服務，就會發生節流錯誤。 儲存服務會以確保沒有單一用戶端或租用戶，可以使用犧牲其他人的服務。 如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Azure 儲存體延展性和效能目標</a>的延展性目標儲存帳戶及儲存帳戶中的分割的效能目標的詳細資訊。

如果**PercentThrottlingError**公制顯示增加百分比節流發生錯誤而失敗的要求，您需要調查兩種情況下︰

- [暫時增加 PercentThrottlingError]
- [永久增加 PercentThrottlingError 錯誤]

增加**PercentThrottlingError**通常時所發生的儲存空間要求數增加同時間，或當您最初載入測試您的應用程式。 這也可能資訊本身清單中的用戶端為 「 503 伺服器忙碌中 」 或 「 500 作業逾時 」 HTTP 儲存作業的狀態訊息。

#### <a name="transient-increase-in-PercentThrottlingError"></a>暫時增加 PercentThrottlingError

如果您看到的**PercentThrottlingError**值與高活動的應用程式的期間一致的波峰上，您應該在您的用戶端中實作關閉策略重試指數 （不線性） 上的一步︰ 這會減少磁碟分割上的立即載入，協助您的應用程式平滑出尖峰流量。 如需有關如何實作重試原則使用儲存空間的用戶端文件庫的詳細資訊，請參閱 MSDN 上的<a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx" target="_blank">Microsoft.WindowsAzure.Storage.RetryPolicies 命名空間</a>。

> [AZURE.NOTE] 您可能也會看到**PercentThrottlingError**的值是否符合高活動的應用程式的期間的波峰上︰ 最常見的原因會移動磁碟分割區以改善負載平衡儲存服務。

#### <a name="permanent-increase-in-PercentThrottlingError"></a>永久增加 PercentThrottlingError 錯誤

如果您看到**PercentThrottlingError**下列的一致高值增加的永久交易區，或當您執行的您最初載入測試您的應用程式上，然後必須先評估您的應用程式如何使用儲存空間的磁碟分割區，以及是否已經達到延展性目標儲存帳戶。 例如，如果您看到節流佇列 （會計數為單一磁碟分割） 上的錯誤]，然後您應該考慮使用其他佇列交易分散到多個資料分割。 如果您看到節流錯誤的表格，您需要考慮使用不同的分割配置您的交易橫跨多個資料分割，使用較大範圍的磁碟分割關鍵值。 此問題的其中一個常見的原因是 prepend/附加反模式位置分割索引鍵選取日期，並在特定日期的所有資料然後都寫入一個磁碟分割︰ 負載，這可能會導致寫入瓶頸。 您應該考慮不同的分割設計，或評估是否使用 blob 儲存體可能會更好的解決方案。 您也應該如果節流發生當做特殊圖文集將流量的核取和調查的要求的圖樣的方式。

如果您的交易分散多個資料分割時，您仍必須知道的延展性限制儲存帳戶的設定。 例如，如果您使用十個佇列每個處理 2000 1 KB 郵件秒的最大值，您將會的儲存空間帳戶秒的 20000 訊息整體限制。 如果您需要處理秒 20000 個以上的項目，您應該考慮使用多個儲存的帳戶。 您應該也請記住大小的要求和實體有影響時儲存服務節流處理您的用戶端︰ 如果您有較大的邀請和項目，您可能會降低更快。

效率的查詢設計也可能會導致您按下表磁碟分割區的延展性限制。 例如，使用篩選，只選取一個百分比的實體在磁碟分割，但的掃描分割區中的所有項目查詢會需要存取每個實體。 讀取每個實體會計算根據分割; 中的交易的總數因此，您可以輕鬆地連絡延展性目標。

> [AZURE.NOTE] 效能測試應該會顯示在您的應用程式中的任何效率的查詢設計。

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>指標顯示 PercentTimeoutError 增加

您的指標顯示的儲存空間服務**PercentTimeoutError**增加。 同時，用戶端會收到大量的 「 500 作業逾時 」 HTTP 狀態訊息作業的儲存空間。

> [AZURE.NOTE] 您可能會看到載入結餘要求將磁碟分割移至新的伺服器暫時為儲存服務的逾時錯誤。

**PercentTimeoutError**公制是彙總的下列指標︰ **ClientTimeoutError**、 **AnonymousClientTimeoutError**、 **SASClientTimeoutError**、 **ServerTimeoutError**、 **AnonymousServerTimeoutError**及**SASServerTimeoutError**。

在伺服器上錯誤被因為伺服器逾時。 用戶端逾時導因於伺服器上的作業超過用戶端; 指定逾時例如，使用儲存空間的用戶端文件庫的用戶端可以使用**QueueRequestOptions**類別**ServerTimeout**屬性以設定作業逾時。

伺服器逾時表示需要進一步調查儲存服務有問題。 若要查看是否您正中延展性限制的服務與識別任何波峰上流量可能會造成此問題，您可以使用指標。 如果問題是間歇性發生，這可能是因為負載平衡服務中的活動。 如果問題持續，而不是導致正中延展性限制的服務應用程式，您應該引發的支援問題。 用戶端逾時，您必須決定是否逾時設定為 [在用戶端及逾時值設定用戶端中的 [變更為適當的值，或調查如何您可以以提升效能的儲存空間服務中的作業，例如資料表查詢最佳化或減少的郵件大小。

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>指標顯示 PercentNetworkError 增加

您的指標的儲存空間服務**PercentNetworkError**顯示增加。 **PercentNetworkError**公制是彙總的下列指標︰ **NetworkError** **AnonymousNetworkError**，與**SASNetworkError**。 當用戶端的儲存空間要求儲存服務偵測到的網路錯誤時，會發生以下。

這項錯誤的常見原因是用戶端中斷連線逾時到期中儲存服務之前。 在您的用戶端，若要了解用戶端為何及何時中斷儲存服務，您應該調查程式碼。 您也可以使用 Wireshark、 Microsoft 訊息分析程式或 Tcping 調查從用戶端的網路連線問題。 [附錄]說明這些工具。

### <a name="the-client-is-receiving-403-messages"></a>用戶端收到 HTTP 403 （禁止） 郵件

如果您用戶端應用程式，就會擲回 HTTP 403 （禁止） 錯誤，可能的原因會是用戶端使用過期共用 Access 簽章 (SA) 時，傳送的儲存空間要求 （雖然其他可能的原因包括時鐘 skew、 無效索引鍵和空白標題）。 如果過期的 SA 機碼原因，您就不會看到的伺服器端記錄儲存記錄檔資料中的任何項目。 下表顯示樣本說明發生此問題的儲存空間用戶端程式庫產生的用戶端記錄︰

來源|詳細等級|詳細等級|要求的用戶端識別碼|作業文字
---|---|---|---|---
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab-...|每一位置模式 PrimaryOnly 的主要位置開始作業。
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab-...|啟動同步要求 https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;si = mypolicy&amp;簽章 = OFnd4Rd7z01fIvh %2bmcr6zbudih2f5ikm%2FyhNYZEmJNQ %3d&amp;api 版本 = 2014年-02-14。
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab-...|正在等待回應。
Microsoft.WindowsAzure.Storage|警告|2|85d077ab-...|擲回等待回應︰ 遠端伺服器傳回錯誤: (403) 禁止.
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab-...|接收到的回應。 狀態碼 = 403，要求 ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d，內容 MD5 =、 ETag =。
Microsoft.WindowsAzure.Storage|警告|2|85d077ab-...|作業期間發生例外狀況︰ 遠端伺服器傳回錯誤: (403) 禁止.
Microsoft.WindowsAzure.Storage|資訊|3 |85d077ab-...|檢查是否應該重試作業。 重試計數 = 0，HTTP 狀態碼 = 403，例外狀況 = 遠端伺服器傳回的錯誤: (403) 禁止.
Microsoft.WindowsAzure.Storage|資訊|3|85d077ab-...|已設定的下一個位置主要，依據位置的模式。
Microsoft.WindowsAzure.Storage|錯誤|1|85d077ab-...|重試原則不允許重試。 無法與遠端伺服器傳回的錯誤: (403) 禁止。

在此案例中，您應該調查為什麼 SA 權杖即將過期之前用戶端會傳送至伺服器的權杖︰

- 一般而言，您應該未設定開始時間，當您建立的用戶端立即使用 SA。 如果有，則可能會收到未啟用 SA 儲存服務產生使用目前的時間和儲存服務，SA 主機的小時鐘差異。
- 您不應該設定 SA 很短的到期時間。 同樣地，明顯並未即將過期預期先前 SA 可能會導致產生 SA 和儲存服務主機的小時鐘差異。
- 會在 SA 機碼中的版本參數 (例如**sv = 2012年-02-12**) 符合您所使用的儲存空間用戶端文件庫的版本。 您應該一律使用最新版的儲存空間的用戶端文件庫。 如需有關 SA token 版本設定的詳細資訊，請參閱[什麼是新的 Microsoft Azure 儲存體](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/14/what-s-new-for-microsoft-azure-storage-at-teched-2014.aspx)。
- 
- 如果您重新產生儲存 access 索引鍵 (Azure 傳統入口網站中儲存帳戶中的任何頁面，按一下 [**管理便捷鍵**)，這就可能造成任何現有的 SA 權杖。 如果您產生 SA 權杖長的到期時間快取的用戶端應用程式，這可能是問題。

如果您使用儲存空間的用戶端文件庫產生 SA 權杖，然後您可以輕鬆建立的有效的權杖。 不過，如果您是使用儲存空間 REST API，並以手動方式建構 SA 權杖應該仔細閱讀<a href="http://msdn.microsoft.com/library/azure/ee395415.aspx" target="_blank">委派存取共用 Access 簽章</a>的 MSDN 上的主題。

### <a name="the-client-is-receiving-404-messages"></a>用戶端收到 HTTP 404 （找不到） 郵件
如果用戶端應用程式從伺服器接收 HTTP 404 （找不到） 訊息，這表示的物件的用戶端嘗試使用 （例如實體、 表格、 blob、 容器或佇列中） 不存在於儲存服務。 有幾個可能的原因，例如︰

- [用戶端或其他處理程序刪除物件]
- [共用 Access 簽章 (SA) 的授權問題]
- [用戶端 JavaScript 程式碼並沒有權限可存取物件]
- [網路連線失敗]

#### <a name="client-previously-deleted-the-object"></a>用戶端或其他處理程序刪除物件
在用戶端嘗試讀取、 更新，或刪除儲存服務中的資料的位置的案例中輕鬆通常識別伺服器端記錄中刪除儲存服務問題的物件的前一個作業。 通常會顯示記錄資料，另一個使用者或程序刪除物件。 伺服器端記錄儲存記錄檔中的作業類型和要求物件索引鍵資料行顯示用戶端刪除物件時。

在用戶端嘗試插入物件的案例中，它可能不會立即為何這會導致 HTTP 404 （找不到） 回應的用戶端會建立新的物件。 不過，如果用戶端會建立 blob，它必須能夠尋找 blob 容器，如果在用戶端會建立郵件，必須為能找到佇列中，而且用戶端，就新增一列必須能夠找出該表格。

取得更詳細了解用戶端傳送到存放服務的特定的要求時，您可以從儲存的用戶端文件庫使用用戶端記錄。

當用戶端為它建立 blob 找不到容器，下列的儲存空間的用戶端程式庫產生的用戶端記錄說明此問題。 此記錄會包含下列的儲存空間作業的詳細資料︰

要求識別碼|作業
---|---
07b26a5d-...|若要刪除 blob 容器**DeleteIfExists**方法。 請注意此項作業，包含檢查存在的容器的**標頭**要求。
e2d06d78...|若要建立 blob 容器**CreateIfNotExists**方法。 請注意此項作業，包含檢查存在容器的**標頭**要求。 **不對**傳回 404 郵件，但會繼續。
de8b1c3c-...|若要建立 blob **UploadFromStream**方法。 **將**邀請失敗的 404 的郵件

日誌項目︰

要求識別碼 |  作業文字
---|---
07b26a5d-...|啟動同步 https://domemaildist.blob.core.windows.net/azuremmblobcontainer 要求。
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue，03 6 月 2014年 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container。
07b26a5d-...|正在等待回應。
07b26a5d-... | 接收到的回應。 狀態碼 = 200，要求 ID = eeead849...內容 MD5 =、 ETag = &quot;0x8D14D2DC63D059B&quot;。
07b26a5d-... | 回應標頭處理成功，繼續執行作業的其餘部分。
07b26a5d-... | 下載回應內容。
07b26a5d-... | 順利完成作業。
07b26a5d-... | 啟動同步 https://domemaildist.blob.core.windows.net/azuremmblobcontainer 要求。
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue，03 6 月 2014年 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container。
07b26a5d-... | 正在等待回應。
07b26a5d-... | 接收到的回應。 狀態碼 = 202，要求 ID = 6ab2a4cf-...]，內容 MD5 =、 ETag =。
07b26a5d-... | 回應標頭處理成功，繼續執行作業的其餘部分。
07b26a5d-... | 下載回應內容。
07b26a5d-... | 順利完成作業。
e2d06d78-... | 啟動 https://domemaildist.blob.core.windows.net/azuremmblobcontainer 非同步要求。</td>
e2d06d78-... | StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue，03 6 月 2014年 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container。
e2d06d78-...| 正在等待回應。
de8b1c3c-... | 啟動同步 https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt 要求。
de8b1c3c-... |  StringToSign = 放入...64.qCmF+TQLPhq/YYK50mP9ZQ==...x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-...x-ms-date:Tue，03 6 月 2014年 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt。
de8b1c3c-... | 準備撰寫要求資料。
e2d06d78-... | 擲回等待回應︰ 遠端伺服器傳回錯誤: (404) 找不到。
e2d06d78-... | 接收到的回應。 狀態碼 = 404，要求 ID = 353ae3bc-...]，內容 MD5 =、 ETag =。
e2d06d78-... | 回應標頭處理成功，繼續執行作業的其餘部分。
e2d06d78-... | 下載回應內容。
e2d06d78-... | 順利完成作業。
e2d06d78-... | 啟動 https://domemaildist.blob.core.windows.net/azuremmblobcontainer 非同步要求。
e2d06d78-...|StringToSign = 放入...0...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue，03 6 月 2014年 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container。
e2d06d78-... | 正在等待回應。
de8b1c3c-... | 撰寫要求資料。
de8b1c3c-... | 正在等待回應。
e2d06d78-... | 擲回等待回應︰ 遠端伺服器傳回錯誤: (409) 衝突。
e2d06d78-... | 接收到的回應。 狀態碼 = 409，要求 ID = c27da20e-...]，內容 MD5 =、 ETag =。
e2d06d78-... | 下載錯誤回應內容。
de8b1c3c-... | 擲回等待回應︰ 遠端伺服器傳回錯誤: (404) 找不到。
de8b1c3c-... | 接收到的回應。 狀態碼 = 404，要求 ID = 0eaeab3e-...]，內容 MD5 =、 ETag =。
de8b1c3c-...| 作業期間發生例外狀況︰ 遠端伺服器傳回錯誤: (404) 找不到。
de8b1c3c-... | 重試原則不允許重試。 無法與遠端伺服器傳回的錯誤: (404) 找不到。
e2d06d78-... | 重試原則不允許重試。 無法與遠端伺服器傳回的錯誤: (409) 衝突。

在此範例中，記錄會顯示於用戶端中要求**CreateIfNotExists**方法 (...要求識別碼 e2d06d78) 從**UploadFromStream**方法要求的交錯 （de8b1c3c-...）。發生此問題的用戶端應用程式叫用下列方法非同步因為。 您應該修改非同步的程式碼，以確保它嘗試上傳至 blob 的容器中的任何資料之前，先建立容器於用戶端。 理想的情況下，您應該預先建立的所有您容器。

#### <a name="SAS-authorization-issue"></a>共用 Access 簽章 (SA) 的授權問題

如果用戶端應用程式嘗試使用 SA 金鑰不包含作業的必要權限，儲存服務會傳回用戶端 HTTP 404 （找不到） 訊息。 同時，您也會看到非零值的**SASAuthorizationError**中指標。

下表顯示樣本伺服器端記錄郵件從記錄儲存記錄檔︰

<table>
  <tr>
    <td>要求的開始時間</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>作業類型</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>要求的狀態</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>HTTP 狀態碼</td>
    <td>404</td>
  </tr>
  <tr>
    <td>驗證類型</td>
    <td>Sa</td>
  </tr>
  <tr>
    <td>服務類型</td>
    <td>Blob</td>
  </tr>
  <tr>
    <td>要求 URL</td>
    <td>
    https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp; sr = c&amp;amp; si = mypolicy&amp;amp; 簽章 = XXXXX&amp;amp; api 版本 = 2014年-02-14&amp;聊天室</td>
  </tr>
  <tr>
    <td>要求識別碼標頭</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>要求的用戶端識別碼</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

您應該調查用戶端應用程式為什麼嘗試執行的作業，不已授與的權限。

#### <a name="JavaScript-code-does-not-have-permission"></a>用戶端 JavaScript 程式碼並沒有權限可存取物件

如果您使用的 JavaScript 用戶端並儲存服務會傳回 HTTP 404 訊息，您檢查下列 JavaScript 錯誤瀏覽器中︰

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] 您可以使用 Internet Explorer 中的 F12 開發工具來追蹤用戶端 JavaScript 問題進行疑難排解，在瀏覽器和儲存服務之間交換的訊息。

網頁瀏覽器實作防止網頁呼叫 [網域] 頁面來自另一個網域中的 [API 的<a href="http://www.w3.org/Security/wiki/Same_Origin_Policy" target="_blank">相同來源的原則</a>安全性限制，就會發生以下錯誤。

若要解決 JavaScript 問題，您可以設定交互原點資源共用 (CORS) 的用戶端存取儲存服務。 如需詳細資訊，請參閱 MSDN 上的<a href="http://msdn.microsoft.com/library/azure/dn535601.aspx" target="_blank">十字形，共用資源 (CORS) 支援 Azure 儲存服務</a>。

下列程式碼範例說明如何設定為允許 JavaScript Contoso 網域中執行，存取在您 blob 儲存體服務 blob 您 blob 的服務︰

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>網路連線失敗

在某些情況下，傳回 HTTP 404 郵件用戶端的儲存空間服務可能會造成遺失的網路封包。 例如，您的用戶端應用程式會刪除表格服務的實體當您看到引發儲存空間的例外狀況報告的用戶端 「 HTTP 404 （找不到） 」 資料表服務的狀態訊息。 當您調查表格儲存服務中的資料表時，會看到服務是否已刪除的實體要求。

在用戶端的例外詳細資料包含要求 id (7e84f12d...)，指定表格服務要求︰ 您可以使用這項資訊中找不到邀請詳細資料的伺服器端儲存記錄檔以搜尋**要求識別碼標頭**中的資料行的記錄檔。 您也可以使用指標來識別時失敗次數，這就會發生]，然後搜尋記錄檔根據計量錄製此錯誤的時間。 這個記錄項目會顯示刪除無法以 HTTP (404) 用戶端其他錯誤] 狀態訊息。 相同的記錄項目也包含 (813ea74f...) 上的 [**用戶端要求識別碼**] 欄中的用戶端所產生的邀請識別碼。

伺服器端記錄也會包含具有相同的**用戶端要求識別碼**值 (813ea74f...) 成功刪除作業相同的實體，並從相同的用戶端的另一個項目。 此成功的刪除作業進行提交之前失敗刪除邀請。

這種情況的最常見的原因是由用戶端傳送刪除要求的實體表格服務中，順利完成，但沒有收到認可從伺服器 （可能是因為暫時網路問題）。 用戶端然後自動重試 （使用相同的**用戶端要求識別碼**） 作業，此重試失敗，因為已經已刪除的實體。

如果經常發生此問題，您應該調查表格服務收到通知用戶端失敗的原因。 如果問題是間歇性發生，您應該攔截 「 找不到 HTTP (404) 」 的錯誤和其登入的用戶端，但允許繼續用戶端。

### <a name="the-client-is-receiving-409-messages"></a>用戶端收到 HTTP 409 （衝突） 郵件

下表顯示解壓縮從兩個用戶端作業的伺服器端記錄︰ **DeleteIfExists**緊接著**CreateIfNotExists**使用相同的 blob 容器名稱。 請注意，在兩個邀請中的每個用戶端作業結果傳送到伺服器，第一次**GetContainerProperties**要求，檢查是否容器存在，後面接著**DeleteContainer**或**CreateContainer**要求。

時間戳記|作業|結果|容器名稱|要求的用戶端識別碼
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-...
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-...
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-...
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-...

用戶端應用程式中的程式碼會刪除，然後立即重新建立 blob 容器使用相同的名稱︰ **CreateIfNotExists**方法 （用戶端要求 ID bc881924-...） 最後會失敗 HTTP 409 （衝突） 錯誤。 當用戶端刪除 blob 容器、 表格或佇列短暫名稱之前，則可使用一次。

常見的刪除/重新建立模式時，它會建立新的容器時，用戶端應用程式應該使用唯一的容器名稱。

### <a name="metrics-show-low-percent-success"></a>指標顯示低 PercentSuccess 或分析記錄項目有 ClientOtherErrors 交易狀態作業

**PercentSuccess**公制擷取成功根據其 HTTP 狀態碼的作業的百分比。 而作業與 3XX 4XX 與 5XX 的範圍中的狀態碼會計算為 [失敗，而**PercentSucess**公制值，計算成功，2XX 與狀態碼作業。 伺服器端儲存記錄檔]，在這些作業會記錄**ClientOtherErrors**交易狀態。

請務必請注意，這些作業成功完成，因此不會影響其他等顯示狀態指標。 作業的順利執行，但會導致失敗 HTTP 狀態碼的部分範例包括︰
- **ResourceNotFound**（找不到 404），例如從不存在的 blob 的 GET 要求。
- **ResouceAlreadyExists**（衝突 409），例如從**CreateIfNotExist**作業的資源已經存在。
- **ConditionNotMet**（不修改 304），例如從條件的作業，例如用戶端時傳送**ETag**值和 HTTP **If 無-比對**標題自上次作業更新時，才要求圖像。

您可以找到的常見 REST API 錯誤碼儲存服務會傳回在<a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">常見的 REST API 錯誤碼</a>] 頁面上的清單。

### <a name="capacity-metrics-show-an-unexpected-increase"></a>容量指標顯示非預期的增加儲存空間容量使用量


如果您看到突然，非預期的變更，在 [容量使用狀況，在您儲存的帳戶，您可以調查原因可用性度量資訊; 在第一次尋找例如，增加失敗刪除要求可能會導致 blob 儲存體用來當做如預期般 （例如，因為用於釋放空間 SA 權杖已過期），可能無法運作的應用程式特定清理操作，您可能會有預期會釋放空間量增加數。

### <a name="you-are-experiencing-unexpected-reboots"></a>發生意外重新啟動電腦的 Azure 虛擬機器有大量的附加 Vhd

如果 Azure 虛擬機器 (VM) 有大量的附加 Vhd 的同一個儲存帳戶，您可能會超過導致失敗 VM 個別儲存帳戶的延展性目標。 您應該檢查 minute 指標的儲存空間帳戶 (**TotalRequests**/**TotalIngress**/**TotalEgress**) 尖峰超過延展性目標儲存帳戶。 請參閱在您儲存的帳戶發生 「[指標顯示 PercentThrottlingError 增加]」 區段，判斷是否節流尋求協助。

一般而言，每個個別輸入或輸出作業從虛擬機器 VHD 翻譯**取得頁面**或**將頁面**上基礎頁面 blob 的作業。 因此，您可以使用您的環境，估計的 IOPS 微調多少 Vhd，您可以在單一儲存帳戶根據應用程式的特定的行為。 我們不建議在單一儲存帳戶中有超過 40 磁碟。 請參閱<a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Azure 儲存體延展性和效能目標</a>目前延展性目標儲存帳戶的詳細資料特別總要求工資率] 和 [總頻寬儲存您所使用的帳戶類型。
如果您已經超過延展性目標儲存帳戶，您應該將您 Vhd 在多個不同的儲存帳戶，以減少每個個別的帳戶中的活動。

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>您的問題發生時使用的儲存空間模擬器開發或測試

通常您會在開發時使用的儲存空間模擬器，並測試，以避免 Azure 儲存體帳戶的需求。 當您使用的儲存空間模擬器可能會發生的一般問題包括︰

- [在儲存模擬器中無法運作 「 X 」 功能]
- [錯誤 」 的值，其中一個 HTTP 標頭不是以正確的格式 「 使用存放模擬器時]
- [執行儲存模擬器需要系統管理員權限]

#### <a name="feature-X-is-not-working"></a>在儲存模擬器中無法運作 「 X 」 功能

儲存模擬器不支援的所有 Azure 儲存服務，例如檔案服務的功能。 如需詳細資訊，請參閱 MSDN 上的<a href="http://msdn.microsoft.com/library/azure/gg433135.aspx" target="_blank">儲存空間模擬器之間的差異和 Azure 儲存服務</a>。

儲存模擬器不支援的功能，在雲端使用 Azure 儲存服務。

#### <a name="error-HTTP-header-not-correct-format"></a>錯誤 」 的值，其中一個 HTTP 標頭不是以正確的格式 「 使用存放模擬器時

您正在測試您使用本機存放區模擬器對儲存用戶端文件庫的應用程式，並方法通話，例如**CreateIfNotExists**失敗錯誤訊息 「 HTTP 標頭的值不正確的格式 」。 這表示您正在使用的儲存空間模擬器版本不支援您正在使用的儲存空間用戶端文件庫的版本。 儲存用戶端文件庫會將其可讓您的所有要求標頭**x ms 版本**。 如果儲存模擬器無法辨識的**x ms 版本**標頭中的值，其會拒絕要求。

若要查看傳送**x ms 版本標頭**的值，您可以使用 [儲存文件庫的用戶端記錄。 如果您是使用 Fiddler 以追蹤要求從用戶端應用程式，您也可以查看**x ms 版本標頭**的值。

如果您安裝，並不會更新儲存模擬器使用最新版的儲存空間的用戶端文件庫，通常會發生這種情況。 您應該安裝最新版的儲存空間模擬器，或使用雲端儲存空間，而不是模擬器開發和測試。

#### <a name="storage-emulator-requires-administrative-privileges"></a>執行儲存模擬器需要系統管理員權限

當您執行的儲存空間模擬器時，系統會提示系統管理員認證。 這只是當您第一次初始化儲存模擬器。 在您初始化儲存模擬器之後，您不需要執行一次的系統管理員權限。

如需詳細資訊，請參閱的 MSDN （您也可以初始化時，也需要系統管理員權限的 Visual Studio 中的儲存空間模擬器） 上的<a href="http://msdn.microsoft.com/library/azure/gg433132.aspx" target="_blank">初始化儲存模擬器使用命令列工具</a>。

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>您所遇到的.NET 安裝 Azure SDK 的問題

當您嘗試安裝 SDK 時，它會失敗嘗試安裝在您的本機電腦上的儲存空間模擬器。 安裝記錄，以包含下列訊息的其中一項︰

- CAQuietExec︰ 錯誤︰ 無法存取 SQL 執行個體
- CAQuietExec︰ 錯誤︰ 無法建立資料庫

原因是現有 LocalDB 安裝發生問題。 根據預設，儲存模擬器使用 LocalDB 保存時將其在模擬 Azure 儲存服務的資料。 在命令提示字元視窗中執行下列命令，再嘗試安裝 SDK，您可以重設您的 LocalDB 執行個體。

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

[**刪除**] 命令會移除先前安裝的儲存空間模擬器中的任何舊的資料庫檔案。

### <a name="you-have-a-different-issue-with-a-storage-service"></a>您有儲存服務的其他問題

如果疑難排解前述未包含儲存服務所遇到的問題，您應採用下列方法診斷和疑難排解您的問題。

- 檢查您有任何變更，從您預期的基準線行為的準則。 從 [度量，您可能可以判斷問題是暫時或永久，然後問題會影響的儲存空間作業。
- 您可以使用的度量資訊可協助您搜尋您的伺服器端記錄資料錯誤發生的相關詳細資訊。 這項資訊可以協助您疑難排解並解決問題。
- 如果伺服器端記錄中的資訊不足無法順利解決這個問題，您可以使用用戶端文件存放庫用戶端記錄檢查您的用戶端應用程式及工具，例如 Fiddler、 Wireshark 中和 Microsoft 訊息分析來檢查您的網路的行為。

如需有關如何使用 Fiddler 的詳細資訊，請參閱 「[附錄 1︰ 使用 Fiddler 擷取 HTTP 和 HTTPS 流量]。 」

如需有關如何使用 Wireshark 的詳細資訊，請參閱 「[附錄 2︰ 使用 Wireshark 擷取網路流量]。 」

如需有關如何使用 Microsoft 訊息分析程式的詳細資訊，請參閱 「[附錄 3︰ 使用 Microsoft 訊息分析器擷取網路流量]。 」

## <a name="appendices"></a>附錄

附錄說明數種工具，您可能會有幫助時找到您的診斷與疑難排解問題 Azure 儲存體 （及其他服務）。 這些工具不是 Azure 儲存體的一部分，部分協力廠商的產品。 附錄所述的工具未涵蓋的任何支援合約，您可能需要使用 Microsoft Azure 或 Azure 儲存，因此評估程序的一部分，您應該檢查授權及支援選項的提供者提供這些工具。

### <a name="appendix-1"></a>附錄 1︰ 使用 Fiddler 擷取 HTTP 和 HTTPS 的資料傳輸

Fiddler 是實用的工具來分析您的用戶端應用程式與您使用的 Azure 儲存服務之間的 HTTP 和 HTTPS 的流量。 您可以從<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>下載 Fiddler。

> [AZURE.NOTE] Fiddler 可以解碼 HTTPS 流量。您應該謹慎若要瞭解如何運作，並瞭解的安全性含意 Fiddler 文件]。

本附錄提供如何設定 Fiddler 擷取本機電腦有安裝 Fiddler 和 Azure 儲存服務之間的資料傳輸的簡短逐步解說。

您已啟動 Fiddler 之後，它就會開始擷取您的本機電腦上的 HTTP 和 HTTPS 流量。 以下是一些實用的命令來控制 Fiddler:

- 停止和開始擷取流量。 在主功能表中，移至 [**檔案**，然後按一下 [**擷取流量**切換擷取開啟和關閉。
- 儲存所擷取的資料傳輸的資料。 在主功能表中，移至**檔案**上,，按一下 [**儲存**]，然後按一下 [**所有工作階段**︰ 這可讓您儲存工作階段封存檔案中的流量。 您可以重新載入工作階段封存稍後的分析，或將其傳送至 Microsoft 支援服務要求。

若要限制的 Fiddler 擷取的流量，您可以使用您在 [**篩選**] 索引標籤中設定的篩選。 下圖顯示 [擷取流量傳送至**contosoemaildist.table.core.windows.net**儲存端點的篩選︰

![][5]

### <a name="appendix-2"></a>附錄 2︰ 使用 Wireshark 擷取網路流量

Wireshark 是網路通訊協定分析程式可讓您檢視的各種網路通訊協定的封包詳細的資訊。 您可以從<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>下載 Wireshark。

下列程序會顯示您如何擷取詳細封包資訊流量從本機電腦在您安裝 Wireshark 表格服務 Azure 儲存體帳戶中。

1.  在您的本機電腦上啟動 Wireshark。
2.  在 [**開始**] 區段中，選取區域網路介面或連線到網際網路的介面。
3.  按一下 [**擷取選項**]。
4.  將**擷取篩選器**] 文字方塊中的篩選。 例如，**主機 contosoemaildist.table.core.windows.net**將會設定擷取只傳送至] 或從**contosoemaildist**儲存帳戶中的表格服務端點的封包 Wireshark。 擷取篩選的完整清單，請參閱<a href="http://wiki.wireshark.org/CaptureFilters" target="_blank">http://wiki.wireshark.org/CaptureFilters</a>。

    ![][6]

5.  按一下 [**開始**]。 Wireshark 現在會擷取所有封包傳送給或從表格服務端點當您在您的本機電腦上使用您的用戶端應用程式。
6.  當您完成之後，請按一下 [主功能表上的 [**擷取**，然後**停止**。
7.  若要儲存所擷取的資料 Wireshark 擷取檔案中，在主功能表上按一下 [**檔案**]，然後**儲存**。

WireShark 會醒目提示任何存在於**packetlist**視窗中的錯誤。 您也可以使用 [**專家資訊**] 視窗 （按一下 [**分析**]，然後**專家資訊**） 若要檢視錯誤及警告的摘要。

![][7]

您也可以選擇檢視應用程式層級的 TCP 資料上按一下滑鼠右鍵，然後選取 [**依照 TCP 串流**所看到的 TCP 資料。 這是您所擷取您傾印不擷取篩選器的特別有用。 請參閱<a href="http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html" target="_blank">以下</a>如需詳細資訊。

![][8]

> [AZURE.NOTE] 如需有關如何使用 Wireshark 的詳細資訊，請參閱<a href="http://www.wireshark.org/docs/wsug_html_chunked/" target="_blank">Wireshark 使用者指南</a>。

### <a name="appendix-3"></a>附錄 3︰ 使用 Microsoft 訊息分析來擷取網路流量

使用 Microsoft 訊息分析擷取 HTTP 和 HTTPS 的資料傳輸 Fiddler，以類似的方式，以類似的方式來 Wireshark 中擷取網路流量。

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>設定網路追蹤的工作階段，使用 Microsoft 訊息分析

要設定 HTTP 和 HTTPS 流量使用 Microsoft 訊息分析程式的網路追蹤工作階段，執行 Microsoft 訊息分析應用程式，然後按一下 [**檔案**] 功能表上的 [**擷取/追蹤**。 在可用的追蹤案例的清單中，選取 [**網頁 Proxy**]。 然後在 [**追蹤案例設定**] 面板中，在**HostnameFilter** ] 文字方塊中新增儲存空間端點 （您可以查詢這些 Azure 傳統入口網站中的名稱） 的名稱。 例如，如果您的 Azure 儲存體帳戶名稱**contosodata**，您應該新增下列**HostnameFilter**文字方塊︰

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] 空格字元來分隔 hostname。

當您準備好開始收集追蹤資料時，按一下 [**開始使用**] 按鈕。

如需 Microsoft 訊息分析**網頁 Proxy**追蹤的詳細資訊，請參閱 TechNet 上的<a href="http://technet.microsoft.com/library/jj674814.aspx" target="_blank">PEF WebProxy 提供者</a>。

在 Microsoft 訊息分析程式的內建**網頁 Proxy**追蹤根據 Fiddler;可擷取用戶端 HTTPS 流量並顯示加密的 HTTPS 郵件。 **網頁 Proxy**追蹤的運作方式設定讓它加密的郵件可以存取的所有 HTTP 和 HTTPS 流量的本機 proxy。

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>使用 Microsoft 訊息分析診斷網路問題

除了使用 Microsoft 訊息分析**網頁 Proxy**追蹤擷取的用戶端應用程式和儲存服務之間 HTTP/HTTPs 流量的詳細資料，您也可以使用內建的**本機連結階層**追蹤擷取網路封包資訊。 這可以讓您擷取資料，您可以使用 Wireshark 中擷取及診斷類似網路問題，例如卸除封包。

下列的螢幕擷取畫面顯示**DiagnosisTypes**欄中的範例**本機連結階層**追蹤的部分**資訊**的郵件。 按一下 [ **DiagnosisTypes** ] 欄中的圖示會顯示訊息的詳細資料。 在此範例中，伺服器重新傳送郵件 #305，因為它沒有從用戶端收到認可︰

![][9]

當您在 Microsoft 訊息分析器建立追蹤工作階段時，您可以指定篩選，以減少雜訊追蹤中。 可讓您定義追蹤**擷取 / 追蹤**的頁面，按一下 [在**Microsoft Windows-NDIS PacketCapture**] 旁的 [**設定**] 連結。 以下螢幕擷取畫面顯示篩選的三個儲存服務的 IP 位址的 TCP 流量的設定︰

![][10]

如需 Microsoft 訊息分析器本機連結層追蹤的詳細資訊，請參閱 TechNet 上的<a href="http://technet.microsoft.com/library/jj659264.aspx" target="_blank">PEF-NDIS-PacketCapture 提供者</a>。

### <a name="appendix-4"></a>附錄 4︰ 使用 Excel 檢視標準及資料記錄

許多工具可讓您從 Azure 資料表儲存體，方便您將資料載入到 Excel，供檢視及分析的分隔格式下載儲存指標資料。 從 Azure blob 儲存體儲存記錄資料位於您可以在 Excel 中載入分隔格式。 不過，您必須新增適當的欄標題的<a href="http://msdn.microsoft.com/library/azure/hh343259.aspx" target="_blank">儲存空間分析記錄格式</a>，<a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">儲存分析指標表格的結構描述</a>的資訊。

您儲存記錄將資料匯入 Excel 下載從 blob 儲存體後︰

- 在 [**資料**] 功能表中，按一下 [**從文字**。
- 瀏覽至您想要檢視，然後按一下 [**匯入**的記錄檔。
- 在**文字匯入精靈**的步驟 1，選取 [**分隔符號**]。

在 [步驟 1**文字匯入精靈**] 的**分號**僅的分隔符號，然後選取選擇雙引號與**文字辨識符號**。 接著按一下 [**完成]** ，然後選擇您的活頁簿中放置資料的位置。

### <a name="appendix-5"></a>Visual Studio 小組服務附錄 5︰ 監控與應用程式的深入見解

您也可以使用 Visual Studio 小組服務的應用程式的深入見解功能做為您的效能和可用性監視的一部分。 此工具可以︰

- 請確定您的 web 服務可用和回應。 無論您的應用程式是網站或使用 web 服務的裝置應用程式，它可以測試您的 URL 每隔幾分鐘，來自世界各地的位置，並可讓您判斷是否有問題。
- 快速診斷任何效能問題或您的 web 服務中的例外狀況。 如果延伸 CPU 或其他資源，取得堆疊追蹤例外]，找出並輕鬆地搜尋記錄追蹤。 如果應用程式的效能低於可接受的限制，我們可以傳送給您的電子郵件。 您可以監視.NET 和 Java web 服務。

在預覽中是撰寫應用程式的深入見解的時間。 您可以在<a href="http://msdn.microsoft.com/library/azure/dn481095.aspx" target="_blank">Visual Studio 小組服務 MSDN 上的應用程式深入資訊</a>，以找到更多的資訊。


<!--Anchors-->
[簡介]: #introduction
[本指南的組織方式]: #how-this-guide-is-organized

[監控您儲存服務]: #monitoring-your-storage-service
[監視服務健康狀況]: #monitoring-service-health
[監控容量]: #monitoring-capacity
[監視可用性]: #monitoring-availability
[監控效能]: #monitoring-performance

[診斷儲存問題]: #diagnosing-storage-issues
[服務健康狀況的問題]: #service-health-issues
[效能問題]: #performance-issues
[診斷錯誤]: #diagnosing-errors
[儲存模擬器問題]: #storage-emulator-issues
[儲存記錄工具]: #storage-logging-tools
[使用網路記錄工具]: #using-network-logging-tools

[端對端追蹤]: #end-to-end-tracing
[相互關聯記錄資料]: #correlating-log-data
[要求的用戶端識別碼]: #client-request-id
[伺服器要求識別碼]: #server-request-id
[加上時間戳記]: #timestamps

[疑難排解指南]: #troubleshooting-guidance
[指標顯示 AverageE2ELatency 高和低 AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[指標顯示低 AverageE2ELatency 和低 AverageServerLatency，但在用戶端發生在高延遲]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[指標顯示高 AverageServerLatency]: #metrics-show-high-AverageServerLatency
[發生無法預期的延遲郵件傳遞的佇列中]: #you-are-experiencing-unexpected-delays-in-message-delivery

[指標顯示 PercentThrottlingError 增加]: #metrics-show-an-increase-in-PercentThrottlingError
[暫時增加 PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[永久增加 PercentThrottlingError 錯誤]: #permanent-increase-in-PercentThrottlingError
[指標顯示 PercentTimeoutError 增加]: #metrics-show-an-increase-in-PercentTimeoutError
[指標顯示 PercentNetworkError 增加]: #metrics-show-an-increase-in-PercentNetworkError

[用戶端收到 HTTP 403 （禁止） 郵件]: #the-client-is-receiving-403-messages
[用戶端收到 HTTP 404 （找不到） 郵件]: #the-client-is-receiving-404-messages
[用戶端或其他處理程序刪除物件]: #client-previously-deleted-the-object
[共用 Access 簽章 (SA) 的授權問題]: #SAS-authorization-issue
[用戶端 JavaScript 程式碼並沒有權限可存取物件]: #JavaScript-code-does-not-have-permission
[網路連線失敗]: #network-failure
[用戶端收到 HTTP 409 （衝突） 郵件]: #the-client-is-receiving-409-messages

[指標顯示低 PercentSuccess 或分析記錄項目有 ClientOtherErrors 交易狀態作業]: #metrics-show-low-percent-success
[容量指標顯示非預期的增加儲存空間容量使用量]: #capacity-metrics-show-an-unexpected-increase
[發生意外重新啟動有大量的附加 Vhd 的虛擬機器]: #you-are-experiencing-unexpected-reboots
[您的問題發生時使用的儲存空間模擬器開發或測試]: #your-issue-arises-from-using-the-storage-emulator
[在儲存模擬器中無法運作 「 X 」 功能]: #feature-X-is-not-working
[錯誤 」 的值，其中一個 HTTP 標頭不是以正確的格式 「 使用存放模擬器時]: #error-HTTP-header-not-correct-format
[執行儲存模擬器需要系統管理員權限]: #storage-emulator-requires-administrative-privileges
[您所遇到的.NET 安裝 Azure SDK 的問題]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[您有儲存服務的其他問題]: #you-have-a-different-issue-with-a-storage-service

[附錄]: #appendices
[附錄 1︰ 使用 Fiddler 擷取 HTTP 和 HTTPS 的資料傳輸]: #appendix-1
[附錄 2︰ 使用 Wireshark 擷取網路流量]: #appendix-2
[附錄 3︰ 使用 Microsoft 訊息分析來擷取網路流量]: #appendix-3
[附錄 4︰ 使用 Excel 檢視標準及資料記錄]: #appendix-4
[Visual Studio 小組服務附錄 5︰ 監控與應用程式的深入見解]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/overview.png
[2]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/portal-screenshot.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-2.png
