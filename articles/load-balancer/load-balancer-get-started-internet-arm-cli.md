<properties
   pageTitle="建立網際網路圖示的負載平衡器資源管理員] 中使用 Azure CLI |Microsoft Azure"
   description="瞭解如何建立對負載平衡器資源管理員] 中使用 Azure CLI 網際網路"
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

# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>建立使用 Azure CLI 內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋資源管理員部署模型。 您也可以[瞭解如何建立網際網路圖示的負載平衡器使用傳統的部署](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>部署使用 Azure CLI 解決方案

下列步驟會顯示如何建立網際網路圖示 Azure 資源管理員使用 CLI 負載平衡器。 使用 Azure 資源管理員各個資源會建立並設定個別，然後放置在一起，建立資源。

您必須建立並設定下列物件部署負載平衡器︰

- 前端 IP 設定-包含傳入網路流量的公用 IP 位址。
- 位址後端資料庫-包含從負載平衡器接收網路流量的虛擬機器網路介面 (Nic)。
- 負載平衡規則-包含對應公用的連接埠負載平衡器位址後端資料庫中的連接埠的規則。
- 連入 NAT 規則-包含對應到特定虛擬機器位址後端資料庫中的連接埠的負載平衡器公用的連接埠的規則。
- 探查-包含用來查看虛擬機器中的執行個體的後端位址集區的顯示狀態的狀況檢查]。

如需詳細資訊請參閱[Azure 資源管理員支援負載平衡器](load-balancer-arm.md)。

## <a name="set-up-cli-to-use-resource-manager"></a>將 CLI 設定為使用資源管理員

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../../articles/xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。

2. 執行**azure config 模式**命令以切換到資源管理員模式，如下所示。

        azure config mode arm

    預期的輸出︰

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>建立虛擬網路和前端 IP 集區的公用 IP 位址

1. 建立名為東亞美國位置使用資源群組名稱*NRPRG* *NRPVnet*虛擬網路 (VNet)。

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    建立名為*NRPVnetSubnet* CIDR 區塊中*NRPVnet*10.0.0.0/24 與子網路。

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. 建立一個名為*NRPPublicIP* DNS 名稱*loadbalancernrp.eastus.cloudapp.azure.com*前端 IP 集區所使用的公用 IP 位址。 [下] 命令會使用靜態配置類型與 4 的分鐘數閒置逾時。

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT]負載平衡器會使用公用 IP 網域的標籤作為 FQDN。 此變更從傳統部署中，使用雲端服務為負載平衡器完全完整網域名稱 (FQDN)。
    >在此範例中，FQDN 是*loadbalancernrp.eastus.cloudapp.azure.com*。

## <a name="create-a-load-balancer"></a>建立負載平衡器

下列命令會建立負載平衡器中名為 「 *NRPlb* *NRPRG*資源群組*東亞美國*Azure 的位置。

    azure network lb create NRPRG NRPlb eastus

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>建立 IP 集區前端與後端位址集區

此範例會示範如何建立接收負載平衡器上的連入網路流量的前端 IP 集區與後端 IP 集區前端集區要傳送給負載平衡網路流量。

1. 建立前端 IP 資料庫建立關聯，以在上一個步驟和負載平衡器中建立的公用 IP。

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. 設定接收內送的流量前端 IP 資料庫中使用的後端地址集區。

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## <a name="create-lb-rules-nat-rules-and-probe"></a>建立 LB 規則、 NAT 規則和探查

此範例會建立下列項目。

- 若要翻譯連接埠 21 連接埠 22<sup>1</sup>的所有內送流量 NAT 規則
- 若要翻譯至連接埠 22 的連接埠 23 上的所有內送流量 NAT 規則
- 要平衡到後端資料庫中的地址的連接埠 80 的連接埠 80 上的所有內送流量負載平衡器規則。
- 若要檢查的頁面上的健康狀態這個規則命名*HealthProbe.aspx*。

<sup>1</sup>至特定的虛擬機器執行個體前負載平衡器相關聯的 NAT 規則。 連接埠 21 送達網路流量傳送至特定的虛擬機器連接埠 22 此 NAT 規則相關聯。 您必須 NAT 規則中指定的通訊協定 （UDP 或 TCP）。 兩個通訊協定無法被指派到相同的連接埠。

1. 建立 NAT 規則。

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22

2. 建立負載平衡器規則。

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. 建立狀況檢查。

        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4

4. 檢查您的設定。

        azure network lb show nrprg nrplb

    預期的輸出︰

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>建立 Nic

您需要建立 Nic （或修改現有的），將其 NAT 規則，負載平衡器規則]，然後探查建立關聯。

1. 建立具名的 NIC *lb nic1 會*並將其*rdp1* NAT 規則，與*NRPbackendpool*位址後端資料庫建立關聯。

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

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

2. 建立具名的 NIC *lb nic2 會*並將其*rdp2* NAT 規則，與*NRPbackendpool*位址後端資料庫建立關聯。

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. 建立名為*web1*，虛擬機器 (VM) 及關聯命名 NIC *lb nic1 會*。 執行下列命令之前，先建立稱為*web1nrp*儲存帳戶。

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] 在負載平衡器 Vm 必須在相同的可用性設定。 使用`azure availset create`若要建立可用性設定。

    輸出應如下︰

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] **這是沒有設定 publicIP NIC**資訊訊息預期自 NIC 建立負載平衡器連線到網際網路，使用負載平衡器公用 IP 位址。

    由於*lb nic1 會*NIC *rdp1* NAT 規則與相關聯，您可以連線至*web1*使用 RDP 透過連接埠 3441 負載平衡器上。

4. 建立名為*web2*，虛擬機器 (VM) 及關聯命名 NIC *lb nic2 會*。 執行下列命令之前，先建立稱為*web1nrp*儲存帳戶。

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="update-an-existing-load-balancer"></a>更新現有的負載平衡器

您可以新增參照現有的負載平衡器的規則。 在下一個範例中，新的負載平衡器規則會新增至現有的負載平衡器**NRPlb**

    azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool

## <a name="delete-a-load-balancer"></a>刪除負載平衡器

若要移除負載平衡器使用下列命令︰

    azure network lb delete --resource-group nrprg --name nrplb

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-cli.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
