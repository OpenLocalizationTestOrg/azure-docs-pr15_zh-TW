<properties 
   pageTitle="介於兩個 Azure 虛擬網路設定 DNS |Microsoft Azure" 
   description="瞭解如何設定 VPN 連線和兩個的虛擬網路之間的網域名稱解析，以及如何設定 HBase 地理複寫。" 
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

# <a name="configure-dns-between-two-azure-virtual-networks"></a>介於兩個 Azure 虛擬網路設定 DNS

> [AZURE.SELECTOR]
- [設定 VPN 連線能力](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [設定 DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [設定 HBase 複寫](hdinsight-hbase-geo-replication.md) 


瞭解如何新增與設定處理名稱解析，且透過虛擬網路 Azure 虛擬網路 DNS 伺服器。

本教學課程為[數列]的第二部分[hdinsight-hbase-geo-replication]建立 HBase 地理複寫︰

- [設定兩個虛擬網路之間 VPN 連線能力][hdinsight-hbase-geo-replication-vnet]
- 設定 DNS 以虛擬網路 （此教學課程）
- [設定 HBase 地理複寫][hdinsight-hbase-geo-replication]


下圖顯示[兩個虛擬網路之間 VPN 連線設定]中所建立的兩個虛擬網路[hdinsight-hbase-geo-replication-vnet]:

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

- **兩個 Azure 虛擬 VPN 連線網路**。  如需相關指示，請參閱[設定兩個 Azure 虛擬網路之間的 VPN 連線][hdinsight-hbase-geo-replication-vnet]。

>[AZURE.NOTE] Azure 服務名稱及虛擬機器名稱必須是唯一的。 在此教學課程中所使用的名稱為 Contoso-[Azure 服務/VM 名稱]-[歐盟 / 美國]。 例如，Contoso-VNet-歐盟是 Azure 虛擬網路北美歐洲資料中心。Contoso DNS-適用於美國是 DNS 伺服器 VM 東亞 US 資料中心。 您必須得到您自己的名稱。
 
 
##<a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>建立要做為 DNS 伺服器 Azure 虛擬機器

**若要建立虛擬機器中 Contoso VNet 歐盟，稱為 [Contoso-DNS-歐盟**

1.  按一下 [**新增]**，**計算**、**虛擬機器**，**從圖庫**。
2.  選擇 [ **Windows Server 2012 R2 資料中心**]。
3.  請輸入︰
    - **虛擬機器名稱**︰ Contoso-DNS-歐盟
    - **新的使用者名稱**︰ 
    - **新的密碼**︰ 
4.  請輸入︰
    - **雲端服務**︰ 建立新的雲端服務
    - **地區/相關性群組/虛擬網路**: （選取 [Contoso-VNet-歐盟）
    - **虛擬網路子**︰ 子網路 1
    - **儲存帳戶**︰ 使用自動產生的儲存空間的帳戶
    
        雲端服務名稱會的虛擬機器名稱相同。 在此情況下，這是 Contoso-DNS-歐盟。 後續的虛擬機器，我可以選擇使用相同的雲端服務。  在相同的雲端服務的所有虛擬機器都共用相同的虛擬網路和網域後置字元。

        儲存帳戶用來儲存虛擬機器圖像檔案。 
    - **端點**: （向下捲動並選取 [ **DNS**） 

建立虛擬機器後，找出內部 IP 與外部的 IP。

1.  按一下虛擬機器名稱，**限 DNS Contoso 歐盟**。
2.  按一下 [**儀表板**]。
3.  寫下︰
    - 公用虛擬 IP 位址
    - 內部 IP 位址


**若要建立虛擬機器中 Contoso VNet-適用於美國，稱為 [Contoso DNS-適用於美國** 

- 重複使用下列的值建立虛擬機器的相同程序︰
    - 虛擬機器名稱︰ Contoso-DNS-美國
    - 地區/相關性群組/虛擬網路︰ 選取 [Contoso VNET-適用於美國
    - 虛擬網路子網路︰ 子網路-1
    - 儲存帳戶︰ 使用自動產生的儲存空間的帳戶
    - 端點: （選取 [DNS]）

##<a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>設定兩個虛擬機器的靜態 IP 位址

DNS 伺服器需要靜態 IP 位址。  從 Azure 傳統入口網站，就無法完成這個步驟。 您會使用 PowerShell 的 Azure。

**若要設定的兩個虛擬機器的靜態 IP 位址**

1. 開啟 Windows PowerShell ise [以系統。
2. 執行下列 cmdlet。  

        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
        
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

    ServiceName 是雲端服務名稱。 因為 DNS 伺服器是雲端服務的第一個虛擬機器，則雲端服務名稱為的虛擬機器名稱相同。

    您可能需要更新 ServiceName 和名稱，以符合您的名稱。


##<a name="add-the-dns-server-role-the-two-virtual-machines"></a>新增的 DNS 伺服器角色兩個虛擬機器

**若要為限 DNS Contoso 歐盟新增 DNS 伺服器角色**

1.  從 Azure 傳統入口網站中，按一下左側的 [**虛擬機器**。 
2.  按一下 [ **Contoso-DNS-歐盟**]。
3.  按一下 [從上方的 [**儀表板**]。
4.  從下方按一下 [**連線**]，然後依照指示連線至 RDP 透過虛擬機器。
2.  RDP 工作階段，按一下以開啟 [開始] 畫面左下角的 [Windows] 按鈕。
3.  按一下 [**伺服器管理員]**磚。
4.  按一下 [**新增角色和功能**]。
5.  按一下 [**下一步**
6.  選取**角色或功能的安裝**]，然後按一下 [**下一步**。
7.  選取您的 DNS 虛擬機器 （它應該會反白顯示已）]，然後按一下 [**下一步**。
8.  檢查**DNS 伺服器**。
9.  按一下 [**新增功能**]，然後按一下 [**繼續]**。
10. **按三次，**然後再按一下 [**安裝**]。 

**若要新增的 Contoso DNS-適用於美國的 DNS 伺服器角色**

- 重複步驟以新增至**Contoso DNS-適用於美國**的 DNS 角色。

##<a name="assign-dns-servers-to-the-virtual-networks"></a>將 DNS 伺服器指派給虛擬網路

**若要註冊的兩個 DNS 伺服器**

1.  從 Azure 傳統入口網站中，按一下 [**新增]**，**網路服務**、**虛擬網路**，**註冊 DNS 伺服器**。
2.  請輸入︰
    - **名稱**︰ Contoso-DNS-歐盟
    - **DNS 伺服器 IP 位址**︰ 10.1.0.4 – IP 位址必須符合 DNS 伺服器虛擬機器 IP 位址。
     
3.  重複此程序 Contoso DNS-適用於美國登錄下列設定︰
    - **名稱**︰ Contoso-DNS-美國
    - **DNS 伺服器 IP 位址**︰ 10.2.0.4

**若要指派到兩個虛擬網路的兩個 DNS 伺服器**

1.  從 [傳統] 入口網站的左窗格中按一下 [**網路**]。
2.  按一下 [ **Contoso-VNet-歐盟**]。
3.  按一下 [**設定**]。
4.  您可以選取 [ **Contoso-DNS-歐盟** **dns 伺服器**] 區段中。
5.  按一下 [**儲存**] 下方的頁面]，然後按一下 [ **]**確認。
6.  重複此程序**Contoso VNet-適用於美國**虛擬網路指派**Contoso DNS-適用於美國**DNS 伺服器。

必須重新啟動所有虛擬機器已部署至虛擬網路更新 DNS 伺服器設定。

**若要重新啟動虛擬機器**

1. 從 Azure 傳統入口網站中，按一下左側的 [**虛擬機器**。
2. 按一下 [ **Contoso-DNS-歐盟**]。
3. 按一下 [從上方的 [**儀表板**]。
4. 在底部，按一下 [**重新啟動**]。
5. 重複相同的步驟來重新啟動**Contoso DNS-適用於美國**。


##<a name="configure-dns-conditional-forwarders"></a>設定 DNS 條件轉寄站

每個虛擬網路上的 DNS 伺服器只可以解決 DNS 的虛擬網路中的名稱。 您需要設定條件轉寄站指向名稱解析等虛擬網路中的對等 DNS 伺服器。

若要設定條件轉寄站，您必須知道兩個 DNS 伺服器的網域尾碼。 可根據 Cloud Services 設定建立虛擬機器時，使用不同的 DNS 尾碼。 如需虛擬網路中使用的每個 DNS 尾碼，您必須新增條件轉寄站。 

**若要尋找的兩個 DNS 伺服器的網域尾碼**

1. 將**Contoso-DNS-歐盟**RDP。
2. 開啟 Windows PowerShell 主控台或命令提示字元。
3. 執行**ipconfig**，並記下**連線特定的尾碼**。
4. 不關閉 RDP 工作階段，您仍需要它。 
5. 重複相同步驟，找出**連線特定尾碼** **Contoso DNS-適用於美國**。


**若要設定 DNS 轉寄站**
 
1.  從 [ **Contoso-DNS-歐盟**RDP 工作階段，按一下左下角的 Windows 鍵。
2.  按一下 [**系統管理工具**]。
3.  按一下 [ **DNS**]。
4.  在左窗格中，展開 [ **DSN**，**限 DNS Contoso 歐盟**]。
5.  輸入下列資訊︰
    - **DNS 網域**︰ 輸入 Contoso DNS-適用於美國的尾碼。 例如︰ Contoso-DNS-US.b5.internal.cloudapp.net。
    - **主控伺服器 IP 位址**︰ 輸入 10.2.0.4，也就是 Contoso DNS-適用於美國的 IP 位址。
6.  按下**enter 鍵**，，然後按一下**[確定]**。  現在您可以解決 Contoso-DNS-歐盟 Contoso DNS-適用於美國的 IP 位址。
7.  重複步驟以新增 DNS 轉寄站 DNS 服務 Contoso DNS-適用於美國虛擬機器使用下列的值︰
    - **DNS 網域**︰ 輸入 Contoso DNS 歐盟的尾碼。 
    - **主控伺服器 IP 位址**︰ 輸入 10.2.0.4，也就是 Contoso-DNS-歐盟的 IP 位址。

##<a name="test-the-name-resolution-across-the-virtual-networks"></a>透過虛擬網路測試名稱解析

現在，您可以透過虛擬網路測試主機名稱解析。 偵測 （ping） 預設的防火牆封鎖。  若要解決等網路中的 DNS 伺服器虛擬機器 （您必須使用 FQDN），您可以使用 nslookup。  


##<a name="next-steps"></a>後續步驟

在本教學課程中，您已經學會如何透過 VPN 連線的虛擬網路設定名稱解析。 數列中的其他兩個文件包含︰

- [設定兩個 Azure 虛擬網路之間的 VPN 連線][hdinsight-hbase-geo-replication-vnet]
- [設定 HBase 地理複寫][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 
