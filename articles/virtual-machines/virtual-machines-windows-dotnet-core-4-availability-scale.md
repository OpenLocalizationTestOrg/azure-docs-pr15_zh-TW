<properties
   pageTitle="可用性及 Azure 資源管理員範本中 |Microsoft Azure"
   description="Azure 虛擬機器 DotNet 核心教學課程"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>可用性及 Azure 資源管理員範本中的縮放比例

可用性及縮放比例參考的存留時間和符合需求的能力。 如果應用程式，必須設定 99.9%的時間，它必須允許多個同時計算資源的架構。 舉例來說，而不是有一個網站，與較高層級的可用性設定，包含相同的網站，平衡技術前面他們的多個執行個體。 在此組態中，應用程式的執行個體之一可以向下維護時, 採取的剩餘繼續運作。 縮放比例另一方面指的是服務要求應用程式的能力。 負載平衡應用程式中，新增或移除資料庫中的執行個體 」 可讓應用程式不按比例縮放以符合需求。

這份文件詳細說明如何設定音樂市集範例部署可用性和小數位數。 所有的相依性和唯一的設定會醒目提示。 為了獲得最佳體驗，預先部署至 Azure 訂閱及工作，以及 Azure 資源管理員範本解決方案的執行個體。 完成範本可找到以下 –[在 Windows 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

## <a name="availability-set"></a>顯示狀態設定

可用的設定方式分門別類應用範圍橫跨 Azure 虛擬機器實體主辦城市] 和 [實體網路硬體電源供應等其他 infrastructural 元件。 可用性集確保在進行的維修作業、 裝置失敗，或其他時間，受到影響並非所有的虛擬機器。 可用性設定可以新增至 Azure 資源管理員範本中使用 Visual Studio 新增資源精靈]，或有效 JSON 插入範本。

請遵循這個連結，請參閱 JSON 資源管理員樣板 –[可用性設定](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368)中的範例。


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

宣告可用性設定為虛擬機器資源的屬性。 

請遵循這個連結，請參閱在資源管理員樣板 –[可用性設定與關聯的虛擬機器](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302)中的 JSON 範例。

```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
設定所見從 Azure 入口網站的可用性。 每個虛擬機器和設定的詳細資料的以下詳細資訊。

![顯示狀態設定](./media/virtual-machines-windows-dotnet-core/ase-win.png)

在您採取進階的可用性集的詳細資訊，請參閱[管理可用性的虛擬機器](./virtual-machines-windows-manage-availability.md)。 

## <a name="network-load-balancer"></a>網路負載平衡器

可用性設定提供應用程式容錯能力，然而負載平衡器可讓應用程式的執行個體使用單一網路位址。 應用程式的多個執行個體可以會裝載於許多虛擬機器，每個連線到負載平衡器。 存取應用程式時，為負載平衡器會傳送要求上附加的成員。 負載平衡器可使用 Visual Studio 新增資源精靈]，進行新增，或插入正確，將格式設定 JSON 資源 Azure 資源管理員範本。

請遵循這個連結，請參閱在資源管理員樣板 –[網路負載平衡器](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198)中的 JSON 範例。

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

範例應用程式公用的 IP 位址的網際網路上公開，因為此位址是與負載平衡器相關聯。 

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[網路負載平衡器關聯的公用 IP 位址](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211)。

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

從 Azure] 入口網站網路負載平衡器概觀顯示關聯的公用 IP 位址。

![網路負載平衡器](./media/virtual-machines-windows-dotnet-core/nlb-win.png)

## <a name="load-balancer-rule"></a>負載平衡器規則

使用時負載平衡器，規則被設定來控制如何流量由平衡預定的資源。 範例音樂市集應用程式，流量送達的公用 IP 位址的連接埠 80 和分佈於目前的所有虛擬機器的連接埠 80。 

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[負載平衡器規則](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226)。


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

從入口網站的網路負載平衡器規則的檢視。

![網路負載平衡器規則](./media/virtual-machines-windows-dotnet-core/lbrule-win.png)

## <a name="load-balancer-probe"></a>負載平衡器探查

監控以便要求會提供，才能執行系統的每個虛擬機器也必須負載平衡器。 監控生效，常數探查的預先定義的連接埠。 設定音樂市集部署探查所有包含的虛擬機器的連接埠 80。 

請遵循這個連結，請參閱中的資源管理員範本 –[負載平衡器探查](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247)JSON 範例。


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

從 Azure 入口網站看到負載平衡器探查。

![網路負載平衡器探查](./media/virtual-machines-windows-dotnet-core/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>輸入的 NAT 規則

使用時負載平衡器 」，規則會需要放到提供每個虛擬機器非負載平衡的存取的位置。 舉例來說，建立時 RDP 連線與每個虛擬機器，此流量不應該負載平衡，而預定的路徑應設定。 使用連入 NAT 規則資源設定預定的路徑。 使用此資源，請輸入的通訊可以對應至個別的虛擬機器。 

音樂市集應用程式中，開始，5000 的連接埠] 已對應至連接埠 3389 RDP 存取每個虛擬機器上。 `copyindex()`函數來將增量的內送的連接埠，例如第二個虛擬機器接收 5001，第三個 5002，向內連接埠等等。

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[輸入 NAT 規則](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260)。 

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Azure 入口網站中所見，其中一個範例連入 NAT 規則。 每個虛擬機器中部署建立 RDP NAT 規則。

![輸入的 NAT 規則](./media/virtual-machines-windows-dotnet-core/natrule-win.png)

在您採取進階 Azure 網路負載平衡器的詳細資訊，請參閱[負載平衡 Azure 基礎結構服務](./virtual-machines-windows-load-balance.md)。

## <a name="deploy-multiple-vms"></a>部署多個 Vm

最後，在可用性設定或負載平衡器有效地運作，多個虛擬機器，才能。 多個 Vm，可以使用 Azure 資源管理員範本複製函數部署。 使用複製函數，不需要定義的虛擬機器有限的數字，而此值以動態方式提供一次的部署。 複製函式會耗費的執行個體建立的數字和部署適當的數字的虛擬機器和相關聯的資源的控點。

音樂儲存範本，定義參數的執行個體計數所需的時間。 建立虛擬機器和相關的資源時，此數字會使用整個範本。

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

虛擬機器資源，複製循環播放提供的名稱，以及用來控制結果的份數的執行個體參數的數目。

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[虛擬機器複製函數](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290)。 


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

可以使用存取複製函數的目前反覆運算`copyIndex()`函數。 虛擬機器及其他資源的名稱可複製 index 函數的值。 例如，如果部署虛擬機器的兩個執行個體，它們會需要不同的名稱。 `copyIndex()`函數可用的虛擬機器名稱的一部分來建立唯一的名稱。 範例`copyindex()`用於命名用途函數所示的虛擬機器資源。 電腦名稱如下的串連`vmName`參數，以及`copyIndex()`函數。 

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[複製 Index 函數](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309)。 


```none
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

`copyIndex`函數用於音樂市集範例範本的數次。 資源與函數利用`copyIndex`包含單一的執行個體的虛擬機器這類和網路介面，負載平衡器規則的特定項目，然後任何取決於函數。 

如需複製函數的詳細資訊，請參閱[建立資源 Azure 資源管理員] 中的多個執行個體](../resource-group-create-multiple.md)。

## <a name="next-step"></a>下一步

<hr>

[步驟 4-與 Azure 資源管理員範本的應用程式部署](./virtual-machines-windows-dotnet-core-5-app-deployment.md)