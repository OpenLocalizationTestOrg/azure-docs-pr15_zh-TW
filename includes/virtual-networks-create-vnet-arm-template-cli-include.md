## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署 ARM 範本

若要部署您下載使用 Azure CLI ARM 範本，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../articles/xplat-cli-install.md) ，然後依照您可在此選取您的 Azure 帳戶及訂閱一點的指示進行。
2. 執行**`azure config mode`**命令，以切換到資源管理員模式，如下所示。

        azure config mode arm

    以下是預期的輸出上方的命令︰

        info:    New mode is arm

3. 如果有需要，請執行**`azure group create`**若要建立新的 [資源] 群組中，如下所示。 請注意命令的輸出。 顯示後輸出說明所用的參數的清單。 如需有關資源群組的詳細資訊，請造訪[Azure 資源管理員的概觀](../articles/resource-group-overview.md)。

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

4. 執行**`azure group deployment create`**cmdlet 來使用的範本及參數部署新 VNet 檔案您下載並修改上方。 顯示後輸出說明所用的參數的清單。

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    以下是預期的輸出上方的命令︰

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g （或--資源群組）**。 中，將會建立新的 VNet 上的 [資源] 群組的名稱。
    - **-f （或--範本檔案）**。 ARM 範本檔案的路徑。
    - **-e （或--參數檔案）**。 ARM 參數檔案的路徑。

5. 執行**`azure network vnet show`**命令，以檢視新 vnet 的內容，如下所示。

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
