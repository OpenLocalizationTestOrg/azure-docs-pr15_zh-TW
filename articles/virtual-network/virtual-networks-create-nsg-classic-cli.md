<properties
   pageTitle="如何使用 Azure CLI 傳統模式中建立 NSGs |Microsoft Azure"
   description="瞭解如何建立及使用 Azure CLI 傳統模式中部署 NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>如何在 Azure CLI 建立 NSGs （傳統）

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋的傳統部署模型。 您也可以[建立 NSGs 資源管理員部署模型中](virtual-networks-create-nsg-arm-cli.md)。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

下列範例 Azure CLI 命令預期已根據上述情況建立簡單環境。 如果您想要執行這份文件中所顯示的命令，先建立測試環境[建立 VNet](virtual-networks-create-vnet-classic-cli.md)。

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>如何建立前端子網路 NSG
若要建立名為根據上述情況命名的**NSG FrontEnd** NSG，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md) ，然後依照您可在此選取您的 Azure 帳戶及訂閱一點的指示進行。

2. 執行**`azure config mode`**命令，以切換到傳統模式，如下所示。

        azure config mode asm

    預期的輸出︰

        info:    New mode is asm

3. 執行**`azure network nsg create`**] 命令，建立 NSG。

        azure network nsg create -l uswest -n NSG-FrontEnd

    預期的輸出︰

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    參數︰

    - **-l （或--位置）**。 將會建立新的 NSG azure 的區域。 我們案例中， *westus*。
    - **-n （或--名稱）**。 新的 NSG 名稱。 我們案例中， *NSG FrontEnd*。

4. 執行**`azure network nsg rule create`**] 命令，建立一個規則，允許連接埠 3389 (RDP) 從網際網路存取。

        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

    預期的輸出︰

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK

    參數︰

    - **-（或-nsg 名稱）**。 NSG 建立規則的名稱。 我們案例中， *NSG FrontEnd*。
    - **-n （或--名稱）**。 新規則的名稱。 我們案例中， *rdp 規則*。
    - **-c （或--動作）**。 存取層級的規則 （拒絕或允許）。
    - **-p （或--通訊協定）**。 通訊協定 (Tcp、 Udp，或 *) 的規則。
    - **-r （或--類型）**。 連線 （輸入或輸出） 的方向。
    - **-y （或--優先順序）**。 規則的優先順序。
    - **-f （或--來源位址首碼）**。 來源 CIDR 或使用預設標籤中的地址前置字元。
    - **command 和 o （或--來源連接埠範圍）**。 來源連接埠] 或連接埠範圍。
    - **-e （或--目的地址前置詞）**。 目的地 CIDR 或使用預設標籤中的地址前置字元。
    - **-u （或--目的地連接埠範圍）**。 目的地連接埠或連接埠範圍。

5. 執行**`azure network nsg rule create`**] 命令，建立一個規則，允許連接埠 80 (HTTP) 從網際網路存取。

        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

    預期的 putput:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. 執行**`azure network nsg subnet add`**NSG 連結至前端子網路的命令。

        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

    預期的輸出︰

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>如何建立後端子網路 NSG
若要建立名為命名的*NSG 後端*為基礎上述 NSG，請遵循下列步驟。

3. 執行**`azure network nsg create`**] 命令，建立 NSG。

        azure network nsg create -l uswest -n NSG-BackEnd

    預期的輸出︰

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    參數︰

    - **-l （或--位置）**。 將會建立新的 NSG azure 的區域。 我們案例中， *westus*。
    - **-n （或--名稱）**。 新的 NSG 名稱。 我們案例中， *NSG FrontEnd*。

4. 執行**`azure network nsg rule create`**] 命令，建立一個規則，允許從前端子網路存取連接埠 1433 (SQL)。

        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

    預期的輸出︰

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK


5. 執行**`azure network nsg rule create`**命令，以建立規則，以拒絕存取網際網路。

        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

    預期的 putput:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. 執行**`azure network nsg subnet add`**NSG 連結到後端的子網路的命令。

        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

    預期的輸出︰

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK
