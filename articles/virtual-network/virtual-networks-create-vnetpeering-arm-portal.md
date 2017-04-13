<properties
   pageTitle="建立 VNet 對等使用 Azure 入口網站 |Microsoft Azure"
   description="瞭解如何建立一個虛擬網路 Azure 入口網站中資源管理員。"
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>建立虛擬網路，外面使用 Azure 入口網站

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

若要建立 VNet 對等的上方案例使用 Azure 入口網站，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2. 若要建立對等的 VNET，必須先建立一個兩個 VNets 之間的每個方向的兩個連結。 您可以建立 VNET 至 VNET2 VNET1 的對等的連結。 在入口網站中，按一下 [**瀏覽** > **選擇虛擬網路**

    ![建立對等 Azure 入口網站中的 VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. 在虛擬網路刀選擇 VNET1，按一下 Peerings，然後按一下 [新增]

    ![選擇 [對等](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. 在新增對等刀，授與對等的連結名稱 LinkToVnet2、 選擇 [訂閱及對虛擬網路 VNET2，按一下 [確定]。

    ![連結至 VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. 一旦建立此 VNET 對等的連結。 您可以查看連結狀態為下列動作︰

    ![連結狀態](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. 下一步建立以 VNET1 VNET2 VNET 對等連結。 在虛擬網路刀選擇 VNET2，按一下 Peerings，然後按一下 [新增]

    ![從其他 VNet 等](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. 在新增對等刀，授與對等的連結名稱 LinkToVnet1，選擇 [訂閱及對虛擬網路中，按一下 [確定]。

    ![建立虛擬網路磚](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. 一旦建立此 VNET 對等的連結。 您可以查看連結狀態為下列動作︰

    ![最終連結狀態](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. 檢查狀態 LinkToVnet2 並將其現在會變更為已連線以及。  

    ![最終連結狀態 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [AZURE.NOTE] 僅建立 VNET 對等，如果連接兩個連結。

有幾個可設定每個連結的內容︰

|選項|描述|預設值|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|是否地址等 VNet 要 Virtual_network 標籤的一部分的空間|[是]|
|AllowForwardedTraffic|是否接受或卸除不源自 peered VNet 流量|無|
|AllowGatewayTransit|允許對使用 VNet 閘道 VNet|無|
|UseRemoteGateways|使用您對 VNet 閘道器。 對 VNet 必須設定閘道器，並選取 AllowGatewayTransit。 如果您有閘道器設定，您便無法使用此選項|無|

在對等 VNet 每個連結都有一組屬性上方。 從入口網站，您可以按一下 VNet 對等連結和變更任何可用的選項，按一下 [儲存]，讓變更生效。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2. 在此範例中我們會使用兩個訂閱 A 和 B 使用者 a 和使用者 b 的兩個使用者的訂閱中的權限分別
3. 在入口網站中，按一下 [瀏覽]，選擇 [虛擬網路]。 按一下 VNET，然後按一下 [新增]。

    ![案例 2 瀏覽](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. 在 [新增存取刀中，按一下 [選取角色和選擇網路參與者新增使用者、 輸入使用者 b 符號在 [名稱]，按一下 [確定]。

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    這不是需求，可以建立對等，即使使用者個別提高對等的要求的其個別 Vnets，只要符合要求。 為本機 VNet 中的使用者新增權限的使用者的其他 VNet 讓您更容易在入口網站執行安裝程式。

5. 然後登入使用者 b SubscriptionB 的權限的使用者身分 Azure 入口網站。 請遵循上述步驟來新增使用者 a 為網路參與者。

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [AZURE.NOTE] 您可以先登出，然後登入兩個使用者工作階段，確定已成功啟用授權的瀏覽器中。

6. 登入使用者 a 入口網站，瀏覽至 VNET3 刀，請按一下 [Peering，核取 [知道我的資源識別碼 」] 核取方塊，然後輸入資源識別碼 VNET5 中的格式] 下方。

    / subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}

    ![資源識別碼](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. 為使用者 b 和依照上述步驟來建立對等連結的 VNET5 VNet3 入口網站登入。

    ![資源識別碼 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. 將會建立對等及任何的虛擬機器中 VNet3 應會與任何的虛擬機器中 VNet5 通訊

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 第一個步驟是，VNET 對等的連結從 HubVnet VNET1。 請注意，允許轉寄流量選項的連結。

    ![基本對等](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. 下一步，就可以建立 HubVnet VNET1 從對等連結。 請注意，已選取儲存格內允許轉寄流量選項。

    ![基本對等](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. 對等建立之後，您可以參照此[文件](virtual-network-create-udr-arm-ps.md)，並定義以便 VNet1 流量重新導向透過虛擬的應用裝置，若要使用其功能的使用者定義 Route(UDR)。 當您指定的下一個躍點的地址傳送時，您可以將其為中對 VNet HubVNet 虛擬應用裝置的 IP 位址


[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]



1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。

2. 若要建立 VNET 對等在這個案例中，您需要建立只有一個連結，在 [傳統] 中的 Azure 資源管理員虛擬網路。 就是從**VNET1**至**VNET2**。 在入口網站中，按一下 [**瀏覽]** > 選擇**虛擬網路**

3. 在虛擬網路刀中，選擇 [ **VNET1**]。 按一下**Peerings**，然後按一下 [**新增**]。

4. 在新增對等刀，名稱為您的連結。 以下稱為**LinkToVNet2**。 對等的詳細資料] 下選取 [**傳統**]。

5. 請選擇 [訂閱及對虛擬網路**VNET2**。 然後按一下 [確定]。

    ![連結 Vnet1 Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)

6. 此 VNet 對等連結建立後，會 peered 兩個虛擬網路，您會看到下列︰

    ![檢查對等的連線](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)


## <a name="remove-vnet-peering"></a>移除 VNet 對等

1.  從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2.  移至虛擬網路刀、 按一下 Peerings，按一下您要移除，請按一下 [刪除] 按鈕的連結。

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. 一旦您在對等 VNET 移除一個連結，請對連結狀態就會前往中斷連線。

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. 在這個狀態，您無法重新建立連結直到等連結狀態變更為起始。 我們建議您移除再重新建立 VNET 對等的兩個連結。
