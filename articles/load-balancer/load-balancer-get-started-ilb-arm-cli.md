<properties
   pageTitle="使用 Azure CLI 資源管理員] 中建立內部負載平衡器 |Microsoft Azure"
   description="瞭解如何使用 Azure CLI 資源管理員] 中建立內部負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>使用 Azure CLI 建立內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](load-balancer-get-started-ilb-classic-cli.md)。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>使用 Azure CLI 部署解決方案

下列步驟說明如何使用 CLI Azure 資源管理員] 來建立具網際網路負載平衡器。 使用 Azure 資源管理員中，每個資源是建立與個別，設定，然後將放在一起，建立資源。

您需要建立及設定下列物件部署負載平衡器︰

- **前端 IP 設定**︰ 包含傳入網路流量的公用 IP 位址
- **後端位址集區**︰ 包含啟用虛擬機器從負載平衡器接收網路流量的網路介面 (Nic)
- **負載平衡規則**︰ 包含對應公用的連接埠負載平衡器位址後端資料庫中的連接埠的規則
- **連入 NAT 規則**︰ 包含對應到特定虛擬機器位址後端資料庫中的連接埠的負載平衡器公用的連接埠的規則
- **探查**︰ 包含用來檢查虛擬機器中的執行個體的後端位址集區狀況探查

如需詳細資訊，請參閱[Azure 資源管理員支援負載平衡器](load-balancer-arm.md)。

## <a name="set-up-cli-to-use-resource-manager"></a>將 CLI 設定為使用資源管理員

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../../articles/xplat-cli-install.md)。 依照指示，選取您的 Azure 帳戶及訂閱為止。

2. 執行 [ **azure config 模式**] 命令，切換至 [資源管理員模式，如下所示︰

        azure config mode arm

    預期的輸出︰

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>建立內部負載平衡器逐步解說

1. 登入 Azure。

        azure login

    出現提示時，輸入您的 Azure 認證。

2. 變更 Azure 資源管理員模式命令工具。

        azure config mode arm

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源管理員] 中的所有資源都的資源群組相關聯。 如果您尚未這麼做尚未，建立 [資源群組。

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>建立內部負載平衡器設定

1. 建立內部負載平衡器

    在下列情況中，用於美國地區中建立名為 nrprg 的資源群組。

        azure network lb create --name nrprg --location eastus

    >[AZURE.NOTE] 所有資源的內部負載平衡器，例如虛擬網路和虛擬網路的子網路，必須都是相同的 [資源] 群組中，在同一個區域中。

2. 建立內部負載平衡器前端 IP 位址。

    虛擬網路的子網路範圍內，必須是您使用的 IP 位址。

        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet

3. 建立位址後端資料庫。

        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb

    定義前端的 IP 位址] 和 [後端地址資料庫後，您可以建立負載平衡器規則，輸入 NAT 規則，請與探查自訂的狀況。

4. 建立內部負載平衡器的負載平衡器規則。

    當您依照上述步驟時，[] 命令會建立收聽前端集區中的連接埠 1433年及傳送負載平衡網路流量至位址後端資料庫，也會使用連接埠 1433年負載平衡器規則。

        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb

5. 建立連入的 NAT 規則。

    輸入的 NAT 規則用來建立負載平衡器，請移至特定的虛擬機器執行個體中的端點。 建立兩個遠端桌面版的 NAT 規則先前的步驟。

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389

6. 建立狀況探查負載平衡器。

    狀況檢查檢查所有的虛擬機器例項，以確保他們可以將傳送網路流量。 失敗的探查檢查的虛擬機器執行個體移除負載平衡器，直到該郵件會移回到線上，並在探查檢查決定狀況良好。

        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4

    >[AZURE.NOTE]Microsoft Azure 平台使用各種不同的系統管理案例的靜態、 公開舉例來說 IPv4 位址。 IP 位址是 168.63.129.16。 這個 IP 位址應該不會封鎖任何防火牆，因為這會造成無法預期的行為。
    >Azure 內部負載平衡與這個 IP 位址是監視探查從負載平衡器用來決定負載平衡集中的虛擬機器的狀況。 如果網路安全性群組用來限制 Azure 虛擬機器集內部的負載平衡流量，或套用至子虛擬網路，請確定已新增至允許從 168.63.129.16 流量的網路安全性規則。

## <a name="create-nics"></a>建立 Nic

您需要建立 Nic （或修改現有的），使其 NAT 規則，負載平衡器規則]，然後探查產生關聯。

1. 建立名為 NIC *lb nic1 會*然後再將它與*rdp1* NAT 規則與*beilb*位址後端資料庫關聯。

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus

    預期的輸出︰

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. 建立名為 NIC *lb nic2 會*然後再將它與*rdp2* NAT 規則與*beilb*位址後端資料庫關聯。

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus

3. 建立名為*DB1*虛擬機器，然後將其關聯命名 NIC *lb nic1 會*。 稱為*web1nrp*儲存帳戶會建立之前執行下列命令︰

        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] 在負載平衡器 Vm 必須在相同的可用性設定。 使用`azure availset create`若要建立可用性設定。

4. 建立名為*DB2*，虛擬機器 (VM)，然後將其關聯 NIC 名為*lb nic2 會*。 執行下列命令之前，先建立稱為*web1nrp*儲存帳戶。

        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>刪除負載平衡器

若要移除負載平衡器，請使用下列命令︰

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>後續步驟

[使用來源 IP 相關性設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
