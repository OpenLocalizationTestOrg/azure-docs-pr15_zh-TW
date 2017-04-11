
<properties
   pageTitle="Azure 指南 |模式和實務 |Microsoft Azure"
   description="最佳作法和 Azure 指導"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="christb"/>

# <a name="azure-guidance"></a>Azure 指南

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Microsoft 模式和實務小組是 Azure 客戶會談小組的一部分。 我們的目的在於協助開發人員、 架構，以及成功 Microsoft Azure 平台上的 IT 專業人員。 我們開發會顯示在 Azure 建置雲端解決方案的最佳作法的指引。

## <a name="checklists"></a>檢查清單

下列清單會檢閱可用性能與延展性的基本方面的快速參考。 

- [可用性檢查清單][AvailabilityChecklist] 

    建議的作法，以確保可用性摘要。

- [延展性檢查清單][ScalabilityChecklist]

    建議的做法來設計及實作可調整服務和處理資料管理的摘要。

## <a name="best-practices-articles"></a>最佳作法文章

這些文章提供經常雲端相關聯的重要概念深入討論運算。 

- [API 設計][APIDesign] 

    設計 web API 時要考慮的設計問題的討論。

- [API 實作][APIImplementation] 

    實作及發佈網路 API 的建議作法一組。

- [API 安全性指南](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 

    驗證與授權疑慮 （例如，權杖類型、 授權通訊協定、 授權流及威脅降低） 的討論。

- [自動縮放指南][AutoscalingGuidance] 

    縮放比例而不需要使用手動作業的解決方案的考量的摘要。

- [背景工作指南][BackgroundJobsGuidance] 

    可用的選項和建議的作法實作應該獨立地從任何前景或互動作業的背景中執行的工作的描述。

- [內容傳遞網路 」 (CDN) 指導方針][CDNGuidance] 

    一般指導方針，使用 CDN 最小化載入您的應用程式，並最大化可用性和效能的建議。

- [快取的指導方針][CachingGuidance] 

    如何使用快取以改善效能與延展性系統的摘要。

- [資料 Partitioning 指南][DataPartitioningGuidance]

    您可以使用分割資料以改善延展性策略降低競爭及最佳化效能。

- [監控和診斷指南][MonitoringandDiagnosticsGuidance] 

    追蹤您的使用者如何使用您的系統、 追蹤資源使用狀況，以及通常監控健康狀況與系統效能的指南。

- [建議的命名慣例][naming-conventions] 

    建議 Azure 資源的命名慣例。

- [再試一次 [一般指示][RetryGeneralGuidance] 

    處理暫時錯誤的一般概念的討論。

- [再試一次服務的指導方針][RetryServiceSpecificGuidance]

    Azure 服務，包括可協助您使用、 調整，或延伸重試機制，該服務的資訊的許多重試功能的摘要。

## <a name="scenario-guides"></a>案例輔助線

- [Azure 上執行 Elasticsearch][elasticsearch] 
    
    Elasticsearch 是彈性的開啟來源搜尋引擎和資料庫。 適合需要快速分析及探索資訊保存在大型資料集的情況。 本指南在設計 Elasticsearch 叢集時要考慮的一些重要層面的外觀。

- [身分識別管理 multitenant 應用程式][identity-multitenant] 
    
    Multitenancy 是架構，多個租用戶共用應用程式，但分開另一個。 本指南將示範如何管理 multitenant 應用程式，使用[Azure Active Directory]中的使用者身分識別[AzureAD]處理登入和驗證。
    
- [開發大型資料解決方案](https://msdn.microsoft.com/library/dn749874.aspx)

    本指南探討 HDInsight 用於例如反覆運算探索，為資料倉庫，ETL 處理程序，以及整合到現有的 BI 系統的狀況。 也包含了解大型資料、 規劃及設計大型資料解決方案的概念與執行這些解決方案的指引。
    
## <a name="patterns"></a>圖樣

- [雲端設計模式︰ 規定的架構指導雲端應用程式](https://msdn.microsoft.com/library/dn568099.aspx)

    雲端設計模式是設計模式和相關的指引主題的文件庫。 它 articulates 以，以顯示每一段如何配合雲端應用程式架構套用模式的優點。
    
- [最佳化效能雲端應用程式](https://github.com/mspnp/performance-optimization)

    本指南為探索的阻礙負載按比例縮放的應用程式的一般反模式。 其包含範例示範八個反模式和[效能分析入門](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md)指南[評估效能與主要的度量](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md)。

## <a name="reference-architectures"></a>參照架構

我們參考架構來排列案例。
每個個別架構提供建議的作法給定的步驟，並意建議的可執行元件。

參照架構的目前文件庫的[http://aka.ms/architecture](http://aka.ms/architecture)。

## <a name="resiliency-guidance"></a>恢復指南

這些主題說明如何設計失敗分散式的雲端環境中的應用程式。   

- [恢復功能概觀][ResiliencyOvervew]

     如何建立 Azure 平台上的可以復原失敗，並繼續運作的應用程式。 說明結構化的方式來達成恢復功能，從設計到實作、 部署及作業。

- [恢復檢查清單][resiliency-checklist]

    檢查清單的建議，可協助您規劃各種不同的可能發生失敗模式。

- [分析失敗模式][resiliency-fma] 

    失敗模式分析 (FMA) 是建置程序，恢復系統，藉由識別可能失敗點。 為 FMA 程序的起點，本文會包含目錄的可能失敗模式和其降低。 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md

