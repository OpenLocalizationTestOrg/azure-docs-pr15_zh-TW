資料工廠是多租用戶服務，請確定客戶的訂閱會防止其他人的工作負載的位置中包含下列的預設限制。 限制許多可輕鬆地引發進位到最大限制您訂閱的連絡支援。 

**資源** | **預設的限制** | **最大限制**
-------- | ------------- | -------------
Azure 的訂閱中的資料工廠 | 50 | [連絡支援人員](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
資料廠內的管線 | 2500 | [連絡支援人員](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
資料集內的資料工廠 | 5000 | [連絡支援人員](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
每個資料集的同時扇形區 | 10 | 10
每個物件的管線物件<sup>1</sup>位元組 | 200 KB | 2000 KB
每個物件的資料集和連結的服務物件<sup>1</sup>位元組 | 100 KB | 2000 KB
HDInsight 視叢集核心內訂閱<sup>2</sup> | 48 | [連絡支援人員](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
雲端資料移動單位<sup>3</sup> | 8 | [連絡支援人員](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
重新計算的管線活動執行 | 1000 | MaxInt （32 位元）

<sup>1</sup>管線與資料集，連結的服務物件代表邏輯群組的工作量。 您可以移動及處理 Azure 資料工廠服務的資料量不與這些物件的限制。 資料工廠是設計用來調整，以處理 petabytes 的資料。

<sup>2</sup>登出訂閱包含資料工廠配置視 HDInsight 核心。 如此一來，上述限制為資料 Factory 強制視 HDInsight 核心限制，不同於 Azure 訂閱相關聯的核心限制。

<sup>3</sup>正在使用雲端資料移動單位 (DMU) 雲端至雲端複製作業中。 則代表 power （CPU、 記憶體和網路資源分派的組合） 的單一資料工廠單位量。 您可以利用更多 DMUs 針對某些案例來達成較高的複製處理量。 請參閱 [詳細資料的[雲端資料移動單位](../../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units)一節。

**資源** | **預設較低的限制** | **最小限制**
-------- | ------------------- | -------------
排程的間隔 | 15 分鐘 | 15 分鐘
重試之間的間隔 | 1 的第二個 | 1 的第二個
再試一次逾時值 | 1 的第二個 | 1 的第二個


### <a name="web-service-call-limits"></a>Web 服務通話限制

Azure 資源管理員有 API 呼叫的限制。 您可以打電話 API [Azure 資源管理員 API 限制](../azure-subscription-service-limits.md#resource-group-limits)內的速度。 


