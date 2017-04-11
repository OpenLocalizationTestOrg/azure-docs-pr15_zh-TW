<properties
    pageTitle="ASP.NET 專案有何改變？ |Microsoft Azure |Visual Studio 連線服務"
    description="說明新增 ASP.NET 專案使用 Visual Studio Azure 儲存體連接服務之後，會發生什麼情況"
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

# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>找不到我的 ASP.NET 專案 （Visual Studio Azure 儲存體已連線服務）？

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

##<a name="connection-string-for-azure-storage-added"></a>Azure 儲存空間新增的連線字串
在您的專案 web.config 檔案中，項目建立選取的儲存帳戶的連線字串和圖例。

如需詳細資訊，請參閱[ASP.NET](http://www.asp.net)。
