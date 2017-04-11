<properties
   pageTitle="技術指南︰ 從 Azure 中的本機失敗復原 |Microsoft Azure"
   description="文章瞭解和設計，可用性、 容錯應用程式]，以及規劃損毀修復著重於 Azure 中的本機失敗。"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Azure 恢復技術指南︰ 從 Azure 中的本機失敗復原

有兩個主要潛在威脅的應用程式的可用性︰

* 失敗的裝置，例如磁碟機和伺服器
* 耗盡重要的資源，例如 [最大使用量載入條件下的計算

Azure 提供資源管理、 elasticity、 負載平衡與分割啟用可用性在下列情況下的組合。 部分功能會自動執行所有 Azure 服務。 不過，在某些情況下，應用程式開發人員必須執行一些額外的工作，以改善它們。

##<a name="cloud-services"></a>雲端服務

Azure 雲端服務所組成的一或多個網頁或背景工作角色集合。 可以同時執行一個或多個執行個體的角色。 設定會決定執行個體數目。 監看與稱為布料的轉印圖樣控制站的元件，透過管理角色執行個體。 布料的轉印圖樣控制器偵測到，並且自動回應軟體及硬體失敗。

每一個角色執行個體在自己的虛擬機器 (VM) 中執行，並透過來賓代理程式其布料的轉印圖樣控制器與通訊。 來賓代理程式收集資源和節點，包括 VM 使用狀況、 狀態、 記錄、 資源使用狀況、 例外]，以及失敗狀況。 布料的轉印圖樣控制器查詢來賓代理程式在設定的時間間隔，並重新啟動 VM 如果來賓代理程式無法回應。 發生硬體失敗，相關聯的結構控制器會將所有受影響的角色執行個體移至新的硬體節點，並重新設定那里路由流量至網路。

若要可受益這些功能，開發人員應該確保所有服務角色都避免儲存角色執行個體上的狀態。 不過，應從長期儲存空間，例如 Azure 儲存體] 或 [Azure SQL 資料庫存取常設的所有資料。 這個選項可讓任何角色來處理要求。 這也表示的角色執行個體可以向下隨時但不建立一致的暫時或永久服務的狀態。

儲存狀態外部給角色的需求廣。 這表示，例如的 Azure 儲存體資料表的所有相關的變更變更應該在單一的實體群組交易，如果可以的話。 當然，它不單一交易中的所有變更。 您必須採取特別小心，以確保的角色執行個體失敗不會導致問題時它們會中斷跨兩個或多個常設服務的狀態更新的較長時間執行作業。 如果這種作業再試一次嘗試另一個角色，它應該將會並處理工時部分完成的情況。

例如，請考慮跨多個儲存區分割資料的服務。 如果工作者角色當機，它會重新放置晶怪時，可能不會完成的晶怪重新配置。 或重新配置可能以不同的背景工作角色，可能會造成孤立的資料或資料損毀重複從開始。 若要避免發生問題，或下列其中一項，必須是長時間執行的作業︰

 * *冪*︰ 可重複沒有影響。 若冪，長期執行作業應該有多少次執行時，它將會中斷執行時，即使無論相同的效果。
 * *從屬參照可重新啟動*︰ 可以繼續失敗的最近點。 若要重新啟動從屬參照，長期執行作業應該包含較小不可部分完成的作業的順序。 它應該也中的 [錄製進度長期儲存空間，讓每一個後續引動挑選停止其前置任務的位置。

最後，所有的較長時間執行作業應叫用鍵直到他們成功。 例如，佈建的作業可能會放置在 Azure 佇列中，，然後佇列中依工作者角色時才從移除成功。 您可能需要清理中斷作業的資料建立回收。

###<a name="elasticity"></a>Elasticity

每個角色設定決定的初始執行每一個角色的執行個體數目。 系統管理員應一開始設定] 以根據預期載入的兩個或多個執行個體來執行每一個角色。 但您可以輕鬆地不按比例縮放的角色執行個體或向下為使用圖樣變更。 您可以手動在 Azure 入口網站，或者您可以使用 Windows PowerShell、 服務管理 API 或協力廠商工具來自動化程序。 如需詳細資訊，請參閱[如何自動調整大小的應用程式](../cloud-services/cloud-services-how-to-scale.md)。

###<a name="partitioning"></a>分割

Azure 布料的轉印圖樣控制器使用兩種類型的磁碟分割區︰

* *更新的網域*用來升級的服務] 群組中的角色執行個體。 Azure 部署到多個更新網域的服務執行個體。 就地更新，布料的轉印圖樣控制器顯示中有一個更新的網域，關閉所有執行個體、 更新，，然後重新啟動其移到下一個更新網域之前。 此方法防止整個服務無法使用更新程序期間。
* *故障網域*定義潛在的硬體或網路失敗。 具有多個執行個體的任何角色，布料的轉印圖樣控制器可確保的執行個體分散到多個錯誤網域，若要防止隔離的硬體失敗中斷服務。 故障網域管理伺服器及叢集失敗的所有曝光度。

[Azure 服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/)保證當兩個或更多的網頁角色執行個體部署至不同的錯誤，並升級網域，他們會有外部連線至少 99.95%的時間。 與更新的網域，沒有方法來控制故障網域數目。 Azure 自動配置故障網域，並分散這些角色執行個體。 在最前兩個執行個體的每個角色會放置在不同的錯誤，並升級以確保至少兩個執行個體的任何角色會符合 SLA 的網域。 這是在下圖中表示。

![簡化的網域隔離錯誤的檢視](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png)

###<a name="load-balancing"></a>負載平衡

所有傳入的流量到網頁角色經過無負載平衡器，分散給角色執行個體之間的用戶端要求。 個別的角色執行個體沒有公用 IP 位址，並不是直接從網際網路定址。 Web 角色沒有狀態，讓任何用戶端要求可以路由至任何角色執行個體。 每 15 秒時，會引發[StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx)事件。 您可以使用此選項表示角色是否可接收流量，或是否為忙碌，而且應該採取的負載平衡器旋轉登出。

##<a name="virtual-machines"></a>虛擬機器

Azure 虛擬機器會與平台服務 (PaaS) 計算相對於可用性幾方面的角色。 在某些情況下，您必須執行額外的工作，以確保高的可用性。

###<a name="disk-durability"></a>磁碟持續性

像 PaaS 角色執行個體，即使在重新定位虛擬機器，會持續虛擬機器磁碟機上所儲存的資料。 Azure 虛擬機器使用 VM 磁碟存在為二進位大型物件中 Azure 儲存體。 由於 Azure 儲存體可用性特性，也是高度可用虛擬機器的磁碟機上所儲存的資料。

請注意，（在 Windows Vm) 的磁碟機 D 此規則的例外狀況。 磁碟機 D 主控 VM 機架伺服器上的實際實體儲存空間，而回收 VM 時，其資料將會遺失。 磁碟機 D 適用於暫時存放區。 在 Linux，Azure 」 通常 」 （但並非一定） 公開本機磁碟暫存成 /dev/sdb 區塊裝置。 通常是為 （可透過 /etc/waagent.conf） /mnt/resource 或 /mnt 連接點裝載 Azure Linux 代理程式。

###<a name="partitioning"></a>分割

Azure 原本瞭解 PaaS 應用程式中的層 （web 角色及工作角色），並因此可以正常分散這些錯誤及更新的網域。 相反地，您必須以手動方式透過可用性集定義服務 (IaaS) 應用程式為基礎結構中的層。 可用性集，才能在 IaaS SLA。

![Azure 虛擬機器設定可用性](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png)

在上圖中 （其運作方式與 web 應用程式層） 的網際網路服務 (IIS) 層和 SQL 層 （其運作方式與資料層） 指派給不同的顯示狀態集。 這樣每一層的所有執行個體，有將虛擬機器分散到故障網域硬體備份，更新時不向下採取整個層。

###<a name="load-balancing"></a>負載平衡

如果 Vm 審視做好分散到他們的流量，您必須群組中的應用程式與負載平衡 Vm 在特定的 TCP 或 UDP 結束點。 如需詳細資訊，請參閱[負載平衡虛擬機器](../virtual-machines/virtual-machines-linux-load-balance.md)。 如果 Vm 收到輸入從其他來源 （例如，佇列機制），則不需要負載平衡器。 負載平衡器會使用基本的狀況檢查，以決定是否流量傳送至節點。 您也可建立您自己探查實作特定應用程式的狀況計量 VM 是否應該接收流量。

##<a name="storage"></a>儲存空間

Azure 儲存體是 Azure 比較基準長期資料服務。 會提供 blob、 表格、 佇列中，以及 VM 磁碟儲存空間。 它會提供可用性單一資料中心內使用複寫和資源管理的組合。 Azure 儲存體可用性 SLA 保證至少 99.9%的時間︰

* 若要新增，更新、 讀取及刪除資料將會成功且正確處理正確格式化的要求。
* 儲存帳戶必須連線至網際網路閘道器。

###<a name="replication"></a>複寫

Azure 儲存空間，協助資料持續性在區域內的完整獨立的實體儲存子系統跨維護不同的磁碟機上的所有資料的多份複本。 資料複寫同步，且所有複本認可之前寫入確認。 Azure 儲存為非常一致，表示保證讀取會反映最新的寫入。 此外，複製的資料會持續掃描到偵測與修復元 rot，儲存資料的完整性經常被忽略威脅。

服務會因複寫只要使用 Azure 儲存體。 服務提供者不需要執行額外的工作，若要從本機無法復原。

###<a name="resource-management"></a>資源管理

儲存帳戶建立之後 2014，可以放大到最多 500 TB （先前的最大值是 200 TB）。 如果需要額外的空間，則必須使用多個儲存帳戶設計應用程式。

###<a name="virtual-machine-disks"></a>虛擬機器磁碟

虛擬機器的磁碟儲存為網頁 blob 中 Azure 儲存空間，讓它完全相同持續性能與延展性屬性為 Blob 儲存體。 此設計讓資料虛擬機器的磁碟上常設，即使失敗，請執行 VM 伺服器，必須重新啟動 VM 另一個伺服器上。

##<a name="database"></a>資料庫

###<a name="sql-database"></a>SQL 資料庫

Azure SQL 資料庫會提供的服務資料庫。 它可讓應用程式，以快速佈建、 插入，將資料與查詢關聯式資料庫。 它會提供許多熟悉的 SQL Server 功能和功能，同時的硬體、 設定、 修補及恢復功能。

>[AZURE.NOTE] Azure SQL 資料庫並不提供與 SQL Server 一對一功能不一致性致歉。 它具有用來滿足不同的需求，具有唯一適合雲端應用程式 （彈性的小數位數，以減少維護成本等服務的資料庫） 的其中一個集。 如需詳細資訊，請參閱[選擇雲端 SQL Server] 選項: (PaaS) 的 Azure SQL 資料庫或 SQL Server Azure Vm (IaaS) 上](../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

####<a name="replication"></a>複寫

Azure SQL 資料庫可提供內建節點層級失敗。 將資料庫的所有寫入自動都複製到透過仲裁認可技術的兩個或多個背景節點。 （主要及次要至少有一個必須確認活動會寫入交易記錄檔交易被視為成功，並傳回之前。）若是節點失敗，資料庫會自動無法透過其中一個次要複本。 這會造成的暫時性連線中斷的用戶端應用程式。 如此一來，所有 Azure SQL 資料庫用戶端必須都實作的暫時性連線處理。 如需詳細資訊，請參閱[服務特定指導再試一次](../best-practices-retry-service-specific.md)。

####<a name="resource-management"></a>資源管理

建立時，每個資料庫會設定為大寫的大小限制。 目前可用的最大值是 1 TB （大小限制不同根據您的服務層，請參閱[服務層和效能層級的 Azure SQL 資料庫](../sql-database/sql-database-resource-limits.md#service-tiers-and-performance-levels)。 當資料庫其上方的大小限制，它會拒絕其他插入或更新的命令。 （查詢及刪除資料是仍然可以）。

在資料庫中 Azure SQL 資料庫會使用布料的轉印圖樣，來管理資源。 不過，而不是布料的轉印圖樣控制站，它會使用撥打拓撲偵測失敗。 集中每個複本有兩個相鄰，而且負責偵測時會向下。 當向下複本，相鄰觸發程序重新建立另一部電腦上重新設定代理程式。 引擎節流可確保邏輯伺服器不在電腦上使用太多的資源，或超出電腦的實體限制。

###<a name="elasticity"></a>Elasticity

在應用程式需要超過 1 TB 資料庫限制，它必須實作擴充方法。 您不按比例縮放出 Azure SQL 資料庫藉由手動分割，也就是 sharding，跨多個 SQL 資料庫的資料。 此擴充方法提供機會達到幾乎線性成本等比級數比例。 彈性的成長或隨選容量可以放大與累加成本視因為資料庫向收費根據平均使用的實際大小每日，不會根據可能大小上限。

##<a name="sql-server-on-virtual-machines"></a>虛擬機器上的 SQL Server

藉由在 Azure 虛擬機器安裝 SQL Server （2014年或更新版本），您可以利用的 SQL Server 傳統的可用功能。 這些功能包括 AlwaysOn 可用性群組和資料庫鏡像。 請注意，Azure Vm、 儲存及網路不同於內部部署，非虛擬化 IT 基礎結構的操作特性。 成功實作高的可用性/損毀修復 (HA/DR) SQL Server Azure 中的方案，您必須瞭解這些差異和設計以容納這些解決方案。

###<a name="high-availability-nodes-in-an-availability-set"></a>設定高可用性節點可用性

當您在 Azure 中實作高可用性解決方案時，您可以使用設定 Azure 中的可用性將高可用性節點放置到另一個錯誤網域及升級網域。 若要清除，顯示狀態就會 Azure 的概念。 是，請確定您的資料庫可以確實高度可用，無論您使用的 AlwaysOn 可用性群組、 鏡像資料庫或其他項目，您應該遵循的最佳作法。 如果您沒有遵循此最佳作法，您可能會是 false 假設您的系統是高度可用。 但事實上，您的節點可以所有失敗同時發生相同的錯誤網域 Azure 區域中。

無法視記錄傳送此建議。 為損毀修復功能，您應該先確定在個別的 Azure 區域執行時的伺服器。 根據預設，這些區域是個別故障網域。

Azure 雲端服務 Vm 透過 [傳統] 入口網站部署位於相同的顯示狀態設定為您必須將其部署相同的雲端服務中。 部署透過 Azure 資源管理員 （目前的入口網站） Vm 沒有這項限制。 如傳統入口網站部署 Vm Azure 雲端服務中的，只有在相同的雲端服務中的節點可以參與相同的可用性設定。 此外，雲端服務 Vm 應該位於相同的虛擬網路，以確保他們即使服務治療維護其 IPs。 如此可避免 DNS 更新干擾。

###<a name="azure-only-high-availability-solutions"></a>Azure 專用︰ 高可用性解決方案

您可以使用 AlwaysOn 可用性群組或資料庫鏡像，SQL Server 資料庫 Azure 中的有高可用性解決方案。

下圖示範 AlwaysOn 可用性群組執行上 Azure 虛擬機器架構。 此圖是來自此主題，[高可用性和 SQL server Azure 虛擬機器上的商務連絡人損毀修復](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)更深入的文章。

![Microsoft Azure 中 AlwaysOn 可用性群組](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

您也會自動可以提供 AlwaysOn 可用性群組部署結束端對端上 Azure Vm Azure 入口網站中使用 AlwaysOn 範本。 如需詳細資訊，請參閱[SQL Server Microsoft Azure 入口網站圖庫中提供的 AlwaysOn](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/)。

下圖示範如何使用資料庫鏡像 Azure 虛擬機器上的商務連絡人。 它也是來自您採取進階的主題[高可用性及損毀修復的 SQL Server Azure 虛擬機器上的商務連絡人](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)。

![Microsoft Azure 中的資料庫鏡像](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

>[AZURE.NOTE] 這兩種架構要求網域控制站。 不過，使用資料庫鏡像，可能是使用伺服器憑證的網域控制站的需求。

##<a name="other-azure-platform-services"></a>其他平台 Azure 服務

若要從本機失敗復原的平台功能可以 Azure 上建立的應用程式。 在某些情況下，您可以採取特定的動作，若要增加您的特定案例的可用性。

###<a name="service-bus"></a>服務匯流排

若要降低針對 Azure 服務匯流排的暫時性的問題，請考慮建立長期的用戶端佇列。 此暫時使用替代的本機儲存機制儲存無法新增至服務匯流排佇列中的郵件。 應用程式可以決定如何處理暫時儲存的郵件之後還原服務。 如需詳細資訊，請參閱[使用服務匯流排改善效能的最佳作法代理訊息](../service-bus-messaging/service-bus-performance-improvements.md)和[服務匯流排 （損毀修復）](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services)。

###<a name="hdinsight"></a>HDInsight

Azure HDInsight 與相關聯的資料會儲存在 Azure Blob 儲存體中預設。 Azure 儲存指定高可用性和持續性屬性 Blob 儲存體。 Hadoop MapReduce 工作與相關聯的多節點處理就會發生的暫時性 Hadoop 分散式檔案系統 (HDFS) 的 HDInsight 需要它時，佈建後。 從 MapReduce 工作的結果也會儲存預設 Azure Blob 儲存體，以便處理的資料是長期，仍會保留高度可用之後 Hadoop 叢集會取消提供。 如需詳細資訊，請參閱[HDInsight （損毀修復）](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services)。

##<a name="checklists-for-local-failures"></a>本機失敗的檢查清單

###<a name="cloud-services"></a>雲端服務

  1. 請檢閱這份文件的雲端服務一節。
  2. 設定為每個角色至少兩個執行個體。
  3. 保存在長期儲存空間，不是在角色執行個體中的狀態。
  4. 正確地處理 StatusCheck 事件。
  5. 交易可能的話，換行相關的變更。
  6. 確認工作者角色工作冪且可以重新啟動。
  7. 繼續叫用作業，直到其成功。
  8. 請考慮自動縮放策略。

###<a name="virtual-machines"></a>虛擬機器

  1. 請檢閱這份文件的虛擬機器一節。
  2. 不要使用常設儲存空間的磁碟機 D。
  3. 將顯示狀態服務層級中的群組電腦設定。
  4. 設定負載平衡與選用探查。

###<a name="storage"></a>儲存空間

  1. 請檢閱這份文件的 [儲存] 區段。
  2. 當資料或頻寬超過配額時，請使用多個儲存的帳戶。

###<a name="sql-database"></a>SQL 資料庫

  1. 請檢閱這份文件的 [SQL 資料庫] 區段。
  2. 實作重試原則處理暫時性錯誤。
  3. 使用向外策略 sharding 分割。

###<a name="sql-server-on-virtual-machines"></a>虛擬機器上的 SQL Server

  1. 請檢閱 SQL Server 上的 [這份文件的虛擬機器區段。
  2. 請遵循上述建議虛擬機器。
  3. 使用 SQL Server 可用性功能，例如 AlwaysOn。

###<a name="service-bus"></a>服務匯流排

  1. 請檢閱這份文件的服務匯流排一節。
  2. 請考慮建立長期用戶端佇列當成備份。

###<a name="hdinsight"></a>HDInsight

  1. 請檢閱這份文件的 HDInsight 一節。
  2. 沒有額外的可用性步驟都需要本機失敗。

##<a name="next-steps"></a>後續步驟

本文是著重於[Azure 恢復技術指南](./resiliency-technical-guidance.md)一系列的一部分。 本系列文章會[從整個區域的服務中斷復原](./resiliency-technical-guidance-recovery-loss-azure-region.md)。
