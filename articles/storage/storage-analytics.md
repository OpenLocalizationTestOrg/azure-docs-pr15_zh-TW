<properties
    pageTitle="使用收集記錄檔和指標資料的儲存空間分析 |Microsoft Azure"
    description="儲存分析，可讓您來追蹤所有的儲存空間服務的指標資料並收集 Blob 與佇列中，表格的儲存空間的記錄。"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="storage-analytics"></a>儲存空間狀況分析

## <a name="overview"></a>概觀

Azure 儲存體分析執行記錄，並提供儲存帳戶的指標資料。 您可以使用這項資料來追蹤要求、 分析趨勢，及使用您儲存的帳戶診斷問題。

若要使用儲存空間分析，您也必須為每個您想要監視的服務個別啟用它。 您可以將它啟用從[Azure 入口網站](https://portal.azure.com)。 如需詳細資訊，請參閱[監視器 Azure 入口網站中的儲存空間帳戶](storage-monitor-storage-account.md)。 您也可以啟用以程式設計方式透過 REST API 或用戶端文件庫的儲存空間分析。 使用[取得 Blob 服務內容](https://msdn.microsoft.com/library/hh452239.aspx)、[取得佇列服務屬性](https://msdn.microsoft.com/library/hh452243.aspx)、[取得資料表服務屬性](https://msdn.microsoft.com/library/hh452238.aspx)和[取得檔案服務屬性](https://msdn.microsoft.com/library/mt427369.aspx)作業啟用為每個服務的儲存空間分析。

彙總的資料儲存在已知 blob （適用於記錄） 的已知的資料表 （標準），這可能會使用 Blob 服務及表格服務 Api 來存取。

儲存分析可容納 20 TB 的儲存無關的總儲存空間帳戶限制的資料量。 如需有關帳單與資料保留原則的詳細資訊，請參閱[儲存分析及帳單](https://msdn.microsoft.com/library/hh360997.aspx)。 如需儲存帳戶限制的詳細資訊，請參閱[Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。

使用識別、 診斷，及疑難排解 Azure 儲存體相關問題的儲存空間分析及其他工具深入指南，請參閱[監視器，診斷，及疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。


## <a name="about-storage-analytics-logging"></a>關於分析儲存記錄

儲存分析至儲存服務記錄成功或失敗要求的詳細的資訊。 監視個別要求和診斷儲存服務的問題，可以使用這項資訊。 要求登入以最佳方式。

只有在儲存服務活動，會建立日誌項目。 例如，如果在儲存帳戶活動 Blob 服務中，而不是在其資料表或佇列中的服務，將會建立 Blob 服務的相關的記錄。

儲存分析記錄不適用於 Azure 檔案的服務。

### <a name="logging-authenticated-requests"></a>記錄驗證要求

會記錄下列類型的已驗證的要求︰

- 成功的要求。

- 無法要求，包括逾時，節流、 網路、 授權、 和其他錯誤。

- 使用共用 Access 簽章 (SA)，包括失敗和成功要求的要求。

- 分析資料的要求。

記錄檔的建立或刪除動作，例如，儲存分析要求並未登入。 中的[儲存空間分析登入作業的狀態訊息](https://msdn.microsoft.com/library/hh343260.aspx)，並[儲存分析的記錄格式](https://msdn.microsoft.com/library/hh343259.aspx)主題會說明登入資料的完整清單。

### <a name="logging-anonymous-requests"></a>記錄匿名要求
會記錄下列類型的匿名要求︰

- 成功的要求。

- 伺服器錯誤。

- 用戶端與伺服器的逾時錯誤。

- 失敗的 GET 要求錯誤碼 304 （未修改）。

所有其他失敗匿名要求並未登入。 中的[儲存空間分析登入作業的狀態訊息](https://msdn.microsoft.com/library/hh343260.aspx)，並[儲存分析的記錄格式](https://msdn.microsoft.com/library/hh343259.aspx)主題會說明登入資料的完整清單。

### <a name="how-logs-are-stored"></a>儲存記錄檔的方式
所有的記錄會儲存在名為 $logs，就會自動建立時儲存分析已儲存的帳戶啟用的容器中封鎖二進位大型物件。 $Logs 容器位於 blob 儲存體帳戶命名空間，例如︰ `http://<accountname>.blob.core.windows.net/$logs`。 此容器無法刪除後儲存分析已啟用，雖然可以刪除其內容。

>[Azure.NOTE] 執行容器列出作業，例如[ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx)方法時，不會顯示 $logs 容器。 必須直接存取。 例如，您可以用[ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx)方法來存取中的 blob`$logs`容器。
當要求登入，儲存分析會以區塊上傳中繼結果。 定期儲存分析會確認這些區塊，並使其提供 blob。

在同一個小時中建立的記錄可能會有重複的記錄。 您可以判斷一筆記錄，請核取**RequestId**及**作業**的數字是重複。

### <a name="log-naming-conventions"></a>登入的命名慣例
每個記錄檔以下列格式撰寫。

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

下表描述每個屬性中的記錄名稱。

| 屬性         | 描述                                                                                                                                                                                   |
|----------------   |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   |
| < 服務名稱 >    | 儲存服務的名稱。 例如︰ blob、 表格或佇列。                                                                                                                          |
| YYYY              | 四位數西元年份的記錄檔。 例如︰ 2011年。                                                                                                                                           |
| MM                | 兩個位數的月記錄檔。 例如︰ 07。                                                                                                                                             |
| DD                | 登入兩位數月份。 例如︰ 07。                                                                                                                                             |
| hh                | 兩位數小時，指出開始小時，記錄中，在 24 小時制 UTC 格式。 例如︰ 18。                                                                                     |
| mm                | 兩個位數的數字，指出開始分鐘，記錄。 此值不支援在目前版本的儲存空間分析，其值，就能 00。     |
| <counter>         | 起始計數器六個位數，表示記錄 blob 儲存服務產生一小時的時間週期的數目。 這個 [計數器開頭 000000。 例如︰ 000001。     |

以下是結合先前範例的完整的範例記錄名稱。

    blob/2011/07/31/1800/000001.log

以下是樣本 URI，可用來存取上一個登入。

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

當記錄的儲存空間要求時，產生的記錄名稱與相關聯的小時完成要求的作業。 例如，如果 GetBlob 要求已完成下午 6:30 7/31/2011，在登入會被寫入下列前置詞為︰`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>登入的中繼資料
使用可以用來識別記錄資料 blob 包含的中繼資料儲存所有記錄二進位大型物件。 下表描述每個中繼資料屬性。

| 屬性     | 描述                                                                                                                                                                                                                                                   |
|------------   |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| LogType       | 描述登入是否包含閱讀、 撰寫或刪除作業的相關資訊。 此值可以包含一種或三個，並以逗號分隔的組合。 範例 1︰ 撰寫;範例 2︰ 讀取、 寫入;範例 3︰ 讀取、 撰寫及刪除。    |
| 開始時間     | 最早的項目中的記錄、 YYYY 的形式的時間-MM-DDThh:mm:ssZ。 例如︰ 2011年-07-31T18:21:46Z。                                                                                                                                             |
| 結束時間       | 最新的項目中的記錄、 YYYY 的形式的時間-MM-DDThh:mm:ssZ。 例如︰ 2011年-07-31T18:22:09Z。                                                                                                                                               |
| LogVersion    | 記錄格式的版本。 目前唯一支援的值為 1.0。                                                                                                                                                                                     |

下列清單會顯示完整的範例使用先前範例的中繼資料。

- LogType = 寫入

- 開始時間 = 2011年-07-31T18:21:46Z

- 結束時間 = 2011年-07-31T18:22:09Z

- LogVersion = 1.0

### <a name="accessing-logging-data"></a>存取資料記錄

中的所有資料`$logs`容器可以存取的使用 Blob service Api，包括 Azure 所提供的.NET Api 受管理的文件庫。 儲存帳戶管理員可以讀取及刪除記錄，但無法建立或更新。 查詢記錄時，可以使用記錄檔的中繼資料和記錄名稱。 可能會在指定的小時記錄顯示順序，但中繼資料永遠指定時段的記錄項目中的記錄。 因此，您可以在搜尋特定記錄時，使用記錄名稱與中繼資料的組合。

## <a name="about-storage-analytics-metrics"></a>關於儲存分析指標

儲存分析可以儲存包含彙總的交易儲存服務要求的相關統計資料和容量資料的指標。 交易的報告 API 作業層級，以及在儲存服務層級，且在報告的儲存空間服務層級的容量。 指標資料可以用於分析儲存服務使用方式，請對儲存服務的要求診斷問題並改善使用服務的應用程式的效能。

若要使用儲存空間分析，您也必須為每個您想要監視的服務個別啟用它。 您可以將它啟用從[Azure 入口網站](https://portal.azure.com)。 如需詳細資訊，請參閱[監視器 Azure 入口網站中的儲存空間帳戶](storage-monitor-storage-account.md)。 您也可以啟用以程式設計方式透過 REST API 或用戶端文件庫的儲存空間分析。 若要啟用儲存分析，每個服務使用**取得服務內容**作業。

### <a name="transaction-metrics"></a>交易的指標

強大的資料集就會記錄在每小時或分鐘的時間間隔，為每個儲存服務要求 API 作業，包括輸入/出口、 可用性、 錯誤，並分類要求百分比。 您可以看到[儲存分析指標表格的結構描述](https://msdn.microsoft.com/library/hh343264.aspx)主題中的交易詳細資料的完整清單。

交易資料會記錄在兩種層級 – 服務等級和 API 作業層級。 在服務等級，統計資料摘要所有要求 API 作業會寫入到資料表實體每小時即使沒有要求所做的服務。 在 API 作業層級，統計資料僅寫入實體如果要求該小時內作業。

比方說，如果您執行**GetBlob**作業您 Blob 的服務，儲存分析指標將記錄要求，並納入 Blob 服務以及**GetBlob**作業的彙總資料。 不過，如果沒有**GetBlob**作業要求期間小時，實體將不會寫入到`$MetricsTransactionsBlob`該作業。

交易指標會記錄使用者要求和邀請所做的本身的儲存空間分析兩者均適用。 要求的儲存空間分析寫入記錄檔和表格項目，例如記錄。 如需有關這些要求的計費方式的詳細資訊，請參閱[儲存分析及帳單](https://msdn.microsoft.com/library/hh360997.aspx)。

### <a name="capacity-metrics"></a>容量的指標

>[AZURE.NOTE] 目前，容量計量值，而且只適用於 Blob 服務。 未來版本中的儲存空間分析將可使用的表格服務及佇列中服務的容量指標。

儲存帳戶的 Blob 服務的每日記錄容量的資料，並兩個資料表的實體寫。 一個實體提供使用者資料的統計資料和其他提供有關的統計資料`$logs`blob 容器使用儲存空間分析。 `$MetricsCapacityBlob`資料表包含下列的統計資料︰

- **容量**︰ 的位元組儲存帳戶的 Blob 服務所使用的儲存空間量。

- **ContainerCount**: blob 儲存體帳戶的 Blob 服務中的容器的數目。

- **ObjectCount**︰ 已確認和未認可區塊] 或 [頁面 blob 儲存體帳戶的 Blob 服務中的數字。

如需有關容量指標的詳細資訊，請參閱[儲存分析指標表格的結構描述](https://msdn.microsoft.com/library/hh343264.aspx)。

### <a name="how-metrics-are-stored"></a>如何儲存指標

每個儲存服務的所有指標資料都會都儲存在該服務的保留的三個資料表︰ 一個資料表的交易資訊與一個資料表的分鐘數次交易的詳細資訊，另一個資料表的容量資訊。 交易和分鐘交易資訊包含的邀請及回應的資料，以及容量資訊所組成儲存空間的使用情況資料。 小時指標、 minute 指標，儲存帳戶的 Blob 服務的容量可以存取的資料表名稱下表所述。

| 指標層級                         | 表格名稱                                                                                                                   | 支援的版本                                                                                                                        |
|------------------------------------   |-----------------------------------------------------------------------------------------------------------------------------  |---------------------------------------------------------------------------------------------------------------------------------------------- |
| 每小時指標，主要位置      |  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                                  | 2013-08-15 只之前的版本。 雖然仍然支援下列名稱，建議您切換到使用下方所列的資料表。  |
| 每小時指標，主要位置      | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue               | 所有的版本，包括 2013年-08-15。                                                                                                               |
| Minute 度量，主要位置      | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue         | 所有的版本，包括 2013年-08-15。                                                                                                               |
| 每小時指標，第二個位置    | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue         | 所有的版本，包括 2013年-08-15。 您必須啟用地理重複試驗的讀取權限。                                                    |
| Minute 指標，第二個位置    | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue   | 所有的版本，包括 2013年-08-15。 您必須啟用地理重複試驗的讀取權限。                                                    |
| 容量 （Blob 服務）          | $MetricsCapacityBlob                                                                                                          | 所有的版本，包括 2013年-08-15。                                                                                                               |


儲存帳戶啟用儲存分析資料時，會自動建立這些表格。 存取透過命名空間的儲存空間的帳戶，例如︰`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>存取指標的資料

指標資料表中的所有資料可以透過使用表格 service Api，包括 Azure 所提供的.NET Api 受管理的文件庫。 儲存帳戶管理員可以讀取和刪除表格項目，但無法建立或更新。

## <a name="billing-for-storage-analytics"></a>帳單寄送的儲存空間狀況分析

儲存分析會啟用 [儲存帳戶擁有者。它不預設為啟用。 儲存帳戶的服務的寫入指標的所有資料。 如此一來，所儲存的分析執行的每個寫入作業是計費。 此外，還有計費的指標資料所使用的儲存空間量。

計費是到儲存分析由執行下列動作︰

- 若要建立的記錄 blob 的要求。 

- 若要建立表格實體眼要求。

如果您已設定資料保留原則，您不會刪除交易時儲存分析刪除舊記錄和指標的資料。 不過，刪除從用戶端交易計費。 如需有關保留原則的詳細資訊，請參閱[設定儲存分析資料保留原則](https://msdn.microsoft.com/library/azure/hh343263.aspx)。

### <a name="understanding-billable-requests"></a>了解計費要求

每個帳戶的儲存空間服務的要求是 [計費] 或 [不計費。 儲存分析記錄所做的服務，包括狀態訊息，指出要求的處理方式每個要求。 同樣地，儲存分析儲存指標服務與該服務，包括的百分比與特定的狀態訊息計數的 API 作業。 在一起，這些功能可協助您分析您的帳單要求、 改善您的應用程式和診斷服務要求的問題。 如需有關帳單的詳細資訊，請參閱[瞭解 Azure 儲存體計費-頻寬、 交易和容量](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)。

在儲存分析資料時，您可以使用主題中的[儲存空間分析記錄作業和狀態訊息](https://msdn.microsoft.com/library/azure/hh343260.aspx)資料表以判斷哪些要求計費。 然後，您可以比較您的記錄檔和指標資料狀態訊息，請參閱是否支付的特定的要求。 您也可以使用前一主題中的資料表，調查儲存服務或個別的 API 作業的可用性。

## <a name="next-steps"></a>後續步驟

### <a name="setting-up-storage-analytics"></a>設定儲存狀況分析
- [監控 Azure 入口網站中的儲存空間帳戶](storage-monitor-storage-account.md)
- [啟用和設定儲存狀況分析](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>儲存分析記錄  
- [關於儲存分析記錄](https://msdn.microsoft.com/library/hh343262.aspx)
- [儲存分析記錄格式](https://msdn.microsoft.com/library/hh343259.aspx)
- [儲存分析登入作業和狀態訊息](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>儲存分析指標
- [關於儲存分析指標](https://msdn.microsoft.com/library/hh343258.aspx)
- [儲存分析指標表格的結構描述](https://msdn.microsoft.com/library/hh343264.aspx)
- [儲存分析登入作業和狀態訊息](https://msdn.microsoft.com/library/hh343260.aspx)  
