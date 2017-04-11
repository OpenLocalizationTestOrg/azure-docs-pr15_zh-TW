<properties
    pageTitle="找不到我的 ASP.NET 5 專案 （Visual Studio 已連線服務） |Microsoft Azure 儲存體"
    description="描述 [連線至 Visual Studio ASP.NET 5 專案使用 Visual Studio 中 Azure 儲存體帳戶已連線服務後，會發生什麼情況"
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

# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>找不到我的 ASP.NET 5 專案 （Visual Studio Azure 儲存體連線 services）？

## <a name="references-added"></a>新增的參照

Azure 儲存體 NuGet 套件已新增至您的 Visual Studio 專案。  
此套件新增下列.NET 參考資料︰

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

此外，已新增 NuGet 套件**Microsoft.Framework.Configuration.Json** 。

## <a name="connection-string-for-azure-storage-added"></a>Azure 儲存空間新增的連線字串
在您的專案 config.json 檔案中，項目建立選取的儲存帳戶的連線字串和圖例。

如需詳細資訊，請參閱[ASP.NET 5](http://www.asp.net/vnext)。
