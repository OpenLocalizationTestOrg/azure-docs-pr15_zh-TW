<properties
   pageTitle="服務布料的轉印圖樣反向 Proxy |Microsoft Azure"
   description="使用服務布料的轉印圖樣的反向 proxy microservices 從內部和外部叢集通訊"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/04/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-reverse-proxy"></a>服務布料的轉印圖樣反向 Proxy

服務布料的轉印圖樣反向 proxy 是反向 proxy 內建到服務布料的轉印圖樣，可讓您處理 microservices 服務布料的轉印圖樣叢集公開 HTTP 端點。

## <a name="microservices-communication-model"></a>Microservices 通訊模型

在服務布料的轉印圖樣 Microservices 通常叢集 VM 的子集上執行，並可以將一個 VM 到另一個各種原因。 因此，可以動態變更 microservices 的結束點。 要傳達給 microservice 的一般模式是下，解決循環播放

1. 解決一開始透過命名服務的服務位置。
2. 連線至服務。
3. 決定連線失敗的原因，以及重新解決必要時的服務位置。

此程序通常涉及文繞圖的用戶端通訊文件庫中實作服務解析度和重試原則重試循環播放。
本主題的詳細資訊，請參閱[與服務通訊](service-fabric-connect-and-communicate-with-services.md)。

### <a name="communicating-via-sf-reverse-proxy"></a>透過 SF 反向 proxy 通訊
服務布料的轉印圖樣反向 proxy 執行叢集內的所有節點。 它代表用戶端上執行的整個服務解析度程序，然後再將轉寄用戶端的要求。 因此叢集上執行的用戶端只可以使用任何用戶端 HTTP 通訊文件庫，與目標服務，透過在本機上相同的節點執行 SF 反向 proxy。

![內部通訊][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>不讓其與 Microservices 從叢集外
Microservices 的預設外部通訊模型已**選擇加入的**位置預設的每個服務不能直接從外部用戶端存取。 [Azure 負載平衡器](../load-balancer/load-balancer-overview.md)是網路之間的邊界 microservices 和外部的用戶端，可以執行網路位址轉譯，將外部要求轉送內部**IP:port**結束點。 若要直接存取外部用戶端 microservice 的端點，請 Azure 負載平衡器必須先設定轉寄的流量到叢集服務使用的每個連接埠。 此外，大部分 microservices (esp.狀態 microservices) 不 live 叢集的所有節點，他們可以在容錯移轉，節點之間移動，在這種情況下，Azure 負載平衡器無法有效判斷目標節點的複本位於要轉寄至的流量。

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>透過 SF 反向 proxy 從叢集外達到 Microservices

而非設定個別服務的連接埠 azure 負載平衡器中，您可以設定 Azure 負載平衡器只 SF 反向 proxy 連接埠。 這個選項可讓叢集以外的用戶端達到透過額外的設定不反向 proxy 叢集內的服務。

![外部通訊][0]

>[AZURE.WARNING] 設定反向 proxy 的連接埠負載平衡器上，將公開 http 端點，為叢集外 addressible 叢集微的所有服務。


## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>URI 格式來處理透過反向 proxy 服務

反向 proxy 會用來識別傳入要求應該轉寄到哪一個服務磁碟分割特定 URI 格式︰

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http(s):**反向 proxy 可以設定為接受 HTTP 或 HTTPS 流量。 若 HTTPS 流量 SSL 終止時所發生的反向 proxy。 轉寄反向 proxy 叢集服務的要求是透過 http。
 - **叢集 FQDN | 內部 IP:**針對外部用戶端，使其位於透過叢集網域 (例如 mycluster.eastus.cloudapp.azure.com) 可以設定反向 proxy。 預設反向 proxy 執行的每個節點，因此內部流量它可以存取任何內部節點 IP (例如 10.0.0.1) 或本機上。
 - **連接埠︰**已指定為反向 proxy 連接埠。 例如︰ 19008。
 - **ServiceInstanceName:**這是您嘗試連絡細的服務的完整部署的服務執行個體名稱 「 布料的轉印圖樣: / 」 配置。 例如，若要達到服務*布料的轉印圖樣: / myapp/myservice/*，您可以使用*myapp/myservice*。
 - **尾碼路徑︰**這是您要連線至服務的實際 URL 路徑。 例如， *myapi/值/加入/3*
 - **PartitionKey:**分割的服務，這是您要連絡的磁碟分割的計算的磁碟分割索引鍵。 請注意，這是**磁碟分割的識別碼 GUID。 這個參數不需要使用單一資料分割配置的服務。
 - **PartitionKind:**服務的資料分割配置。 這可能是 「 Int64Range 」 或 「 名稱 」。 這個參數不需要使用單一資料分割配置的服務。
 - **逾時︰** 指定 http 要求代表用戶端要求服務反向 proxy 所建立的逾時。 這樣的預設值為 60 秒。 這是選用的參數。

### <a name="example-usage"></a>使用範例

例如，讓我們來看服務**布料的轉印圖樣: / MyApp/MyService**開啟 HTTP 上的接聽程式將下列 URL:

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

使用下列資源︰

 - `/index.html`
 - `/api/users/<userId>`

如果服務使用的是單一分割配置， *PartitionKey*和*PartitionKind*查詢字串參數且並非必要，並且服務可以無法透過閘道器為︰

 - 從外部︰`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - 在內部︰`http://localhost:19008/MyApp/MyService`

如果服務會使用一致的 Int64 分割配置，必須使用*PartitionKey*和*PartitionKind*查詢字串參數達到服務的一部分︰

 - 從外部︰`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - 在內部︰`http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

若要達到服務所公開的資源，只要將 URL 中服務名稱後面的資源路徑︰

 - 從外部︰`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - 在內部︰`http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

閘道器會再將這些要求轉寄給該服務的 URL:

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>特殊的處理連接埠共用服務

若要重新解決服務的地址，並再試一次要求時無法存取服務，嘗試應用程式閘道器。 這是一個的主要優點閘道器，因為實作自己服務解析度並解決循環播放，並不需要用戶端程式碼。

通常時無法存取服務，表示服務執行個體或複本會移到不同的節點做為其標準的生命週期的一部分。 這種情況，閘道器可能會收到網路連線錯誤指出端點已不再原本址上開啟。

不過，複本或服務執行個體可以共用主機處理程序，以及可能也會共用連接埠時裝載 http.sys 型網頁伺服器，包括︰

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [ASP.NET 核心 WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [武士](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

在此情況下可能是網頁伺服器適用於主機處理程序和回應的要求，但解決的服務執行個體或複本已不再提供主機上。 在此情況下，閘道器會收到 HTTP 404 回應網頁伺服器。 如此一來，HTTP 404 具有兩個不同的意義︰

 1. 服務地址正確無誤，但不是存在要求使用者的資源。
 2. 服務地址不正確，並要求使用者的資源可能實際上存在上不同的節點。

在第一個的情況下，這是標準的 HTTP 404，其視為使用者錯誤。 不過，在第二個案例中，使用者已要求資源存在，但閘道器找不到它，因為已經移動本身的服務，在此情況下需要重新解析地址，並再試一次閘道器。

閘道器，因此需要區分這兩種情況的方式。 若要讓該差異，請從伺服器提示需要。

 - 根據預設，應用程式閘道假設 #2 的大小寫，並嘗試重新解決並重新發行的要求。
 - 若要將應用程式閘道的案例 #1，服務應傳回下列 HTTP 回應標頭︰

`X-ServiceFabric : ResourceNotFound`

此 HTTP 回應標頭表示標準的 HTTP 404 情況中不存在要求的資源，及閘道器不會嘗試重新解決服務地址。

## <a name="setup-and-configuration"></a>安裝和設定
您可以啟用透過[Azure 資源管理員範本](./service-fabric-cluster-creation-via-arm.md)叢集服務布料的轉印圖樣反向 proxy。

一旦您擁有您想要部署叢集的範本 （從 [範例範本或建立自訂的資源管理員範本） 反向 proxy 可以啟用範本中的下列步驟。

1. 反向 proxy 範本的[參數] 區段](../resource-group-authoring-templates.md)中定義的連接埠。

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. 節點類型物件**叢集**[資源類型] 區段](../resource-group-authoring-templates.md)中的每個指定的連接埠

    為 apiVersion 的之前 ' 2016年-09-01 連接埠由參數名稱***httpApplicationGatewayEndpointPort***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

    針對 apiVersion 的在或之後 ' 2016年-09-01 連接埠由參數名稱***reverseProxyEndpointPort***

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

3. 若要解決反向 proxy 從 azure 叢集外的，設定的步驟 1 中指定的連接埠**azure 負載平衡器規則**。

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. 若要連接埠反向 proxy 設定的 SSL 憑證，請將憑證新增至**叢集**[資源類型] 區段](../resource-group-authoring-templates.md)中的 [httpApplicationGatewayCertificate] 屬性

    為 apiVersion 的之前 ' 2016年-09-01 憑證由參數名稱***httpApplicationGatewayCertificate***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    針對 apiVersion 的在或之後 ' 2016年-09-01 憑證由參數名稱***reverseProxyCertificate***
    
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>後續步驟
 - 請參閱[在 GitHUb 範例專案](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount)中的服務之間的 HTTP 通訊的範例。

 - [使用可靠的服務遠端遠端程序呼叫](service-fabric-reliable-services-communication-remoting.md)

 - [網路可靠的服務中使用 OWIN 的 API](service-fabric-reliable-services-communication-webapi.md)

 - [使用可靠 Services WCF 通訊](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
