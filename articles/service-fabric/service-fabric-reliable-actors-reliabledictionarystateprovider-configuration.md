<properties
   pageTitle="Azure 服務布料的轉印圖樣可靠動作項目 ReliableDictionaryActorStateProvider 設定的概觀 |Microsoft Azure"
   description="深入了解設定 Azure 服務布料的轉印圖樣類型 ReliableDictionaryActorStateProvider 的 [狀態動作項目。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/18/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>設定可靠的動作項目-ReliableDictionaryActorStateProvider
您可以藉由變更產生 Visual Studio 套件根資料夾底下的 [設定中指定的動作項目 settings.xml 檔案修改 ReliableDictionaryActorStateProvider 的預設設定。

Azure 服務布料的轉印圖樣執行階段尋找 settings.xml 檔案中的預先定義的章節名稱，並建立基本的執行階段元件時使用的設定值。

>[AZURE.NOTE] 執行**不**刪除或修改下列設定 Visual Studio 解決方案，則會產生 settings.xml 檔案中的區段名稱。

此外，還有通用設定會影響 ReliableDictionaryActorStateProvider 的設定。

## <a name="global-configuration"></a>通用設定

通用設定指定 KtlLogger] 區段下叢集叢集清單中。 允許共用的記錄檔的位置和大小以及全域記憶體限制記錄器所使用的設定。 請注意，叢集資訊清單中的變更會影響所有服務的使用 ReliableDictionaryActorStateProvider 和可靠的狀態服務。

叢集資訊清單是單一的 XML 檔案保留設定] 和 [套用至所有節點和叢集服務的設定。 檔案通常稱為 ClusterManifest.xml。 您可以看到叢集資訊叢集使用取得 ServiceFabricClusterManifest powershell 命令清單。

### <a name="configuration-names"></a>設定名稱

|名稱|單位|預設值|註解|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Kb|8388608|KB 配置核心模式記錄器寫入緩衝記憶體集區最小值。 此記憶體集區來快取寫入到磁碟之前的陳述式資訊。|
|WriteBufferMemoryPoolMaximumInKB|Kb|沒有限制|可以放大的記錄器撰寫緩衝記憶體集區的大小上限。|
|SharedLogId|GUID|""|指定唯一的 GUID 用來識別叢集未指定 SharedLogId 其服務特定設定中的所有節點上的所有可靠服務所使用的預設共用的記錄檔。 如果指定 SharedLogId，SharedLogPath 必須也指定。|
|SharedLogPath|完整的路徑名稱|""|指定共用的記錄檔位置使用叢集中沒有指定 SharedLogPath 其服務特定設定中的所有節點上的所有可靠服務的完整的路徑。 不過，如果有指定 SharedLogPath，然後 SharedLogId 還必須指定。|
|SharedLogSizeInMB|Mb|8192|指定 MB 的磁碟空間靜態配置共用的記錄數目。 值必須是 2048年或更大。|

### <a name="sample-cluster-manifest-section"></a>範例叢集資訊清單] 區段
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>註解
記錄器具有從 [可用的所有可靠服務節點之前寫入專用的記錄檔相關聯的可靠的服務複本快取狀態資料的非分頁的核心記憶體配置的記憶體全域集區。 資料庫大小是由 WriteBufferMemoryPoolMinimumInKB 和 WriteBufferMemoryPoolMaximumInKB 設定控制。 WriteBufferMemoryPoolMinimumInKB 指定初始此記憶體集區的大小和最低的可能縮小記憶體集區的大小。 WriteBufferMemoryPoolMaximumInKB 是最高記憶體集區可能會變大的大小。 開啟的每個可靠的服務複本可能會依最 WriteBufferMemoryPoolMaximumInKB 取決於系統量增加記憶體集區的大小。 如果有更多記憶體集區大於可用的記憶體要求，請等到記憶體可用，將會延遲的記憶體要求。 因此如果寫入緩衝記憶體集區太小特定的設定，然後效能可能會降低。

叢集中定義的 GUID 及所有節點的預設共用記錄的位置，會永遠一起使用 SharedLogId 和 SharedLogPath 設定。 預設共用的記錄用於未指定設定在特定的服務 settings.xml 中的所有可靠服務。 為獲得最佳效能，應只適用於共用的記錄檔若要降低競爭的磁碟上放置共用的記錄檔。

SharedLogSizeInMB 指定預設共用記錄所有節點上預先配置的磁碟空間的量。  SharedLogId 和 SharedLogPath 不需要在 SharedLogSizeInMB 指定順序中加以指定。

## <a name="replicator-security-configuration"></a>複寫安全性設定
複寫的安全性設定用來保護複寫時使用的通訊通道。 這表示服務無法看到其他人複寫流量，確保也安全的進行高度可用的資料。
根據預設，空白的安全性設定] 區段會防止複寫安全性。

### <a name="section-name"></a>章節名稱
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>複寫器設定
複寫器設定用來設定複寫所負責的動作項目狀態提供者狀態可靠的複製及保存在本機的狀態。
預設的設定 Visual Studio 範本會產生，並且應該可以滿足。 本節說話有關可改善複寫的其他設定。

### <a name="section-name"></a>章節名稱
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>設定名稱

|名稱|單位|預設值|註解|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|（秒)|0.015|用在後收到操作，才能傳送到第二個等候複寫返回確認主要的時段。 任何其他通知，才能傳送處理這個時間間隔內的作業方式回應傳送。||
|ReplicatorEndpoint|N/A|沒有預設值-必要的參數|設定 IP 位址和主要/次要複寫會用來與其他購複本通訊的連接埠。 此應參考服務資訊清單中的 TCP 資源端點。 請參閱[服務資訊清單資源](service-fabric-service-manifest-resources.md)，若要閱讀更多關於定義服務資訊清單中的端點。 |
|MaxReplicationMessageSize|位元組|50 MB|複寫資料可傳輸單一郵件大小上限。|
|MaxPrimaryReplicationQueueSize|數字的作業|8192|最大數目主要佇列中作業的詳細資訊。 運算釋放之後主要複寫從所有次要購收到通知。 此值必須是 2 的大於 64 和次方。|
|MaxSecondaryReplicationQueueSize|數字的作業|16384|在第二個佇列中作業的數目上限。 釋放作業之後高度可透過持續性的狀態。 此值必須是 2 的大於 64 和次方。|
|CheckpointThresholdInMB|MB|200|之後的狀態是檢查記錄檔的空間量。|
|MaxRecordSizeInKB|KB|1024|複寫可能寫入記錄檔中的最大記錄大小。 此值必須是 4 或大於 16 的倍數。|
|OptimizeLogForLowerDiskUsage|布林值|true|為 true 時，記錄設定，讓使用 NTFS 疏鬆檔案建立複本的專用的記錄檔。 此降低實際的磁碟空間的使用檔案。 為 false，檔案會建立與固定的配置，提供最佳的寫入的效能。|
|SharedLogId|guid|""|指定唯一的 guid 用來識別共用的記錄檔使用這個複本。 一般而言，服務不應該使用這項設定。 不過，如果有指定 SharedLogId，然後 SharedLogPath 還必須指定。|
|SharedLogPath|完整的路徑名稱|""|指定建立共用的記錄檔的複本的位置的完整的路徑。 一般而言，服務不應該使用這項設定。 不過，如果有指定 SharedLogPath，然後 SharedLogId 還必須指定。|


## <a name="sample-configuration-file"></a>設定檔範例

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>註解
BatchAcknowledgementInterval 參數控制複寫延遲。 '0' 值產生的最低可能延遲，犧牲處理量 （如需認可訊息必須傳送和處理，每個包含較少的通知）。
BatchAcknowledgementInterval 較大的值，較高的整體複寫處理能力，犧牲較高的作業延遲。 直接轉換的交易認可延遲。

CheckpointThresholdInMB 參數控制複寫可用來儲存複本的專用的記錄檔中的狀態資訊的空間的量。 此增加預設為較高值，可能會導致當新的複本會新增至設定更快速地重新設定時間。 這是因為會因為詳細歷程記錄的記錄檔中作業的可用性發生部分狀態傳輸。 這可能可以增加複本的修復時間後當機。

如果您設定 OptimizeForLowerDiskUsage 為 true 時，記錄檔案的空間會覆能夠讓作用中的複本可以儲存更多的陳述式資訊在其記錄檔]，而非使用中的複本會使用較少的磁碟空間。 這可讓裝載的節點上的多個複本。 如果您可以設定 OptimizeForLowerDiskUsage 為 false，則更快速地記錄檔寫入陳述式資訊。

MaxRecordSizeInKB 設定定義記錄可寫入複寫的記錄檔的大小上限。 在大部分情況下，預設 1024 KB 記錄大小是最佳選擇。 不過，如果服務導致較大資料項目組件的狀態資訊，此值可能需要增加。 沒有進行 MaxRecordSizeInKB 小於 1024，為較小的記錄使用只較小的記錄所需的空間的好處。 我們預期此值必須只在某些情況中進行變更。

[SharedLogId] 及 [SharedLogPath 設定一律來共同使用另一個共用的記錄從預設共用記錄節點的服務。 最佳的效率，盡可能與許多服務應該指定相同的共用記錄檔。 只適用於共用的記錄檔，以減少競爭頭移動的磁碟上應放置共用的記錄檔。 我們預期會需要只在某些情況變更這些值。
