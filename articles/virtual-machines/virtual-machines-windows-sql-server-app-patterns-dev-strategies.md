<properties
    pageTitle="SQL Server 應用程式圖樣 Vm |Microsoft Azure"
    description="本文涵蓋的 SQL Server Azure Vm 上的應用程式模式。 它提供解決方案設計師和開發人員的基礎架構良好的應用程式] 和 [設計。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="luisherring"
    manager="jhubbard"
    editor=""
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="lvargas" />

# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>應用程式模式和 Azure 虛擬機器中的 SQL Server 的開發策略

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="summary"></a>摘要︰
判斷哪些應用程式圖樣或圖樣使用應用程式的 SQL Server 基礎 Azure 中環境重要的設計決策，而需要深入瞭解 SQL Server 和 Azure 的每一個基礎結構元件如何共同合作。 Azure 基礎結構服務中的 SQL server，您可以輕鬆地移轉、 維持，並監控您現有 SQL Server 上的應用程式內建的 Windows Server 中 Azure 虛擬機器。

本文的目標是提供解決方案設計師和開發人員 foundation 良好的應用程式結構和設計，移轉現有 Azure，以及開發 Azure 中的新應用程式的應用程式時，他們可以依照。

針對每個應用程式模式，您會發現內部部署案例、 其個別雲端啟用的解決方案的和相關技術的建議。 另外，本文將說明 Azure 特定開發策略，好讓您可以正確設計您的應用程式。 多個可能的應用程式模式中，因為建議設計師和開發人員應該選擇最適合的圖樣的應用程式和使用者。

**技術參與者︰**路易斯卡洛斯斯空閒 Madhan Arumugam Ramakrishnan

**技術檢閱者︰**Corey Sanders Drew McDaniel、 Narayan Annamalai、 Nir Mashkowski、 Sanjay Mishra、 Silvano Coriani，宗 Schackow Tim Hickey Tim Wieman、 看來斤

## <a name="introduction"></a>簡介

您可以分隔不同的應用程式上的圖層不同電腦與不同元件的元件來開發多種類型的 n 層應用程式。 例如，您可以將用戶端應用程式，並商務規則在一部電腦、 前端網頁層及資料存取層元件在另一部電腦，並在另一部電腦的後端資料庫層元件。 這種組織結構可協助隔離彼此的每一層。 如果您變更資料來源，您不需要變更用戶端或 web 應用程式，但只資料的存取層元件。

一般*n 層*應用程式包含簡報層、 商務層和資料層︰


| 層              | 描述                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **簡報** | *展示層*（web 層、 前端） 是使用者的應用程式的互動的圖層。                                                                      |
| **企業版**     | *商務層*（中間層） 簡報層的圖層，而資料層使用彼此，而且系統的核心功能。 |
| **資料**         | 在*資料層*基本上是儲存應用程式的資料 （例如執行 SQL Server 的伺服器） 的伺服器。                                                             |

應用程式層級描述功能及在應用程式; 元件邏輯的群組而層說明的功能和元件的實際分配獨立的實體伺服器、 電腦、 網路或遠端位置上。 應用程式的圖層可能會位於相同的實體電腦 （相同層），或可能分佈不同的電腦 （n-層級），並透過明確定義的介面其他圖層中的元件與通訊的各個圖層中的元件。 您可以將為指的實體分配模式，例如兩層、 三層和 n 層的字詞層。 **2 層圖樣**包含兩個應用程式層︰ 應用程式伺服器與資料庫伺服器。 直接通訊，這時之間的應用程式伺服器與資料庫伺服器。 應用程式伺服器包含網站層和商務層元件。 在**3 層應用程式的模式**中，有三個應用程式層︰ 網頁伺服器、 應用程式伺服器，其中包含商務邏輯層及/或商務層資料存取元件，與資料庫伺服器。 在應用程式伺服器，這時網頁伺服器與資料庫伺服器之間的通訊。 應用程式層級和層級的詳細資訊，請參閱[Microsoft 應用程式架構指南](https://msdn.microsoft.com/library/ff650706.aspx)。

閱讀本文開始之前，您應該已在 SQL Server 和 Azure 的基本概念的知識庫。 如需的資訊，請參閱[SQL Server 線上活頁簿](https://msdn.microsoft.com/library/bb545450.aspx)、 [SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)與[Azure.com](https://azure.microsoft.com/)。

本文將說明數個可用於簡單應用程式，以及高度複雜的企業應用程式的應用程式模式。 之前詳細說明每一個模式，建議您，您應該瞭解 Azure，例如[Azure 儲存體](../storage/storage-introduction.md) [Azure SQL 資料庫](../sql-database/sql-database-technical-overview.md)]，與[SQL Server Azure 虛擬機器中](virtual-machines-windows-sql-server-iaas-overview.md)的可用的資料儲存區服務。 若要讓您的應用程式的最佳設計決策，了解清楚地使用哪些資料儲存服務。

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>選擇 [Azure 虛擬機器中的 [SQL Server 時︰

- 您需要 SQL Server 和 Windows 上的控制項。 例如，這可能會包含 SQL Server 版本、 特殊快速修正程式、 效能設定等。

- 如果您需要使用 SQL Server 內部部署的完整相容性，想要移動 Azure 為現有的應用程式-是。

- 您想要運用 Azure 環境的功能，但 Azure SQL 資料庫不支援的應用程式所需的所有功能。 這可能包含以下區域︰

    - **資料庫大小**︰ 這份文件已更新時，SQL 資料庫支援 1 TB 的資料的資料庫。 如果您的應用程式需要多個 1 TB 的資料，而且您不想要實作自訂 sharding 解決方案，建議您使用 SQL Server Azure 虛擬機器中。 最新的資訊，請參閱[縮放出 Azure SQL 資料庫](https://msdn.microsoft.com/library/azure/dn495641.aspx)及[Azure SQL 資料庫服務層和效能層級](../sql-database/sql-database-service-tiers.md)。
    - **HIPAA 規範**︰ 醫療客戶和獨立軟體廠商 (Isv) 可能選擇，而不是[Azure SQL 資料庫](../sql-database/sql-database-technical-overview.md)的[SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)因為 SQL Server Azure 虛擬機器中涵蓋的 HIPAA 商務關聯合約 (BAA)。 在規範的資訊，請參閱[Microsoft Azure 信任中心︰ 規範](https://azure.microsoft.com/support/trust-center/compliance/)。
    - **執行個體層級功能**︰ 這次 SQL 資料庫不支援的外部資料庫 live 功能 （例如連結伺服器代理程式工作，FileStream、 服務代理人等等。）。 如需詳細資訊，請參閱[Azure SQL 資料庫指導方針和限制](https://msdn.microsoft.com/library/azure/ff394102.aspx)。

## <a name="1-tier-simple-single-virtual-machine"></a>1 層 （簡單）︰ 單一虛擬機器

在此應用程式的模式，您獨立虛擬機器中 Azure 部署您的 SQL Server 應用程式和資料庫。 相同的虛擬機器包含用戶端/web 應用程式、 商業元件、 資料的存取層級]，與資料庫伺服器。 簡報、 商業及資料存取碼邏輯分隔，但實際位於單一伺服器電腦。 大部分的客戶開始此應用程式的模式，然後他們擴充至其系統新增更多的網頁角色或虛擬機器。

這個應用程式模式很有用︰

- 您想要執行移轉至評估是否在平台解答應用程式的需求，或不 Azure 平台的簡單移轉。

- 您想要保留所有應用程式層裝載於相同的虛擬機器中相同的 Azure 資料中心，以減少層之間的延遲。

- 您想要快速佈建開發和短時間內的測試環境。

- 您想要執行壓力測試不同的工作量層級，但同時您不希望擁有和維護許多實體機器所有的時間。

下圖示範簡單的內部部署案例及如何部署其單一中 Azure 虛擬機器中的雲端啟用方案。

![1 層應用程式圖樣](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

在相同的實體層為簡報圖層上部署 （商務邏輯及資料存取元件） 的商務層可以最大化應用程式效能，除非您必須使用另一個層因為延展性或安全性的考量。

開始使用，這是常見的圖樣，因為您會發現下列文章上移轉實用將資料移至您的 SQL Server VM 的︰[移轉至 Azure VM 上 SQL Server 資料庫](virtual-machines-windows-migrate-sql.md)。

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 層 （簡單）︰ 多個虛擬機器

在此應用程式模式，您會放在不同的虛擬機器中的每個應用程式層部署 Azure 中的 3 層應用程式。 此提供非常有彈性的環境簡單的縮放比例向上和向外案例。 一個虛擬機器中包含您的用戶端/web 應用程式，另一個主控您的商業元件，另一個裝載的資料庫伺服器。

這個應用程式模式很有用︰

- 您想要執行複雜的資料庫應用程式至 Azure 虛擬機器移轉。

- 您想要裝載在不同區域中的不同的應用程式層。 例如，您可能會共用部署至用於報告用途的多個區域的資料庫。

- 您想要從內部部署虛擬化平台至 Azure 虛擬機器移動企業應用程式。 企業應用程式的詳細說明，請參閱[什麼是企業應用程式](https://msdn.microsoft.com/library/aa267045.aspx)。

- 您想要快速佈建開發和短時間內的測試環境。

- 您想要執行壓力測試不同的工作量層級，但同時您不希望擁有和維護許多實體機器所有的時間。

下圖示範如何將簡單的 3 層應用程式在 Azure 放在不同的虛擬機器中的每一個應用程式層級。

![3 層應用程式圖樣](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

在此應用程式的模式，只有一個虛擬機器 (VM) 中沒有每一層。 如果您有多個 Vm Azure 中時，建議您設定虛擬網路。 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)建立受信任的安全性邊界，並還可讓 Vm 彼此之間通訊的私用的 IP 位址。 此外，請務必所有的網際網路連線只前往簡報層。 當追蹤此應用程式的模式，管理網路安全性群組規則來控制存取權。 如需詳細資訊，請參閱[使用 [Azure 入口網站您 VM 允許外部存取](virtual-machines-windows-nsg-quickstart-portal.md)。

在圖表中的網際網路通訊協定可以 TCP、 UDP、 HTTP] 或 HTTPS。

>[AZURE.NOTE] 設定中 Azure 虛擬網路是免費。 不過，您會連線到內部部署 VPN 閘道器。 此費用根據連線且能夠使用的時間量。

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 層和與簡報層延展 3 層

在此應用程式模式，您會放在不同的虛擬機器中的每個應用程式層部署層 2 或 3 層資料庫應用程式至 Azure 虛擬機器。 此外，您延展展示層因為的內送的用戶端要求提高音量。

這個應用程式模式很有用︰

- 您想要從內部部署虛擬化平台至 Azure 虛擬機器移動企業應用程式。

- 您想要展示層的內送的用戶端要求提高音量因為擴充。

- 您想要快速佈建開發和短時間內的測試環境。

- 您想要執行壓力測試不同的工作量層級，但同時您不希望擁有和維護許多實體機器所有的時間。

- 您想要擁有基礎結構的環境的向上和向下可以視需要調整。

下圖示範如何將應用程式層 Azure 中的多個虛擬機器中所展示層因為的內送的用戶端要求提高音量。 如圖所示，Azure 負載平衡器負責將流量分散到多個虛擬機器和也判斷要連線的網頁伺服器。 有多個執行個體的網頁伺服器負載平衡器前，可確保簡報層的可用性。

![應用程式模式-簡報層延展](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>在一層的多個 Vm 層 2、 3-層] 或 [n 層圖樣的最佳作法

建議您將屬於相同的雲端服務中，在同一個在相同層可用性虛擬機器設定。 例如， **CloudService1**和**AvailabilitySet1**和**CloudService2**和**AvailabilitySet2**資料庫伺服器的一組位置網頁伺服器的一的組。 Azure 中設定可用性可讓您將可用性節點放置到另一個錯誤網域並升級網域。

若要運用多個層的 VM 執行個體，您必須設定 Azure 負載平衡器應用程式層之間。 若要在每一個層級設定負載平衡器 」，分別建立每一層 Vm 中負載平衡結束點。 為特定的層，先建立相同的雲端服務中的 Vm。 如此一來，可確保他們擁有相同的公用虛擬 IP 位址。 接下來，請在其中一個虛擬機器該層上建立端點。 然後在該負載平衡層的其他虛擬機器指派相同的端點。 藉由建立負載平衡的設定，您將流量分散到多個虛擬機器，也可讓負載平衡器 」 來判斷哪一個節點連線時的後端 VM 節點失敗。 例如，有多個執行個體的網頁伺服器前負載平衡器可確保簡報層的可用性。

最佳做法，請務必所有的網際網路連線先前往簡報層。 在 [圖層簡報存取業務層級，並商務層然後存取資料層。 如需有關如何允許存取簡報圖層的詳細資訊，請參閱[使用 [Azure 入口網站您 VM 允許外部存取](virtual-machines-windows-nsg-quickstart-portal.md)。

請注意負載平衡器 」 Azure 中運作類似負載平衡器內部部署環境中。 如需詳細資訊，請參閱[負載平衡 Azure 基礎結構服務](virtual-machines-windows-load-balance.md)。

此外，建議您設定私人網路的虛擬機器使用 Azure 虛擬網路。 這個選項可讓它們彼此之間通訊的私用的 IP 位址。 如需詳細資訊，請參閱[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 層和商務層延展與 3 層

在此應用程式模式，您會放在不同的虛擬機器中的每個應用程式層部署層 2 或 3 層資料庫應用程式至 Azure 虛擬機器。 此外，您可能要分散到多個虛擬機器因為應用程式的應用程式伺服器元件。

這個應用程式模式很有用︰

- 您想要從內部部署虛擬化平台至 Azure 虛擬機器移動企業應用程式。

- 您想要分散到多個虛擬機器因為應用程式的應用程式伺服器元件。

- 您想要移動商務邏輯大量內部部署 LOB （線條的企業） 應用程式至 Azure 虛擬機器。 LOB 應用程式是一組都是很重要執行企業，例如會計、 人力資源 (HR)、 薪資、 供應鏈管理及資源規劃應用程式的要徑電腦應用程式。

- 您想要快速佈建開發和短時間內的測試環境。

- 您想要執行壓力測試不同的工作量層級，但同時您不希望擁有和維護許多實體機器所有的時間。

- 您想要擁有基礎結構的環境的向上和向下可以視需要調整。

下圖示範的內部部署案例，以及其啟用雲端解決方案。 在此案例中，您可以將應用程式層 Azure 中的多個虛擬機器中所業務層級，其中包含的商務邏輯層及資料存取元件。 如圖所示，Azure 負載平衡器負責將流量分散到多個虛擬機器和也判斷要連線的網頁伺服器。 有多個執行個體的應用程式伺服器負載平衡器前，可確保業務層級的可用性。 如需詳細資訊，請參閱[最佳作法層 2、 3 層或有多個其中一層的虛擬機器 n 層應用程式模式](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier)。

![使用商務層延展應用程式圖樣](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 層和 3 層簡報和商務層延展與 HADR

在此應用程式模式，您部署層 2 或 3 層資料庫應用程式至 Azure 虛擬機器發佈簡報層 （網頁伺服器） 和商務層 （應用程式伺服器） 元件，以多個虛擬機器。 此外，您可以實作高可用性和損壞修復解決方案資料庫 Azure 虛擬機器中的。

這個應用程式模式很有用︰

- 您想要將企業應用程式虛擬化的平台內部部署到 Azure 實作 SQL Server 可用性和損毀修復功能。

- 您想要不按比例縮放的簡報層和商務層因為的內送的用戶端要求提高的音量和提高複雜度應用程式。

- 您想要快速佈建開發和短時間內的測試環境。

- 您想要執行壓力測試不同的工作量層級，但同時您不希望擁有和維護許多實體機器所有的時間。

- 您想要擁有基礎結構的環境的向上和向下可以視需要調整。

下圖示範的內部部署案例，以及其啟用雲端解決方案。 在此案例中，您不按比例縮放的簡報層和 Azure 中的多個虛擬機器中的商務層元件。 此外，您會在 Azure 中實作高可用性和損毀復原 (HADR) 的技巧 SQL Server 資料庫。

在不同執行應用程式的多份複本 Vm 請確定您是跨負載平衡要求他們。 如果您有多個虛擬機器，必須先確定您的 Vm 可存取和使用一個點執行時間。 如果您設定負載平衡，Azure 負載平衡器追蹤 Vm 的狀況，並指示來電正常運作的 VM 節點正確。 如何設定負載平衡虛擬機器的詳細資訊，請參閱[負載平衡 Azure 基礎結構服務](virtual-machines-windows-load-balance.md)。 有多個執行個體的網頁和應用程式伺服器負載平衡器前，可確保將簡報和商務層的可用性。

![向外] 和 [高可用性](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>需要 SQL HADR 的應用程式模式的最佳作法

當您設定 SQL Server 可用性和損毀復原方案 Azure 虛擬機器中的商務連絡人時，設定您的虛擬機器使用[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的虛擬網路是必要欄位。  虛擬機器中虛擬網路會有穩定私人的 IP 位址，即使服務停擺，因此您可以在其中避免所需的 DNS 名稱解析更新時間。 此外，虛擬的網路可讓您將內部部署網路延伸到 Azure，並建立受信任的安全性邊界。 例如，如果應用程式 （例如，[Active Directory Windows 驗證） 的公司網域限制，設定[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)是必要。

大部分的客戶執行的實際執行程式碼 Azure，Azure 中保持主要和次要複本。

可用性及損毀復原技巧教學課程和完整詳細資訊，請參閱[高可用性及損毀修復的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-high-availability-dr.md)。

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2 層和 3 層使用 Azure Vm 和雲端服務

在此應用程式的模式，您必須部署 Azure 層 2 或 3 層應用程式使用兩個[Azure 雲端服務](../cloud-services/cloud-services-choose-me.md#tellmecs)（web 及工作者角色-為服務 (PaaS) 的平台） 和[Azure 虛擬機器](virtual-machines-windows-about.md)（基礎結構的服務 (IaaS)）。 使用資料層簡報層/商務層和[Azure 虛擬機器](virtual-machines-windows-about.md)中的 SQL Server [Azure 雲端服務](https://azure.microsoft.com/documentation/services/cloud-services/)是有幫助 Azure 上執行大部分的應用程式。 原因是，無法在雲端服務上執行計算執行個體提供容易管理、 部署、 監控及擴充。

與雲端服務，Azure 會為您維護基礎結構、 執行例行維護、 修補程式作業系統上，並想要復原的服務與硬體失敗。 當您的應用程式需要向外時，自動和手動擴充會提供選項雲端服務專案以增加或減少的執行個體或您的應用程式，可使用的虛擬機器數。 此外，您可以使用內部部署 Visual Studio 部署至雲端服務中的專案 Azure 應用程式。

在 [摘要] 如果您不想要擁有更多層簡報/商務用的系統管理工作，您的應用程式不需要的軟體或作業系統的任何複雜的設定，使用 Azure 雲端服務。 如果 Azure SQL 資料庫並不支援您正在尋找的所有功能，使用 SQL Server Azure 虛擬機器中的資料層。 Azure 雲端服務上執行應用程式，並將資料儲存 Azure 虛擬機器中的商務連絡人會結合兩個服務的優勢。 如需詳細的比較，請參閱本主題中上[比較開發策略 Azure 中](#comparing-web-development-strategies-in-azure)。

在此應用程式的模式，展示層會包含網頁角色，正在執行 Azure 執行環境中的雲端服務元件，而且它自訂支援 IIS 和 ASP.NET web 應用程式。 企業版或後端層包含工作者角色，雲端服務元件執行 Azure 執行環境中，並可用於一般化開發、 和可能會執行背景處理 web 角色的。 資料庫層存放在 SQL Server 中 Azure 虛擬機器。 直接或透過商務層 – 工作者角色元件，就會發生的簡報層和資料庫層之間的通訊。

這個應用程式模式很有用︰

- 您想要將企業應用程式虛擬化的平台內部部署到 Azure 實作 SQL Server 可用性和損毀修復功能。

- 您想要擁有基礎結構的環境的向上和向下可以視需要調整。

- Azure SQL 資料庫不支援您的資料庫或應用程式需要的所有功能。

- 您想要執行壓力測試不同的工作量層級，但同時您不希望擁有和維護許多實體機器所有的時間。

下圖示範的內部部署案例，以及其啟用雲端解決方案。 在此案例中，您可以將簡報中層 web 角色，工作者角色業務層級，但在 Azure 虛擬機器中的資料層。 在不同網頁角色執行簡報層的多份複本可確保在它們之間負載平衡要求。 當您合併 Azure 雲端服務與 「 Azure 虛擬機器時，我們建議您也將[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)設定。 與[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)，您可以讓穩定及持續私人 IP 位址相同的雲端服務中雲端中。 一旦您為您的虛擬機器中定義虛擬網路，雲端服務，他們可以開始彼此之間的私用的 IP 位址進行通訊。 此外，在相同的[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)時遇到虛擬機器和 Azure 的 web 工作者角色提供低延遲和更安全的連線。 如需詳細資訊，請參閱[什麼是雲端服務](../cloud-services/cloud-services-choose-me.md)。

如圖所示，Azure 負載平衡器將流量分散到多個虛擬機器，也會決定哪些網頁伺服器或連線到的應用程式伺服器。 有多個執行個體的網頁和應用程式伺服器負載平衡器前，可確保簡報層和商務層的可用性。 如需詳細資訊，請參閱[需要 SQL HADR 的應用程式模式的最佳作法](#best-practices-for-application-patterns-requiring-sql-hadr)。

![與雲端服務應用程式模式](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

實作此應用程式模式的另一個方法是使用包含簡報層和商務層元件，如下圖所示的合併的 web 角色。 這個應用程式模式非常適合需要狀態設計的應用程式。 Azure 提供 web 及工作者角色無運算節點，因為我們建議您執行來儲存工作階段狀態使用下列技術的其中一個邏輯︰ [Azure 快取](https://azure.microsoft.com/documentation/services/redis-cache/)、 [Azure 資料表儲存體](../storage/storage-dotnet-how-to-use-tables.md)或[Azure SQL 資料庫](../sql-database/sql-database-technical-overview.md)。

![與雲端服務應用程式模式](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Azure Vm、 Azure SQL 資料庫與 Azure 應用程式服務 （Web 應用程式） 的圖樣

這個應用程式模式的主要目標是您顯示如何合併 Azure 基礎結構為使用您的方案中 Azure 的平台為-的-服務元件 (PaaS) 服務 (IaaS) 元件。 此模式的焦點 Azure SQL 資料庫關聯式資料的儲存空間。 它不包含 SQL Server Azure 虛擬機器中已為服務產品的 Azure 基礎結構的一部分。

在此應用程式模式，您可以部署 Azure 資料庫應用程式方式置於相同的虛擬機器中的簡報和商務層存取資料庫中 （SQL 資料庫） 的 Azure SQL 資料庫伺服器。 您可以使用傳統 IIS 網站解決方案實作展示層。 或者，您可以藉由使用[Azure Web 應用程式](https://azure.microsoft.com/documentation/services/app-service/web/)實作合併的簡報和商務層。

這個應用程式模式很有用︰

- 您已有現有的 SQL 資料庫伺服器設定 Azure 中和您想要快速測試您的應用程式。

- 您想要測試 Azure 環境的功能。

- 您想要快速佈建開發和短時間內的測試環境。

- 您的商務邏輯和資料 access 元件可獨立 web 應用程式中。

下圖示範的內部部署案例，以及其啟用雲端解決方案。 在此案例中，您將應用程式層在單一虛擬機器中 Azure 及存取 Azure SQL 資料庫中的資料。

![混合應用程式的模式](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

如果您選擇使用 Azure Web 應用程式實作合併的網頁和應用程式層，建議您保留中間層或應用程式層為 web 應用程式的內容中的動態連結程式庫 (Dll)。

此外，檢閱指定在本文結尾的[比較的 web 開發策略 Azure 中](#comparing-web-development-strategies-in-azure)區段中，若要進一步瞭解程式設計技巧的建議。

## <a name="n-tier-hybrid-application-pattern"></a>N 層混合式應用程式模式

在 n 層混合式應用程式模式中，您可以執行應用程式中分佈的內部部署和 Azure 之間的多個層。 因此，您建立的彈性，可重複使用的混合式系統，您可以修改或新增的特定層，而不變更其他層。 若要擴充至雲端公司網路，您可以使用[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)服務。

此混合式應用程式的模式很有用︰

- 您想要建立應用程式，只能在雲端中執行，只能在內部部署。

- 您想要移轉的雲端現有的內部部署應用程式的部分或所有項目。

- 您想要將內部部署虛擬化平台企業應用程式移到 Azure。

- 您想要擁有基礎結構的環境的向上和向下可以視需要調整。

- 您想要快速佈建開發和短時間內的測試環境。

- 您想要成本有效企業資料庫應用程式的備份。

下圖示範的內部部署和 Azure 應用範圍橫跨 n 層混合式應用程式模式。 圖表所示，內部部署基礎結構便會包括[Active Directory 網域服務](https://technet.microsoft.com/library/hh831484.aspx)網域控制站支援使用者驗證和授權。 請注意圖示範的案例中，資料層的某些部分居住在內部部署資料中心而 live Azure 中的資料層的某些部分。 根據您的應用程式的需求，您可以執行許多其他的混合式狀況。 例如，您可能會將展示層和商務層內部部署環境但 Azure 中的資料層。

![N 層應用程式的模式](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

在 Azure，您可以使用 Active Directory 作為獨立雲端目錄為您的組織或您也可以整合現有的內部部署 Active Directory 與[Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)。 如圖所示，商務層元件可以存取多個資料來源，例如[SQL](virtual-machines-windows-sql-server-iaas-overview.md) server Azure 中透過私人內部的 IP 位址，內部部署的 SQL Server 透過[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)，或使用.NET Framework 資料提供者技術[SQL 資料庫](../sql-database/sql-database-technical-overview.md)。 此圖表中 Azure SQL 資料庫是選擇性的資料儲存區服務。

在 n 層混合式應用程式模式中，您可以執行下列工作流程中指定的順序︰

1. 識別需要會移至雲端使用[Microsoft 評估和規劃 (Map)](http://microsoft.com/map)的企業資料庫應用程式。 Map 庫存和效能資料收集您考慮的虛擬化的電腦，並提供建議容量和評估規劃。

1. 規劃資源和 Azure 的平台，例如儲存帳戶和虛擬機器中所需的設定。

1. 設定公司網路內部部署和[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)之間的網路連線。 若要設定公司網路內部部署和中 Azure 虛擬機器之間的連線，請使用下列兩種方法之一︰

    1. 之間建立連線內部部署和 Azure 虛擬機器公用結束點透過 Azure 中。 這個方法提供簡單的設定，並可讓您在您的虛擬機器中使用 SQL Server 驗證。 此外，設定您的網路安全性群組規則來控制的 vm 公用流量。 如需詳細資訊，請參閱[使用 [Azure 入口網站您 VM 允許外部存取](virtual-machines-windows-nsg-quickstart-portal.md)。

    1. 建立內部部署和 Azure 之間的連線，透過 Azure 虛擬私人網路 (VPN) 通道。 這個方法可讓您將延伸到中 Azure 虛擬機器網域原則。 此外，您可以設定防火牆規則，並在您的虛擬機器中使用 Windows 驗證。 目前 Azure 支援安全網站-VPN 和點為網站 VPN 連線︰

        - 安全的網站的連線，您可以建立內部網路和虛擬網路之間的網路連線 Azure 中。 建議您 Azure 連接您的內部部署資料中心環境。

        - 安全點為網站的連線，您可以在 Azure 虛擬網路和個別電腦執行的任何位置之間建立網路連線。 通常建議的開發和測試。

    如何連線至 SQL Server Azure 中的資訊，請參閱[連線至 SQL Server 虛擬機器上 Azure](virtual-machines-windows-classic-sql-connect.md)。

1. 設定已排程的工作和備份中 Azure 虛擬機器磁碟的內部部署資料的通知。 如需詳細資訊，請參閱[SQL Server 備份與還原 Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148.aspx)和[備份與還原的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-backup-recovery.md)。

1. 根據您的應用程式的需求，您可以執行下列三個常見的案例其中一項︰

    1. 您可以將您的網頁伺服器、 應用程式伺服器及不區分大小寫的資料在資料庫伺服器 Azure 中而保留機密資料內部。

    1. 您可以將您的網頁伺服器及應用程式伺服器內部部署而中 Azure 虛擬機器中的資料庫伺服器。

    1. 您可以將您的資料庫伺服器、 網頁伺服器，及應用程式伺服器內部部署而您保持在 Azure 虛擬機器中的資料庫複本。 這項設定可以讓內部部署網頁伺服器或報表的應用程式存取 Azure 資料庫的複本。 因此，您可以達到至右下的內部部署資料庫中的工作量。 我們建議您執行大量這個案例閱讀工作量及他們的目的。 建立資料庫複本 Azure 中的資訊，請參閱在[高可用性和 SQL server Azure 虛擬機器中的商務連絡人損毀修復](virtual-machines-windows-sql-high-availability-dr.md)AlwaysOn 可用性群組。

## <a name="comparing-web-development-strategies-in-azure"></a>比較 Azure 中的網頁開發策略

若要實作及部署多層 Azure 中的 [SQL Server 型應用程式，您可以使用下列兩種程式設計方法之一︰

- 在 SQL Server Azure 虛擬機器中的商務連絡人的 Azure 和 access 資料庫中設定的傳統的網頁伺服器 (IIS-Internet Information Services)。

- 實作及部署 Azure 雲端服務。 然後，請確定此雲端服務可以存取 Azure 虛擬機器中的 SQL Server 資料庫。 在雲端服務可以包含多個網頁和背景工作的角色。

下表提供傳統的 web 開發 Azure 雲端服務與 SQL Server Azure 虛擬機器中的商務連絡人與 Azure Web 應用程式的比較。 資料表包含 Azure Web 應用程式，因為它是可以使用 SQL Server Azure VM 做為資料來源的 Azure Web 應用程式透過其虛擬的公用 IP 位址或 DNS 名稱。

||傳統的 web 開發 Azure 虛擬機器中的商務連絡人|Azure 中的雲端服務|虛擬主機 Azure Web 應用程式|
|---|---|---|---|
|**從內部部署的應用程式移轉**|為現有的應用程式-是。|應用程式需要網頁和背景工作的角色。|為現有的應用程式-但適用於獨立的 web 應用程式和需要快速延展性的 web 服務。|
|**開發及部署**|Visual Studio WebMatrix、 視覺 Web 開發人員、 WebDeploy、 FTP、 TFS、 IIS 管理員，PowerShell。|Visual Studio 中，Azure sdk，您可以 TFS，PowerShell。 每個雲端服務有兩種環境，您可以部署您的服務封裝並設定︰ 執行與生產。 您可以將執行加以測試之前升級生產環境部署雲端服務。|Visual Studio WebMatrix、 視覺 Web 開發人員、 FTP、 給、 BitBucket、 CodePlex、 DropBox、 GitHub、 水星、 TFS、 Web 部署 PowerShell。|
|**管理與設定**|您負責管理應用程式、 資料、 防火牆規則，虛擬網路，以及作業系統的系統管理工作。|您負責管理應用程式、 資料、 防火牆規則和虛擬網路上的工作。|您負責上的應用程式及僅限資料管理工作。|
|**可用性及損毀修復 (HADR)**|我們建議您在相同的可用性設定和相同的雲端服務中，將虛擬機器。 在同一個保留您的 Vm 可用性設定可讓 Azure 將可用性節點放置到另一個錯誤網域及升級網域。 同樣地，，相同的雲端服務中保留您的 Vm 啟用負載平衡，Vm 可以彼此直接到本機 Azure 資料中心的網路。<br/><br/>您負責實作的可用性和損毀修復解決方案的 SQL Server 中 Azure 虛擬機器以避免任何停機時間。 支援 HADR 技術，請參閱[高可用性及損毀修復的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-high-availability-dr.md)。<br/><br/>您負責備份資料與應用程式。<br/><br/>如果資料中心主機機器失敗由於硬體問題，因此，azure 可以移動您的虛擬機器。 此外，有可能是您 VM 的計劃停機時間主機上的安全性或軟體更新時更新。 因此，建議您維護至少兩個 Vm 中每一個應用程式層級，以確保連續的可用性。 Azure 虛擬機器單一不提供 SLA。 如需詳細資訊，請參閱[Azure 恢復技術指南](../resiliency/resiliency-technical-guidance.md)。|Azure 管理所產生的基礎的硬體或作業系統軟體失敗。 我們建議您執行多個網頁或背景工作的角色，以確保您的應用程式的執行個體。 資訊，請參閱[雲端服務，請虛擬機器虛擬網路服務等級協定](http://www.microsoft.com/download/details.aspx?id=38427)，[損毀修復和 Azure 應用程式的可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>您負責備份資料與應用程式。<br/><br/>位於 [SQL Server 資料庫中 Azure VM 的資料庫，您負責實作避免任何停機時間高可用性和損毀修復解決方案。 支援 HDAR 技術，請參閱 SQL Server Azure 虛擬機器中的商務連絡人可用性和損毀修復。<br/><br/>**SQL Server 資料庫鏡像**︰ 使用 Azure 雲端服務 （web/工作者角色）。 SQL Server Vm 和雲端服務專案可在相同的 Azure 虛擬網路。 SQL Server VM 不在相同的虛擬網路中，如果您需要建立 SQL Server 別名，若要將通訊傳送到 SQL Server 執行個體。 此外，別名名稱必須與 SQL Server 名稱相符。|可用性繼承自 Azure 工作者角色與 Azure blob 儲存體，Azure SQL 資料庫。 例如，Azure 儲存體維護所有 blob、 表格和佇列中資料的三個的複本。 在任何時候，Azure SQL 資料庫會一直執行的資料的三個複本，一個主要複本和兩個次要的複本。 如需詳細資訊，請參閱[Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/)和[Azure SQL 資料庫](../sql-database/sql-database-technical-overview.md)。<br/><br/>使用 SQL Server Azure VM 」 作為資料來源的 Azure Web 應用程式，請記住，Azure Web 應用程式不支援 Azure 虛擬網路。 換句話說，Azure 中的 SQL Server Vm Azure Web 應用程式的所有連線必須都經過虛擬機器公用結束點。 這可能會導致高可用性和損毀修復狀況一些限制。 例如 Azure Web 應用程式與資料庫鏡像連線至 SQL Server VM 用戶端應用程式會無法連線至新的主要伺服器資料庫鏡像需要設定 Azure 虛擬網路之間 Azure 中的 SQL Server host （主機) Vm。 因此，使用**SQL Server 資料庫鏡像**Azure Web 應用程式不支援目前。<br/><br/>**SQL Server AlwaysOn 可用性群組**︰ 使用 Azure 中的 SQL Server Vm Azure Web 應用程式時，您可以將 AlwaysOn 可用性群組設定。 但您需要設定 AlwaysOn 可用性群組接聽程式，若要將通訊傳送到公用負載平衡端點透過主要的複本。|
|**跨內部部署連線**|您可以使用 Azure 虛擬網路連線到內部部署。|您可以使用 Azure 虛擬網路連線到內部部署。|Azure 虛擬網路是受支援。 如需詳細資訊，請參閱[Web 應用程式虛擬網路的整合](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)。|
|**延展性**|向上調整，請增加虛擬機器大小，或新增更多磁碟。 如需有關虛擬機器大小的詳細資訊，請參閱[Azure 虛擬機器大小](virtual-machines-windows-sizes.md)。<br/><br/>**針對 [資料庫伺服器**︰ 擴充有透過資料庫分割技巧和 SQL Server AlwaysOn 可用性群組。<br/><br/>對於經常讀取工作負載，您可以使用[AlwaysOn 可用性群組](https://msdn.microsoft.com/library/hh510230.aspx)上多個次要節點，以及 SQL Server 複寫。<br/><br/>重寫工作負載，您可以在多個提供應用程式擴充的實體伺服器實作水平分割的資料。<br/><br/>此外，您可以在使用[SQL Server 資料相依路由](https://technet.microsoft.com/library/cc966448.aspx)實作向外。 使用資料相依路由 (DDR)，您需要在用戶端應用程式，通常是以商務層層級]，將資料庫要求路由至多個 SQL Server 節點實作磁碟分割機制。 商務層包含對應至資料分割方式和哪一個節點包含的資料。<br/><br/>您可以調整執行虛擬機器中的應用程式。 如需詳細資訊，請參閱[如何調整應用程式](../cloud-services/cloud-services-how-to-scale.md)。<br/><br/>**重要附註**︰ Azure 中的 [**自動調整大小**] 功能可讓您自動增加或減少虛擬機器所使用的應用程式。 此功能可確保使用者經驗不會受影響負面最大使用量期間，並要求較低時關閉 Vm。 建議您沒有設定自動調整大小選項加雲端服務中，包含 SQL Server Vm。 原因是 [自動調整大小] 功能可讓 Azure 虛擬機器中該 VM CPU 使用率高於一些閥值時, 開啟，並關閉虛擬機器 CPU 使用率低於，當。 自動調整大小功能很適合用於無狀態的應用程式，例如網頁伺服器，可讓任何 VM 管理沒有任何先前的狀態的任何參照的工作量。 不過，[自動調整大小] 功能不適合狀態的應用程式，例如 SQL Server，只有一個執行個體允許寫入資料庫的位置。|向上調整，請使用多個網頁和背景工作的角色。 如需 web 角色和工作者角色的虛擬機器大小的詳細資訊，請參閱[設定雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)。<br/><br/>使用**雲端服務**時，您可以定義多個角色分送處理及也達到 [應用程式的彈性縮放比例。 每個雲端服務包含一或多個網頁角色及/或工作者角色，兩者都有自己的應用程式的檔案及設定。 可以向上調整雲端服務增加角色執行個體 （虛擬機器） 數目角色的部署，並向下調整雲端服務的角色執行個體數目。 如詳細資訊，請參閱[Azure 執行模型](../cloud-services/cloud-services-choose-me.md)。<br/><br/>使用內建 Azure 可用性支援透過[雲端服務，請虛擬機器虛擬網路服務等級協定](http://www.microsoft.com/download/details.aspx?id=38427)，負載平衡器透過擴充。<br/><br/>多層應用程式，我們建議您連線至資料庫伺服器透過 Azure 虛擬網路 Vm web/工作者角色應用程式。 此外，Azure 提供的負載平衡 Vm 在相同的雲端服務中，在它們之間分配使用者要求。 虛擬機器連線以這種方式可以直接與另一個透過 Azure 資料中心的區域網路進行通訊。<br/><br/>Azure 傳統入口網站，以及的排程時間，您可以設定**自動調整大小**。 如需詳細資訊，請參閱[如何調整應用程式](../cloud-services/cloud-services-how-to-scale.md)。|**向上和向下縮放**︰ 您可以增加/減少保留做為您的網站的執行個體 (VM) 的大小。<br/><br/>延展︰ 您可以新增更多保留執行個體 (Vm)，您的網站。<br/><br/>您可以設定**自動調整大小**，在入口網站，以及的排程時間。 如需詳細資訊，請參閱[如何比例 Web 應用程式](../app-service-web/web-sites-scale.md)。|

如需有關如何選擇程式設計種方法的詳細資訊，請參閱[Azure Web 應用程式、 雲端服務，以及 Vm︰ 使用時機](../app-service-web/choose-web-site-cloud-service-vm.md)。

## <a name="next-steps"></a>後續步驟

如需有關如何執行 SQL Server Azure 虛擬機器中的詳細資訊，請參閱[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)。
