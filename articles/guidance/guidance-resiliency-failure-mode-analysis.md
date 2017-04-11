<properties
   pageTitle="分析失敗模式 |Microsoft Azure"
   description="雲端基礎之解決方案 Azure 執行分析失敗模式的指導方針。"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="mwasson"/>

# <a name="azure-resiliency-guidance-failure-mode-analysis"></a>Azure 恢復指南︰ 模式分析失敗

失敗模式分析 (FMA) 是建置程序，恢復系統，藉由識別可能失敗點系統中。 FMA 應該架構] 和 [設計階段的一部分，以便您可以建立失敗復原從頭系統。

以下是進行 FMA 的一般程序︰ 

1. 識別系統中的所有元件。 包含外部相依性，例如，身分識別提供者或協力廠商服務等等。   

2. 每個元件，找出可能會發生的潛在失敗。 單一元件可能會有一個以上的失敗模式。 例如，您應該考慮讀取的失敗，並分別撰寫失敗，因為的影響，以及可能防護功能可能會不同。

3. 對每個失敗模式，根據其整體的風險。 請考慮下列因素︰  

    - 什麼是失敗的可能性。 這是常見？ Extrememly 少見嗎？ 您不需要確切的數字。若要協助排列優先順序用途。

    - 在應用程式可用性、 資料遺失、 成本，及業務中斷的影響為何？ 

4. 針對每個失敗模式] 中，決定如何回應的應用程式，復原。 考慮必須做的取捨成本和應用程式的複雜性。   

為 FMA 程序的起點，本文會包含目錄的可能失敗模式和其降低。 目錄組織的技術或 Azure 服務，以及應用程式層級設計的 [一般] 類別。 目錄] 並不詳盡，但是涵蓋許多核心 Azure 服務。 


## <a name="app-service"></a>應用程式服務

### <a name="app-service-app-shuts-down"></a>應用程式服務應用程式會關閉。

**偵測**。 可能的原因︰

- 預期的關閉
    
    - 運算子關閉應用程式。例如，使用 Azure 入口網站。

    - 應用程式已卸載，因為它是閒置。 (如果`Always On`設定已停用。)

- 未預期的關機

    - 應用程式當機。

    - 應用程式服務 VM 執行個體就無法使用。


Application_End 記錄將攔截應用程式網域關閉 （柔邊的程序當機），而是唯一能掌握應用程式網域關閉。

**復原**

- 如果預期關機，使用應用程式的關機事件正常關閉。 例如，在 ASP.NET 中，使用`Application_End`方法。

- 如果應用程式卸載閒置時，它會自動重新啟動，下次要求。 不過，您會造成 「 低溫開始 「 成本。 

- 若要避免應用程式正在卸載閒置時，啟用`Always On`在 web 應用程式中設定。 請參閱[Azure 應用程式服務中的 [設定 web 應用程式][app-service-configure]。

- 若要防止運算子關閉應用程式，請將設定與資源鎖定`ReadOnly`層級。 請參閱[鎖定資源與 Azure 資源管理員][rm-locks]。

- 如果應用程式當機或應用程式服務 VM 變成無法使用時，應用程式服務會自動重新啟動應用程式。 


**診斷**。 應用程式的記錄，以及網頁伺服器記錄。 請參閱[啟用診斷記錄的 web 應用程式中 Azure 應用程式服務][app-service-logging]。


### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>特定使用者重複不正確的要求或 overloads 系統。 

**偵測**。 驗證的使用者，並包含在應用程式的記錄中的使用者識別碼。

**復原**

- 使用[Azure API 管理][api-management]流速要求使用者。 請參閱[節流 Azure API 管理的進階的要求][api-management-throttling]

- 封鎖使用者。

**診斷**。 記錄所有驗證要求。


### <a name="a-bad-update-was-deployed"></a>部署不正確的更新。

**偵測**。 監控應用程式運作情況，透過 Azure 入口網站 (請參閱[監視器 Azure web 應用程式的效能][app-insights-web-apps]) 或實作[健康狀況端點監控圖樣][health-endpoint-monitoring-pattern]。

**復原**。 使用多個[部署位置][app-service-slots]及回復到上次正確部署。 如需詳細資訊，請參閱[基本的 web 應用程式參考架構][ra-web-apps-basic]。


## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="openid-connect-oidc-authentication-fails"></a>OpenID 連線 (OIDC) 驗證失敗。

**偵測**。 可能失敗模式包括︰

1. Azure AD 無法使用，或無法透過因為網路問題。 重新導向至端點驗證失敗，請與 OIDC 介軟體會產生例外狀況。
2. Azure AD 租用戶不存在。 重新導向至驗證端點傳回 HTTP 錯誤碼，並 OIDC 介軟體會產生例外狀況。
3. 使用者無法進行驗證。 沒有偵測策略有必要;Azure AD 處理登入失敗。

**復原**

1. 掌握從介軟體的例外狀況。
2. 處理`AuthenticationFailed`事件。
3. 將使用者導向至錯誤頁面。
4. 使用者重試。


## <a name="azure-search"></a>Azure 搜尋

### <a name="writing-data-to-azure-search-fails"></a>Azure 搜尋撰寫資料將會失敗。

**偵測**。 掌握`Microsoft.Rest.Azure.CloudException`錯誤。

**復原**

[搜尋.NET SDK] [search-sdk]暫時性失敗後自動重試。 例外狀況用戶端 SDK 應視為非暫時性錯誤。

預設的重試原則使用指數後關閉。 若要使用不同的重試原則，請連絡`SetRetryPolicy`上`SearchIndexClient`或`SearchServiceClient`類別。 如需詳細資訊，請參閱[自動重試][auto-rest-client-retry]。

**診斷**。 使用[搜尋流量分析][search-analytics]。


### <a name="reading-data-from-azure-search-fails"></a>從 Azure 搜尋讀取資料將會失敗。

**偵測**。 掌握`Microsoft.Rest.Azure.CloudException`錯誤。

**復原**

[搜尋.NET SDK] [search-sdk]暫時性失敗後自動重試。 例外狀況用戶端 SDK 應視為非暫時性錯誤。

預設的重試原則使用指數後關閉。 若要使用不同的重試原則，請連絡`SetRetryPolicy`上`SearchIndexClient`或`SearchServiceClient`類別。 如需詳細資訊，請參閱[自動重試][auto-rest-client-retry]。

**診斷**。 使用[搜尋流量分析][search-analytics]。



## <a name="cassandra"></a>Cassandra


### <a name="reading-or-writing-to-a-node-fails"></a>讀取或寫入節點將會失敗。

**偵測**。 掌握例外狀況。 對於.NET 用戶端，這通常是`System.Web.HttpException`。 其他用戶端可能有其他的例外狀況類型。  如需詳細資訊，請參閱[Cassandra 錯誤處理正確完成](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right)。

**復原**

- 每個[Cassandra 用戶端](https://wiki.apache.org/cassandra/ClientOptions)都有自己的重試原則和功能。 如需詳細資訊，請參閱[Cassandra 錯誤處理正確完成][cassandra-error-handling]。
- 機架注意部署中，使用資料節點分佈於故障網域。
- 使用本機仲裁一致性部署到多個區域。 如果發生非暫時性失敗，無法移到另一個區域。

**診斷**。 應用程式的記錄


## <a name="cloud-service"></a>雲端服務

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>網頁或背景工作角色會意外被關閉。

**偵測**。 [RoleEnvironment.Stopping] [RoleEnvironment.Stopping]觸發事件。

**復原**。 覆寫[RoleEntryPoint.OnStop] [RoleEntryPoint.OnStop]正常清理的方法。 如需詳細資訊，請參閱[向方式處理 Azure OnStop 事件][ onstop-events] （部落格）。


## <a name="documentdb"></a>DocumentDB

### <a name="reading-data-from-documentdb-fails"></a>從 DocumentDB 讀取資料將會失敗。

**偵測**。 掌握`System.Net.Http.HttpRequestException`或`Microsoft.Azure.Documents.DocumentClientException`。 

**復原**

- SDK 自動重試失敗的嘗試。 若要設定重試與最大等待時間的數目，設定`ConnectionPolicy.RetryOptions`。 用戶端會引發例外狀況不是超出重試原則，或者不是暫時性錯誤。 

- 如果 DocumentDB 節流用戶端，則會傳回 HTTP 429 錯誤。 存回狀態碼`DocumentClientException`。 如果您發生錯誤 429 一致的方式，請考慮增加的 DocumentDB 集合處理量值。

- 將 DocumentDB 資料庫複寫跨兩個或多個區域。 所有複本都可讀取。 使用用戶端 Sdk，指定`PreferredLocations`參數。 這是 Azure 區域的排序的清單。 所有讀取便會都傳送至清單中的第一個可用的區域。 如果邀請失敗，請用戶端會嘗試在清單中，順序的其他區域。 如需詳細資訊，請參閱[多區域 DocumentDB 帳戶開發][docdb-multi-region]。

**診斷**。 記錄在用戶端的所有錯誤。


### <a name="writing-data-to-documentdb-fails"></a>資料寫入 DocumentDB 失敗。

**偵測**。 掌握`System.Net.Http.HttpRequestException`或`Microsoft.Azure.Documents.DocumentClientException`。 

**復原**

- SDK 自動重試失敗的嘗試。 若要設定重試與最大等待時間的數目，設定`ConnectionPolicy.RetryOptions`。 用戶端會引發例外狀況不是超出重試原則，或者不是暫時性錯誤。 

- 如果 DocumentDB 節流用戶端，則會傳回 HTTP 429 錯誤。 存回狀態碼`DocumentClientException`。 如果您發生錯誤 429 一致的方式，請考慮增加的 DocumentDB 集合處理量值。

- 將 DocumentDB 資料庫複寫跨兩個或多個區域。 如果主要區域失敗，請另一個區域會升級撰寫。 您也可以手動觸發容錯移轉。 SDK 並自動探索路由]，讓應用程式碼仍會繼續執行後移轉後。 在容錯移轉期間內 （通常是分鐘），寫入作業會有 SDK 尋找新的寫入區域較高的延遲。 如需詳細資訊，請參閱[多區域 DocumentDB 帳戶開發][docdb-multi-region]。

- 後援，固定備份佇列中，文件，稍後處理佇列。

**診斷**。 記錄在用戶端的所有錯誤。


## <a name="elasticsearch"></a>Elasticsearch

### <a name="reading-data-from-elasticsearch-fails"></a>從 Elasticsearch 讀取資料將會失敗。

**偵測**。 掌握適當的例外狀況的特定[Elasticsearch 用戶端][elasticsearch-client]所使用。 

**復原**

- 使用重試機制。 每個用戶端有自己的重試原則。 

- 部署多個 Elasticsearch 節點並使用複寫的可用性。

如需詳細資訊，請參閱[Azure 上執行的 Elasticsearch][elasticsearch-azure]。

**診斷**。 您可以監視工具用於 Elasticsearch，或在用戶端的所有錯誤都記錄的內容。 請參閱[Azure 上執行的 Elasticsearch]的 「 監控 」 章節[elasticsearch-azure]。


### <a name="writing-data-to-elasticsearch-fails"></a>資料寫入 Elasticsearch 失敗。

**偵測**。 掌握適當的例外狀況的特定[Elasticsearch 用戶端][elasticsearch-client]所使用。  

**復原**

- 使用重試機制。 每個用戶端有自己的重試原則。 
 
- 如果應用程式可容許精簡的一致性層級，請考慮使用撰寫`write_consistency`設定的`quorum`。

如需詳細資訊，請參閱[Azure 上執行的 Elasticsearch][elasticsearch-azure]。

**診斷**。 您可以監視工具用於 Elasticsearch，或在用戶端的所有錯誤都記錄的內容。 請參閱[Azure 上執行的 Elasticsearch]的 「 監控 」 章節[elasticsearch-azure]。


## <a name="queue-storage"></a>佇列中的儲存空間

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>將訊息寫入 Azure 佇列中儲存失敗一致的方式。

**偵測**。 *N*再試一次嘗試之後，仍然失敗寫入作業。

**復原**

- 將資料儲存在本機快取，及轉寄寫入儲存之後，可以使用服務時。
- 建立次要佇列中，和寫入佇列如果主要佇列中無法使用。

**診斷**。 使用[儲存指標][storage-metrics]。


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>應用程式無法處理從佇列中的特定訊息。 

**偵測**。 特定的應用程式。 例如，郵件包含無效的資料，或因某些原因失敗的商務邏輯。 

**復原**

將郵件移到另一個佇列中。 執行另一個處理程序，檢查該佇列中的郵件。

請考慮使用 Azure 服務匯流排訊息佇列，其中提供[信件佇列][sb-dead-letter-queue]此用途的功能。

注意︰ 如果您使用的儲存佇列與 WebJobs，WebJobs SDK 提供內建有害訊息處理。 瞭解[如何使用 Azure 佇列中儲存的 WebJobs SDK][sb-poison-message]。

**診斷**。 使用應用程式的記錄。


## <a name="redis-cache"></a>Redis 快取

### <a name="reading-from-the-cache-fails"></a>從快取的讀取會失敗。

**偵測**。 掌握`StackExchange.Redis.RedisConnectionException`。

**復原**

1. 再試一次暫時性失敗。 Azure 意指快取支援透過，請參閱[Redis 快取重試指導方針]的內建重試[redis-retry]。
2. 非暫時性失敗視為快取遺漏，並回到原始的資料來源。

**診斷**。 使用[Redis 快取診斷][redis-monitor]。


### <a name="writing-to-the-cache-fails"></a>撰寫快取將會失敗。

**偵測**。 掌握`StackExchange.Redis.RedisConnectionException`。

**復原**

1. 再試一次暫時性失敗。 Azure 意指快取支援透過，請參閱[Redis 快取重試指導方針]的內建重試[redis-retry]。
2. 如果是非暫時性的錯誤，請略過，並讓其他交易稍後寫入快取。

**診斷**。 使用[Redis 快取診斷][redis-monitor]。


## <a name="sql-database"></a>SQL 資料庫

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>無法連線至資料庫中的主要區域。

**偵測**。 連線失敗。

**復原**

必要︰ 作用中的地理複寫必須設定資料庫。 請參閱[SQL 資料庫作用中地理複寫][sql-db-replication]。

- 查詢，請閱讀從次要複本。 
- 插入和更新，以手動方式無法透過次要的複本。 請參閱[啟動 Azure SQL 資料庫的計畫之內容錯移轉][sql-db-failover]。

複本會使用不同的連接字串，因此您需要更新您的應用程式中的連接字串。


### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>用戶端執行登出連接集區中的連線。

**偵測**。 掌握`System.InvalidOperationException`錯誤。 

**復原**

- 再試。
- 減輕計劃，為隔離連接集區的每個使用大小寫，讓一的使用案例無法支配所有連線。
- 增加的最大連線集區。

**診斷**。 應用程式的記錄。


### <a name="database-connection-limit-is-reached"></a>達到資料庫連線限制。 

**偵測**。 Azure SQL 資料庫限制同時工作者、 登入和工作階段的數目。 限制服務層級而定。 如需詳細資訊，請參閱[Azure SQL 資料庫資源限制][sql-db-limits]。

若要偵測到這些錯誤，掌握`System.Data.SqlClient.SqlException`，並檢查的值`SqlException.Number`SQL 錯誤碼。 如需相關錯誤碼清單，請參閱[SQL 資料庫用戶端應用程式的 SQL 錯誤碼︰ 資料庫連線錯誤及其他問題][sql-db-errors]。

**復原**。 這些錯誤被視為暫時性，，因此重試可能會解決此問題。 如果您一致叫用這些錯誤，請考慮縮放資料庫。

**診斷**。 - [Sys.event_log] [sys.event_log]查詢傳回成功的資料庫連線，連線失敗，以及死結。

- 建立[提醒的規則][azure-alerts]的失敗的連線。

- 啟用 [ [SQL 資料庫稽核][sql-db-audit]核取的登入失敗。


## <a name="service-bus-messaging"></a>服務匯流排訊息

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>閱讀從服務匯流排佇列中的郵件就會失敗。

**偵測**。 掌握從用戶端 SDK 的例外狀況。 服務匯流排例外狀況的基本類別是[MessagingException][sb-messagingexception-class]。 如果是暫時性，錯誤`IsTransient`屬性為 true。 

如需詳細資訊，請參閱[服務匯流排訊息例外][sb-messaging-exceptions]。

**復原**

1. 再試一次暫時性失敗。 請參閱[重試指導方針的服務匯流排][sb-retry]。

2. 您無法傳送給任何收件者的郵件會放在*信件佇列*中。 若要查看無法接收的郵件中使用此佇列。 有的信件佇列中沒有自動清除。 郵件會那里保留直到明確地擷取。 請參閱[概觀服務匯流排信件佇列][sb-dead-letter-queue]。


### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>將訊息寫入服務匯流排佇列中將會失敗。

**偵測**。 掌握從用戶端 SDK 的例外狀況。 服務匯流排例外狀況的基本類別是[MessagingException][sb-messagingexception-class]。 如果是暫時性，錯誤`IsTransient`屬性為 true。 

如需詳細資訊，請參閱[服務匯流排訊息例外][sb-messaging-exceptions]。

**復原**

1. 服務匯流排用戶端自動重試之後暫時性錯誤。 根據預設，它會使用指數後關閉。 在 [最大重試計數] 或 [最大逾時期間中，用戶端會擲回例外狀況。 如需詳細資訊，請參閱[重試指導方針的服務匯流排][sb-retry]。

2. 如果超過佇列配額時，用戶端會擲回[QuotaExceededException][QuotaExceededException]。 例外狀況訊息會提供更多詳細資料。 清空來自佇列中的某些郵件前，並請考慮使用電路分隔模式以避免繼續重試時超出配額。 此外，請確定未設定得太高[BrokeredMessage.TimeToLive]屬性。 

3. 可以使用[分割的佇列] 或 [主題]改良區域，內恢復[sb-partition]。 非分割佇列或主題會被指派到一個訊息存放區。 如果無法使用此郵件儲存，則會失敗佇列或主題上的所有作業。 分割的佇列或主題分割到多個訊息存放區。 

4. 其他恢復，建立兩個服務匯流排命名空間在不同區域]，然後複製郵件。 您可以使用 [作用中的複寫] 或 [被動複寫。

    - 作用中的複寫︰ 用戶端到兩個佇列傳送每封郵件。 兩個佇列接聽接收者。 標記的唯一識別碼，郵件中，用戶端可以放棄重複的訊息。

    - 被動式複寫︰ 用戶端傳送到一個佇列中的郵件。 如果有錯誤，請用戶端回到其他佇列中。 兩個佇列接聽接收者。 這種方法可減少重複寄送的郵件數。 不過，收件者必須仍會處理重複的訊息。

    如需詳細資訊，請參閱[GeoReplication 範例][sb-georeplication-sample]及[做的應用程式與服務匯流排中斷和損毀的最佳作法][sb-outages]。


### <a name="duplicate-message"></a>重複的訊息。

**偵測**。 檢查`MessageId`和`DeliveryCount`訊息的屬性。

**復原**

- 如果可能的話，設計您的訊息處理作業，冪。 否則，儲存郵件識別碼的已處理，並處理郵件之前，先檢查識別碼的郵件。

- 建立與佇列中，以啟用重複偵測，`RequiresDuplicateDetection`設為 true。 使用此設定，服務匯流排會自動刪除郵件會傳送相同的任何郵件`MessageId`為上一封郵件。  請注意下列事項︰

    -  此設定會防止放入佇列中重複的訊息。 它不會防止收件者一次以上處理相同的郵件。

    - 重複偵測有時間範圍。 如果重複傳送限制，超出此視窗時，它不會偵測到。

**診斷**。 登入重複的郵件。


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>應用程式無法處理從佇列中的特定訊息。 

**偵測**。 特定的應用程式。 例如，郵件包含無效的資料，或因某些原因失敗的商務邏輯。 

**復原**

模式有兩種失敗考慮。 

- 收件者會偵測失敗。 在此情況下，將郵件移至信件佇列中。 更新版本中，執行另一個程序來檢查信件佇列中的郵件。

- 中間處理郵件的收件者失敗&mdash;，例如，因為處理的例外狀況。 若要處理這種情況下，使用`PeekLock`模式。 在此模式中，如果鎖定到期時，郵件就會使用其他接收器。 如果郵件超過最大值傳遞計數或時間存留，則郵件會自動移至信件佇列中。

如需詳細資訊，請參閱[概觀服務匯流排信件佇列][sb-dead-letter-queue]。

**診斷**。 應用程式會將郵件移至信件佇列中，每當撰寫事件記錄檔，應用程式。


## <a name="service-fabric"></a>服務布料的轉印圖樣

### <a name="a-request-to-a-service-fails"></a>服務的要求將會失敗。

**偵測**。 服務會傳回錯誤。

**復原**

- 找出 proxy 一次 (`ServiceProxy`或`ActorProxy`)，然後再次呼叫服務/動作項目方法。 

- **設定狀態的服務**。 交易中包裝上可靠的集合的作業。 如果發生錯誤時，會復原交易。 邀請，如果取自佇列中，將處理一次。 
 
- **無狀態的服務**。 如果服務仍然存在外部存放區的資料，所有作業都必須冪。


**診斷**。 應用程式記錄檔

### <a name="service-fabric-node-is-shut-down"></a>服務布料的轉印圖樣節點會關閉。

**偵測**。 取消權杖傳遞給服務的`RunAsync`方法。 服務布料的轉印圖樣取消關閉節點之前的工作。

**復原**。 使用取消偵測關閉。 當服務布料的轉印圖樣要求確認取消的步驟時，完成任何工作，並結束 `RunAsync`盡快。

**診斷**。 應用程式的記錄


## <a name="storage"></a>儲存空間

### <a name="writing-data-to-azure-storage-fails"></a>Azure 儲存體資料寫入失敗

**偵測**。 用戶端在撰寫時收到錯誤。

**復原**

1. 重試作業，若要復原的暫時性失敗。 [再試一次原則][Storage.RetryPolicies]在用戶端 SDK 處理此自動。
2. 實作電路分隔模式，以避免甚儲存空間。
3. 如果 N 重失敗，請執行正常後援。 例如︰

    - 將資料儲存在本機快取，及轉寄寫入儲存之後，可以使用服務時。
    - 如果 [寫入] 動作是在交易的範圍內，賠償交易。

**診斷**。 使用[儲存指標][storage-metrics]。


### <a name="reading-data-from-azure-storage-fails"></a>從 Azure 儲存體讀取資料將會失敗。

**偵測**。 用戶端在閱讀時收到錯誤。

**復原**

1. 重試作業，若要復原的暫時性失敗。 [再試一次原則][Storage.RetryPolicies]在用戶端 SDK 處理此自動。
2. RA GRS 儲存空間，如果從主要的端點讀取失敗，請嘗試從第二個端點讀取。 用戶端 SDK 可處理這自動。 請參閱[Azure 儲存體複寫][storage-replication]。
3. 如果*N*重失敗，需要能適當降級後援動作。 例如，如果產品圖像無法擷取從儲存空間，顯示預留位置圖像。

**診斷**。 使用[儲存指標][storage-metrics]。


## <a name="virtual-machine"></a>虛擬機器

### <a name="connection-to-a-backend-vm-fails"></a>連線到後端 VM 會失敗。

**偵測**。 網路連線錯誤。

**復原**

- 部署在可用性設定，負載平衡器前兩個以上的後端 Vm。

- 暫時性連線錯誤時，有時候 TCP 會成功重新傳送郵件。 

- 在應用程式實作重試原則。 

- 持續性或非暫時性錯誤實作[電路分隔][circuit-breaker]圖樣。

- 如果呼叫 VM 超過其網路出口限制，輸出佇列中會填滿。 如果一致完整輸出佇列中，請考慮出縮放比例。 

**診斷**。 在服務界限記錄事件。

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>VM 執行個體就無法使用或不佳。

**偵測**。 設定負載平衡器[狀況探查][lb-probe]發出是否良好的 VM 執行個體。 探查應該檢查是否正確回應重要功能。 

**復原**。 每個應用程式層，將多個 VM 執行個體放入相同的可用性設定，並將負載平衡器 Vm 前面。 如果狀況探查失敗，負載平衡器會停止傳送新連線至不健全的執行個體。

**診斷**。 -使用負載平衡器[記錄分析][lb-monitor]。
- 設定監控系統，監視所有健康監視結束點。


### <a name="operator-accidentally-shuts-down-a-vm"></a>不小心關機 VM 運算子。

**偵測**。 N/A

**復原**。 設定與資源鎖定`ReadOnly`層級。 請參閱[鎖定資源與 Azure 資源管理員][rm-locks]。

**診斷**。 使用[Azure 活動記錄][azure-activity-logs]。


## <a name="webjobs"></a>WebJobs

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>連續工作停止執行 SCM 主機閒置時。

**偵測**。 將取消權杖傳遞給 WebJob 函數。 如需詳細資訊，請參閱[正常的關閉][web-jobs-shutdown]。 

**復原**。 啟用`Always On`在 web 應用程式中設定。 如需詳細資訊，請參閱[執行背景工作 WebJobs][web-jobs]。


## <a name="application-design"></a>應用程式的設計

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>應用程式無法處理傳入的邀請中的特殊圖文集。

**偵測**。 應用程式而定。 常見的徵狀︰

- 網站開始傳回 HTTP 5xx 錯誤碼。
- 相依的服務，例如資料庫或儲存空間，開始節流要求。 尋找 HTTP 429 （太多的要求），例如根據服務的 HTTP 錯誤。
- 會在 HTTP 佇列長度。

**復原**

- 擴充處理負載增加。 

- 減輕失敗，若要避免中斷的整個應用程式的階層式失敗。 排除策略包括︰

    - 實作[節流圖樣][throttling-pattern]避免甚後端系統。
    - 使用[資源撫平的佇列型載入][queue-based-load-leveling]要緩衝要求並處理適當的速度。
    - 排列優先順序特定的用戶端。 例如，如果應用程式免費與付費的層級，節流客戶在免費層上，但不是付費的客戶。 請參閱[優先順序佇列圖樣][priority-queue-pattern]。

**診斷**。 使用[應用程式服務診斷記錄][app-service-logging]。 使用的服務，例如[Azure 記錄分析][azure-log-analytics]，[應用程式的深入見解][app-insights]，或[新聖器][ new-relic] ，有助於了解診斷記錄。


### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>在 [工作流程或分散式的交易作業失敗。

**偵測**。 *N*再試一次嘗試之後，仍然失敗。

**復原**

- 減輕計劃，為實作[排程器代理程式主管][scheduler-agent-supervisor]管理整個工作流程的圖樣。 
- 不重試逾時。 有低成功率，這項錯誤。 
- 佇列中工作，才能稍後再試。

**診斷**。 記錄所有作業 （成功或失敗），包括補償動作。 使用相互關聯識別碼，使您可以在同一個交易內的所有作業。


### <a name="a-call-to-a-remote-service-fails"></a>打電話給遠端服務將會失敗。

**偵測**。 HTTP 錯誤碼。

**復原**

1. 再試一次暫時性失敗。 
2. 如果通話失敗之後*N*嘗試，後援的動作。 （應用程式特定。）
3. 實作[電路分隔圖樣][circuit-breaker]為避免階層式失敗。 

**診斷**。 記錄所有遠端呼叫失敗。


## <a name="next-steps"></a>後續步驟

如需有關 FMA 程序的詳細資訊，請參閱[恢復雲端服務的設計][ resilience-by-design-pdf] （PDF 下載）。

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: ../api-management/api-management-sample-flexible-throttling.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-web-apps]: ../application-insights/app-insights-azure-web-apps.md
[app-service-configure]: ../app-service-web/web-sites-configure.md
[app-service-logging]: ../app-service-web/web-sites-enable-diagnostic-log.md
[app-service-slots]: ../app-service-web/web-sites-staged-publishing.md
[auto-rest-client-retry]: https://github.com/Azure/autorest/blob/master/Documentation/clients-retry.md
[azure-activity-logs]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[azure-alerts]: ../monitoring-and-diagnostics/insights-alerts-portal.md
[azure-log-analytics]: ../log-analytics/log-analytics-overview.md
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: ../documentdb/documentdb-developing-with-multiple-regions.md
[elasticsearch-azure]: guidance-elasticsearch-running-on-azure.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: ../load-balancer/load-balancer-monitor-log.md
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: guidance-web-apps-basic.md
[redis-monitor]: ../redis-cache/cache-how-to-monitor.md
[redis-retry]: ../best-practices-retry-service-specific.md#cache-redis-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: ../resource-group-lock-resources.md
[sb-dead-letter-queue]: ../service-bus-messaging/service-bus-dead-letter-queues.md
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: ../service-bus-messaging/service-bus-messaging-exceptions.md
[sb-outages]: ../service-bus/service-bus-outages-disasters.md#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: ../service-bus-messaging/service-bus-partitioning.md
[sb-poison-message]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#poison
[sb-retry]: ../best-practices-retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: ../search/search-traffic-analytics.md
[sql-db-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-db-errors]: ../sql-database/sql-database-develop-error-messages.md#resource-governanance-errors
[sql-db-failover]: ../sql-database/sql-database-geo-replication-failover-portal.md
[sql-db-limits]: ../sql-database/sql-database-resource-limits.md
[sql-db-replication]: ../sql-database/sql-database-geo-replication-overview.md
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: ../storage/storage-redundancy.md
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: ../app-service-web/web-sites-create-web-jobs.md
[web-jobs-shutdown]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful

