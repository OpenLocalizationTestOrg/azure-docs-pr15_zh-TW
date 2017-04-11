<properties
   pageTitle="設定負載平衡器分配模式 |Microsoft Azure"
   description="如何設定支援來源 IP 相關性 Azure 負載平衡器分配模式"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="configure-the-distribution-mode-for-load-balancer"></a>設定負載平衡器的通訊群組模式

## <a name="hash-based-distribution-mode"></a>雜湊分配模式

預設的通訊群組演算法是 5 表示 tuple (來源 IP，來源連接埠] 目的地 IP，目的地連接埠] 通訊協定類型) 雜湊對應到可用的伺服器的流量。 只在傳輸工作階段中提供神奇結果。 在相同的工作階段中的封包會導向至相同的資料中心 IP (DIP) 執行個體前負載平衡結束點。 用戶端從相同的來源 IP 啟動新的工作階段時, 的來源連接埠會變更，而且會造成以移至不同的 DIP 端點流量。

![根據雜湊負載平衡器](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

圖 1-5-tuple 通訊群組

## <a name="source-ip-affinity-mode"></a>來源 IP 相關性模式

我們還有另一種稱為來源 IP 相關性 （也稱為工作階段相關性或用戶端 IP 關聯性） 的通訊群組模式。 Azure 負載平衡器可以使用 2 tuple （來源 IP、 目的地 IP） 或 3 表示 tuple （來源 IP，目的地 IP 通訊協定），將流量對應到可用的伺服器設定。 藉由使用來源 IP 相關性，起始從相同的用戶端電腦連線到相同的 DIP 端點。

下圖顯示 2 表示 tuple 設定。 請注意到負載平衡器至虛擬機器 1 (VM1) 然後 VM2 和 VM3 備份 2 表示 tuple 的執行方式。

![工作階段的相似性](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

圖 2-2 表示 tuple 通訊群組

來源 IP 相關性解決 Azure 負載平衡器與遠端桌面 (RD) 閘道之間的不相容。 現在您可以建立單一雲端服務中的 RD 閘道伺服器陣列。

另一個使用狀況分析藍本是媒體上傳位置資料上傳到 UDP 但控制項平面透過 TCP 的方法是︰

- 用戶端第一次啟動負載平衡公用地址的 TCP 工作階段，會導向至特定的 DIP，此通道處於作用中監控連線狀況
- 新的 UDP 工作階段從同一個用戶端電腦啟動至相同的負載平衡公用端點，以下的期望，此連線也導向至相同的 DIP 端點為先前的 TCP 連接以便媒體上傳可執行高處理量，同時也維持透過 TCP 控制項頻道。

>[AZURE.NOTE] 負載平衡設定變更時 （移除或新增虛擬機器） 的用戶端要求會重新計算。 您無法取決於新從現有的用戶端結束在相同的伺服器的連線。 此外，使用來源 IP 相關性分配模式可能會導致不相等的通訊群組的流量。 執行前 proxy 的用戶端可能會被視為一個唯一的用戶端應用程式。

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>設定來源 IP 相關性設定負載平衡器

虛擬機器中，您可以使用 PowerShell 來變更逾時設定︰

新增端點 Azure 虛擬機器並設定負載平衡器分配模式

    Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

LoadBalancerDistribution 可以將 sourceIP 2 tuple （來源 IP、 目的地 IP） 負載平衡，3 表示 tuple （來源 IP，目的地 IP 通訊協定） 負載平衡或無 sourceIPProtocol 如果您想要的預設行為 5 tuple 負載平衡。

您可以使用下列動作來擷取端點負載平衡器分配模式的設定︰

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

如果 LoadBalancerDistribution 項目不存在 Azure 負載平衡器會使用預設的 5 tuple 演算法。

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>設定負載平衡端點設定分配模式

如果端點負載平衡端點集的一部分，必須在負載平衡端點設定設定分配模式︰

    Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>若要變更分配模式的雲端服務設定

更新您的雲端服務，您可以利用 Azure SDK 的.NET 2.5 （若要釋放中年 11 月）。 雲端服務的端點設定所做的.csdef。 若要更新負載平衡器分配模式雲端服務部署，部署升級資格。
以下是.csdef 變更端點設定的範例︰

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
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

## <a name="api-example"></a>API 範例

您可以設定使用的服務管理 API 負載平衡器通訊群組。 請確定您新增`x-ms-version`版本設定標頭`2014-09-01`或更新版本。

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>更新部署中指定負載平衡設定的設定

#### <a name="request-example"></a>要求範例

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet    x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

LoadBalancerDistribution 的值可 sourceIP 2 表示 tuple 相關性、 sourceIPProtocol 的 3 tuple 相關性或無 （適用於沒有任何關聯性。 即 5-tuple)

#### <a name="response"></a>回應

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路圖示的負載平衡器](load-balancer-get-started-internet-arm-ps.md)

[設定您的負載平衡器閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
