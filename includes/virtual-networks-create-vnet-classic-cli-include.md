## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>如何建立使用 Azure CLI 傳統 VNet

若要管理 Azure 資源的電腦執行 Windows、 Linux 或 OSX 命令提示字元中，您可以使用 Azure CLI。 若要建立 VNet 使用 Azure CLI，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../articles/xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。
2. 執行**azure 網路 vnet 建立**的命令來建立 VNet 及子網路，如下所示。 顯示後輸出說明所用的參數的清單。

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    預期的輸出︰

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **-vnet**。 若要建立 VNet 的名稱。 我們的案例*TestVNet*
    - **-e （或--位址空間）**。 VNet 位址空間。 我們的案例*192.168.0.0*
    - **-i （或-cidr）**。 網路遮罩 CIDR 格式。 我們案例中， *16*。
    - **-n (或--子網路名稱**)。 第一個子網路的名稱。 我們案例中， *FrontEnd*。
    - **-p （或--子網路開始 ip）**。 啟動子網路或子網路位址空間的 IP 位址。 我們案例中， *192.168.1.0*。
    - **-r （或--子網路 cidr）**。 網路遮罩子網路 CIDR 格式。 我們案例中， *24*。
    - **-l （或--位置）**。 將會建立 VNet azure 的區域。 我們案例中，*美國中部*。

3. 執行**azure 網路 vnet 子網路建立**命令以建立子網路，如下所示。 顯示後輸出說明所用的參數的清單。

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    以下是預期的輸出上方的命令︰

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (或-vnet 名稱**。 將會建立子網路 VNet 的名稱。 我們案例中， *TestVNet*。
    - **-n （或--名稱）**。 新的子網路的名稱。 我們案例中*後, 端*。
    - **-（或--地址前置字元）**。 子網路 CIDR 區塊。 四個我們的案例、 *192.168.2.0/24*。

4. 執行**azure 網路 vnet 顯示**命令以檢視摘要資訊的新 vnet，如下所示。

            azure network vnet show

    以下是預期的輸出上方的命令︰

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK
