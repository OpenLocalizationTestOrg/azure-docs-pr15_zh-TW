<properties
   pageTitle="恢復技術指南索引 |Microsoft Azure"
   description="了解和設計，可用性、 容錯應用程式]，以及規劃損毀復原和企業版連續性技術文件的索引"
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

#<a name="azure-resiliency-technical-guidance"></a>Azure 恢復技術指南

##<a name="introduction"></a>簡介

會議可用性與損毀復原需求需要兩種類型的知識︰

- 深入的瞭解技術雲端平台的功能
- 瞭解如何正確設計分散式的服務

這一系列的文章涵蓋前者︰ 的功能和限制 Azure 的平台提供恢復 （有時稱為業務連續性） 解答。 如果您感興趣的後面，請參閱文章數列著重於[損毀修復和 Azure 應用程式的可用性](https://aka.ms/drtechguide)。 本文系列緊接著架構] 和 [設計模式中，雖然這不是一系列的焦點。 設計指導方針，您可以參閱[其他資源](#additional-resources)章節的資料。

資訊會組織成下列文章︰

- [從本機失敗復原](resiliency-technical-guidance-recovery-local-failures.md)。
實體硬體 （例如，磁碟機、 伺服器和網路裝置） 可能會失敗。 載入暫停時，可以用完資源。 本文將說明 Azure 提供給維護可用性符合下列條件的功能。

- [從 Azure 整個區域的服務中斷復原](resiliency-technical-guidance-recovery-loss-azure-region.md)。
失敗很少，但它們是理論廣泛。 整個區域可能會變得隔離網路失敗次數，因為，或者他們可以實際受到自然嚴重毀損。 本文說明如何使用 Azure 建立橫跨地理區域的應用程式。

- [從內部部署至 Azure 復原](resiliency-technical-guidance-recovery-on-premises-azure.md)。
雲端大幅變更損毀修復，讓組織使用 Azure 建立第二個網站復原經濟。 您可以在建立和維護次要資料中心的成本。 本文說明 Azure 提供擴充至雲端的內部部署資料中心的功能。

- [復原資料損毀或不慎刪除](resiliency-technical-guidance-recovery-data-corruption.md)。
應用程式可以有錯誤的損毀的資料。 運算子不正確的方式可以刪除重要資料。 本文說明 Azure 提供備份資料和其時間還原至先前的點。

##<a name="additional-resources"></a>其他資源

- [損毀修復和內建在 Microsoft Azure 應用程式的可用性](resiliency-disaster-recovery-high-availability-azure-applications.md)。
本文是可用性和損毀修復詳細的說明。 矩形手動複寫參照和交易資料的挑戰。 最終的各節提供的不同類型的損毀復原拓撲橫跨 Azure 最高層級的顯示狀態區域的摘要。

- [高可用性檢查清單](resiliency-high-availability-checklist.md)。
這份文件的功能，服務清單，而設計，可協助您增加恢復及應用程式的可用性。

- [Microsoft Azure 服務恢復指引](resiliency-service-guidance-index.md)。
本文是 Azure 服務的索引，並提供連結至損毀復原指引和設計指南。

- [概觀︰ 雲端與 SQL 資料庫的業務連續性和資料庫損毀修復](../sql-database/sql-database-business-continuity.md)。
本文提供可用性 Azure SQL 資料庫技術。 主要放置中心備份與還原策略。 如果您在您的雲端服務中使用 Azure SQL 資料庫，您應該檢閱這本白皮書和其相關的資源。

- [可用性及損毀修復的 SQL Server Azure 虛擬機器中的商務連絡人](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)。
本文將說明您可以瀏覽時作為基礎結構服務 (IaaS) 來裝載資料庫服務的可用選項。 它討論 AlwaysOn 可用性群組、 資料庫鏡像、 記錄傳送，以及備份/還原。 多個教學課程顯示如何使用這些技巧。

- [設計 Azure 雲端服務上的大型服務的最佳作法](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/)。
本文著重於開發彈性的雲端架構。 您用來改善延展性技巧許多也改善可用性。 此外，如果您的應用程式無法在增加負載縮放，延展性會變成可用性問題。

- [備份與還原的 SQL Server Azure 虛擬機器中的商務連絡人](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)。
本文提供如何備份及還原執行 Azure 虛擬機器上的商務連絡人的 Microsoft SQL Server 技術指南。

- [保全︰ 有彈性的雲端架構提供的指導](https://channel9.msdn.com/Series/FailSafe)。
本文提供 Microsoft 技術，及實作這些不同情況下的架構食譜實作這些架構提供的指導方針的指導方針建置有彈性的雲端架構。

- [技術案例研究︰ 使用雲端技術來改善損毀修復](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery)。
此案例研究顯示 Microsoft IT 如何使用 Azure 來改善損毀修復。

##<a name="next-steps"></a>後續步驟

本文是著重於 Azure 恢復技術指南一系列的一部分。 如果您有興趣閱讀其他關於此系列文章中，您可以開始復原[從本機失敗](resiliency-technical-guidance-recovery-local-failures.md)。
