<properties
   pageTitle="服務布料的轉印圖樣節點類型和 VM 比例組 |Microsoft Azure"
   description="描述如何服務布料的轉印圖樣節點類型關聯到 VM 縮放比例設定，以及如何遠端連線到 VM 縮放比例設定的執行個體或叢集節點。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>服務布料的轉印圖樣節點類型與虛擬機器縮放比例集之間的關係

虛擬機器比例集是您可以使用部署及管理所設定的虛擬機器集合 Azure 計算的資源。 服務布料的轉印圖樣叢集中定義的每個節點類型設定為不同的 VM 縮放比例設定。 每個節點輸入然後可以縮放或向下獨立，有幾組不同的連接埠開啟，並可以有不同的容量指標。

以下螢幕擷取畫面顯示有兩種節點類型叢集︰ FrontEnd 和後端。  每個節點類型有五個節點。

![有兩種節點類型叢集的螢幕擷取畫面][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>將 VM 縮放比例設定執行個體對應至節點

您可以看到上方，VM 縮放比例設定執行個體的 [啟動執行個體 0，然後向上移。 編號會反映在名稱]。 例如，BackEnd_0 是組執行個體 0 的後端 VM 小數位數。 此特定的 VM 縮放比例設定有五個執行個體，名為 BackEnd_0、 BackEnd_1、 BackEnd_2、 BackEnd_3 和 BackEnd_4。

當您調整設定 VM 縮放比例設定會建立新的執行個體。 新的 VM 縮放比例設定執行個體名稱通常會 VM 縮放比例設定名稱 + 下一個執行個體數目。 在此範例中，是 BackEnd_5。


## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>對應 VM 縮放比例設定為 [每個節點類型/VM 縮放比例設定的負載平衡器

如果您已經部署叢集從入口網站，或使用範例資源管理員範本我們提供，然後當您收到資源群組] 底下的所有資源的清單時，您會看到每一種 VM 縮放比例設定或節點類型負載平衡器。

名稱會類似︰ **LB-&lt;節點類型名稱&gt;**。 例如，LB-sfcluster4doc-0，此螢幕擷取畫面所示︰


![資源][Resources]


## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>遠端連接至 VM 縮放比例設定的執行個體或叢集節點
定義集中每個節點類型設定為不同的 VM 縮放比例設定。  這表示可調整節點類型向上或向下獨立及可以進行的不同 VM sku 皆可。 不同於單一的執行個體 Vm，VM 縮放比例設定執行個體不會取得其本身的虛擬 IP 位址。 因此，也不比較容易時所需的 IP 位址和連接埠，您可以使用遠端連線至特定的執行個體。

以下是找出它們，您可以遵循的步驟。

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>步驟 1︰ 找出節點類型的虛擬 IP 位址，然後輸入 NAT RDP 規則

若要取得的您需要取得連入的 NAT 規則值定義為**Microsoft.Network/loadBalancers**的資源定義的一部分。

在入口網站，瀏覽至負載平衡器刀，然後**設定**。

![LBBlade][LBBlade]


在 [**設定**] 中，按一下 [上**連入 NAT 規則**。 現在這個可為您的 IP 位址，您可以使用遠端的連接埠連線至 VM 縮放比例設定的第一個執行個體。 在以下的螢幕擷取畫面，它位於**104.42.106.156** **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>步驟 2︰ 找的出您可以使用遠端的連接埠連接到特定 VM 縮放比例設定執行個體/節點

本文件先前我討論如何 VM 縮放比例設定執行個體對應到節點。 我們會使用該找出的確切的連接埠。

連接埠被配置 VM 縮放比例設定執行個體的遞增順序。 因此，在 FrontEnd 節點類型的範例中，每個五個執行個體的連接埠如下。 現在，您需要執行相同的對應 VM 縮放比例設定執行個體。

|**VM 縮放比例設定執行個體**|**連接埠**|
|-----------------------|--------------------------|
|FrontEnd_0|3389|
|FrontEnd_1|3390|
|FrontEnd_2|3391|
|FrontEnd_3|3392|
|FrontEnd_4|3393|
|FrontEnd_5|3394|


### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>步驟 3︰ 遠端連線至特定的 VM 縮放比例設定執行個體

在下面的螢幕擷取畫面中，我可以使用遠端桌面連線連線到 FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>如何變更 RDP 連接埠範圍值

### <a name="before-cluster-deployment"></a>部署叢集之前

當您正在設定叢集使用資源管理員範本時，您可以指定範圍中**inboundNatPools**。

移至**Microsoft.Network/loadBalancers**的資源定義。 底下所找**inboundNatPools**的描述。  取代*frontendPortRangeStart* ] 和 [ *frontendPortRangeEnd*值。

![InboundNatPools][InboundNatPools]


### <a name="after-cluster-deployment"></a>部署叢集之後
這是有些複雜，而且可能會導致 Vm 快速回收。 您現在必須設定新的值，使用 PowerShell 的 Azure。 請確定您的電腦上安裝就 Azure PowerShell 1.0 或更新版本。 如果您不這麼做之前，強烈建議您追蹤中所述的步驟[如何安裝和設定 PowerShell 的 Azure。](../powershell-install-configure.md)

Azure 帳戶登入。 如果基於某種原因，這個 PowerShell 命令失敗，您應該檢查是否有安裝正確 PowerShell 的 Azure。

```
Login-AzureRmAccount
```

執行下列動作，取得您負載平衡器詳細資料，您看到**inboundNatPools**描述的值︰

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

現在，請設定*frontendPortRangeEnd*和*frontendPortRangeStart*您想要的值。

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>後續步驟

- [「 部署任何 」 功能，與 Azure 管理叢集比較的概觀](service-fabric-deploy-anywhere.md)
- [叢集安全性](service-fabric-cluster-security.md)
- [服務布料的轉印圖樣 SDK 及快速入門](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
