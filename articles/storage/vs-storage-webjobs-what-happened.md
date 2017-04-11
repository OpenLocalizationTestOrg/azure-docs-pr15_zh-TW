<properties
    pageTitle="找不到我的 WebJob 專案 （Visual Studio Azure 儲存體已連線服務）？ |Microsoft Azure"
    description="將有何改變 Azure WebJob 專案中連線到儲存帳戶使用 Visual Studio 連接服務之後的說明"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>找不到我的 WebJob 專案 （Visual Studio Azure 儲存體已連線服務）？

## <a name="references-added"></a>新增的參照

Azure 儲存體 NuGet 套件已加入，或在 Visual Studio 專案中的更新。  
此套件新增下列.NET 參考資料︰

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Azure 儲存空間新增的連線字串
在您的專案 App.config 檔案， **AzureWebJobsStorage**和**AzureWebJobsDashboard**項目已更新選取的儲存帳戶的連線字串與索引鍵。

如需詳細資訊，請參閱[Azure WebJobs 文件的資源](http://go.microsoft.com/fwlink/?linkid=390226)。
