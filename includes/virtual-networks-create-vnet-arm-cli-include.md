## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>如何建立使用 Azure CLI VNet

若要管理 Azure 資源的電腦執行 Windows、 Linux 或 OSX 命令提示字元中，您可以使用 Azure CLI。 若要建立 VNet 使用 Azure CLI，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../articles/xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。
2. 執行**azure config 模式**命令以切換到資源管理員模式，如下所示。

        azure config mode arm

    以下是預期的輸出上方的命令︰

        info:    New mode is arm

3. 如果有需要，請執行 [ **azure 群組建立**若要建立新的 [資源] 群組中，，如下所示。 請注意命令的輸出。 顯示後輸出說明所用的參數的清單。 如需有關資源群組的詳細資訊，請造訪[Azure 資源管理員的概觀](../articles/virtual-network/resource-group-overview.md#resource-groups)。

        azure group create -n TestRG -l centralus

    以下是預期的輸出上方的命令︰

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n （或--名稱）**。 新的資源群組的的名稱。 我們案例中， *TestRG*。
    - **-l （或--位置）**。 將會建立新的資源群組 azure 的區域。 我們案例中， *centralus*。

4. 執行**azure 網路 vnet 建立**的命令來建立 VNet 及子網路，如下所示。 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    以下是預期的輸出上方的命令︰

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g （或--資源群組）**。 將會建立 VNet 資源群組的名稱。 我們案例中， *TestRG*。
    - **-n （或--名稱）**。 若要建立 VNet 的名稱。 我們的案例*TestVNet*
    - **-（或--地址前置字元）**。 用於 VNet 位址空間 CIDR 區塊的清單。 我們的案例*192.168.0.0/16*
    - **-l （或--位置）**。 將會建立 VNet azure 的區域。 我們案例中， *centralus*。

5. 執行**azure 網路 vnet 子網路建立**命令以建立子網路，如下所示。 請注意命令的輸出。 顯示後輸出說明所用的參數的清單。

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    以下是預期的輸出上方的命令︰

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (或-vnet 名稱**。 將會建立子網路 VNet 的名稱。 我們案例中， *TestVNet*。
    - **-n （或--名稱）**。 新的子網路的名稱。 我們案例中， *FrontEnd*。
    - **-（或--地址前置字元）**。 子網路 CIDR 區塊。 四個我們的案例、 *192.168.1.0/24*。

6. 如有必要，請重複步驟 5 上述建立其他子網路。 我們案例中，執行下列命令以建立*後端*子網路。

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. 執行**azure 網路 vnet 顯示**命令以檢視摘要資訊的新 vnet，如下所示。

        azure network vnet show -g TestRG -n TestVNet

    以下是預期的輸出上方的命令︰

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK
