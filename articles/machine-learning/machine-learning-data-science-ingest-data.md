<properties 
    pageTitle="將資料載入分析的儲存空間環境 |Microsoft Azure" 
    description="移動資料 Azure Blob 儲存體" 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="bradsev" />

# <a name="load-data-into-storage-environments-for-analytics"></a>載入分析的儲存空間環境中的資料

小組資料科學程序需要 ingested 或載入至各種不同的儲存空間環境來處理的程序的每個階段中的最適當的方式分析資料。 常用的處理資料目的地包含 Azure Blob 儲存體，SQL Azure 資料庫，SQL Server Azure VM、 HDInsight (Hadoop)，以及 Azure 電腦學習。 

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

這個**功能表**會連結至主題說明如何將內嵌的資料到目標環境儲存及處理資料的位置。

技術和業務需求，以及初始的位置，格式設定，並將資料的大小會決定需要 ingested 達成目標的分析資料到目標環境。 您不需要幾個環境達成各種工作所需建構預測模型之間移動資料分析藍本很。 例如，這一系列的任務可以包含資料探索、 測試處理、 清理、 向下取樣和模型訓練課程。
