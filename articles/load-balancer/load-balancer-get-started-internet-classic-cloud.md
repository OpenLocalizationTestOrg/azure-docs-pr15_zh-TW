<properties
   pageTitle="開始建立對負載平衡器中的雲端服務的傳統部署模型使用網際網路 |Microsoft Azure"
   description="瞭解如何建立網際網路圖示在傳統的部署模型中的雲端服務的負載平衡器"
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
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>開始建立網際網路圖示的雲端服務的負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。 您也可以[瞭解如何建立網際網路圖示的負載平衡器使用 Azure 資源管理員](load-balancer-get-started-internet-arm-cli.md)。

雲端服務與負載平衡器會自動設定，並可以自訂透過 service 模型。

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>建立使用服務定義檔案負載平衡器

您可以利用更新您的雲端服務的.NET 2.5 Azure SDK。 雲端服務的端點設定所做的[服務定義](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef 檔案中。

下列範例顯示 servicedefinition.csdef 檔案雲端部署設定的方式︰

檢查.csdef 檔案產生的雲端部署 snipet，您可以查看設定為使用連接埠 HTTP 連接埠 10000、 10001 及 10002 外部端點。


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




## <a name="check-load-balancer-health-status-for-cloud-services"></a>檢查雲端服務的負載平衡器健康狀態


狀況檢查的範例如下︰

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

負載平衡器結合端點的資訊，並在表單中的可用來查詢的服務健康狀況的 VM}:80/Probe.aspx http://{DIP 的建立 URL 探查的資訊。

服務會偵測定期探查從相同的 IP 位址。 這是來自節點的主機正在虛擬機器狀況探查要求。
服務必須使用 HTTP 200 狀態碼負載平衡器假設服務是健全的回覆。 任何其他 HTTP 狀態碼 (例如 503) 直接採用虛擬機器登出旋轉。

探查定義也會控制探查的頻率。 在上述範例中，負載平衡器探查每 5 秒的端點。 如果 10 秒 （兩個探查間隔） 收到沒有正數的回應，則探查往下移，假設是和虛擬機器超越旋轉。 同樣地，如果旋轉超出服務收到正數的答案，服務是放回立即旋轉。 如果服務是變動之間健全和不健全、 負載平衡器可以決定要延遲重新簡介回要旋轉的服務，直到已健全探查數。

檢查服務定義的結構描述的[狀況探查](https://msdn.microsoft.com/library/azure/jj151530.aspx)如需詳細資訊。

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

