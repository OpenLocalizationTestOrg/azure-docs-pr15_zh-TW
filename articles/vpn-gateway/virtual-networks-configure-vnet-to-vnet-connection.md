<properties
   pageTitle="設定傳統部署模型 VNet-VNet 連線 |Microsoft Azure"
   description="如何使用 PowerShell 和 Azure 傳統入口網站合作 Azure 虛擬網路連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>傳統的部署模型 VNet-VNet 連線的設定

> [AZURE.SELECTOR]
- [資源管理員-Azure 入口網站](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [資源管理員-PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [傳統-傳統入口網站](virtual-networks-configure-vnet-to-vnet-connection.md)



本文會引導您建立及使用傳統的部署模型 （也稱為服務管理） 合作的虛擬網路連線的步驟。 下列步驟使用 Azure 傳統入口網站，來建立 VNets 和閘道和 PowerShell 設定 VNet-VNet 連線。 您無法在入口網站設定連線。

![VNet VNet 連線的圖表](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>部署模型和 VNet-VNet 連線的方法

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表顯示目前可用的部署模型和 VNet-VNet 設定的方法。 當有包含設定步驟的文章時，我們連結直接從這個資料表。

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>關於 VNet-VNet 連線

虛擬網路連線到另一個虛擬網路 (VNet-VNet) 很類似虛擬網路連線至內部部署網站位置。 這兩種連線類型使用 VPN 閘道器提供使用 IPsec/IKE 的安全通道。 

您連線 VNets 可以在不同的訂閱及不同區域。 您可以結合 VNet VNet 通訊的多網站設定。 這個選項可讓您建立的網路拓撲結合跨內部部署連線虛擬間的網路連線。


### <a name="why-connect-virtual-networks"></a>為什麼連線虛擬網路？

若要將虛擬網路連線，原因如下︰

- **跨地區地理重複性和地理目前狀態**
    - 您可以設定您自己的地理複寫或同步處理使用安全連線不透過網際網路的結束點。
    - Azure 負載平衡器及 Microsoft 或協力廠商叢集技術，您可以設定跨多個 Azure 區域高度可用地理重複使用的工作量。 其中一個重要的範例是設定 SQL 永遠分配跨多個 Azure 區域的可用群組。

- **加強隔離界限與地區多層應用程式**
    - 內的相同的區域，您可以設定與多個 VNets 連線與強式隔離和安全間層通訊搭配多層應用程式。

- **跨組織間通訊 Azure 中的訂閱**
    - 如果您有多個 Azure 訂閱時，您可以連線的不同訂閱的工作負載一起安全地虛擬網路之間。
    - 適用於企業或服務提供者，您可以啟用安全的 VPN 技術 Azure 中的跨組織通訊。

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>傳統 VNets VNet-VNet 常見問題集

- 虛擬網路可以在相同或不同的訂閱。

- 虛擬網路可以在相同或不同 Azure 區域 （位置）。

- 在雲端服務或負載平衡端點無法跨虛擬網路，即使他們連接在一起。

- 連接在一起的多個虛擬網路，不需要任何 VPN 裝置。

- VNet-VNet 支援連接 Azure 虛擬網路。 它不支援連線的虛擬機器或雲端服務不部署虛擬網路。

- VNet-VNet 需要動態路由閘道器。 不支援 azure 靜態路由閘道器。

- 虛擬網路連線可以同時使用多個網站 vpn。 有 10 VPN 通道連線至其他的虛擬網路或內部部署網站的虛擬網路 VPN 閘道器的最大值。

- 不能重疊的虛擬網路與內部部署的區域網路網站的位址空間。 重疊的地址空格會建立虛擬網路或上傳失敗的 netcfg 設定檔。

- 不支援的虛擬網路之間的多餘通道。

- 所有要有 vpn 才能通道 VNet，包括 P2S Vpn 共用 VPN 閘道器與相同的 VPN 閘道器存留時間 SLA Azure 中可用的頻寬。

- VNet-VNet 流量會透過 Azure 骨幹。


## <a name="step1"></a>步驟 1-規劃您的 IP 位址範圍

請務必決定用來設定您的虛擬網路的範圍。 此設定，您必須確定不 VNet 範圍的重疊彼此，或任何連線到本機網路。

下表顯示如何定義您 VNets 的範例。 使用範圍為只指導方針。 寫下您的虛擬網路的範圍。 您需要更新版本的步驟執行這項資訊。

**範例設定**

|虛擬網路  |位址空間               |區域      |連線到本機網路網站|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1            |VNet1 (10.1.0.0/16)         |美國西部     |VNet2Local (10.2.0.0/16)      |
|VNet2            |VNet2 (10.2.0.0/16)         |日本東部  |VNet1Local (10.1.0.0/16)      |
  
## <a name="step-2---create-vnet1"></a>步驟 2-建立 VNet1

在此步驟中，我們會建立 VNet1。 使用任何範例，請務必替代成您自己的值。 如果您 VNet 已經存在，您不需要執行此步驟。 但是，您需要驗證的 IP 位址範圍沒有與重疊之處範圍為您的第二個 VNet，或您要使用的其他 VNets 一個連線。

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。 本文中，我們會使用 [傳統] 入口網站，因為某些必要的組態設定目前尚不提供 Azure 入口網站中。

2. 在畫面的左上角，按一下 [**新增** > **網路服務** > **虛擬網路** > 若要開始設定精靈的 [**建立自訂**。 當您瀏覽精靈，將指定的值至每個頁面。

### <a name="virtual-network-details"></a>虛擬網路詳細資料

在虛擬網路詳細資料頁面上，輸入下列資訊︰

  ![虛擬網路詳細資料](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **名稱**-虛擬網路的名稱。 例如，VNet1。
  - **位置**– 當您建立虛擬網路時，您與產生關聯的 Azure 的位置 （地區）。 例如，如果您已部署至虛擬網路實體位於西適用於美國的 Vm 要請選取該位置。 您無法變更建立後，您的虛擬網路與相關聯的位置。

### <a name="dns-servers-and-vpn-connectivity"></a>DNS 伺服器及 VPN 連線

在 [DNS 伺服器及 VPN 連線] 頁面中，輸入下列資訊，然後再按一下上右下方的下一個箭號。

  ![DNS 伺服器及 VPN 連線](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

- **DNS 伺服器**-輸入的 DNS 伺服器名稱及 IP 位址，或從下拉式清單中選取先前已註冊的 DNS 伺服器。 此設定不會建立 DNS 伺服器。 其可讓您指定您想要使用這個虛擬網路名稱解析 DNS 伺服器。 如果您想要有虛擬網路之間的名稱解析，您必須設定您自己的 DNS 伺服器，而不是使用所提供的 Azure 名稱解析。
- 沒有選取任何 P2S 或 S2S 連線的核取方塊。 按一下 [上移至下一個畫面右下方的箭號。

### <a name="virtual-network-address-spaces"></a>虛擬網路位址空間

在虛擬網路位址空間頁面上，指定您想要用於虛擬網路位址範圍。 這些是動態 IP 位址 (DIPS)，會指派 Vm 及其他您部署到這個虛擬網路的角色執行個體。 

如果您正在建立也必須與您內部網路的連線 VNet，務必選取範圍不會重疊使用的用於您的內部網路的範圍。 在此情況下，您需要進行共同作業與您的網路系統管理員。 您的網路系統管理員可能需要設想的 IP 位址範圍從內部部署網路位址空間，以使用您 VNet。

  ![虛擬網路位址空間頁面](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **位址空間**-包括開始的 IP 和地址計數。 請確認您所指定的地址空格不致重疊的地址空間內部網路上的任何。 針對此範例中，我們使用 VNet1 10.1.0.0/16。
  - **新增子網路**-包括開始的 IP 和地址計數。 其他的子網路且並非必要，但您可能會想要的靜態 DIPS Vm 建立不同的子網路。 要與其他角色執行個體是分開的子網路中有您 Vm 或者。
 
在右下角的頁面和虛擬網路上的 [**按一下核取記號**會開始建立。 完成之後，您會看到 「 建立 」 狀態] 下所列在 [網路] 頁面上。

## <a name="step-3---create-vnet2"></a>步驟 3-建立 VNet2

接下來，請重複上述步驟來建立另一個 VNet。 更新版本的步驟，您將連接兩個 VNets。 您可以在 [步驟 1 中的[範例設定](#step1)參考。 如果您 VNet 已經存在，您不需要執行此步驟。 不過，您需要驗證的 IP 位址範圍沒有與任何其他 VNets 或內部網路的連線到您想要的重疊之處。

## <a name="step-4---add-the-local-network-sites"></a>步驟 4-新增區域網路網站

當您建立 VNet-VNet 設定時，必須先設定會顯示在入口網站的 [**區域網路**頁面上的區域網路網站。 Azure 會使用每個區域網路網站中指定的設定，以決定如何將 VNets 之間的流量路由傳送。 您決定要使用每個區域網路網站參照的名稱。 最好使用敘述，從下拉式清單在稍後步驟中選取的值。

例如，VNet1 連線至您建立的名稱為 「 VNet2Local 」 的區域網路網站。 設定 VNet2Local 包含地址的前置字元 VNet2 及 VNet2 閘道器的公用 IP 位址。 VNet2 連線至區域網路網站建立名為 「 VNet1Local 」，其中包含的地址首碼 VNet1 和 VNet1 閘道器的公用 IP 位址。

### <a name="localnet"></a>新增 [區域網路網站 VNet1Local

1. 在畫面的左上角，按一下 [**新增** > **網路服務** > **虛擬網路** > **新增本機的網路**。

2. 在**指定的區域網路詳細資料**頁面上，[**名稱**] 中，輸入您想要用來代表您要連線至網路的名稱。 在此範例中，您可以使用 「 VNet1Local 「 若要參照的 VNet1 的 IP 位址範圍及閘道器。

3. 如果**要有 vpn 才能裝置 IP 位址 （選用）**]，指定任何有效的公用 IP 位址。 一般而言，您可使用 VPN 裝置的實際的外部 IP 位址。 VNet-VNet 設定，您可以使用給您 VNet 閘道器的公用 IP 位址。 但假設您還沒有建立閘道器，您可以指定任何有效的公用 IP 位址為版面配置區。 不會將此留白-這不是選擇性的這項設定。 在接下來的步驟，您將這些設定，請返回使用及設定其對應的閘道器 IP 位址後 Azure 產生。 按一下箭號以前進到下一個畫面。

4. 在**指定 [地址] 頁面**中，輸入 VNet1 的 IP 位址範圍和地址計數。 這必須符合確實設定為 VNet1 的範圍。 Azure 會使用您將適用於 VNet1 流量路由傳送至指定的 IP 位址範圍。 按一下 [建立區域網路的核取記號。

### <a name="add-the-local-network-site-vnet2local"></a>新增 [區域網路網站 VNet2Local

使用上述步驟來建立的區域網路的網站 」 VNet2Local 」。 如有需要，您可以參照[範例設定](#step1)步驟 1 中的值。

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>設定為指向 [區域網路的每個 VNet

每個 VNet 必須指向您想要將流量路由傳送個別的區域網路。 

#### <a name="for-vnet1"></a>針對 VNet1

1. 瀏覽至 [**設定**] 頁面的虛擬網路**VNet1**。 
2. 底下網站的連線，選取 [「 連接至本機網路 」，然後選取**VNet2Local**為區域網路，從下拉式清單。 
3. 儲存您的設定。

#### <a name="for-vnet2"></a>針對 VNet2

1. 瀏覽至 [**設定**] 頁面的虛擬網路**VNet2**。 
2. 在網站的連線，選取 [連線到區域網路]，然後選取 [ **VNet1Local**從下拉式清單為區域網路。 
3. 儲存您的設定。

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>步驟 5-針對每個 VNet 設定閘道器

設定為每個虛擬網路動態路由閘道器。 此設定不支援靜態路由閘道器。 如果您使用的 VNets 的先前已設定，以及已有動態路由閘道器，您不需要執行此步驟。 如果您的閘道器是靜態路由，必須先將它們刪除和重新建立為動態路由閘道器。 如果您刪除閘道器，取得發行指派的公用 IP 位址，及您需要以返回並重新設定任何的區域網路和新的公用 IP 位址新增閘道器的 VPN 裝置。

1. 在**網路**頁面上，請確認 [狀態] 欄虛擬網路**建立**。

2. 在 [**名稱**] 欄中，按一下虛擬網路的名稱。 例如，我們會使用 「 VNet1 」。

3. 在**儀表板**頁面，請注意此 VNet 沒有尚未設定的閘道器。 您會看到此狀態變更為設定您的閘道器的步驟進行。

4. 在頁面底部，按一下 [**建立閘道器**和**動態路由**]。 當系統提示您確認您要建立閘道器時，請按一下 [是]。

    ![閘道器類型](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  

5. 在您的閘道器建立時，請注意頁面上的閘道器圖形變成黃色，則 「 建立閘道器]。 通常會採用建立閘道器的資訊 30 分鐘。

6. 針對 VNet2 中重複相同的步驟。 您不需要完成之前，您其他 VNet 建立閘道器的第一個 VNet 閘道器。

7. 閘道器狀態變更為 [連線]，請對每個閘道的公用 IP 位址時，在儀表板中顯示。 請記小心不要混和的每個 VNet 至對應的 IP 位址。 以下是當您編輯版面配置區的 IP 位址的每個區域網路 VPN 裝置時所使用的 IP 位址。

## <a name="step-6---edit-the-local-network"></a>步驟 6-編輯區域網路

1. 在**本機的網路**] 頁面上，按一下您想要編輯的區域網路名稱的名稱，然後按一下 [在頁面底部的 [**編輯**]。 如果**要有 vpn 才能裝置 IP 位址**]，輸入對應到 VNet 的閘道器的 IP 位址。 例如，VNet1Local，將放在指派給 VNet1 的閘道器 IP 位址。 然後按一下在頁面底部的箭號。

2. 在**指定的地址空間**頁面上，按一下 [在右下方的核取記號而不進行任何變更。

## <a name="step-7---create-the-vpn-connection"></a>步驟 7-建立 VPN 連線

當已完成所有先前的步驟時，設定 IPsec/IKE 預先共用索引鍵，然後建立的連線]。 這組步驟使用 PowerShell，並無法在入口網站設定。 了解如何安裝 Azure PowerShell cmdlet 的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 請務必下載最新版的服務管理 (SM) cmdlet。 

1. 開啟 Windows PowerShell，並登入。

        Add-AzureAccount

2. 選取您 VNets 位於訂閱]。

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. 建立關聯。 在範例中，請注意，共用的金鑰完全相同。 共用的金鑰一定要相符。


    VNet1 到 VNet2 連線

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 到 VNet1 連線

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. 等待初始化連線。 一旦初始化閘道器，閘道器看起來類似下圖中。

    ![閘道器狀態-連線](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  

    [AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>後續步驟

您可以新增您的虛擬網路虛擬機器。 請參閱[虛擬機器文件](https://azure.microsoft.com/documentation/services/virtual-machines/)的詳細資訊。



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 
