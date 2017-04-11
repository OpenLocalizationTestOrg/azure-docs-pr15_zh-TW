<properties
    pageTitle="Azure 監視器指標的每個資源類型的支援指標 |Microsoft Azure"
    description="指標可用的每個 Azure 監視器的資源類型的清單。"
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="supported-metrics-with-azure-monitor"></a>Azure 監視器支援的指標

Azure 監視器提供數種方式可與指標，包括其入口網站中的圖表、 存取這些透過 REST API，或查詢進行互動使用 PowerShell 或 CLI。 以下是所有指標的完整清單目前提供 Azure 監視器公制管道的郵件。

> [AZURE.NOTE] 其他標準可在入口網站，或是使用舊版的 Api。 此清單僅包含公用預覽指標提供使用的合併彙算的 Azure 監視器公制的管線公用預覽。

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|CoreCount|計算核心|字數統計|總計|在批次帳戶核心的總數|
|TotalNodeCount|節點計數|字數統計|總計|批次帳戶中的節點總數|
|CreatingNodeCount|建立節點計數|字數統計|總計|建立的節點數目|
|StartingNodeCount|啟動節點計數|字數統計|總計|啟動的節點數目|
|WaitingForStartTaskNodeCount|等待的 「 開始工作節點計數|字數統計|總計|等待開始完成任務的節點數目|
|StartTaskFailedNodeCount|開始工作無法節點計數|字數統計|總計|失敗開始工作的節點數目|
|IdleNodeCount|閒置節點計數|字數統計|總計|閒置的節點數目|
|OfflineNodeCount|離線節點計數|字數統計|總計|離線的節點數目|
|RebootingNodeCount|重新啟動節點計數|字數統計|總計|重新啟動節點的數字|
|ReimagingNodeCount|因節點計數|字數統計|總計|因節點數目|
|RunningNodeCount|執行節點計數|字數統計|總計|執行節點的數字|
|LeavingPoolNodeCount|離開集區節點數目|字數統計|總計|保留資料庫的節點數目|
|UnusableNodeCount|無法使用節點計數|字數統計|總計|無法使用的節點數目|
|TaskStartEvent|任務開始事件|字數統計|總計|開始的任務的總數量|
|TaskCompleteEvent|工作完成事件|字數統計|總計|已完成的任務總數|
|TaskFailEvent|任務會失敗事件|字數統計|總計|失敗狀態中已完成的任務總數|
|PoolCreateEvent|集區建立事件|字數統計|總計|已建立資料庫的總數|
|PoolResizeStartEvent|資料庫大小調整啟動事件|字數統計|總計|啟動的資料庫調整大小的總數|
|PoolResizeCompleteEvent|資料庫大小調整完成事件|字數統計|總計|已完成的資料庫調整大小的總數|
|PoolDeleteStartEvent|集區刪除啟動事件|字數統計|總計|集區刪除開始的總數|
|PoolDeleteCompleteEvent|集區刪除完成事件|字數統計|總計|已完成的資料庫刪除總數|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|connectedclients|連線的用戶端|字數統計|最大值||
|totalcommandsprocessed|總作業|字數統計|總計||
|cachehits|快取點擊率|字數統計|總計||
|cachemisses|快取遺失|字數統計|總計||
|getcommands|取得|字數統計|總計||
|setcommands|設定|字數統計|總計||
|evictedkeys|收回索引鍵|字數統計|總計||
|totalkeys|總索引鍵|字數統計|最大值||
|expiredkeys|過期的按鍵|字數統計|總計||
|usedmemory|使用的記憶體|位元組|最大值||
|usedmemoryRss|使用的記憶體 RSS|位元組|最大值||
|serverLoad|伺服器負載|完成百分比|最大值||
|cacheWrite|快取寫入|BytesPerSecond|最大值||
|cacheRead|快取已讀取|BytesPerSecond|最大值||
|percentProcessorTime|CPU|完成百分比|最大值||
|connectedclients0|連線的用戶端 （晶怪 0）|字數統計|最大值||
|totalcommandsprocessed0|總作業 （晶怪 0）|字數統計|總計||
|cachehits0|快取點擊率 （晶怪 0）|字數統計|總計||
|cachemisses0|快取遺漏 （晶怪 0）|字數統計|總計||
|getcommands0|取得 （晶怪 0）|字數統計|總計||
|setcommands0|設定 （晶怪 0）|字數統計|總計||
|evictedkeys0|收回的鍵 （晶怪 0）|字數統計|總計||
|totalkeys0|總鍵 （節點 0）|字數統計|最大值||
|expiredkeys0|過期的鍵 （晶怪 0）|字數統計|總計||
|usedmemory0|使用的記憶體 （晶怪 0）|位元組|最大值||
|usedmemoryRss0|使用的記憶體 RSS （晶怪 0）|位元組|最大值||
|serverLoad0|伺服器載入 （晶怪 0）|完成百分比|最大值||
|cacheWrite0|快取寫入 （晶怪 0）|BytesPerSecond|最大值||
|cacheRead0|快取讀取 （晶怪 0）|BytesPerSecond|最大值||
|percentProcessorTime0|[CPU （晶怪 0）|完成百分比|最大值||
|connectedclients1|連線的用戶端 （晶怪 1）|字數統計|最大值||
|totalcommandsprocessed1|總作業 （晶怪 1）|字數統計|總計||
|cachehits1|快取點擊率 （晶怪 1）|字數統計|總計||
|cachemisses1|快取遺漏 （晶怪 1）|字數統計|總計||
|getcommands1|取得 （晶怪 1）|字數統計|總計||
|setcommands1|設定 （晶怪 1）|字數統計|總計||
|evictedkeys1|收回的鍵 （晶怪 1）|字數統計|總計||
|totalkeys1|總鍵 （節點 1）|字數統計|最大值||
|expiredkeys1|過期的鍵 （晶怪 1）|字數統計|總計||
|usedmemory1|使用的記憶體 （晶怪 1）|位元組|最大值||
|usedmemoryRss1|使用的記憶體 RSS （晶怪 1）|位元組|最大值||
|serverLoad1|伺服器載入 （晶怪 1）|完成百分比|最大值||
|cacheWrite1|快取寫入 （晶怪 1）|BytesPerSecond|最大值||
|cacheRead1|快取讀取 （晶怪 1）|BytesPerSecond|最大值||
|percentProcessorTime1|[CPU （晶怪 1）|完成百分比|最大值||
|connectedclients2|連線的用戶端 （晶怪 2）|字數統計|最大值||
|totalcommandsprocessed2|總作業 （晶怪 2）|字數統計|總計||
|cachehits2|快取點擊率 （晶怪 2）|字數統計|總計||
|cachemisses2|快取遺漏 （晶怪 2）|字數統計|總計||
|getcommands2|取得 （晶怪 2）|字數統計|總計||
|setcommands2|設定 （晶怪 2）|字數統計|總計||
|evictedkeys2|收回的鍵 （晶怪 2）|字數統計|總計||
|totalkeys2|總鍵 （節點 2）|字數統計|最大值||
|expiredkeys2|過期的鍵 （晶怪 2）|字數統計|總計||
|usedmemory2|使用的記憶體 （晶怪 2）|位元組|最大值||
|usedmemoryRss2|使用的記憶體 RSS （晶怪 2）|位元組|最大值||
|serverLoad2|伺服器載入 （晶怪 2）|完成百分比|最大值||
|cacheWrite2|快取寫入 （晶怪 2）|BytesPerSecond|最大值||
|cacheRead2|快取讀取 （晶怪 2）|BytesPerSecond|最大值||
|percentProcessorTime2|[CPU （晶怪 2）|完成百分比|最大值||
|connectedclients3|連線的用戶端 （晶怪 3）|字數統計|最大值||
|totalcommandsprocessed3|總作業 （晶怪 3）|字數統計|總計||
|cachehits3|快取點擊率 （晶怪 3）|字數統計|總計||
|cachemisses3|快取遺漏 （晶怪 3）|字數統計|總計||
|getcommands3|取得 （晶怪 3）|字數統計|總計||
|setcommands3|設定 （晶怪 3）|字數統計|總計||
|evictedkeys3|收回的鍵 （晶怪 3）|字數統計|總計||
|totalkeys3|總鍵 （節點 3）|字數統計|最大值||
|expiredkeys3|過期的鍵 （晶怪 3）|字數統計|總計||
|usedmemory3|使用的記憶體 （晶怪 3）|位元組|最大值||
|usedmemoryRss3|使用的記憶體 RSS （晶怪 3）|位元組|最大值||
|serverLoad3|伺服器載入 （晶怪 3）|完成百分比|最大值||
|cacheWrite3|快取寫入 （晶怪 3）|BytesPerSecond|最大值||
|cacheRead3|快取讀取 （晶怪 3）|BytesPerSecond|最大值||
|percentProcessorTime3|[CPU （晶怪 3）|完成百分比|最大值||
|connectedclients4|連線的用戶端 （晶怪 4）|字數統計|最大值||
|totalcommandsprocessed4|總作業 （晶怪 4）|字數統計|總計||
|cachehits4|快取點擊率 （晶怪 4）|字數統計|總計||
|cachemisses4|快取遺漏 （晶怪 4）|字數統計|總計||
|getcommands4|取得 （晶怪 4）|字數統計|總計||
|setcommands4|設定 （晶怪 4）|字數統計|總計||
|evictedkeys4|收回的鍵 （晶怪 4）|字數統計|總計||
|totalkeys4|總鍵 （節點 4）|字數統計|最大值||
|expiredkeys4|過期的鍵 （晶怪 4）|字數統計|總計||
|usedmemory4|使用的記憶體 （晶怪 4）|位元組|最大值||
|usedmemoryRss4|使用的記憶體 RSS （晶怪 4）|位元組|最大值||
|serverLoad4|伺服器載入 （晶怪 4）|完成百分比|最大值||
|cacheWrite4|快取寫入 （晶怪 4）|BytesPerSecond|最大值||
|cacheRead4|快取讀取 （晶怪 4）|BytesPerSecond|最大值||
|percentProcessorTime4|[CPU （晶怪 4）|完成百分比|最大值||
|connectedclients5|連線的用戶端 （晶怪 5）|字數統計|最大值||
|totalcommandsprocessed5|總作業 （晶怪 5）|字數統計|總計||
|cachehits5|快取點擊率 （晶怪 5）|字數統計|總計||
|cachemisses5|快取遺漏 （晶怪 5）|字數統計|總計||
|getcommands5|取得 （晶怪 5）|字數統計|總計||
|setcommands5|設定 （晶怪 5）|字數統計|總計||
|evictedkeys5|收回的鍵 （晶怪 5）|字數統計|總計||
|totalkeys5|總鍵 （節點 5）|字數統計|最大值||
|expiredkeys5|過期的鍵 （晶怪 5）|字數統計|總計||
|usedmemory5|使用的記憶體 （晶怪 5）|位元組|最大值||
|usedmemoryRss5|使用的記憶體 RSS （晶怪 5）|位元組|最大值||
|serverLoad5|伺服器載入 （晶怪 5）|完成百分比|最大值||
|cacheWrite5|快取寫入 （晶怪 5）|BytesPerSecond|最大值||
|cacheRead5|快取讀取 （晶怪 5）|BytesPerSecond|最大值||
|percentProcessorTime5|[CPU （晶怪 5）|完成百分比|最大值||
|connectedclients6|連線的用戶端 （晶怪 6）|字數統計|最大值||
|totalcommandsprocessed6|總作業 （晶怪 6）|字數統計|總計||
|cachehits6|快取點擊率 （晶怪 6）|字數統計|總計||
|cachemisses6|快取遺漏 （晶怪 6）|字數統計|總計||
|getcommands6|取得 （晶怪 6）|字數統計|總計||
|setcommands6|設定 （晶怪 6）|字數統計|總計||
|evictedkeys6|收回的鍵 （晶怪 6）|字數統計|總計||
|totalkeys6|總鍵 （節點 6）|字數統計|最大值||
|expiredkeys6|過期的鍵 （晶怪 6）|字數統計|總計||
|usedmemory6|使用的記憶體 （晶怪 6）|位元組|最大值||
|usedmemoryRss6|使用的記憶體 RSS （晶怪 6）|位元組|最大值||
|serverLoad6|伺服器載入 （晶怪 6）|完成百分比|最大值||
|cacheWrite6|快取寫入 （晶怪 6）|BytesPerSecond|最大值||
|cacheRead6|快取讀取 （晶怪 6）|BytesPerSecond|最大值||
|percentProcessorTime6|[CPU （晶怪 6）|完成百分比|最大值||
|connectedclients7|連線的用戶端 （晶怪 7）|字數統計|最大值||
|totalcommandsprocessed7|總作業 （晶怪 7）|字數統計|總計||
|cachehits7|快取點擊率 （晶怪 7）|字數統計|總計||
|cachemisses7|快取遺漏 （晶怪 7）|字數統計|總計||
|getcommands7|取得 （晶怪 7）|字數統計|總計||
|setcommands7|設定 （晶怪 7）|字數統計|總計||
|evictedkeys7|收回的鍵 （晶怪 7）|字數統計|總計||
|totalkeys7|總鍵 （節點 7）|字數統計|最大值||
|expiredkeys7|過期的鍵 （晶怪 7）|字數統計|總計||
|usedmemory7|使用的記憶體 （晶怪 7）|位元組|最大值||
|usedmemoryRss7|使用的記憶體 RSS （晶怪 7）|位元組|最大值||
|serverLoad7|伺服器載入 （晶怪 7）|完成百分比|最大值||
|cacheWrite7|快取寫入 （晶怪 7）|BytesPerSecond|最大值||
|cacheRead7|快取讀取 （晶怪 7）|BytesPerSecond|最大值||
|percentProcessorTime7|[CPU （晶怪 7）|完成百分比|最大值||
|connectedclients8|連線的用戶端 （晶怪 8）|字數統計|最大值||
|totalcommandsprocessed8|總作業 （晶怪 8）|字數統計|總計||
|cachehits8|快取點擊率 （晶怪 8）|字數統計|總計||
|cachemisses8|快取遺漏 （晶怪 8）|字數統計|總計||
|getcommands8|取得 （晶怪 8）|字數統計|總計||
|setcommands8|設定 （晶怪 8）|字數統計|總計||
|evictedkeys8|收回的鍵 （晶怪 8）|字數統計|總計||
|totalkeys8|總鍵 （節點 8）|字數統計|最大值||
|expiredkeys8|過期的鍵 （晶怪 8）|字數統計|總計||
|usedmemory8|使用的記憶體 （晶怪 8）|位元組|最大值||
|usedmemoryRss8|使用的記憶體 RSS （晶怪 8）|位元組|最大值||
|serverLoad8|伺服器載入 （晶怪 8）|完成百分比|最大值||
|cacheWrite8|快取寫入 （晶怪 8）|BytesPerSecond|最大值||
|cacheRead8|快取讀取 （晶怪 8）|BytesPerSecond|最大值||
|percentProcessorTime8|[CPU （晶怪 8）|完成百分比|最大值||
|connectedclients9|連線的用戶端 （晶怪 9）|字數統計|最大值||
|totalcommandsprocessed9|總作業 （晶怪 9）|字數統計|總計||
|cachehits9|快取點擊率 （晶怪 9）|字數統計|總計||
|cachemisses9|快取遺漏 （晶怪 9）|字數統計|總計||
|getcommands9|取得 （晶怪 9）|字數統計|總計||
|setcommands9|設定 （晶怪 9）|字數統計|總計||
|evictedkeys9|收回的鍵 （晶怪 9）|字數統計|總計||
|totalkeys9|總鍵 （節點 9）|字數統計|最大值||
|expiredkeys9|過期的鍵 （晶怪 9）|字數統計|總計||
|usedmemory9|使用的記憶體 （晶怪 9）|位元組|最大值||
|usedmemoryRss9|使用的記憶體 RSS （晶怪 9）|位元組|最大值||
|serverLoad9|伺服器載入 （晶怪 9）|完成百分比|最大值||
|cacheWrite9|快取寫入 （晶怪 9）|BytesPerSecond|最大值||
|cacheRead9|快取讀取 （晶怪 9）|BytesPerSecond|最大值||
|percentProcessorTime9|[CPU （晶怪 9）|完成百分比|最大值||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|NumberOfCalls|API 呼叫總數|字數統計|總計|API 呼叫的總數。|
|NumberOfFailedCalls|失敗的 API 呼叫總數|字數統計|總計|失敗的 API 呼叫總數。|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|百分比 CPU|百分比 CPU|完成百分比|平均值|目前正在使用的虛擬機器移的配置的計算單位的百分比|
|在網路|在網路|位元組|總計|接收所有網路介面上虛擬機器移 （內送流量） 的位元組數字|
|查看網路|查看網路|位元組|總計|縮小上所有的網路介面，來虛擬機器移 （外寄流量） 的位元組數|
|磁碟讀取位元組|磁碟讀取位元組|位元組|總計|從磁碟讀取監視期間的位元組總計|
|磁碟寫入位元組|磁碟寫入位元組|位元組|總計|總寫入磁碟期間監視期間的位元組數|
|閱讀作業秒的磁碟|閱讀作業秒的磁碟|CountPerSecond|平均值|磁碟讀取 IOPS|
|磁碟寫入作業秒|磁碟寫入作業秒|CountPerSecond|平均值|磁碟寫入 IOPS|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|百分比 CPU|百分比 CPU|完成百分比|平均值|目前正在使用的虛擬機器移的配置的計算單位的百分比|
|在網路|在網路|位元組|總計|接收所有網路介面上虛擬機器移 （內送流量） 的位元組數字|
|查看網路|查看網路|位元組|總計|縮小上所有的網路介面，來虛擬機器移 （外寄流量） 的位元組數|
|磁碟讀取位元組|磁碟讀取位元組|位元組|總計|從磁碟讀取監視期間的位元組總計|
|磁碟寫入位元組|磁碟寫入位元組|位元組|總計|總寫入磁碟期間監視期間的位元組數|
|閱讀作業秒的磁碟|閱讀作業秒的磁碟|CountPerSecond|平均值|磁碟讀取 IOPS|
|磁碟寫入作業秒|磁碟寫入作業秒|CountPerSecond|平均值|磁碟寫入 IOPS|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|百分比 CPU|百分比 CPU|完成百分比|平均值|目前正在使用的虛擬機器移的配置的計算單位的百分比|
|在網路|在網路|位元組|總計|接收所有網路介面上虛擬機器移 （內送流量） 的位元組數字|
|查看網路|查看網路|位元組|總計|縮小上所有的網路介面，來虛擬機器移 （外寄流量） 的位元組數|
|磁碟讀取位元組|磁碟讀取位元組|位元組|總計|從磁碟讀取監視期間的位元組總計|
|磁碟寫入位元組|磁碟寫入位元組|位元組|總計|總寫入磁碟期間監視期間的位元組數|
|閱讀作業秒的磁碟|閱讀作業秒的磁碟|CountPerSecond|平均值|磁碟讀取 IOPS|
|磁碟寫入作業秒|磁碟寫入作業秒|CountPerSecond|平均值|磁碟寫入 IOPS|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遙測訊息傳送嘗試|字數統計|總計|傳送給您 IoT 中心嘗試裝置雲端遙測訊息的數目|
|d2c.telemetry.ingress.success|遙測傳送的郵件|字數統計|總計|雲端遙測郵件順利傳送到您 IoT 集線器裝置的數目|
|c2d.commands.egress.complete.success|完成的命令|字數統計|總計|雲端的裝置已順利完成的裝置命令的數字|
|c2d.commands.egress.abandon.success|放棄的命令|字數統計|總計|雲端的裝置已放棄的裝置命令的數字|
|c2d.commands.egress.reject.success|拒絕的命令|字數統計|總計|數字的雲端裝置被拒絕的裝置命令|
|devices.totalDevices|總裝置|字數統計|總計|數字的裝置註冊，讓您 IoT 中心|
|devices.connectedDevices.allProtocol|連線的裝置|字數統計|總計|裝置連接到您 IoT 集線器數目|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|INREQS|傳入的邀請|字數統計|總計|事件] 中心內送郵件處理量命名空間|
|SUCCREQ|成功的要求|字數統計|總計|命名空間的總成功要求|
|FAILREQ|失敗的要求|字數統計|總計|命名空間總失敗的要求|
|SVRBSY|伺服器忙碌錯誤|字數統計|總計|命名空間的伺服器總忙碌錯誤|
|INTERR|內部伺服器錯誤|字數統計|總計|命名空間的總內部伺服器錯誤|
|MISCERR|其他錯誤|字數統計|總計|命名空間總失敗的要求|
|INMSGS|內送郵件|字數統計|總計|命名空間的總內送郵件|
|OUTMSGS|外寄郵件|字數統計|總計|外寄郵件的命名空間總計|
|EHINMBS|每秒內送位元組|BytesPerSecond|總計|事件] 中心內送郵件處理量命名空間|
|EHOUTMBS|每秒的外寄位元組|BytesPerSecond|總計|外寄郵件的命名空間總計|
|EHABL|封存積存郵件|字數統計|總計|命名空間積存中的事件中心封存郵件|
|EHAMSGS|封存郵件|字數統計|總計|事件中心封存命名空間中的郵件|
|EHAMBS|封存郵件處理量|BytesPerSecond|總計|命名空間中的事件中心封存的郵件處理量|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|RunsStarted|開始執行|字數統計|總計|工作流程的執行啟動。|
|RunsCompleted|執行完成|字數統計|總計|工作流程的執行已完成。|
|RunsSucceeded|執行已成功|字數統計|總計|工作流程的執行成功。|
|RunsFailed|無法執行|字數統計|總計|工作流程的執行失敗。|
|RunsCancelled|取消執行|字數統計|總計|工作流程的執行已取消。|
|RunLatency|執行延遲|（秒)|平均值|延遲已完成的工作流程的執行。|
|RunSuccessLatency|執行成功延遲|（秒)|平均值|延遲成功的工作流程的執行。|
|RunThrottledEvents|執行流速控制的事件|字數統計|總計|工作流程 」 動作或觸發程序經流速控制事件。|
|RunFailurePercentage|執行失敗百分比|完成百分比|總計|工作流程的百分比執行失敗。|
|ActionsStarted|啟動的動作 |字數統計|總計|啟動工作流程動作的數目。|
|ActionsCompleted|完成的動作 |字數統計|總計|完成的工作流程動作數目。|
|ActionsSucceeded|成功的動作 |字數統計|總計|成功的工作流程動作數。|
|ActionsFailed|失敗的動作|字數統計|總計|失敗的工作流程動作數目。|
|ActionsSkipped|略過的動作 |字數統計|總計|略過工作流程動作數目。|
|ActionLatency|動作延遲 |（秒)|平均值|已完成的工作流程動作的延遲。|
|ActionSuccessLatency|巨集指令成功延遲 |（秒)|平均值|成功的工作流程動作的延遲。|
|ActionThrottledEvents|巨集指令經流速控制事件|字數統計|總計|工作流程 」 動作數經流速控制事件。|
|TriggersStarted|啟動觸發程序 |字數統計|總計|啟動工作流程引動程序的數目。|
|TriggersCompleted|觸發程序完成 |字數統計|總計|完成的工作流程觸發程序的數字。|
|TriggersSucceeded|成功的觸發程序 |字數統計|總計|成功的工作流程引動程序的數字。|
|TriggersFailed|觸發程序失敗 |字數統計|總計|失敗的工作流程引動程序數目。|
|TriggersSkipped|略過的觸發程序|字數統計|總計|略過工作流程引動程序的數目。|
|TriggersFired|引發的觸發程序 |字數統計|總計|引發的工作流程引動程序的數字。|
|TriggerLatency|觸發程序延遲 |（秒)|平均值|已完成的工作流程觸發程序的延遲。|
|TriggerFireLatency|觸發程序 Fire 延遲 |（秒)|平均值|延遲引發的工作流程引動程序。|
|TriggerSuccessLatency|觸發程序成功延遲 |（秒)|平均值|延遲的成功工作流程引動程序。|
|TriggerThrottledEvents|觸發程序經流速控制事件|字數統計|總計|工作流程觸發程序的數字經流速控制事件。|
|BillableActionExecutions|計費巨集指令執行|字數統計|總計|工作流程動作執行快速計費數目。|
|BillableTriggerExecutions|計費觸發程序執行|字數統計|總計|數字的快速計費的工作流程觸發程序執行。|
|TotalBillableExecutions|總計費執行次數|字數統計|總計|工作流程執行快速計費數目。|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|處理量|處理量|BytesPerSecond|平均值||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|SearchLatency|搜尋延遲|（秒)|平均值|搜尋服務的平均搜尋延遲|
|SearchQueriesPerSecond|每秒的搜尋查詢|CountPerSecond|平均值|搜尋服務的秒的搜尋查詢|
|ThrottledSearchQueriesPercentage|流速控制的搜尋查詢百分比|完成百分比|平均值|已在經流速控制搜尋服務的搜尋查詢的百分比|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|CPUXNS|每個命名空間的 CPU 使用率|完成百分比|最大值|服務匯流排進階版命名空間 CPU 使用公制|
|WSXNS|記憶體大小使用每個命名空間|完成百分比|最大值|服務匯流排進階版命名空間記憶體使用量公制|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|cpu_percent|CPU 百分比|完成百分比|平均值|CPU 百分比|
|physical_data_read_percent|資料 IO 百分比|完成百分比|平均值|資料 IO 百分比|
|log_write_percent|記錄 IO 百分比|完成百分比|平均值|記錄 IO 百分比|
|dtu_consumption_percent|DTU 百分比|完成百分比|平均值|DTU 百分比|
|儲存空間|資料庫總大小|位元組|最大值|資料庫總大小|
|connection_successful|成功的連線|字數統計|總計|成功的連線|
|connection_failed|連線失敗|字數統計|總計|連線失敗|
|blocked_by_firewall|防火牆封鎖|字數統計|總計|防火牆封鎖|
|死結|死結|字數統計|總計|死結|
|storage_percent|資料庫大小的百分比|完成百分比|最大值|資料庫大小的百分比|
|xtp_storage_percent|記憶體內 OLTP 儲存 percent(Preview)|完成百分比|平均值|記憶體內 OLTP 儲存 percent(Preview)|
|workers_percent|同事百分比|完成百分比|平均值|同事百分比|
|sessions_percent|工作階段百分比|完成百分比|平均值|工作階段百分比|
|dtu_limit|DTU 限制|字數統計|平均值|DTU 限制|
|dtu_used|使用 DTU|字數統計|平均值|使用 DTU|
|service_level_objective|服務資料庫的層級的方針|字數統計|總計|服務資料庫的層級的方針|
|dwu_limit|dwu 限制|字數統計|最大值|dwu 限制|
|dwu_consumption_percent|DWU 百分比|完成百分比|平均值|DWU 百分比|
|dwu_used|使用 DWU|字數統計|平均值|使用 DWU|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|cpu_percent|CPU 百分比|完成百分比|平均值|CPU 百分比|
|physical_data_read_percent|資料 IO 百分比|完成百分比|平均值|資料 IO 百分比|
|log_write_percent|記錄 IO 百分比|完成百分比|平均值|記錄 IO 百分比|
|dtu_consumption_percent|DTU 百分比|完成百分比|平均值|DTU 百分比|
|storage_percent|儲存空間百分比|完成百分比|平均值|儲存空間百分比|
|workers_percent|同事百分比|完成百分比|平均值|同事百分比|
|sessions_percent|工作階段百分比|完成百分比|平均值|工作階段百分比|
|eDTU_limit|eDTU 限制|字數統計|平均值|eDTU 限制|
|storage_limit|儲存空間限制|位元組|平均值|儲存空間限制|
|eDTU_used|使用 eDTU|字數統計|平均值|使用 eDTU|
|storage_used|使用儲存空間|位元組|平均值|使用儲存空間|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|ResourceUtilization|SU %使用率|完成百分比|最大值|SU %使用率|
|InputEvents|輸入的事件|字數統計|總計|輸入的事件|
|InputEventBytes|輸入的事件位元組|位元組|總計|輸入的事件位元組|
|LateInputEvents|最遲輸入的事件|字數統計|總計|最遲輸入的事件|
|OutputEvents|輸出事件|字數統計|總計|輸出事件|
|ConversionErrors|資料轉換錯誤|字數統計|總計|資料轉換錯誤|
|錯誤|執行階段錯誤|字數統計|總計|執行階段錯誤|
|DroppedOrAdjustedEvents|順序的事件|字數統計|總計|順序的事件|
|AMLCalloutRequests|函數的要求|字數統計|總計|函數的要求|
|AMLCalloutFailedRequests|失敗函式的要求|字數統計|總計|失敗函式的要求|
|AMLCalloutInputEvents|函數事件|字數統計|總計|函數事件|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|CpuPercentage|CPU 百分比|完成百分比|平均值|CPU 百分比|
|MemoryPercentage|記憶體百分比|完成百分比|平均值|記憶體百分比|
|DiskQueueLength|磁碟佇列長度|字數統計|總計|磁碟佇列長度|
|HttpQueueLength|Http 佇列長度|字數統計|總計|Http 佇列長度|
|BytesReceived|中的資料|位元組|總計|中的資料|
|BytesSent|查看的資料|位元組|總計|查看的資料|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|CpuTime|CPU 時間|（秒)|總計|CPU 時間|
|要求|要求|字數統計|總計|要求|
|BytesReceived|中的資料|位元組|總計|中的資料|
|BytesSent|查看的資料|位元組|總計|查看的資料|
|Http2xx|Http 2xx|字數統計|總計|Http 2xx|
|Http3xx|Http 3xx|字數統計|總計|Http 3xx|
|Http401|Http 401|字數統計|總計|Http 401|
|Http403|Http 403|字數統計|總計|Http 403|
|Http404|Http 404|字數統計|總計|Http 404|
|Http406|Http 406|字數統計|總計|Http 406|
|Http4xx|Http 4xx|字數統計|總計|Http 4xx|
|Http5xx|Http 伺服器錯誤|字數統計|總計|Http 伺服器錯誤|
|MemoryWorkingSet|記憶體工作集|位元組|總計|記憶體工作集|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|平均值|平均記憶體工作集|
|AverageResponseTime|平均回應時間|（秒)|平均值|平均回應時間|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|公制|單位的顯示名稱|單位|彙總類型|描述|
|---|---|---|---|---|
|CpuTime|CPU 時間|（秒)|總計|CPU 時間|
|要求|要求|字數統計|總計|要求|
|BytesReceived|中的資料|位元組|總計|中的資料|
|BytesSent|查看的資料|位元組|總計|查看的資料|
|Http2xx|Http 2xx|字數統計|總計|Http 2xx|
|Http3xx|Http 3xx|字數統計|總計|Http 3xx|
|Http401|Http 401|字數統計|總計|Http 401|
|Http403|Http 403|字數統計|總計|Http 403|
|Http404|Http 404|字數統計|總計|Http 404|
|Http406|Http 406|字數統計|總計|Http 406|
|Http4xx|Http 4xx|字數統計|總計|Http 4xx|
|Http5xx|Http 伺服器錯誤|字數統計|總計|Http 伺服器錯誤|
|MemoryWorkingSet|記憶體工作集|位元組|總計|記憶體工作集|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|平均值|平均記憶體工作集|
|AverageResponseTime|平均回應時間|（秒)|平均值|平均回應時間|

## <a name="next-steps"></a>後續步驟

- [瞭解在 Azure 監視器的指標](./monitoring-overview.md#monitoring-sources)
- [在 [度量建立提醒](./insights-receive-alert-notifications.md)
- [將指標匯出至存放裝置、 事件] 中心內或記錄狀況分析](./monitoring-overview-of-diagnostic-logs.md)
