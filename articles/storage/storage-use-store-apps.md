<properties
    pageTitle="Windows 市集應用程式中使用 Azure 儲存 |Microsoft Azure"
    description="瞭解如何建立使用 Azure Blob、 佇列、 表格或檔案的儲存空間的 Windows 市集應用程式。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>
    
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>如何使用 Windows 市集應用程式中的 Azure 儲存體

## <a name="overview"></a>概觀

本指南示範如何快速入門開發會使用 Azure 儲存體的 Windows 市集應用程式。

## <a name="download-required-tools"></a>下載的必要的工具

- [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)可讓您輕鬆建立、 偵錯、 當地語系化，套件，並部署 Windows 市集應用程式。 需要 visual Studio 2012 或更新版本。
- [Azure 儲存用戶端文件庫](https://www.nuget.org/packages/WindowsAzure.Storage)提供 Windows 執行階段類別文件庫使用 Azure 儲存體。
- [WCF 資料服務工具的 Windows 市集應用程式](http://www.microsoft.com/download/details.aspx?id=30714)新增的服務參考將體驗延伸的用戶端 OData 支援 for Visual Studio 中 Windows 市集應用程式。

## <a name="develop-apps"></a>開發具備應用程式

### <a name="getting-ready"></a>準備好

建立新的 Windows 市集應用程式專案在 Visual Studio 2012 或更新版本︰

![儲存應用程式專案間的儲存空間-與-][store-apps-storage-vs-project]

接下來，新增 Azure 儲存空間的用戶端程式庫的以滑鼠右鍵按一下 [**參考資料**，按一下 [**新增參照**，然後瀏覽至儲存用戶端文件庫的 Windows 執行階段下載︰

![市集的應用程式-儲存空間-選擇-文件庫][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>Blob 和佇列服務中使用文件庫

此時，您的應用程式已可呼叫 Azure Blob 和佇列服務。 新增下列**使用**陳述式，以便可以直接參照 Azure 儲存體類型︰

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

接下來，新增按鈕至您的頁面。 下列程式碼新增至其**Click**事件及修改您的事件處理常式方法使用[非同步關鍵字](http://msdn.microsoft.com/library/vstudio/hh156513.aspx)︰

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

將此程式碼，假設您有兩個字串變數、 *accountName*和*accountKey*。 代表您儲存的帳戶和帳戶金鑰使用該帳戶相關聯的名稱。

建立並執行應用程式。 按一下 [] 按鈕，將核取名為*container1*容器是否在您的帳戶，然後再建立如果不。

### <a name="using-the-library-with-the-table-service"></a>使用表格服務中的文件庫

用來與 Azure 資料表服務通訊的類型而定 WCF Data Services 的 Windows 市集應用程式的文件庫。 使用封裝管理員主控台，接下來，新增所需的 WCF 文件庫的參照︰

![儲存應用程式的儲存空間-套件-管理員][store-apps-storage-package-manager]

使用下列命令以點封裝管理員，您的電腦上的位置︰

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

這個命令會自動新增至您的專案的所有必要的參考。 如果您不想要使用封裝管理員主控台，您就可以將您的本機電腦上的 WCF 資料服務 NuGet 資料夾新增至套件來源的清單，然後再新增透過 UI，參照，[使用] 對話方塊管理 NuGet 封包](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog)所述。

當您參考 WCF 資料服務 NuGet 套件時，變更 [中] 按鈕的 [**按一下**] 事件的程式碼︰

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

將此程式碼檢查是否*table1*的資料表存在於中您的帳戶，然後將之建立是否不是。

您也可以新增的參考 Microsoft.WindowsAzure.Storage.Table.dll，這是您所下載的相同套件中可用。 此文件庫包含其他的功能，例如反射型序列化和一般的查詢。 請注意，此文件庫不支援 JavaScript。



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
