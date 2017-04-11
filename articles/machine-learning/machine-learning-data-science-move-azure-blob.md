<properties
    pageTitle="將資料移至並從 Azure Blob 儲存體 |Microsoft Azure"
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
    ms.date="09/14/2016"
    ms.author="bradsev;sachouks" />

# <a name="move-data-to-and-from-azure-blob-storage"></a>移動資料 Azure Blob 儲存體

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

用來將資料移到及/或從 Azure Blob 儲存體技術指南以下連結︰

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
哪種方法最適合您取決於您的狀況。 [進階分析中 Azure 電腦學習的案例](machine-learning-data-science-plan-sample-scenarios.md)文章可協助您判斷各種不同的資料科學工作流程中的進階的分析程序使用，您需要的資源。

> [AZURE.NOTE] Azure blob 儲存體完成簡介，請參閱[Azure Blob 的基本概念](../storage/storage-dotnet-how-to-use-blobs.md)，並[Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。

或者，您可以使用[Azure 資料工廠](https://azure.microsoft.com/services/data-factory/)至︰ 

- 建立及排程從 Azure blob 儲存體，下載資料的管線 
- 為了已發佈的 Azure 電腦學習 web 服務 
- 接收的預測狀況分析結果，和 
- 若要儲存空間上, 傳結果。 

如需詳細資訊，請參閱[建立預測管線 Azure 資料工廠和 Azure 電腦學習使用](../data-factory/data-factory-azure-ml-batch-execution-activity.md)。

## <a name="prerequisites"></a>必要條件

這份文件，假設您有 Azure 訂閱儲存的帳戶，與該帳戶的對應的儲存空間鍵。 上傳/下載前的資料，您必須知道您 Azure 儲存體帳戶名稱和帳戶金鑰]。

- 若要設定的 Azure 訂閱，請參閱[一個月免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。
- 如需建立儲存帳戶的指示及取得帳戶和重要資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。
