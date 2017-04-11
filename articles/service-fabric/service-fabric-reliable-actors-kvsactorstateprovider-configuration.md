<properties
   pageTitle="Azure 服務布料的轉印圖樣可靠動作項目 KVSActorStateProvider 設定的概觀 |Microsoft Azure"
   description="深入了解設定 Azure 服務布料的轉印圖樣類型 KVSActorStateProvider 的 [狀態動作項目。"
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
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>設定可靠的動作項目-KVSActorStateProvider
您可以藉由變更 settings.xml 檔案產生 Microsoft Visual Studio 套件根資料夾底下的 [設定中指定的動作項目修改 KVSActorStateProvider 的預設設定。

Azure 服務布料的轉印圖樣執行階段尋找 settings.xml 檔案中的預先定義的章節名稱，並建立基本的執行階段元件時使用的設定值。

>[AZURE.NOTE] 執行**不**刪除或修改下列設定 Visual Studio 解決方案，則會產生 settings.xml 檔案中的區段名稱。

## <a name="replicator-security-configuration"></a>複寫安全性設定
複寫的安全性設定用來保護複寫時使用的通訊通道。 這表示服務無法看到其他人複寫流量，確保資料的高度可用也安全。
根據預設，空白的安全性設定] 區段會防止複寫安全性。

### <a name="section-name"></a>章節名稱
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>複寫器設定
複寫器組態設定會負責進行的動作項目狀態提供者狀態可靠複寫器。
預設的設定 Visual Studio 範本會產生，並且應該可以滿足。 本節說話有關可改善複寫的其他設定。

### <a name="section-name"></a>章節名稱
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>設定名稱

|名稱|單位|預設值|註解|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|（秒)|0.015|用在後收到操作，才能傳送到第二個等候複寫返回確認主要的時段。 任何其他通知，才能傳送處理這個時間間隔內的作業方式回應傳送。|
|ReplicatorEndpoint|N/A|沒有預設值-必要的參數|設定 IP 位址和主要/次要複寫會用來與其他購複本通訊的連接埠。 此應參考服務資訊清單中的 TCP 資源端點。 請參閱[服務資訊清單資源](service-fabric-service-manifest-resources.md)，若要閱讀更多關於定義服務資訊清單中的端點。 |
|RetryInterval|（秒)|5|之後複寫重新傳送郵件如果不會收到通知作業期間。|
|MaxReplicationMessageSize|位元組|50 MB|複寫資料可傳輸單一郵件大小上限。|
|MaxPrimaryReplicationQueueSize|數字的作業|1024|最大數目主要佇列中作業的詳細資訊。 運算釋放之後主要複寫從所有次要購收到通知。 此值必須是 2 的大於 64 和次方。|
|MaxSecondaryReplicationQueueSize|數字的作業|2048|在第二個佇列中作業的數目上限。 釋放作業之後高度可透過持續性的狀態。 此值必須是 2 的大於 64 和次方。|

## <a name="store-configuration"></a>存放區設定
儲存設定用來設定本機存放區中，用來保留正在複寫的狀態。
預設的設定 Visual Studio 範本會產生，並且應該可以滿足。 本節說話有關可改善本機存放區中的其他設定。

### <a name="section-name"></a>章節名稱
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>設定名稱

|名稱|單位|預設值|註解|
|----|----|-------------|-------|
|MaxAsyncCommitDelayInMilliseconds|毫秒為單位|200|設定批次處理長期本機存放區認可區間的最大值。|
|MaxVerPages|頁數|16384|在本機的版本頁面數目上限儲存資料庫。 它會決定未完成的交易的最大數目。|

## <a name="sample-configuration-file"></a>設定檔範例

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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
