<properties
   pageTitle="疑難排解應用程式閘道不正確的閘道 (502) 錯誤 |Microsoft Azure"
   description="瞭解如何疑難排解應用程式閘道器 502 錯誤"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>在 [應用程式的閘道不正確的閘道錯誤疑難排解

## <a name="overview"></a>概觀

設定後 Azure 應用程式閘道器，其中一個錯誤的使用者可能會遇到的是 「 伺服器錯誤︰ 502-網頁伺服器時，收到不正確的回應做為閘道或 proxy 伺服器 」。 這可能會發生下列主要原因︰

- Azure 應用程式閘道器的後端資料庫未設定或空白。
- Vm 或執行個體中 VM 縮放比例設定都健全。
- 預設狀況探查沒有回應後端 Vm 或 VM 比例組執行個體。
- 自訂的狀況檢查無效或不正確設定。
- 要求使用者要求的逾時或連線問題。

## <a name="empty-backendaddresspool"></a>空白 BackendAddressPool

### <a name="cause"></a>原因

如果應用程式閘道沒有 Vm 或 VM 縮放比例設定位址後端資料庫中的設定，它不能將任何客戶要求，並會擲回不正確的閘道錯誤。

### <a name="solution"></a>解決方案

確定沒有空白的位址後端資料庫。 這可以透過 PowerShell、 CLI 或入口網站。

    Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

從先前的 cmdlet 輸出包含非空白的後端位址集區。 以下是範例位置，會傳回這兩個集區設定的後端 Vm FQDN 或 IP 位址。 佈建 BackendAddressPool 狀態必須是 「 成功 」。
    
    BackendAddressPoolsText: 
            [{
                "BackendAddresses": [{
                    "ipAddress": "10.0.0.10",
                    "ipAddress": "10.0.0.11"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool01",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
            }, {
                "BackendAddresses": [{
                    "Fqdn": "xyx.cloudapp.net",
                    "Fqdn": "abc.cloudapp.net"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool02",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
            }]


## <a name="unhealthy-instances-in-backendaddresspool"></a>不佳的執行個體中 BackendAddressPool

### <a name="cause"></a>原因

如果不佳的 BackendAddressPool 的所有例項，應用程式閘道將不會包含任何後端要傳送使用者要求。 狀況良好後端執行個體，但沒有必要的應用程式部署時，這也可能是大小寫。

### <a name="solution"></a>解決方案

確定執行個體是健全應用程式的設定正確。 檢查是否可以從另一個 VM 中相同的 VNet 偵測 （ping） 來回應後端執行個體。 如果設定公用的結束點，請確定 web 應用程式的瀏覽器要求容易維護。

## <a name="problems-with-default-health-probe"></a>預設狀況探查的問題

### <a name="cause"></a>原因

502 錯誤也可以是預設狀況探查無法達到後端 Vm 經常標記。 當應用程式閘道執行個體佈建後時，就會自動設定預設来使用的 BackendHttpSetting 屬性每個 BackendAddressPool 狀況檢查。 沒有使用者輸入，才能設定這個探查。 具體來說，是設定負載平衡規則，關聯是由 BackendHttpSetting 與 BackendAddressPool 之間。 預設探查設定為每項關聯，並應用程式閘道啟始定期狀況核取連線在 BackendHttpSetting 項目中指定的連接埠 BackendAddressPool 中每個執行個體。 下表列出預設狀況探查相關聯的值。


|這個屬性 | 值 | 描述|
|---|---|---|
| 這個 URL| http://127.0.0.1/ | URL 路徑 |
| 間隔 | 30 | 探查間隔秒數 |
| 逾時  | 30 | 秒數探查逾時 |
| 不佳的閥值 | 3 | 檢查重試計數。 後端伺服器是之後標示為 [向下探查連續失敗次數達到不佳的閥值。 |

### <a name="solution"></a>解決方案

- 請確定預設網站設定妥當，並在 127.0.0.1 接聽。
- 如果 BackendHttpSetting 指定連接埠 80 以外，應該要聆聽的連接埠設定預設網站。
- 通話 http://127.0.0.1:port 應傳回 200 HTTP 結果程式碼。 這應該會傳回 30 秒逾時期間內。
- 請確定連接埠設定已開啟，而且沒有任何防火牆規則或 Azure 網路安全性群組，其封鎖送或外寄流量的連接埠設定。
- 如果 FQDN 的公用 IP 使用 Azure 傳統 Vm 或雲端服務，請確定開啟的相對應的[結束點](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)。
- 如果 VM 設定透過 Azure 資源管理員，並且是在應用程式的閘道器的部署位置 VNet 以外，[網路安全性群組](../virtual-network/virtual-networks-nsg.md)必須設定為允許存取所需的連接埠。


## <a name="problems-with-custom-health-probe"></a>自訂的狀況檢查問題

### <a name="cause"></a>原因

自訂健康探查可讓更多的彈性探查行為預設值。 使用自訂的檢查，使用者可以設定探查間隔、 URL，及路徑，若要測試，以及多少失敗的回覆標示為不佳的後端資料庫執行個體之前，可接受。 新增下列其他屬性。

|這個屬性| 描述|
|---|---|
| 名稱 | 探查的名稱。 若要參照的後端 HTTP 設定探查會使用此名稱。 |
| 通訊協定 | 用來傳送探查通訊協定。 探查會使用的後端 HTTP 設定中所定義的通訊協定 |
| Host （主機) |  若要傳送探查主機名稱。 多網站上的應用程式的閘道器設定時，才適用。 這是不同的 VM 主機名稱。  |
| 路徑 | 探查相對路徑。 有效的路徑開頭是從第 '/'。 探查會傳送至\<通訊協定\>://\<主機\>:\<連接埠\>\<路徑\> |
| 間隔 | 檢查間隔秒數。 這是兩個連續探查之間的時間間隔。|
| 逾時 | 檢查逾時秒數。 探查標示為失敗如果有效的回應不會收到此逾時期間內。 |
| 不佳的閥值 | 檢查重試計數。 後端伺服器是之後標示為 [向下探查連續失敗次數達到不佳的閥值。 |


### <a name="solution"></a>解決方案

驗證的自訂狀況探查已正確設定為上述的表格。 除了之前的疑難排解步驟，也請確定下列動作︰

- 請確定依照[指南](application-gateway-create-probe-ps.md)探查已指定正確。
- 如果應用程式的閘道器設定一個網站，預設主機名稱應指定為 '127.0.0.1'，否則在中設定自訂探查除非。
- 確定 http:// 呼叫\<主機\>:\<連接埠\>\<路徑\>傳回 200 HTTP 結果程式碼。
- 確定間隔逾時與 UnhealtyThreshold 可接受的範圍內。

## <a name="request-time-out"></a>要求逾時

### <a name="cause"></a>原因

收到使用者要求時，應用程式閘道設定的規則套用至要求，並將其傳送到後端資料庫執行個體。 等待可設定之時間間隔的後端執行個體的回應。 根據預設，此間隔時間為**30 秒**。 如果應用程式閘道沒有收到回應後端應用程式，在此間隔] 中，使用者要求就會看到 502 的錯誤。

### <a name="solution"></a>解決方案

應用程式的閘道器可讓使用者設定此設定，透過 BackendHttpSetting，然後套用至不同的資料庫。 不同的 BackendHttpSetting，因此不同的要求逾時設定，可以有不同的後端資料庫。

    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟[支援票證](https://azure.microsoft.com/support/options/)。
