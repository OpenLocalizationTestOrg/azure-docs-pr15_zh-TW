
<properties
   pageTitle="在 Azure 指引 Elasticsearch |Microsoft Azure"
   description="在 Azure 指引 Elasticsearch。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="elasticsearch-on-azure-guidance"></a>Elasticsearch 上 Azure 指南 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Elasticsearch 是彈性開啟來源搜尋引擎和資料庫。 很有用的快速分析及探索資訊保存在大型資料集的需要。 本指南查看一些重要考量的事項設計 Elasticsearch 叢集，並將焦點放在最佳化及測試您的設定方式。

> [AZURE.NOTE]本指南假設 Elasticsearch 基本的熟悉。 如需詳細資訊，請造訪[Elasticsearch 網站](https://www.elastic.co/products/elasticsearch)。 

- **[Azure 上執行的 Elasticsearch][]**提供簡介的 Elasticsearch，一般的結構，並說明如何實作使用 Azure Elasticsearch 叢集。 
- **[調整資料 ingestion 效能上 Azure Elasticsearch][]**說明 Elasticsearch 叢集的部署，並提供深入分析您預期的高的資料 ingestion 率時要考慮的各種設定選項。
- **[調整資料彙總和 Azure 上 Elasticsearch 的查詢效能][]**提供深入分析的選項] 時的考量決定如何最佳化您的系統的查詢和搜尋的效能。
- **[設定可靠度及復原上 Azure Elasticsearch][]**描述，可協助您最小化的資料遺失和擴充的資料復原次數機會 Elasticsearch 叢集的一些重要功能。
- **[建立在 Azure Elasticsearch 效能測試環境][]**說明如何設定測試效能資料 ingestion 和查詢負載 Elasticsearch 叢集環境。 
- **[實作 JMeter 測試規劃 Elasticsearch][]**摘要列出實作使用 JMeter 測試方案 Java 程式碼為 JUnit 測試執行工作，例如將 Elasticsearch 叢集上載資料的整合與執行效能測試。
- **[部署 JMeter JUnit 樣本中的，以進行測試 Elasticsearch 效能][]**說明如何建立及使用 JUnit 樣本中可產生及上傳到 Elasticsearch 叢集的資料。 這會提供彈性載入測試方法可以產生大量測試資料而不根據外部資料檔案。 
- **[執行自動的 Elasticsearch 恢復測試][]**摘要如何執行使用這個系列的恢復測試。 
- **[執行自動的 Elasticsearch 效能測試][]**摘要如何執行使用這個系列的效能測試。


[Azure 上執行 Elasticsearch]: guidance-elasticsearch-running-on-azure.md
[調整資料 Ingestion 效能的 Elasticsearch Azure 上]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[建立在 Azure Elasticsearch 測試環境效能]: guidance-elasticsearch-creating-performance-testing-environment.md
[實作 Elasticsearch JMeter 測試方案]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[部署 JMeter JUnit 樣本中，以進行測試 Elasticsearch 效能]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[調整資料彙總和 Azure 上 Elasticsearch 的查詢效能]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[設定上 Azure Elasticsearch 可靠度及修復]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[執行自動化的 Elasticsearch 恢復測試]: guidance-elasticsearch-running-automated-resilience-tests.md
[執行自動化的 Elasticsearch 效能測試]: guidance-elasticsearch-running-automated-performance-tests.md
