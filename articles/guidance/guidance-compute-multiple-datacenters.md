<properties
   pageTitle="在高可用性的多個區域執行 Windows Vm |參照架構 |Microsoft Azure"
   description="如何部署 Vm 在多個區域的可用性和恢復 Azure。"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-in-multiple-regions-for-high-availability"></a>執行 Windows Vm 在多個區域內的可用性

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [執行 Linux Vm 在多個區域內的可用性](guidance-compute-multiple-datacenters-linux.md)
- [執行 Windows Vm 在多個區域內的可用性](guidance-compute-multiple-datacenters.md)

在本文中，我們建議您執行 Windows 虛擬機器 (Vm) 在多個 Azure 區域]，以達到可用性和強大損壞修復基礎結構的作法一組的內容。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource groups]和傳統。 本文所用的資源管理員，Microsoft 建議新的部署。

多區域架構可以提供更高的可用性比部署到單一的區域。 如果地區的資料會影響的主要區域，您可以使用[流量管理員][traffic-manager]容錯移轉至第二個區域。 如果應用程式的個別子系統失敗，也可以協助此架構。 

有多個一般種達到跨資料中心的可用性︰

- 主動/被動與熱備用。 準備就緒其他等待時一區域，進入流量。 第二區域中的 Vm 隨時都配置和執行。

- 主動/被動與低溫待命。 直到所需的容錯移轉，將不會配置相同，但是 Vm 第二個區域。 此方法成本小於執行，但通常必須再期間失敗的時間。

- 作用中/作用。 兩個區域會啟動，且要求負載平衡它們之間。 如果無法使用一個資料中心，它會被登出旋轉。 

此結構著重於主動/被動與熱備用，容錯移轉使用流量管理員。 請注意，您可以部署的熱備用小型 Vm 的數字，然後視需要調整出。

## <a name="architecture-diagram"></a>架構圖表

下圖顯示[新增可靠性上 Azure N 層架構](guidance-compute-n-tier-vm.md)的架構是根據。

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是在 「 計算-多重區域 (Windows) 頁面。

[![0]][0]

- **主要和次要區域**。 此架構會使用兩個區域，以獲得更高的可用性。 其中一個是主要的區域。 在標準作業時，網路流量路由至主要的區域。 但是，無法使用，如果要將流量路由至第二個區域。

- ** [Azure 流量管理員][traffic-manager]**路由至主要的傳入的邀請。 如果無法使用該區域，流量管理員無法在第二個區域。 如需詳細資訊，請參閱[設定流量管理員](#configuring-traffic-manager)的區段。

- **資源群組**。 建立個別的[資源群組][resource groups]主要區域，第二區域中，與流量管理員的。 這可讓您管理為單一集合的資源的每個區域的彈性。 例如，您無法重新一個區域，部署不需關閉另一個。 [連結的資源群組][resource-group-links]、，您可以執行的查詢] 清單中的應用程式的所有資源。

- **VNets**。 建立個別的每個地區 VNet。 請確定未重疊的位址空間。 

- **一律在可用性] 群組中的 SQL Server**。 如果您使用 SQL Server，我們建議[SQL 永遠在 Availabilty 群組][sql-always-on]高可用性。 建立包含兩個區域中的 SQL Server 執行個體的單一可用性群組。 如需詳細資訊，請參閱[設定 [SQL Server 永遠在可用性] 群組中](#configuring-the-sql-server-alwayson-availability-group )的一節。

> [AZURE.NOTE] 也可以考慮[Azure SQL 資料庫][azure-sql-db]，其中提供關聯式資料庫雲端服務。 使用 SQL 資料庫]，您不需要設定可用性群組或管理容錯移轉。  

- **VPN 閘道**︰ 建立[VPN 閘道][vpn-gateway]中每個 VNet，並設定[VNet-VNet 連線][vnet-to-vnet]，若要啟用兩個 VNets 之間的網路流量。 這是必要的 SQL 一直在可用性群組。

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇要建立您自己的參考架構請遵循這些建議，除非您有建議不符合特定需求。

### <a name="regional-pairing"></a>地區配對

每個 Azure 區域被搭配相同的地理區域內的其他區域。 一般而言，選擇相同的地區組 （例如，東亞美式 2 和美國中部） 區域。 這樣的優點包括︰

- 如果有大量的資料，請登出每組至少有一個區域的復原選擇優先將。

- 計劃 Azure 系統更新導入成對區域依序可能停機降到最低。

- 組位於相同的地理位置，以符合資料 residency 需求。

不過，請確定兩個區域支援所有應用程式所需的 Azure 服務 (請參閱[依地區服務][services-by-region])。 如需有關地區成對的詳細資訊，請參閱[業務連續性與損毀復原 (BCDR): Azure 配對區域][regional-pairs]。

### <a name="traffic-manager-configuration"></a>流量管理員設定

請考慮下列點設定流量時管理員，您的狀況︰

- **路由。** 流量管理員支援幾個[路由演算法][tm-routing]。 本文所述的案例中，您可以使用_優先順序_路由 （之前稱為_容錯移轉_路由）。 使用此設定，流量管理員傳送所有要求到主要區域，除非的主要區域會變成無法存取。 此時，自動無法在第二個區域。 請參閱[設定容錯移轉路由方法][tm-configure-failover]。

- **狀況探查。** 流量管理員使用[探查]HTTP （或 HTTPS）[tm-monitoring]監控每個區域的可用性。 探查會檢查指定的 URL 路徑 HTTP 200 回應。 最佳作法是建立端點報表的整體狀況的應用程式，並使用這個端點的狀況檢查。 否則，探查實際失敗的重要部分應用程式時，可能會報告的 「 良好 」 的端點。 如需詳細資訊，請參閱[狀況端點監控圖樣][health-endpoint-monitoring-pattern]。   

當流量管理員無法透過時，有一段時間時用戶端無法達到應用程式，可以是幾分鐘的時間。 兩個因素影響總工期︰

- 狀況探查必須偵測主要資料中心都無法存取。

- DNS 伺服器必須更新的 IP 位址，取決於 DNS 存留時間 (TTL) 的快取的 DNS 記錄。 [TTL] 的預設值是 300 秒 （5 分鐘），但當您建立的流量管理員設定檔時，您可以設定此值。

如需詳細資訊，請參閱[關於監控流量管理員][tm-monitoring]。 

如果流量管理員失敗，我們建議執行手動回復，而不是自動回失敗。 請確認所有應用程式子系統健全第一次。 否則，您可以建立的資料中心之間來回翻轉應用程式的情況。

根據預設，流量管理員自動回失敗。 若要避免此，手動較低的優先順序的主要區域之後容錯移轉事件。 例如，假設的主要區域是優先順序 1 次要是優先順序 2。 後移轉後，將優先順序 3，若要防止自動回復設定的主要區域。 當您準備好切換回時，更新優先順序，為 1。

下列 Azure CLI 命令更新優先順序︰

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

若要避免觸發器的另一個方法是暫時停用的結束點︰

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```

根據容錯移轉的原因，您可能需要 redploy 區域內的資源。 前回失敗，執行作業的整備測試。 測試應該確認項目，例如︰

- Vm 設定正確。 （已安裝所有需要的軟體，IIS 是執行等等）。

- 應用程式子系統是健全。 

- 功能測試。 （例如資料庫層是可從 web 層）。

### <a name="sql-server-always-on-configuration"></a>SQL Server 永遠上設定

SQL Server 永遠在可用性群組要求網域控制站。 [可用性] 群組中的所有節點都必須在同一個 AD 網域。 下列點提供有關如何設定 SQL Server 永遠在可用性群組的指導方針︰

- 在最小值，將兩個網域控制站在每個區域。 

- 為每個網域控制站的靜態 IP 位址。

- 建立 VNet-VNet 連線啟用 VNets 之間的通訊。

- 針對每個 VNet，新增至 DNS 伺服器清單 （從兩個區域） 的網域控制站的 IP 位址。 您可以使用下列 CLI 命令。 更多詳細資訊，請參閱[管理 DNS 伺服器使用虛擬網路 (VNet)][vnet-dns]。

```bat
azure network vnet set --resource-group dc01-rg --name dc01-vnet --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
```

- 建立[Windows Server 容錯][ wsfc] (WSFC) 叢集包含兩個區域中的 SQL Server 執行個體。 

- 建立 SQL Server 永遠在可用性群組在主要和次要區域內包含 SQL Server 執行個體。 請參閱步驟[延伸永遠在可用性群組到遠端 Azure 資料中心 (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) 。 

- 將主要區域的主要複本。

- 將一或多個的次要複本放在主要的區域。 設定這些同步認可使用自動容錯移轉。

- 將一或多個的次要複本放在第二區域中。 設定這些*非同步*認可]，用於效能。 （否則所有的 SQL 交易必須等候來回次要區域網路上。） 

> [AZURE.NOTE] 非同步認可複本不支援自動容錯移轉。 

如需詳細資訊，請參閱[執行 Windows Vm 上 Azure N 層架構](guidance-compute-n-tier-vm.md#SQL-AlwaysOn-Availability-Group)。

## <a name="availability-considerations"></a>可用性考量

使用複雜的 N 層應用程式，您可能不需要複製整個應用程式中的第二個區域。 不過，您可能只會複製要徑子系統所需支援業務連續性。

流量管理員是可能失敗點系統中。 如果服務失敗，請用戶端無法存取您的應用程式當機期間。 檢閱[流量管理員 SLA][tm-sla]，並決定是否使用流量管理員了解單獨使用符合您的業務需求高可用性。 如果沒有的話，請考慮回復為新增另一種流量管理解決方案。 如果 Azure 流量管理員服務失敗，請變更 [CNAME 記錄指向其他流量管理服務的 dns。 （必須執行此步驟以手動方式，與您的應用程式會無法使用，直到 DNS 變更傳播）。 

SQL Server 叢集，有兩個容錯移轉案例︰

1. 所有的主要區域中的 SQL 複本會失敗。 例如，會發生此問題地區過時期間。 在此情況下，您必須以手動方式容錯 SQL 可用性] 群組中，即使流量管理員自動無法透過前端。 請遵循中[執行的 SQL Server 可用性群組強制手動容錯移轉](https://msdn.microsoft.com/library/ff877957.aspx)，其中說明如何使用 [SQL Server 2016 中的 [SQL Server Management Studio、 TRANSACT-SQL 或 PowerShell 執行強制容錯移轉的步驟進行。 

    > [AZURE.WARNING] 強制容錯移轉時，就資料遺失的風險。 主要區域恢復連線後，取得資料庫的快照，並使用[tablediff]尋找差異。

2. 流量管理員無法在第二區域中，但仍可使用的主要 SQL 複本。 例如，前端可能失敗，而不會影響 SQL Vm。 在此情況下，網際網路流量路由至第二區域中，與該區域仍然可以連線到主要 SQL 複本。 不過，將會增加的延遲時間，因為 SQL 連線表示各區域。 在此情況下，您應該執行手動容錯移轉如下所示︰ 

    - 暫時切換至*同步*的認可的第二區域中的 SQL 複本。 如此一來，可確保在容錯移轉不會有資料遺失。

    - 無法在 SQL 複本。 
    
    - 當您無法回到主要區域時，還原非同步認可設定。 

## <a name="manageability-considerations"></a>管理能力考量

當您更新您的部署時，更新一個區域，一次，以減少全域失敗的機率設定不正確或應用程式中的錯誤。

測試系統失敗的恢復。 以下是一些常見的錯誤案例測試︰

- 關閉 VM 執行個體。

- 壓力資源，例如 CPU 和記憶體。

- 延遲中斷連線網路。

- 當機程序。

- 過期憑證。

- 模擬硬體錯誤。

- 關閉網域控制站的 DNS 服務。

測量復原次數，並確認其符合您的業務需求。 測試失敗模式，以及的組合。

## <a name="next-steps"></a>後續步驟

本系列的重點純粹雲端部署。 企業案例通常需要混合式網路，與 Azure 虛擬網路連線內部部署網路。 若要瞭解如何建立混合式網路，請參閱[實作混合的網路架構與 Azure 及內部部署 VPN][hybrid-vpn]。

<!-- Links -->

[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-always-on]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[tablediff]: https://msdn.microsoft.com/en-us/library/ms162843.aspx
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[vnet-to-vnet]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[wsfc]: https://msdn.microsoft.com/en-us/library/hh270278.aspx
[0]: ./media/blueprints/compute-multi-dc.png "Azure N 層應用程式的可用性網路架構"