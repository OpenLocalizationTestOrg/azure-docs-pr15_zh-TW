<properties
   pageTitle="在傳統的部署模型中建立的雲端服務內部負載平衡器 |Microsoft Azure"
   description="瞭解如何建立使用傳統的部署模型中的 PowerShell 內部負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>開始建立雲端服務 （傳統） 的內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](load-balancer-get-started-ilb-arm-ps.md)。


## <a name="configure-internal-load-balancer-for-cloud-services"></a>設定內部負載平衡器雲端服務

內部負載平衡器支援虛擬機器和雲端服務。 以外地區的虛擬網路的雲端服務中建立內部負載平衡器端點會只在雲端服務中存取。

內部負載平衡器設定有將建立在雲端服務中的第一個部署時，如下列範例所示。

>[AZURE.IMPORTANT] 執行下列步驟的必要條件是有雲端部署已經建立一個虛擬網路。 您需要的虛擬網路名稱與建立內部的負載平衡的子網路名稱。

### <a name="step-1"></a>步驟 1

Visual Studio 中開啟雲端部署的服務設定檔案 (.cscfg)，並新增下一節，建立內部負載平衡在最後一個 「`</Role>`」 網路設定的項目。




    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


讓我們來加總的值，網路設定檔，以顯示的外觀。 在範例中，假設您建立子網路與 test_subnet] 和 [10.0.0.4 的靜態 IP 子網路 10.0.0.0/24 稱為 「 test_vnet 」。 負載平衡器會命名 testLB。

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

如需有關負載平衡器結構描述的詳細資訊，請參閱[新增負載平衡器](https://msdn.microsoft.com/library/azure/dn722411.aspx)。

### <a name="step-2"></a>步驟 2


變更用來將端點新增至內部的負載平衡服務定義 (.csdef) 檔案。 建立角色執行個體，森積差服務定義檔案會將新增至內部的負載平衡的角色執行個體。


    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

追蹤與上述範例相同的值，讓我們來新增至服務定義檔案的值。

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

網路流量與負載平衡使用連接埠 80 用傳入的邀請，傳送給工作者角色執行個體，也會在連接埠 80 testLB 負載平衡器會。


## <a name="next-steps"></a>後續步驟

[設定使用來源 IP 相關性負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)