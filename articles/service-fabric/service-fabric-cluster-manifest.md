<properties
   pageTitle="設定獨立叢集 |Microsoft Azure"
   description="本文將說明如何設定您的獨立或私人服務布料的轉印圖樣叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="dkshir"/>


# <a name="configuration-settings-for-standalone-windows-cluster"></a>獨立 Windows 叢集組態設定

本文將說明如何設定獨立版服務布料的轉印圖樣叢集使用_**ClusterConfig.JSON**_檔案。 您可以使用此檔案，以指定獨立叢集叢集、 安全性設定，以及網路拓撲而言故障或升級的網域，例如服務布料的轉印圖樣節點其 IP 位址、 不同類型的節點的資訊。

當您[下載獨立版服務布料的轉印圖樣套件](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)，ClusterConfig.JSON 檔案的幾個範例會下載至您公司的電腦。 在他們的名稱時遇到*DevCluster*範例將協助您在同一部電腦，例如邏輯節點的所有三個節點以建立叢集。 登出，至少有一個節點必須標示為主要的節點。 這個叢集適用於開發或測試環境，而不支援生產叢集做。 在他們的名稱，不必*MultiMachine*範例將協助您建立生產品質叢集，以在不同的電腦上的每個節點。 這些叢集主要的節點數目會根據[可靠性層級](#reliability)。

1. *ClusterConfig.Unsecure.DevCluster.JSON*和*ClusterConfig.Unsecure.MultiMachine.JSON*示範如何建立測試或實際執行不安全的叢集分別。 
    
2. *ClusterConfig.Windows.DevCluster.JSON*和*ClusterConfig.Windows.MultiMachine.JSON*顯示如何建立測試或實際執行叢集，使用[Windows 安全性](service-fabric-windows-cluster-windows-security.md)保護。

3. *ClusterConfig.X509.DevCluster.JSON*和*ClusterConfig.X509.MultiMachine.JSON*顯示如何建立測試或實際執行叢集，使用保護[X509 憑證為基礎的安全性](service-fabric-windows-cluster-x509-security.md)。 


現在，我們會檢查各種_**ClusterConfig.JSON**_檔案為] 下方的節。

## <a name="general-cluster-configurations"></a>一般叢集設定
這會包含主要叢集特定設定、 JSON 程式碼片段以下所示。

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

您可以指定給**名稱**變數服務布料的轉印圖樣叢集授予好記的名稱。 **ClusterConfigurationVersion**是叢集; 版本數目每當您在升級您的服務布料的轉印圖樣叢集，您應該增加它。 不過您應該將**apiVersion**預設值。


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>叢集節點
使用 [**節點**] 區段中，依下列顯示的程式碼片段，您可以在您的服務布料的轉印圖樣叢集設定節點。

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

服務布料的轉印圖樣叢集必須包含至少 3 節點。 您可以新增至 [此節的更多節點，依照您的設定。 下表說明每個節點設定的設定。

|**節點設定**|**描述**|
|-----------------------|--------------------------|
|節點名稱不|您可以授權讓節點好記的名稱。|
|iPAddress|瞭解您的節點的 IP 位址，請開啟命令視窗，輸入`ipconfig`。 請注意 IPV4 位址，並將其指派給**iPAddress**變數。|
|nodeTypeRef|每個節點，可以指定不同的節點類型。 [節點類型](#nodetypes)定義以下一節。|
|faultDomain|故障網域啟用叢集系統管理員定義可能會失敗，因為共用的實體相依性同時實體節點。|
|upgradeDomain|升級網域說明組關閉在相同的時間相關的服務布料的轉印圖樣升級的節點。 為不限於任何實體的需求，您可以選擇要指派給哪些升級的網域，哪些節點。| 


## <a name="cluster-properties"></a>叢集內容

[**屬性**] 區段中 ClusterConfig.JSON 用來設定叢集，如下所示。

<a id="reliability"></a>
### <a name="reliability"></a>可靠性 
[ **ReliabilityLevel** ] 區段中定義系統服務可以在主要叢集節點上執行的份的數。 這個動作會增加可靠性這些服務，因此叢集。 您可以將此變數*青銅*、*銀色*、*金色*或*白金*3、 5、 7 或 9 複製這些服務分別。 請參閱下面的範例。

    "reliabilityLevel": "Bronze",
    
請注意，主要的節點執行系統服務的單一複本，因為您需要至少有 3 個主要的節點的*青銅*，*銀色*、*金色*7 及 9*白金*可靠性等級的 5。


### <a name="diagnostics"></a>診斷程式
[ **DiagnosticsStore** ] 區段中，可讓您設定來啟用診斷與疑難排解節點或叢集失敗次數，如下所示的下列程式碼片段的參數。 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**中繼資料**是您叢集診斷程式的描述，而且可以設定依照您的設定。 這些變數協助收集 ETW 追蹤記錄、 損毀傾印以及效能計數器。 閱讀[Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx)和[ETW 追蹤](https://msdn.microsoft.com/library/ms751538.aspx)ETW 追蹤記錄的詳細資訊。 所有的記錄，包括[損毀傾印](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/)和[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)可以會導向至您的電腦上的 [**連接字串**] 資料夾。 您也可以使用*AzureStorage*儲存診斷。 請參閱下方範例的程式碼片段。

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>安全性 
[**安全性**] 區段，則需要安全獨立版服務布料的轉印圖樣叢集。 下列程式碼片段會顯示此節的一部分。

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

**中繼資料**的安全叢集描述，而您可以依照您的安裝程式設定。 **ClusterCredentialType**和**ServerCredentialType**決定安全叢集和節點將實作的類型。 他們可以任一*X509*憑證為基礎的安全性，或*Windows* Azure Active Directory 為基礎的安全性設定。 其餘的 [**安全性**] 區段會根據安全性的類型。 如需在填寫其餘的 [**安全性**] 區段的閱讀[獨立叢集的憑證安全性](service-fabric-windows-cluster-x509-security.md)或[在獨立叢集的 Windows 安全性](service-fabric-windows-cluster-windows-security.md)。


<a id="nodetypes"></a>
### <a name="node-types"></a>節點類型
[ **NodeTypes** ] 區段中說明叢集具有節點的類型。 至少有一個節點類型必須指定叢集，程式碼片段以下所示。 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

**名稱**是此特定節點類型易記的名稱。 [若要建立此節點類型的節點，將其好記的名稱**nodeTypeRef**變數該節點，指定為上述。](#clusternodes) 每個節點類型定義適用於連線結束點。 只要未與任何其他端點這個叢集發生衝突，您可以選擇這些連線端點，任何連接埠號碼。 在多節點叢集，會有一或多個主要節點 （亦即**isPrimary**設為*true*），根據[**reliabilityLevel**](#reliability)。 閱讀[服務布料的轉印圖樣叢集容量規劃考量](service-fabric-cluster-capacity.md) **nodeTypes**和**reliabilityLevel**的值，以及要知道什麼是主要的資訊和非主要節點類型。 

#### <a name="endpoints-used-to-configure-the-node-types"></a>用來設定節點類型的端點

- *clientConnectionEndpointPort*是用戶端連線到叢集，使用用戶端 Api 時使用的連接埠。 
- *clusterConnectionEndpointPort*是節點彼此通訊的連接埠。
- *leaseDriverEndpointPort*是叢集租用驅動程式用來找出是否仍在使用中節點的連接埠。 
- *serviceConnectionEndpointPort*是使用應用程式與服務部署節點，在與服務布料的轉印圖樣上的用戶端特定節點通訊的連接埠。
- *httpGatewayEndpointPort*是用來連線到叢集服務布料的轉印圖樣檔案總管的連接埠。
- *ephemeralPorts*是[OS 所使用的動態連接埠](https://support.microsoft.com/kb/929851)。 服務布料的轉印圖樣會使用這些為應用程式的連接埠的一部分，剩餘會使用的作業系統。 它也會對應到現有的範圍中 OS，此範圍，讓所有的目的，您可以使用範例 JSON 檔案中指定的範圍。 您需要確認的開始及結束連接埠之間的差異至少 255。 
- *applicationPorts*都將使用的服務布料的轉印圖樣應用程式的連接埠。 這些應該*ephemeralPorts*，足以涵蓋的應用程式端點需求的子集合。 服務布料的轉印圖樣會使用這些新的連接埠是必要的以及可開啟下列連接埠的防火牆。 
- *reverseProxyEndpointPort*是選擇性的反向 proxy 端點。 如需詳細資訊，請參閱[服務布料的轉印圖樣反向 Proxy](service-fabric-reverseproxy.md) 。 


### <a name="other-settings"></a>其他設定
[ **FabricSettings** ] 區段中，可讓您設定服務布料的轉印圖樣資料與記錄檔的根目錄。 您可以自訂這些只在初始叢集建立。 請參閱下方範例程式碼片段的這一節。

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

我們建議您為 FabricDataRoot 和 FabricLogRoot 使用非 OS 磁碟機，因為它提供更多可靠性針對 OS 當機。 請注意，是否您自訂資料根目錄，然後記錄根就會在資料根下的一層級。


## <a name="next-steps"></a>後續步驟

依照您獨立叢集安裝設定完成 ClusterConfig.JSON 檔案之後，您可以依照本文部署叢集[建立 Azure 服務布料的轉印圖樣叢集內部部署或雲端中](service-fabric-cluster-creation-for-windows-server.md)再繼續[視覺化叢集服務布料的轉印圖樣檔案總管](service-fabric-visualizing-your-cluster.md)。


