<properties 
    pageTitle="資料工廠資料整合服務簡介 |Microsoft Azure" 
    description="了解 Azure 資料工廠︰ 雲端資料整合服務協調，會自動執行動作和轉換的資料。" 
    keywords="資料整合，雲端資料整合，什麼是 azure 資料工廠"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/22/2016" 
    ms.author="shlo"/>

# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Azure 資料工廠服務，在雲端的資料整合服務的簡介

## <a name="what-is-azure-data-factory"></a>什麼是 Azure 資料工廠？ 
資料工廠是以雲端為基礎的資料整合服務，協調和自動化**移動**和**轉換**的資料。 您可以建立資料整合使用資料工廠服務的解決方案可以內嵌來自各種不同的資料存放區的資料、 轉換處理資料，並將其結果資料至資料存放區。 

資料工廠服務可讓您建立資料的管線，移動及轉換資料，並執行管線指定的排程 （每小時每天、 每週等）。 同時也會提供顯示歷程和資料管線，之間的相依性，並監控輕鬆找出問題並監控提醒的設定單一整合檢視您所有的資料管線的豐富視覺效果。

![圖表︰ 資料工廠概觀、 資料整合服務](./media/data-factory-introduction/what-is-azure-data-factory.png)
**圖 1。** 內嵌來自各種資料來源的資料、 準備、 轉換與分析資料，以及然後發佈消耗準備要使用資料。

## <a name="pipelines-and-activities"></a>管線與活動
在資料工廠解決方案中，您可以建立一或多個資料**管線**。 管線是邏輯群組的活動。 它們用來將單位共同執行工作的群組活動。 

**活動**定義在您的資料上執行的動作。 例如，您可能會使用複製活動，將資料複製到另一個資料存放區的一個資料儲存區。 同樣地，您可以使用 Azure HDInsight 叢集轉換，或是分析您的資料執行登錄區查詢的登錄區活動。 資料工廠支援兩種類型的活動︰ 資料移動活動 」 和 「 資料轉換的活動。 
  
## <a name="data-movement-activities"></a>資料移動活動 
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

如需詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文件。 

## <a name="data-transformation-activities"></a>資料轉換活動
[AZURE.INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

如需詳細資訊，請參閱[資料轉換活動](data-factory-data-transformation-activities.md)文件。

如果您要移動的資料的資料存放區複製活動不支援，或使用您自己的邏輯轉換的資料，建立**自訂的.NET 活動**。 建立及使用自訂活動的詳細資訊，請參閱[使用自訂活動 Azure 資料工廠管道](data-factory-use-custom-activities.md)。

## <a name="linked-services"></a>連結的服務
連結的服務定義資料工廠連線到外部資源所需的資訊 (範例︰ Azure 儲存體，內部部署 SQL Server，Azure HDInsight)。 連結的服務適用於資料工廠兩種用途︰

- 若要代表**資料存放區**包括但不是限於內部部署的 SQL Server，Oracle 資料庫檔案共用] 或 Azure Blob 儲存體帳戶。 請參閱支援的資料存放區的 [[資料移動活動](data-factory-data-movement-activities.md)] 區段的清單。 
- 若要代表**計算資源**可裝載執行的活動。 例如，HDInsightHive 活動 HDInsight Hadoop 叢集上執行。 請參閱[資料轉換活動](data-factory-data-transformation-activities.md)區段清單支援計算環境。 

## <a name="datasets"></a>資料集 
連結的服務會連結至 Azure 資料工廠的資料存放區。 資料集代表與資料儲存區中的資料結構。 例如，連結的 Azure 儲存體服務會提供資料工廠連線至 Azure 儲存體帳戶的連線資訊。 Azure Blob 資料集指定 blob 容器和資料夾中的管線應該要讀取資料 Azure Blob 儲存體。 同樣地，連結的 Azure SQL 服務提供 Azure SQL 資料庫連線資訊，並不 Azure SQL 資料集，指定包含資料的資料表。   

## <a name="relationship-between-data-factory-entities"></a>資料工廠實體之間的關聯
資料工廠有幾個重要實體共同作業的定義的輸入與輸出資料，以處理事件，以及排程和資源所需執行的所需的資料流。

![圖表︰ 資料工廠雲端資料整合服務-重要概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**圖 2。** 資料集、 活動、 管線與連結的服務之間的關聯

以連結的服務、 資料集、 活動及管線的四個簡單的概念，您準備好開始使用 ！ 您可以[建立您的第一個管線](data-factory-build-your-first-pipeline.md)。 

## <a name="supported-regions"></a>支援的區域
目前，您可以建立資料工廠，在 [**美國西**、**東亞美國**及**北美歐洲**區域]。 不過，資料工廠可以存取資料存放區，並計算資料存放區之間移動資料其他 Azure 區域的服務或使用的程序資料計算服務。 

Azure 資料工廠本身不會儲存任何資料。 讓您建立的資料導向流程協調資料[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores)」 和 「 使用[計算服務](data-factory-compute-linked-services.md)的其他區域或內部部署環境中的資料處理之間移動。 您也可以[監視及管理工作流程](data-factory-monitor-manage-pipelines.md)使用同時以程式設計方式和 UI 機制。 

即使 Azure 資料工廠適用於**美國西**、**東亞美國**和**北美歐洲**地區，是使用[全域](data-factory-data-movement-activities.md#global)在多個區域內電源資料工廠中的資料移動的服務。 以避免資料存放區位於後面的防火牆然後[資料管理閘道](data-factory-move-data-between-onprem-and-cloud.md)安裝於內部部署環境會改為移動資料。 

例如，讓我們假設您計算環境，例如 Azure HDInsight 叢集和 Azure 電腦學習完西歐區域。 您可以建立及使用 Azure 資料工廠執行個體的北美，而且用來計算環境中西歐上的工作排程。 花幾個毫秒資料工廠觸發計算環境上的工作，但不是會變更您的電腦環境上執行工作的時間。

我們想要在未來支援 Azure 每個地理位置中有 Azure 資料工廠。
  
## <a name="next-steps"></a>後續步驟
若要瞭解如何建立資料的管線與資料工廠，請遵循下列教學課程中的逐步指示。 

教學課程 | 描述
-------- | -----------
[建立程序使用 Hadoop 叢集資料的資料管線](data-factory-build-your-first-pipeline.md) | 在本教學課程中，您建立資料管線與您第一份 Azure 資料工廠該**程序資料**Azure HDInsight (Hadoop) 叢集上執行登錄區指令碼。 |
[建立兩個雲端資料儲存區之間移動資料的資料管線](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | 在本教學課程中，您建立的資料工廠管線與**移動資料**Blob 儲存體到 SQL 資料庫。
[建立資料管線的內部部署資料存放區並使用資料管理閘道器的雲端資料儲存區之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) | 在本教學課程中，您建立的管線資料工廠該**移動資料**從**內部部署**的 SQL Server 資料庫至 Azure blob。 逐步解說的一部分，您可以安裝並設定您的電腦上的資料管理閘道器。 
