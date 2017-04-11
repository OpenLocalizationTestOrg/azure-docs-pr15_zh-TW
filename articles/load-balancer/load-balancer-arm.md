<properties
   pageTitle="Azure 資源管理員支援負載平衡器 |Microsoft Azure "
   description="使用 powershell 的負載平衡器與 Azure 資源管理員。 使用範本的負載平衡器"
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


# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Azure 資源管理員支援使用 Azure 負載平衡器

Azure 資源管理員是慣用的管理架構中 Azure 服務。 Azure 負載平衡器可以透過 Azure 資源管理員的 Api 及工具來管理。

## <a name="concepts"></a>概念

使用資源管理員，Azure 負載平衡器包含下列子資源︰

- 前端 IP 設定 – 負載平衡器可以包含一或多個前端 IP 位址，否則會以虛擬 IPs (Vip)。 這些 IP 位址做為輸入流量。

- 位址後端資料庫 – 這些是相關聯的虛擬機器網路介面卡 (NIC) 散佈載入 IP 位址。

- 負載平衡規則 – 規則屬性對應指定的前端 IP 連接埠組合一組後端 IP 位址和連接埠組合。 單一負載平衡器可以有多個負載平衡規則。 每個規則就是前端 IP 和連接埠和後端 IP 和連接埠 Vm 相關聯的組合。

- 探查 – 探查可讓您追蹤的 VM 執行個體的狀況。 如果失敗的狀況檢查，請 VM 執行個體會超越旋轉自動。

- 輸入的 NAT 規則 – NAT 規則，以定義通過前面輸入的流量結束 IP，並分佈的後端 IP。

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>快速入門範本

Azure 資源管理員可讓您佈建應用程式使用宣告式範本。 在單一範本中，您可以部署多個服務，以及它們的相依性。 您可以使用相同的範本以重複部署應用程式在每個階段的應用程式生命週期。

範本可以包含虛擬機器、 虛擬網路、 可用性集、 網路介面 (Nic)、 儲存帳戶、 負載平衡器、 網路安全性群組和公用 IPs 定義。 您可以使用範本建立複雜的應用程式，您需要的所有項目。 將範本檔案可以檢查內容管理系統的版本控制及共同作業。

[進一步瞭解範本](http://go.microsoft.com/fwlink/?LinkId=544798)

[進一步瞭解網路資源](../virtual-network/resource-groups-networking.md)

快速入門範本使用 Azure 負載平衡器可以找到[GitHub 存放庫](https://github.com/Azure/azure-quickstart-templates)中裝載一組產生的社群範本。

範本的範例︰

- [在負載平衡器與負載平衡規則 2 Vm](http://go.microsoft.com/fwlink/?LinkId=544799)
- [2 Vm 中 VNET 內部負載平衡器與負載平衡器的規則](http://go.microsoft.com/fwlink/?LinkId=544800)
- [2 Vm 中負載平衡器與設定 LB NAT 規則](http://go.microsoft.com/fwlink/?LinkId=544801)


## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>設定使用 PowerShell 或 CLI Azure 負載平衡器

開始使用 Azure 資源管理員 cmdlet、 命令列工具和 REST Api

- [Azure 網路指令程式](https://msdn.microsoft.com/library/azure/mt163510.aspx)可用來建立負載平衡器。
- [如何建立使用 Azure 資源管理員負載平衡器](load-balancer-get-started-ilb-arm-ps.md)
- [Azure CLI 使用 Azure 資源管理](../xplat-cli-azure-resource-manager.md)
- [負載平衡器 REST Api](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## <a name="next-steps"></a>後續步驟

您也可以[開始建立網際網路圖示的負載平衡器](load-balancer-get-started-internet-arm-ps.md)及設定何種類型的特定負載平衡器網路流量表現方式[散發模式](load-balancer-distribution-mode.md)。

瞭解如何管理[閒置 TCP 逾時設定的負載平衡器](load-balancer-tcp-idle-timeout.md)。 當您的應用程式需要保留作用之後負載平衡器伺服器的連線，這是很重要。
