<properties 
   pageTitle="設定兩個虛擬網路之間的 VPN 連線 |Microsoft Azure" 
   description="瞭解如何設定 VPN 連線與網域名稱解析介於兩個 Azure 虛擬網路，以及如何設定 HBase 地理複寫。" 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>設定兩個 Azure 虛擬網路之間的 VPN 連線  

> [AZURE.SELECTOR]
- [設定 VPN 連線能力](hdinsight-hbase-geo-replication-configure-vnets.md)
- [設定 DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [設定 HBase 複寫](hdinsight-hbase-geo-replication.md) 

Azure 虛擬網路網站-連線使用 VPN 閘道器提供使用 Ipsec/IKE 的安全通道。 VNets 可以在不同的訂閱及不同區域。 您甚至可以結合 VNet VNet 通訊的多網站設定。 有以下 VNet VNet 連線幾個原因︰

- 跨地區地理重複性和地理目前狀態 
- 加強隔離界限與地區多層應用程式 
- 跨組織間通訊 Azure 中的訂閱

如需詳細資訊，請參閱[設定 VNet 到 VNet 連線](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。 

若要查看其在視訊上︰

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

本教學課程屬於[數列][hdinsight-hbase-replication]建立 HBase 地理複寫。 

- 設定兩個虛擬網路 （此教學課程） 之間 VPN 連線能力
- [設定 DNS 以虛擬網路][hdinsight-hbase-geo-replication-dns]
- [設定 HBase 地理複寫][hdinsight-hbase-geo-replication]

下圖顯示兩個的虛擬網路，您將建立在本教學課程︰

![HDInsight HBase 複寫虛擬網狀圖][img-vnet-diagram]
 

##<a name="prerequisites"></a>必要條件
本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **使用 PowerShell 的 Azure 工作站**。

    之前執行的 PowerShell 指令碼，請先確認您已連線到您 Azure 的訂閱，請使用下列 cmdlet:

        Add-AzureAccount

    如果您有多個 Azure 訂閱，請使用下列 cmdlet 設定目前的訂閱︰

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


>[AZURE.NOTE] Azure 服務名稱及虛擬機器名稱必須是唯一的。 在此教學課程中所使用的名稱為 Contoso-[Azure 服務/VM 名稱]-[歐盟 / 美國]。 例如，Contoso-VNet-歐盟是 Azure 虛擬網路北美歐洲資料中心。Contoso DNS-適用於美國是 DNS 伺服器 VM 東亞 US 資料中心。 您必須得到您自己的名稱。
 

##<a name="create-two-azure-vnets"></a>建立兩個 Azure VNets



**若要建立虛擬網路中名為 [Contoso-VNet-歐盟北美歐洲**

1.  [Azure 傳統入口網站]登入[azure-portal]。
2.  按一下 [**新增]**，**網路服務**、**虛擬網路**，**自訂建立**。
3.  請輸入︰

    - **名稱**︰ Contoso-VNet-歐盟
    - **位置**︰ 北美歐洲

        本教學課程中使用北美歐洲及設定適用於美國的資料中心。 您可以選擇您自己的資料中心。
4.  請輸入︰

    - **DNS 伺服器**: （空白） 
    
        您必須在虛擬的網路中的名稱解析您自己的 DNS 伺服器。 如需何時使用 Azure 所提供的名稱解析，並使用您自己的 DNS 伺服器，請參閱[名稱解析 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。 若要設定名稱解析 VNets 之間的指示，請參閱[設定兩個 Azure 虛擬網路之間的 DNS][hdinsight-hbase-dns]。
  
    - **設定點為網站 VPN**: （未選取）

        點-網站不適用於這種情況。

    - **設定網站-VPN**: （未選取）
    
        您將設定網站-VPN 連線至 Azure 虛擬網路中的東亞 US 資料中心。
5.  請輸入︰

    -   **地址空間開始 IP**: 10.1.0.0
    -   **地址空間 CIDR**: / 16
    -   **IP 子網路 1 開始**︰ 10.1.0.0
    -   **子網路 1 CIDR**: / 24

    位址空間不可以與適用於美國的虛擬網路重疊。  

**若要建立虛擬網路中名為 [Contoso-VNet-歐盟西部歐洲**

- 重複使用下列的值的最後一個程序︰

    - **名稱**︰ Contoso-VNet-美國
    - **位置**︰ 東亞美制]。
     
    - **DNS 伺服器**: （空白）
    - **設定點為網站 VPN**: （未選取）
    - **設定網站-VPN**: （未選取）
     
    - **地址空間開始 IP**: 10.2.0.0
    - **地址空間 CIDR**: / 16
    - **IP 子網路 1 開始**︰ 10.2.0.0
    - **子網路 1 CIDR**: / 24

















##<a name="configure-a-vpn-connection-between-the-two-vnets"></a>設定兩個 VNets 之間的連線

###<a name="create-local-networks"></a>建立本機的網路

當您建立 VNet VNet 設定時，必須先設定每個 VNet 識別每個區域網路網站。 在此區段中，您會將每個 VNet 設定為 [區域網路。 區域網路與相對應的 VNet 共用相同的 IP 位址空間。

![設定 Azure VPN 網站-設定-azure 區域網路][img-vnet-lnet-diagram]


**若要建立區域網路稱為 [Contoso-LNet-歐盟比對 Contoso-VNet-歐盟網路位址空間**

1. 從 Azure 傳統入口網站中，按一下 [**新增]**，**網路服務**、**虛擬網路**，**新增的區域網路**。
3. 請輸入︰

    - **名稱**︰ Contoso-LNet-歐盟
    - **VPN 裝置的 IP 位址**︰ 192.168.0.1 （此位址會更新更新版本）

        一般而言，您可使用 VPN 裝置的實際的外部 IP 位址。 VNet VNet 設定，則會使用 VPN 閘道器 IP 位址。 假設您有無法建立 VPN 閘道器的兩個 VNets，您輸入 arbitary IP 位址，並回到 [修正]。
4.  請輸入︰

    - **地址空間開始 IP:** 10.1.0.0
    - **地址空間 CIDR:** /16
    
    這必須完全對應到您先前指定 Contoso-VNet-歐盟的範圍。

**若要建立區域網路稱為 「 Contoso LNet-適用於美國比對 Contoso VNet-適用於美國的網路位址空間**

- 重複使用下列參數的最後一個程序︰

    - **名稱**︰ Contoso-LNet-美國
    - **VPN 裝置的 IP 位址**︰ 192.168.0.1 （此位址會更新更新版本）
     
    - **地址空間開始 IP**: 10.2.0.0
    - **地址空間 CIDR**: / 16


###<a name="create-vpn-gateways"></a>建立 VPN 閘道器

在此組態中有兩個部分。 您設定 VNet 網站-連線到本機網路的第一次，然後您建立動態路由 VPN。 以 VNet VNet 需要使用動態路由 Vpn Azure VPN 閘道器。 Azure 靜態路由 Vpn 不受支援。

**若要設定 Contoso LNet-適用於美國 Contoso-VNet-歐盟網站-連線**

1.  從 Azure 傳統入口網站，請在左窗格中，按一下**網路**
2.  按一下 [ **Contoso-VNet-歐盟**]。
3.  按一下 [ **CONFIGUE** ] 索引標籤。
4.  檢查**連線到本機的網路**。
5.  在 [**區域網路**中，選取 [ **Contoso LNet-適用於美國**]。
6.  按一下 [**新增閘道器子網路**虛擬網路地址空格] 區段中。
7.  按一下 [**儲存**]。
8.  按一下**[確定**] 以確認。


**若要建立 Contoso-VNet-歐盟 VPN 閘道器**

1.  從 Azure 傳統入口網站中，按一下 [**儀表板**] 索引標籤。
4.  按一下 [**建立閘道器**] 底部的頁面]，然後按一下 [**動態路由**。
5.  按一下 [ **]**以確認。 請注意頁面上的閘道器圖形變成黃色以顯示 [建立閘道器。 通常會採用建立閘道器的 15 分鐘。

    當閘道器狀態變更為連線時，則會顯示在儀表板每一個閘道器的 IP 位址。 請記小心不要混合這些的每個 VNet 至對應的 IP 位址。 以下是當您編輯版面配置區的 IP 位址 VPN 裝置在 [區域網路使用的 IP 位址。

6.  製作其複本**閘道器 IP 位址**。 您會使用它來設定 VPN 閘道器 IP 位址的 Contoso-VNet-歐盟下一節。

**若要建立 Contoso-VNet-歐盟 VPN 閘道器**

- 重複 Contoso Vnet-適用於美國的設定 Contoso VNet-適用於美國網站-連線至 Contoso LNet 歐盟，並建立 VPN 閘道器的最後兩個程序。 當您完成時，您必須要有 vpn 才能閘道器 IP 位址 Contoso VNet-適用於美國的。


### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>IP 位址的區域網路設定的 VPN 裝置
在最後一個區段中，您可以建立 VPN 閘道器的每個 VNets。 您擁有適合 VPN 閘道器的 IP 位址。 現在您可以回到設定的區域網路 VPN 裝置 IP 位址。

**若要設定 Contoso-LNet-歐盟 VPN 裝置的 IP 位址** 

1.  從 Azure 傳統入口網站中，按一下 [在左窗格的 [**網路**]。
2.  按一下 [從上方的 [**區域網路**]。
3.  按一下 [ **Contoso-LNet-歐盟**，，然後按一下 [底部的 [**編輯**。
4.  更新**VPN 裝置的 IP 位址**。  這是您從 [儀表板] 索引標籤的歐盟-VNET Contoso-取得的地址。
5.  按一下 [從左至右] 按鈕。
6.  按一下 [檢查] 按鈕。

**若要設定 Contoso LNet-適用於美國 VPN 裝置的 IP 位址** 

- 重複 Contoso LNet-適用於美國的設定 VPN 裝置的 IP 位址的最後一個程序。

###<a name="set-vnet-gateway-keys"></a>設定 VNet 閘道器金鑰

Vnet 閘道器會使用共用的金鑰來驗證虛擬網路之間的連線。 無法從 Azure 傳統入口網站設定金鑰。 您必須使用 PowerShell 或.NET SDK。

**若要設定按鍵**

1. 從您的工作站開啟**Windows PowerShell ise [以系統**或 Windows PowerShell 主控台。
2. 更新這個追蹤指令碼中的參數，並執行︰

        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##<a name="check-the-vpn-connection"></a>檢查 VPN 連線 

部署至 VNets 任何 Vm，而您可以使用虛擬網路視覺化圖表 VNet 儀表板頁面 Azure 傳統入口網站上，核取連線狀態︰

![HDInsight HBase 複寫虛擬網路 VPN 連線狀態][img-vpn-status]
  



##<a name="next-steps"></a>後續步驟

在本教學課程中，您已經學會如何設定兩個 Azure 虛擬網路之間的連線。 數列中的其他兩個文件包含︰

- [介於兩個 Azure 虛擬網路設定 DNS][hdinsight-hbase-geo-replication-dns]
- [設定 HBase 地理複寫][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 
