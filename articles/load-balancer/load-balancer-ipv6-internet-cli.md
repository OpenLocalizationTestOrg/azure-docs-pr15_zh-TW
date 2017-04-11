<properties
    pageTitle="建立網際網路圖示的 IPv6 Azure 資源管理員] 中使用 Azure CLI 的負載平衡器 |Microsoft Azure"
    description="瞭解如何建立網際網路圖示的 IPv6 Azure 資源管理員] 中使用 Azure CLI 的負載平衡器"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="ipv6 azure 負載平衡器、 雙堆疊、 公用 ip、 原生 ipv6、 行動電話、 iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>建立網際網路圖示的 IPv6 Azure 資源管理員] 中使用 Azure CLI 的負載平衡器

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Azure CLI](./load-balancer-ipv6-internet-cli.md)
- [範本](./load-balancer-ipv6-internet-template.md)

Azure 負載平衡器是層級 4 （TCP、 UDP） 負載平衡器。 負載平衡器提供高可用性來發送連入流量的各項在雲端服務的健康服務執行個體或虛擬機器中負載平衡器設定。 Azure 負載平衡器也可以顯示多個連接埠、 多個 IP 位址，或兩者皆這些服務。

## <a name="example-deployment-scenario"></a>部署案例

下圖說明負載平衡解決方案部署使用本文所述的範例範本。

![負載平衡器案例](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

在這個案例中，您會建立下列 Azure 資源︰

- 兩個虛擬機器 (Vm)
- 每個 VM IPv4 和 IPv6 位址指派虛擬網路介面
- 具網際網路負載平衡器 IPv4 與 IPv6 公用 IP 位址
- 顯示狀態設定為的包含兩個 Vm
- 兩個負載平衡規則，將公用 Vip 對應到私人的端點

## <a name="deploying-the-solution-using-the-azure-cli"></a>部署使用 Azure CLI 解決方案

下列步驟會顯示如何建立網際網路圖示 Azure 資源管理員使用 CLI 負載平衡器。 使用 Azure 資源管理員中，每個資源是建立與個別，設定，然後將放在一起，建立資源。

若要部署負載平衡器，您可以建立並設定下列物件︰

- 前端 IP 設定-包含傳入網路流量的公用 IP 位址。
- 位址後端資料庫-包含從負載平衡器接收網路流量的虛擬機器網路介面 (Nic)。
- 負載平衡規則-包含對應公用的連接埠負載平衡器位址後端資料庫中的連接埠的規則。
- 連入 NAT 規則-包含對應到特定虛擬機器位址後端資料庫中的連接埠的負載平衡器公用的連接埠的規則。
- 探查-包含用來查看虛擬機器中的執行個體的後端位址集區的顯示狀態的狀況檢查]。

如需詳細資訊，請參閱[Azure 資源管理員支援負載平衡器](load-balancer-arm.md)。

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>將您的 CLI 環境設定為使用 Azure 資源管理員

例如，我們正在執行 CLI 工具 PowerShell 命令視窗中。 我們不使用 PowerShell 的 Azure cmdlet，但我們使用 PowerShell 的指令碼功能來提升可讀性和重複使用。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../../articles/xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。

2. 執行 [ **azure config 模式**] 命令，切換到資源管理員模式。

        azure config mode arm

    預期的輸出︰

        info:    New mode is arm

3. 登入 Azure，並取得訂閱的清單。

        azure login

    輸入出現提示時，您 Azure 的認證。

        azure account list

    挑選您想要使用的訂閱。 記下一步訂閱 Id。

4. 設定用於 CLI 命令 PowerShell 變數。

        ```
        $subscriptionid = "########-####-####-####-############"  # enter subscription id
        $location = "southcentralus"
        $rgName = "pscontosorg1southctrlus09152016"
        $vnetName = "contosoIPv4Vnet"
        $vnetPrefix = "10.0.0.0/16"
        $subnet1Name = "clicontosoIPv4Subnet1"
        $subnet1Prefix = "10.0.0.0/24"
        $subnet2Name = "clicontosoIPv4Subnet2"
        $subnet2Prefix = "10.0.1.0/24"
        $dnsLabel = "contoso09152016"
        $lbName = "myIPv4IPv6Lb"
        ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>建立資源群組、 負載平衡器、 虛擬網路及子網路

1. 建立資源群組

        azure group create $rgName $location

2. 建立負載平衡器

        $lb = azure network lb create --resource-group $rgname --location $location --name $lbName

3. 建立虛擬網路 (VNet)。

        $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix

    在此 VNet 中建立兩個子網路。

        $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
        $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>建立前端集區的公用 IP 位址

1. 設定 PowerShell 變數

        $publicIpv4Name = "myIPv4Vip"
        $publicIpv6Name = "myIPv6Vip"

2. 建立的公用 IP 位址的前端 IP 集區。

        $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
        $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel

    >[AZURE.IMPORTANT]負載平衡器 FQDN 文使用公用 IP 網域的標籤。 這個傳統部署中，使用雲端服務的已變更名稱為負載平衡器 FQDN。
    >在此範例中，FQDN 是*contoso09152016.southcentralus.cloudapp.azure.com*。

## <a name="create-front-end-and-back-end-pools"></a>建立前端與後端資料庫

此範例會建立接收負載平衡器上的連入網路流量的前端 IP 集區與後端 IP 集區前端集區要傳送給負載平衡網路流量。

1. 設定 PowerShell 變數

        $frontendV4Name = "FrontendVipIPv4"
        $frontendV6Name = "FrontendVipIPv6"
        $backendAddressPoolV4Name = "BackendPoolIPv4"
        $backendAddressPoolV6Name = "BackendPoolIPv6"

2. 建立前端 IP 資料庫建立關聯，以在上一個步驟和負載平衡器中建立的公用 IP。

        $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
        $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
        $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
        $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName

## <a name="create-the-probe-nat-rules-and-lb-rules"></a>建立探查 NAT 規則與 LB 規則

此範例會建立下列項目︰

- 檢查連線至 TCP 連接埠 80 探查規則
- 翻譯至連接埠 3389 RDP<sup>1</sup>的連接埠 3389 上的所有內送流量 NAT 規則
- 若要翻譯至連接埠 3389 RDP<sup>1</sup>的連接埠 3391 上的所有內送流量 NAT 規則
- 要平衡到後端資料庫中的地址的連接埠 80 的連接埠 80 上的所有內送流量負載平衡器規則。

<sup>1</sup>至特定的虛擬機器執行個體前負載平衡器相關聯的 NAT 規則。 送達連接埠 3389 上的網路流量傳送至特定的虛擬機器和 NAT 規則相關聯的連接埠。 您必須 NAT 規則中指定的通訊協定 （UDP 或 TCP）。 兩個通訊協定無法被指派到相同的連接埠。

1. 設定 PowerShell 變數

        $probeV4V6Name = "ProbeForIPv4AndIPv6"
        $natRule1V4Name = "NatRule-For-Rdp-VM1"
        $natRule2V4Name = "NatRule-For-Rdp-VM2"
        $lbRule1V4Name = "LBRuleForIPv4-Port80"
        $lbRule1V6Name = "LBRuleForIPv6-Port80"

2. 建立探查

    下列範例會建立連線到後端 TCP 連接埠 80 檢查 TCP 探查每 15 秒。 它會將標記後端資源無法使用兩個連續失敗之後。

        $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName

3. 建立連入允許 RDP 連線後端資源的 NAT 規則

        $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
        $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName

4. 建立負載平衡器流量傳送至不同的後端連接埠根據的前端收到要求的規則

        $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
        $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName

5. 檢查您的設定

        azure network lb show --resource-group $rgName --name $lbName

    預期的輸出︰

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show


## <a name="create-nics"></a>建立 Nic

建立 Nic，並將其 NAT 規則，負載平衡器規則]，然後探查建立關聯。

1. 設定 PowerShell 變數

        $nic1Name = "myIPv4IPv6Nic1"
        $nic2Name = "myIPv4IPv6Nic2"
        $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
        $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
        $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
        $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
        $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
        $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"

2. 建立的每個後端 NIC 並新增 IPv6 設定。

        $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

        $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>建立後端 VM 資源並附加每個 NIC

若要建立 Vm，您必須儲存帳戶。 負載平衡，Vm 必須可用性集合中的成員。 如需有關如何建立 Vm 的詳細資訊，請參閱[建立 Azure VM 使用 PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

1. 設定 PowerShell 變數

        $storageAccountName = "ps08092016v6sa0"
        $availabilitySetName = "myIPv4IPv6AvailabilitySet"
        $vm1Name = "myIPv4IPv6VM1"
        $vm2Name = "myIPv4IPv6VM2"
        $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
        $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
        $disk1Name = "WindowsVMosDisk1"
        $disk2Name = "WindowsVMosDisk2"
        $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
        $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
        $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
        $vmUserName = "vmUser"
        $mySecurePassword = "PlainTextPassword*1"

    >[AZURE.WARNING] 此範例使用純文字 Vm 使用者名稱和密碼。 適當小心時使用認證中清除。 處理認證在 PowerShell 更安全的方法，請參閱[取得認證](https://technet.microsoft.com/library/hh849815.aspx)指令程式。

2. 建立儲存帳戶及可用性設定

    當您建立 Vm 時，您可以使用現有的儲存空間帳戶。 下列命令會建立新的儲存空間帳戶。

        $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"

    接下來，建立可用性設定。

        $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location

3. 建立與相關聯的 Nic 的虛擬機器

        $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

        $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn  --storage-account-name $storageAccountName --disable-bginfo-extension

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-cli.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
