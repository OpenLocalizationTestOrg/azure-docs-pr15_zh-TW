<properties
   pageTitle="設定負載平衡器 TCP 閒置逾時 |Microsoft Azure"
   description="設定負載平衡器 TCP 閒置逾時"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>設定 Azure 負載平衡器的 TCP 閒置逾時設定

在預設設定，Azure 負載平衡器具有 4 分鐘閒置逾時設定。 如果一段閒置時間長於逾時值，不保證有的用戶端與您的雲端服務之間保留的 TCP 或 HTTP 工作階段。

關閉連線後，您的用戶端應用程式可能會收到下列錯誤訊息: 「 基礎連接已關閉︰ 已關閉的伺服器連線到保持運作。 」

常見的做法是使用保持 TCP。 此作法會保持連線作用中的較長期間。 如需詳細資訊，請參閱以下[.NET 範例](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。 保持啟用，封包傳送期間閒置連線。 這些保持封包確定並未達到閒置逾時值連線維護長的時間。

這項設定適用於僅連入連線。 若要避免遺失連線，您必須設定保持 TCP 小於閒置逾時設定的間隔，或增加閒置逾時值。 若要支援這種情況，我們已新增支援可設定閒置逾時。 您現在可以將其 4 到 30 分鐘的持續時間。

TCP 保持適用於電池耐用年限不限制式的案例。 不建議的行動應用程式。 使用保持在行動應用程式中的 TCP 消耗裝置電池速度更快。

![TCP 逾時](./media/load-balancer-tcp-idle-timeout/image1.png)

下列各節說明如何變更虛擬機器中的閒置逾時設定和雲端服務。

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>針對您執行個體層級的公用 IP 為 15 分鐘設定 TCP 逾時

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes`為選用步驟。 如果沒有設定，預設會出現逾時就會是 4 分鐘。 接受逾時範圍是 4 到 30 分鐘。

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>建立端點 Azure 虛擬機器上時，設定閒置逾時

若要變更端點逾時設定，請使用下列步驟︰

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

若要擷取您閒置逾時設定，請使用下列命令︰

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>設定負載平衡端點設定 TCP 逾時

如果端點負載平衡端點集的一部分，必須在負載平衡端點設定設定 TCP 逾時。 例如︰

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## <a name="change-timeout-settings-for-cloud-services"></a>變更逾時設定的雲端服務

您可以使用 Azure SDK 更新您的雲端服務。 您可以進行雲端服務的端點的設定.csdef 檔案中。 更新以供部署雲端服務的 TCP 逾時，您必須部署升級。 例外狀況的是 TCP 逾時，如果要指定僅適用於公用 IP。 公用 IP 設定在.cscfg 的檔案，及透過部署更新和升級進行更新。

結束點設定.csdef 變更為︰

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

在公用 IPs 逾時設定.cscfg 變更為︰

    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
        <InstanceAddress roleName="VMRolePersisted">
        <PublicIPs>
          <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
        </PublicIPs>
        </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## <a name="rest-api-example"></a>REST API 範例

您可以使用的服務管理 API 設定 TCP 閒置逾時。 請確定`x-ms-version`版本設定標頭`2014-06-01`或更新版本。 更新指定負載平衡輸入結束點上所有的虛擬機器部署中的設定。

### <a name="request"></a>要求

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>回應

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>

## <a name="next-steps"></a>後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定具網際網路負載平衡器](load-balancer-get-started-internet-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)
