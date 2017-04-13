<properties
   pageTitle="接聽 AlwaysOn availabilty 群組建立 SQL Server Azure 虛擬機器中的商務連絡人"
   description="建立 SQL Server Azure 虛擬機器中的商務連絡人的接聽 AlwaysOn availabilty 群組的逐步指示"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Azure 中設定 AlwaysOn 可用性群組內部負載平衡器

本主題說明如何建立資源管理員模型中執行的 Azure 虛擬機器中的 SQL Server AlwaysOn 可用性群組內部負載平衡器。 SQL Server 執行個體 Azure 虛擬機器上時，AlwaysOn 可用性群組需要負載平衡器。 負載平衡器的顯示狀態群組接聽儲存的 IP 位址。 如果可用性群組橫跨多個區域，每個區域會需要負載平衡器。

若要完成此工作，您需要有資源管理員模型中的 Azure 虛擬機器上部署的 SQL Server AlwaysOn 可用性群組。 兩個 SQL Server 虛擬機器必須屬於相同的可用性設定。 您可以使用[Microsoft 範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)Azure 資源管理員中的 [自動建立 AlwaysOn 可用性群組。 此範本會自動為您建立內部負載平衡器。 

如果您想要的話，您可以[手動設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

本主題需要已設定可用性群組。  

相關的主題包括︰

 - [Azure VM (GUI) 中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [使用 Azure 資源管理員與 PowerShell 設定 VNet-VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>步驟

藉由逐步解說這份文件中，您會建立並設定 Azure 入口網站中的負載平衡器。 這是完成後，您將設定叢集 AlwaysOn 可用性群組接聽程式使用從負載平衡器的 IP 位址。

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>在建立及設定負載平衡器 Azure 入口網站

在此工作中，您將會執行 Azure 入口網站中的下列步驟︰

1. 建立負載平衡器及設定的 IP 位址

1. 設定後端資料庫

1. 建立探查 

1. 設定負載平衡規則

>[AZURE.NOTE] 如果 SQL 伺服器是不同的資源群組和地區，您會執行這些步驟兩次，一次在每個資源群組。

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1.建立負載平衡器及設定的 IP 位址

第一個步驟是，建立負載平衡器。 在 Azure 入口網站中，開啟 [包含 SQL Server 虛擬機器的資源群組]。 在 [資源] 群組中，按一下 [**新增**]。

- 搜尋**負載平衡器**。 在搜尋結果中選取 [**負載平衡器**，這由**Microsoft**發佈]。

- 在**負載平衡器**刀中，按一下 [**建立**]。

- 在 [**建立負載平衡器**，請設定負載平衡器，如下所示︰

| 設定 | 值 |
| ----- | ----- |
| **名稱** | 代表負載平衡器文字名稱。 例如， **sqlLB**。 |
| **結構描述** | **內部** |
| **虛擬網路** | 選擇 [虛擬網路中的 SQL Server]。   |
| **子網路**  | 選擇 [子網路中的 SQL Server]。 |
| **訂閱** | 如果您有多個訂閱時，可能會出現這個欄位。 選取您要與此資源相關的訂閱。 通常是為所有資源可用性群組相同的訂閱。  |
| **資源群組** | 選擇 [資源] 群組中的 SQL Server。 | 
| **位置** | 選擇 [Azure 位置中的 SQL Server]。 |

- 按一下 [**建立**]。 

Azure 建立負載平衡器您設定的上方。 負載平衡器所屬的特定的網路、 子網路、 資源] 群組中及位置。 Azure 完成之後，請確認 Azure 的負載平衡器設定。 

現在，設定負載平衡器 IP 位址。  

- 在負載平衡器**設定**刀中，按一下 [ **IP 位址**。 **IP 位址**刀顯示這是您的 SQL Server 相同的虛擬網路上的私人的負載平衡器。 

- 設定下列設定︰ 

| 設定 | 值 |
| ----- | ----- |
| **子網路** | 選擇 [子網路中的 SQL Server]。 |
| **工作分派** | **靜態** |
| **IP 位址** | 輸入子網路中未使用過的虛擬 IP 位址。  |

- 儲存設定。

現在負載平衡器有 IP 位址。 記錄此 IP 位址。 當您建立的接聽叢集上時，您會使用這個 IP 位址。 在本文稍後的 PowerShell 指令碼，使用此位址`$ILBIP`變數。



## <a name="2-configure-the-backend-pool"></a>2.設定後端資料庫

下一步是建立地址後端資料庫。 Azure 通話後端的地址資料庫*後端資料庫*。 在此情況下後, 端資料庫是兩個 SQL Server 您可用性] 群組中的地址。 

- 在您資源] 群組中，按一下您所建立的負載平衡器。 

- 在 [**設定**] 中，按一下 [**後端資料庫**]。

- 在**後端位址集區**，按一下 [**新增**]，以建立地址後端資料庫。 

- 在**新增後端資料庫****名稱**底下，輸入後端資料庫的名稱。

- **虛擬機器**下的 [ **+ 新增虛擬機器**。 

- 在 [**選擇虛擬機器**按一下 [**選擇可用性設定**，指定可用性設定 SQL Server 虛擬機器屬於。

- 選擇可用性設定之後，按一下 [**選擇虛擬機器**。 按一下 [主控可用性] 群組中的 SQL Server 執行個體的兩個虛擬機器。 按一下 [**選取**]。 

- 按一下**[確定]**以關閉刀**選擇虛擬機器**，及**新增後端資料庫**。 

Azure 更新後端地址資料庫的設定。 現在您可用性集有兩個 SQL Server 的集區。

## <a name="3-create-a-probe"></a>3.建立檢查

下一步是建立檢查。 探查定義 Azure 會如何確認其中的 SQL Server 目前擁有的可用性群組接聽程式。 Azure 會探查根據您定義當您建立探查的連接埠的 IP 位址的服務。

- 在 [負載平衡器**設定**刀中，按一下 [**探查**]。 

- 按一下 [**探查**刀的 [**新增**]。

- 設定**新增探查**刀探查。 若要設定探查使用下列的值︰

| 設定 | 值 |
| ----- | ----- |
| **名稱** | 代表探查文字名稱。 例如， **SQLAlwaysOnEndPointProbe**。 |
| **通訊協定** | **TCP** |
| **連接埠** | 您可以使用任何可用的連接埠。 例如， *59999*。    |
| **間隔**  | *5* | 
| **不佳的閥值**  | *2* | 

- 按一下**[確定]**。 

>[AZURE.NOTE] 請確定您指定的連接埠是兩個 SQL 伺服器的防火牆上開啟。 兩個伺服器需要輸入的規則，您使用的 TCP 連接埠。 如需詳細資訊，請參閱[新增或編輯防火牆規則](http://technet.microsoft.com/library/cc753558.aspx)。 

Azure 建立探查。 Azure 會使用探查若要測試的 SQL Server 具有 [可用性] 群組中的接聽程式。

## <a name="4-set-the-load-balancing-rules"></a>4.設定負載平衡規則

設定負載平衡規則。 負載平衡規則設定負載平衡器路由流量至 SQL Server 的方式。 針對此負載平衡器中，您將啟用直接伺服器傳回因為僅有一個的兩個 SQL Server 時擁有一次的可用性群組接聽資源。

- 在 [負載平衡器**設定**刀中，按一下 [**負載平衡規則**]。 

- 在**負載平衡規則**刀，按一下 [**新增]**。

- 使用**新增負載平衡規則**刀設定負載平衡規則。 使用下列設定︰ 

| 設定 | 值 |
| ----- | ----- |
| **名稱** | 文字名稱，代表負載平衡規則。 例如， **SQLAlwaysOnEndPointListener**。 |
| **通訊協定** | **TCP** |
| **連接埠** | *1433*   |
| **後端連接埠** | *1433*。 請注意，這會停用，因為此規則使用**浮動 IP （傳回直接伺服器）**。   |
| **探查** | 使用此負載平衡器探查您所建立的名稱。 |
| **工作階段 persistance**  | **無** | 
| **閒置逾時 （分鐘）**  | *4* | 
| **浮動 IP （直接伺服器傳回）**  | **啟用** | 

 >[AZURE.NOTE] 您可能必須向下捲動以查看所有設定刀。

- 按一下**[確定]**。 

- Azure 設定負載平衡規則。 現在負載平衡器經路由流量至裝載可用性群組接聽 SQL Server。 

此時資源群組有負載平衡器連線到兩個 SQL Server 的電腦。 負載平衡器也包含 IP 位址的 SQL Server AlwaysOn 可用性群組接聽程式，讓電腦可以回應可用性群組的要求。

>[AZURE.NOTE] 如果您的 SQL Server 兩個不同的區域，請重複的其他區域中的步驟。 每個區域需要負載平衡器。 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>設定叢集使用負載平衡器 IP 位址 

下一步是在叢集上，設定接聽程式，並開啟接聽線上。 若要這麼做，請執行下列動作︰ 

1. 建立可用性群組接聽容錯移轉叢集上 

1. 顯示接聽線上

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1.在容錯移轉叢集上建立可用性群組接聽程式

在此步驟中，您可以手動建立容錯移轉叢集管理員和 SQL Server 管理 Studio (SSMS) 中的可用性群組接聽程式。

- 使用 RDP 連線至 Azure 虛擬機器裝載的主要複本。 

- 開啟容錯移轉叢集管理員。

- 選取 [**網路**] 節點，並記下叢集網路名稱。 此名稱將用於`$ClusterNetworkName`變數中的 PowerShell 指令碼。

- 展開叢集名稱，然後按一下 [**角色**。

- 在 [**角色**] 窗格中，以滑鼠右鍵按一下 [可用的群組名稱，然後選取**新增資源** > **用戶端存取點**。

- 在 [**名稱**] 方塊中，建立這個新的接聽程式的名稱，然後**按兩次，** ，然後按一下**完成**。 請不要攜帶接聽或線上資源為止。

 >[AZURE.NOTE] 新接聽的名稱為網路名稱的應用程式會使用連線至 SQL Server 可用性群組中的資料庫。

- 按一下 [**資源**] 索引標籤，然後展開您剛剛建立的用戶端存取點。 以滑鼠右鍵按一下 IP 資源，然後按一下 [屬性]。 請注意的 IP 位址的名稱。 您會使用此名稱`$IPResourceName`變數中的 PowerShell 指令碼。

- [ **IP 位址**] 底下按一下 [**靜態 IP 位址**並設定的靜態 IP 位址以使用您設定的負載平衡器 IP 位址 Azure 入口網站上時所用的相同地址。 啟用 NetBIOS 此地址，然後按一下**[確定**]。 如果您的方案包含多個 Azure VNets，請針對每個 IP 資源重複此步驟。 

- 在目前裝載的主要複本叢集節點，開啟提高權限的 PowerShell ise [以系統並貼上下列命令將新的指令碼。

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- 更新變數，並執行的 PowerShell 指令碼來設定新接聽的 IP 位址和連接埠。

 >[AZURE.NOTE] 如果您的 SQL Server 是在另一個區域中，您需要執行的 PowerShell 指令碼兩次。 第一次使用叢集網路名稱，叢集 IP 資源名稱，然後從第一個資源群組負載平衡器 IP 位址。 第一次使用叢集網路名稱，叢集 IP 資源名稱，並從第二個資源群組負載平衡器 IP 位址。

現在有可用性群組接聽資源。

## <a name="2-bring-the-listener-online"></a>2.顯示接聽線上

與可用性群組接聽資源設定，您就可以將接聽線上，讓應用程式可以連線至接聽程式的可用性] 群組中的資料庫。

- 瀏覽回至容錯移轉叢集管理員。 展開 [**角色**]，並醒目提示您可用的群組。 在 [**資源**] 索引標籤上以滑鼠右鍵按一下接聽程式名稱，然後按一下 [**內容**。

- 按一下 [**相依性**] 索引標籤。 如果有多個列的資源，請確認 [IP 位址已或不 AND、 相依性。 按一下**[確定]**。

- 以滑鼠右鍵按一下接聽程式名稱，然後按一下**[連線**]。


- 接聽 web app 中，從 [**資源**] 索引標籤後，以滑鼠右鍵按一下 [可用性] 群組中，按一下 [**內容**。

- 建立相依性接聽名稱資源 （非 IP 位址資源名稱）。 按一下**[確定]**。


- 啟動 SQL Server Management Studio 中並連接至主要的複本。


- 瀏覽至**AlwaysOn 高可用性** | **可用性群組** | **可用性群組接聽程式**。 


- 您現在應該會看到您所建立的容錯移轉叢集管理員接聽程式名稱。 以滑鼠右鍵按一下接聽程式名稱，然後按一下 [**屬性**]。


- **連接埠**] 方塊中，指定連接埠號碼可用性群組接聽程式使用先前所用 $EndpointPort （1433年是預設值），然後按一下**[確定]**。

您現在會有在資源管理員模式下執行 Azure 虛擬機器中的 SQL Server AlwaysOn 可用性群組。 

## <a name="test-the-connection-to-the-listener"></a>測試接聽的連線

若要測試連線︰

1. SQL server，在相同的虛擬網路中，但不是屬於複本 RDP。 這可能是在叢集其他的 SQL Server。

1. 若要測試連線使用**sqlcmd**公用程式。 例如，下列指令碼建立透過 Windows 驗證的接聽程式的主要複本的**sqlcmd**連線︰

        sqlcmd -S <listenerName> -E

SQLCMD 連線自動連線至何種 SQL Server 執行個體主控的主要複本。 

## <a name="guidelines-and-limitations"></a>指導方針和限制

請注意下列方針，使用內部 Azure 中的可用性群組接聽程式負載平衡器︰

- 只有一個內部可用性群組接聽被支援每雲端服務，因為接聽已設定為負載平衡器，而且沒有只有一個內部負載平衡器。 不過，可建立 multipe 外部接聽程式。 

- 使用內部負載平衡器只能存取從接聽，在相同的虛擬網路。
 
