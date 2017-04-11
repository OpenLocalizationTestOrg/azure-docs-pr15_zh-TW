<properties
    pageTitle="Azure 資源管理員支援的資料傳輸 Manager |Microsoft Azure "
    description="使用 PowerShell 的流量管理員與 Azure 資源管理員"
    services="traffic-manager"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="azure-resource-manager-support-for-azure-traffic-manager"></a>Azure 資源管理員支援的 Azure 流量 Manager

Azure 資源管理員是慣用的管理介面中 Azure 服務。 Azure 流量管理員設定檔，可以使用 Azure 資源管理員的 Api 和工具來管理。

## <a name="resource-model"></a>資源模型

Azure 流量管理員已使用一組稱為流量管理員設定檔的設定。 這個設定檔包含 DNS 設定、 流量路由設定、 端點監控設定與服務端點流量路由傳送的清單。

以 「 TrafficManagerProfiles 」 類型的資源，表示每個流量管理員設定檔。 在 REST API 層級，每個設定檔 URI 如下︰

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="comparison-with-the-azure-traffic-manager-classic-api"></a>Azure 流量管理員傳統 API 與比較

Azure 資源管理員支援的資料傳輸 Manager 會使用不同的術語比傳統部署模型。 下表顯示資源管理員] 及 [傳統字詞之間的差異︰

| 資源管理員字詞 | 傳統的字詞 |
|-----------------------|--------------|
| 傳輸路由的方法 | 負載平衡的方法 |
| 優先順序方法 | 容錯移轉方法 |
| 加權的方法 | 循環方法 |
| 效能方法 | 效能方法 |

我們根據客戶意見反應，變更改進並減少常見誤解術語。 沒有任何功能的不同。

## <a name="limitations"></a>限制

參照 「 AzureEndpoints 」 類型的端點 Web 應用程式時, 流量管理員結束點只能參考預設 （生產） [Web 應用程式的位置](../app-service-web/web-sites-staged-publishing.md)。 不支援自訂的位置。 因應措施，以自訂的位置，都可以使用 「 ExternalEndpoints 」 類型來設定。

## <a name="setting-up-azure-powershell"></a>PowerShell 的 Azure 設定

這些指示使用 Microsoft Azure PowerShell。 下列文章說明如何安裝和設定 PowerShell 的 Azure。

- [如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)

本文中的範例假設您有現有的資源群組。 您可以建立資源群組使用下列命令︰

```powershell
    New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

>[AZURE.NOTE] Azure 資源管理員必須具備的所有資源群組的位置。 此位置作為預設值建立該資源] 群組中的資源。 不過，由於流量管理員設定檔資源是全域管理員、 不地區，資源群組位置的選項有不會影響在 Azure 流量管理員。

## <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔

若要建立流量管理員設定檔，請使用新增 AzureRmTrafficManagerProfile 指令程式︰

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

下表說明參數︰

| 參數 | 描述 |
|-----------|-------------|
| 名稱 | 流量管理員設定檔資源的資源名稱。 在相同的 [資源] 群組中的設定檔必須具備唯一的名稱。 DNS 名稱 DNS 查詢所用的是此名稱。|
| ResourceGroupName | 包含設定檔資源的 [資源] 群組的名稱。|
| TrafficRoutingMethod | 指定用來決定哪些端點是在回應中傳回 DNS 查詢的流量路由方法。 可能的值是 '效能 」、 「 加權' 或 '優先順序 」。|
| RelativeDnsName | 指定此流量管理員設定檔所提供的 DNS 名稱主機名稱。 此值會結合使用 DNS 網域名稱用於由 Azure 流量管理員表單的設定檔的完整的網域名稱 (FQDN)。 設定 「 contoso 」 的值，例如變成 'contoso.trafficmanager.net'。|
| [TTL] | 指定 DNS 時間存留 (TTL)，以秒。 此 TTL 通知的本機 DNS 解析程式和用戶端 DNS 快取 DNS 回應流量管理員設定檔的時間。|
| MonitorProtocol | 指定要使用監視端點狀況的通訊協定。 可能的值為 「 HTTP 」 和 「 HTTPS 」。|
| MonitorPort | 指定用來監視端點狀況的 TCP 連接埠。|
| MonitorPath | 指定相對於端點網域名稱用於探查端點狀況的路徑。|

Cmdlet Azure 中建立流量管理員設定檔，並傳回 PowerShell 對應的設定檔物件。 此時，設定檔並不會包含任何結束點。 如需有關如何將端點加入流量管理員設定檔的詳細資訊，請參閱[新增流量管理員結束點](#adding-traffic-manager-endpoints)。

## <a name="get-a-traffic-manager-profile"></a>取得流量管理員設定檔

若要擷取的現有的流量管理員設定檔物件，請使用取得 AzureRmTrafficManagerProfle 指令程式︰

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

此指令程式會傳回流量管理員設定檔物件。

## <a name="update-a-traffic-manager-profile"></a>更新流量管理員設定檔

修改流量管理員設定檔所示的 3 個步驟的程序︰

1. 擷取使用取得 AzureRmTrafficManagerProfile 的設定檔，或使用新 AzureRmTrafficManagerProfile 所傳回的設定檔。
2. 修改設定檔。 您可以新增及移除端點，或變更端點或設定檔的參數。 這些變更會離線作業。 您只會變更，表示在設定檔的記憶體中的本機物件。
3. 確認您使用設定 AzureRmTrafficManagerProfile 指令程式的變更。

除了設定檔的 RelativeDnsName 您可以變更所有的設定檔屬性。 若要變更 RelativeDnsName，您必須刪除設定檔和新的設定檔，以新的名稱。

下列範例會示範如何變更設定檔的 [TTL]:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    $profile.Ttl = 300
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

有三種類型的流量管理員結束點︰

1. **Azure 端點**是裝載於 Azure 服務
2. **外部端點**是 Azure 外部裝載的服務
3. **巢狀端點**用來建構流量管理員設定檔的巢狀的階層。 巢狀的端點啟用複雜的應用程式中傳輸路由的進階的設定。

在所有的三種情況下，您可以加入端點兩種方法︰

1. 使用先前所述的步驟 3 程序。 這個方法的優點在於，多個端點，可以變更單一更新中。
2. 使用新增 AzureRmTrafficManagerEndpoint 指令程式。 這個指令程式會新增至現有的資料傳輸管理員設定檔在單一作業的端點。

## <a name="adding-azure-endpoints"></a>加入 Azure 端點

Azure 端點參考裝載於 Azure 服務。 支援三種類型的 Azure 結束點︰

1. Azure Web 應用程式
2. 傳統' 雲端服務 （其中可以包含 PaaS 服務或 IaaS 虛擬機器）
3. Azure PublicIpAddress 資源 （其中可附加至負載平衡器或虛擬機器 NIC）。 PublicIpAddress 必須使用流量管理員中的 DNS 名稱。

每一種情況︰

- 使用 「 targetResourceId 」 參數新增 AzureRmTrafficManagerEndpointConfig 或新增 AzureRmTrafficManagerEndpoint 指定服務。
- TargetResourceId 表示 「 目標 」 和 「 EndpointLocation 」。
- 指定 [粗細] 是選擇性的。 如果設定檔設定為使用 「 加權' 流量路由方法，只用線寬]。 否則，會忽略。 如果指定的值必須是數字 1 和 1000年之間。 預設值是 '1'。
- 指定 「 優先順序 」 是選擇性的。 如果要使用 [優先順序] 流量路由方法設定設定檔之後，只會使用優先順序。 否則，會忽略。 有效的值是從 1 到 1000年有較小的值，表示較高的優先順序。 如果指定一個端點，則必須指定所有的結束點。 如果省略則為從 '1' 開始的預設值會套用端點會列出的順序。

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>範例 1︰ 新增使用新增 AzureRmTrafficManagerEndpointConfig 的 Web 應用程式端點

在此範例中，我們建立流量管理員設定檔，並新增兩個 Web 應用程式端點使用新增 AzureRmTrafficManagerEndpointConfig 指令程式。

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $webapp1 = Get-AzureRMWebApp -Name webapp1
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
    $webapp2 = Get-AzureRMWebApp -Name webapp2
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-a-classic-cloud-service-endpoint-using-new-azurermtrafficmanagerendpoint"></a>範例 2︰ 新增使用新增 AzureRmTrafficManagerEndpoint '傳統' 雲端服務端點

在此範例中，「 傳統 」 的雲端服務端點會新增至流量管理員的設定檔。 在此範例中，我們已指定使用的設定檔與資源的群組名稱，而不是傳遞的設定檔物件的設定檔。 支援這兩種方法。

    $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    New-AzureRmTrafficManagerEndpoint -Name MyCloudServiceEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $cloudService.Id -EndpointStatus Enabled

### <a name="example-3-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>範例 3︰ 新增使用新增 AzureRmTrafficManagerEndpoint publicIpAddress 端點

在此範例中，公用的 IP 位址資源會新增至流量管理員的設定檔。 公用 IP 位址必須設定，請使用 DNS 名稱，並可以繫結的 VM NIC 或負載平衡器。

    $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled

## <a name="adding-external-endpoints"></a>新增外部的端點

流量管理員會使用外部的結束點，將流量導向 Azure 外部裝載的服務。 就跟 Azure 端點，外部可以加入端點請使用 [新增 AzureRmTrafficManagerEndpointConfig 接設定 AzureRmTrafficManagerProfile 或新增 AzureRMTrafficManagerEndpoint。

當指定外部的結束點︰

- 使用 「 目標 」 參數，必須指定端點網域名稱
- 如果使用的 '效能' 流量路由方法，則 「 EndpointLocation 」 需要。 否則，其為選用步驟。 值必須是[有效的 Azure 地區名稱](https://azure.microsoft.com/regions/)。
- [寬度] 和 [優先順序] 是選擇性的。

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>範例 1︰ 新增使用新增 AzureRmTrafficManagerEndpointConfig 和設定 AzureRmTrafficManagerProfile 外部端點

在此範例中，我們可以建立流量管理員設定檔、 新增兩個外部端點，以及認可變更。

    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>範例 2︰ 新增使用新增 AzureRmTrafficManagerEndpoint 外部端點

在此範例中，我們將外部端點新增至現有的設定檔。 設定檔被指定使用的設定檔與資源的群組名稱。

    New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled

## <a name="adding-nested-endpoints"></a>新增 「 巢狀 」 結束點

每個流量管理員設定檔指定單一流量路由方法。 然而，有需要更複雜且流量路由比路由單一流量管理員設定檔所提供的案例。 您可以使用巢狀流量管理員設定檔，合併多個流量路由方法的優點。 巢狀的設定檔可讓您覆寫預設流量管理員行為，以支援更大和較複雜的應用程式部署。 如需詳細範例，請參閱[巢狀流量管理員設定檔](traffic-manager-nested-profiles.md)。

在上層設定檔，使用特定的結束點類型，'NestedEndpoints' 設定巢狀的結束點。 當指定巢狀結束點︰

- 使用 「 targetResourceId 」 參數，必須指定端點
- 如果使用的 '效能' 流量路由方法，則 「 EndpointLocation 」 需要。 否則，其為選用步驟。 值必須是[有效的 Azure 地區名稱](http://azure.microsoft.com/regions/)。
- '粗細 」 和 「 優先順序 」 是選用，先 Azure 結束點。
- 「 MinChildEndpoints 」 參數是選擇性的。 預設值是 '1'。 如果使用的結束點的數目低於此閥值，子設定檔 '降低'，diverts 上層設定檔中的其他端點的流量會考慮上層設定檔。

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>範例 1︰ 新增使用新增 AzureRmTrafficManagerEndpointConfig 和設定 AzureRmTrafficManagerProfile 的巢狀的端點

在此範例中，我們可以建立新的流量管理員子系和上層設定檔，以巢狀的結束點的子加入父項並認可變更。

    $child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

求在此範例中，我們沒有子或上層設定檔新增任何其他結束點。

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>範例 2︰ 新增使用新增 AzureRmTrafficManagerEndpoint 的巢狀的端點

在此範例中，我們新增現有的子設定檔以巢狀的端點至現有的父項設定檔。 設定檔被指定使用的設定檔與資源的群組名稱。

    $child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2

## <a name="update-a-traffic-manager-endpoint"></a>更新流量管理員端點

有兩種方式更新現有的資料傳輸管理員結束點︰

1. 取得使用取得 AzureRmTrafficManagerProfile 流量管理員設定檔、 更新設定檔] 中的端點屬性和認可使用設定 AzureRmTrafficManagerProfile 的變更。 這個方法的優點能夠更新一次一個以上的端點。
2. 取得使用取得 AzureRmTrafficManagerEndpoint 流量管理員結束點，更新 [結束點] 屬性，認可使用設定 AzureRmTrafficManagerEndpoint 的變更。 這個方法是簡單，，因為它不需要將設定檔中的端點陣列編製索引作業。

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>範例 1︰ 更新使用取得 AzureRmTrafficManagerProfile 和設定 AzureRmTrafficManagerProfile 的端點

在此範例中，我們可以修改現有的設定檔中的兩個端點上優先順序。

    $profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
    $profile.Endpoints[0].Priority = 2
    $profile.Endpoints[1].Priority = 1
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>範例 2︰ 更新使用取得 AzureRmTrafficManagerEndpoint 和設定 AzureRmTrafficManagerEndpoint 端點

在此範例中，我們可以修改現有的設定檔中的單一端點的粗細。

    $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
    $endpoint.Weight = 20
    Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>啟用及停用端點和設定檔

流量管理員允許個別的端點，以啟用及停用，以及允許啟用及停用的整個的設定檔。
這些變更可取得和更新/設定來端點或設定檔的資源。 若要簡化這些共同作業，他們也支援透過專用的 cmdlet。

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>範例 1︰ 啟用及停用流量管理員設定檔

若要啟用流量管理員設定檔，請使用啟用 AzureRmTrafficManagerProfile。 可以使用的設定檔物件指定設定檔。 可以傳遞的設定檔的物件，透過管道的郵件，或使用 「-TrafficManagerProfile' 參數。 在此範例中，我們可以指定的設定檔的設定檔與資源的群組名稱。

```powershell
    Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

若要停用流量管理員設定檔︰

```powershell
    Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

停用 AzureRmTrafficManagerProfile cmdlet 提示確認。 可以隱藏此提示中，使用 「-強制 ' 參數。

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>範例 2︰ 啟用及停用流量管理員端點

若要啟用流量管理員端點，請使用啟用 AzureRmTrafficManagerEndpoint。 有兩種方式來指定端點

1. 使用傳遞管道透過 TrafficManagerEndpoint 物件，或使用 「-TrafficManagerEndpoint' 參數
2. 使用結束點名稱、 結束點類型、 設定檔名稱和資源群組名稱︰

```powershell
    Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

同樣地，若要停用流量管理員結束點︰

```powershell
     Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

如同停用 AzureRmTrafficManagerProfile，停用 AzureRmTrafficManagerEndpoint cmdlet 提示確認。 可以隱藏此提示中，使用 「-強制 ' 參數。

## <a name="delete-a-traffic-manager-endpoint"></a>刪除流量管理員端點

若要移除個別的結束點，使用 [移除 AzureRmTrafficManagerEndpoint 指令程式︰

```powershell
    Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

這個 cmdlet 提示確認。 可以隱藏此提示中，使用 「-強制 ' 參數。

## <a name="delete-a-traffic-manager-profile"></a>刪除流量管理員設定檔

若要刪除流量管理員設定檔，請使用移除 AzureRmTrafficManagerProfile cmdlet，指定的設定檔與資源的群組名稱︰

```powershell
    Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

這個 cmdlet 提示確認。 可以隱藏此提示中，使用 「-強制 ' 參數。

若要刪除的設定檔也可以指定使用的設定檔物件︰

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

也可傳送此順序︰

```powershell
    Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>後續步驟

[監控流量管理員](traffic-manager-monitoring.md)

[流量管理員效能考量](traffic-manager-performance-considerations.md)
