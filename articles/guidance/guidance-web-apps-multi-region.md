<properties
   pageTitle="使用高可用性 web 應用程式 |Azure 參考架構 |Microsoft Azure"
   description="使用高的顯示狀態，在 Microsoft Azure 中執行的 web 應用程式的建議的架構。"
   services="app-service,app-service\web,sql-database" 
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
   ms.date="06/27/2016"
   ms.author="mwasson"/>

# <a name="azure-reference-architecture-web-application-with-high-availability"></a>Azure 參考架構︰ Web 應用程式的可用性

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

本文將示範 web 應用程式的建議的架構與高的顯示狀態，在 Microsoft Azure 上執行。 基礎架構，這是在[Azure 參考架構︰ 改善擴充 web 應用程式中的][guidance-web-apps-scalability]。

## <a name="architecture-diagram"></a>架構圖表

![參照架構︰ Web 應用程式的可用性](media/blueprints/paas-web-app-multi-region.png)

此結構是根據[改善擴充 web 應用程式]中顯示的項目[guidance-web-apps-scalability]。 主要差異如下︰

- **主要和次要區域**。 此架構會使用兩個區域，以獲得更高的可用性。 應用程式部署到每個區域。 在標準作業時，網路流量路由至主要的區域。 但是，無法使用，如果要將流量路由至第二個區域。 如需容錯移轉的詳細資訊，請參閱[管理容錯移轉](#managing-failover-and-failback)。

- **Azure 流量管理員**。 [流量管理員][traffic-manager]路由至主要的傳入的邀請。 如果執行該區域的應用程式無法使用，流量管理員無法在第二個區域。 

- **地理複寫**SQL 資料庫及 DocumentDB。

## <a name="recommendations"></a>建議

### <a name="regional-pairing"></a>地區配對

每個 Azure 區域被搭配相同的地理區域內的其他區域。 一般而言，選擇相同的地區組 （例如，用於美國 2 和美國中部） 區域。 這樣的優點包括︰

- 如果有大量的資料，請登出每組至少有一個區域的復原選擇優先將。
- 計劃 Azure 系統更新導入成對區域依序可能停機降到最低。
- 在大部分情況下，則會位於相同的地理位置，以符合資料 residency 需求。

不過，請確定兩個區域支援所有應用程式所需的 Azure 服務。 請參閱[依地區服務][services-by-region]。 更多地區成對的詳細資訊，請參閱[業務連續性與損毀復原 (BCDR): Azure 配對區域][regional-pairs]。

### <a name="resource-groups"></a>資源群組

請考慮放在不同的[資源群組]的主要區域次要區域，與流量管理員[resource groups]。 這個選項可讓您管理部署至單一集合為每個區域的資源&mdash;您可以個別部署、 刪除部署，等等。 

### <a name="traffic-manager"></a>流量管理員

**路由。** 流量管理員支援幾個[路由演算法][tm-routing]。 本文所述的案例中，您可以使用_優先順序_路由 （之前稱為_容錯移轉_路由）。 使用此設定，流量管理員傳送所有要求到主要區域，除非該區域的端點變成無法存取。 此時，自動無法在第二個區域。 請參閱[設定容錯移轉路由方法][tm-configure-failover]。

**狀況探查。** 流量管理員會使用 HTTP （或 HTTPS） 探查監控每個端點的可用性。 探查提供流量管理員容錯移轉至第二個區域的成功或失敗測試。 其運作方式要求傳送至指定 URL 路徑。 如果它會出現逾時期間內取得非 200 回應，則探查會失敗。 四個失敗的要求之後流量管理員降級標記端點，且無法移到另一個結束點。 如需詳細資訊，請參閱[流量管理員端點監控和容錯移轉][tm-monitoring]。

最佳作法是建立狀況探查結束點的報表的整體狀況的應用程式，並使用這個端點狀況探查。 端點應該核取 [要徑的相依性，例如應用程式服務應用程式、 儲存空間佇列和 SQL 資料庫。 否則，探查實際失敗的重要部分應用程式時，可能會報告的 「 良好 」 的端點。 

另一方面，不使用狀況探查來檢查較低的優先順序服務。 例如，如果的電子郵件服務中斷時，應用程式可以切換至第二個提供者，或只稍後再傳送電子郵件。 應用程式可能不應容錯這種情況。 如需詳細資訊，請參閱[狀況端點監控圖樣][health-endpoint-monitoring-pattern]。
  
### <a name="sql-database"></a>SQL 資料庫

使用 [[作用中的地理複寫][sql-replication]不同區域中建立可讀的次要。 您可以有四個可讀取次要連結。 如果您的主要資料庫失敗，或只需要離線，您可以容錯移轉給任何第二個資料庫。 您可以設定作用中的地理複寫任何彈性的資料庫資料庫中的任何資料庫。

### <a name="documentdb"></a>DocumentDB

DocumentDB 支援地理複寫各區域。 一個區域指定為可寫入，而其他人唯讀複本。 

如果沒有地區的資料，您可能會失敗被選取您要可寫入區域的其他區域。 DocumentDB 用戶端 SDK 會自動傳送至目前的寫入範圍，撰寫要求，因此您不需要更新後移轉後的用戶端設定。 如需詳細資訊，請參閱[等距分佈資料全域 DocumentDB][docdb-geo]。 

> [AZURE.NOTE] 所有複本屬於相同的資源群組。

### <a name="storage"></a>儲存空間

Azure 儲存空間，使用 [[讀取權限地理多餘的儲存][ ra-grs] (RA GRS)。 使用 RA GRS 儲存空間，資料會複製到第二個區域。 您在第二區域中，透過個別的端點有資料的唯讀存取。 如果沒有地區中斷或損毀，Azure 儲存體小組可能會決定執行地理容錯移轉至第二個區域。 沒有此容錯移轉所需的客戶動作。

佇列中的儲存空間，建立備份佇列中的第二個區域。 在容錯移轉，應用程式可以使用備份佇列中，直到的主要區域會再次可用。 如此一來，應用程式仍可以處理新的要求。 

## <a name="availability-considerations"></a>可用性考量

多區域架構可以提供更高的可用性比部署到單一的區域。 如果地區的資料會影響的主要區域，您可能無法在第二個區域。 如果應用程式的個別子系統失敗，也可以協助此架構。  
     
有多個一般種達到跨資料中心的可用性︰      
- 主動/被動與熱備用。 準備就緒其他等待時一區域，進入流量。 應用程式，而且已部署執行第二區域中。 您可能會開始較小的執行個體計數次要資料中心，並視需要調整出。 

- 主動/被動與低溫待命。 直到所需的容錯移轉，將不會部署相同，但是應用程式。 此方法成本小於執行，但通常必須再期間失敗的時間。 

- 作用中/作用。 兩個區域會啟動，且要求負載平衡它們之間。 如果無法使用一個資料中心，它會被登出旋轉。 

本文著重於主動/被動熱備用，使用[Azure 流量管理員]與[traffic-manager]路由流量至區域。 

### <a name="traffic-manager"></a>流量管理員

如果無法使用的主要區域流量管理員自動無法透過。 當流量管理員無法透過時，有一段時間時用戶端無法達到應用程式，可以是幾分鐘的時間。 兩個因素影響總工期︰

- 狀況探查必須偵測主要資料中心都無法存取。

- DNS 伺服器必須更新的 IP 位址，取決於 DNS 存留時間 (TTL) 的快取的 DNS 記錄。 [TTL] 的預設值是 300 秒 （5 分鐘），但當您建立的流量管理員設定檔時，您可以設定此值。

如需詳細資訊，請參閱[關於監控流量管理員][tm-monitoring]。 

流量管理員是可能失敗點系統中。 如果服務失敗，請用戶端無法存取您的應用程式當機期間。 檢閱[流量管理員 SLA][tm-sla]，並決定是否使用流量管理員了解單獨使用符合您的業務需求高可用性。 如果沒有的話，請考慮回復為新增另一種流量管理解決方案。 如果 Azure 流量管理員服務失敗，請變更 [CNAME 記錄指向其他流量管理服務的 dns。 （必須執行此步驟以手動方式，與您的應用程式會無法使用，直到 DNS 變更傳播）。 

### <a name="sql-database"></a>SQL 資料庫

描述復原點目標 (RPO) 和 SQL 資料庫的估計的復原時間 (ERT)[以下][sql-rpo]。 

### <a name="storage"></a>儲存空間

RA GRS 儲存提供長期的儲存空間，但請務必瞭解過時期間會發生什麼事︰ 

- 如果儲存中斷發生時，會有一段時間，當您沒有寫入存取權的資料。 您仍然可以過時期間讀取從第二個端點。

- 如果地區中斷或損毀會影響主要的位置，而且無法復原的資料那里，Azure 儲存體小組可能會決定執行地理容錯移轉至第二個區域。 

- 非同步執行資料複製到第二個區域。 因此，如果執行地理容錯移轉時，部分資料遺失可能是，如果無法復原資料從主要的區域。

- 暫時性的失敗次數，例如網路中斷，不會觸發儲存容錯移轉。 設計您的應用程式有彈性的暫時性失敗。 可能降低︰

    - 從次要讀取。

    - 暫時切換到其他儲存帳戶 （例如，若要佇列中的郵件） 的新寫入作業。 

    - 次要資料複製到其他儲存的帳戶。

    - 提供精簡的功能，直到系統回失敗。

如需詳細資訊，請參閱[Azure 儲存體過時發生時該怎麼辦][storage-outage]。

## <a name="managing-failover-and-failback"></a>管理錯誤移轉及回復

### <a name="traffic-manager"></a>流量管理員

如果無法使用的主要區域流量管理員自動無法透過。 根據預設，它將會也會自動失敗上一步] 之後的主要區域會再次可用。

不過，我們建議您執行手動回復，而不是自動回失敗。 前回失敗，請確認所有應用程式子系統健全。 否則，您可以建立的資料中心之間來回翻轉應用程式的情況。 

若要防止自動回復，以手動方式降低容錯移轉事件後的主要區域的優先順序。 例如，假設的主要區域是優先順序 1 次要是優先順序 2。 後移轉後，將優先順序 3，若要防止自動回復設定的主要區域。 當您準備好切換回時，重設優先順序，為 1。

下列命令更新的優先順序。

**PowerShell** 

```bat
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name <endpoint> -ProfileName <profile> -ResourceGroupName <resource-group> -Type AzureEndpoints
$endpoint.Priority = 3
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

如需詳細資訊，請參閱[Azure 流量管理員 Cmdlet][tm-ps]。

**Azure CLI**

```bat
azure network traffic-manager endpoint set --name <endpoint> --profile-name <profile> --resource-group <resource-group> --type AzureEndpoints --priority 3
```    

### <a name="sql-database"></a>SQL 資料庫

如果主要資料庫失敗，請執行手動容錯移轉至次要資料庫。 請參閱[還原 Azure SQL 資料庫或容錯移轉至次要][sql-failover]。 您無法在直到次要資料庫會維持唯讀狀態。 


<!-- links -->

[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[docdb-geo]: ../documentdb/documentdb-distribute-data-globally.md
[guidance-web-apps-scalability]: guidance-web-apps-scalability.md
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[ra-grs]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../resource-group-overview.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-failover]: ../sql-database/sql-database-disaster-recovery.md
[sql-replication]: ../sql-database/sql-database-geo-replication-overview.md
[sql-rpo]: ../sql-database/sql-database-business-continuity.md#sql-database-business-continuity-features
[storage-outage]: ../storage/storage-disaster-recovery-guidance.md
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-ps]: https://msdn.microsoft.com/en-us/library/mt125941.aspx
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
