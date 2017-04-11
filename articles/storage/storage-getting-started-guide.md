<properties
    pageTitle="快速入門 Azure 儲存體 5 分鐘內 |Microsoft Azure"
    description="快速增加上 Microsoft Azure Blob、 表格和佇列使用 Azure 儲存體快速啟動、 Visual Studio 中和 Azure 儲存模擬器。 執行 5 分鐘內第一個 Azure 儲存體應用程式。"
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="get-started-with-azure-storage-in-five-minutes"></a>快速入門 5 分鐘內的 Azure 儲存體

## <a name="overview"></a>概觀

您可以輕鬆開始開發與 Azure 儲存體。 本教學課程教您如何取得 Azure 儲存體應用程式設定，快速地執行。 您可以使用隨附於 Azure SDK for.NET 快速入門的範本。 這些快速入門包含示範 Azure 儲存體的一些基本程式設計案例就緒-執行程式碼。

若要進一步瞭解 Azure 儲存體在探究程式碼之前，請參閱[後續步驟](#next-steps)。

## <a name="prerequisites"></a>必要條件

在您開始之前，您可能需要下列先決條件︰

1. 編譯和建置應用程式，您必須在電腦上安裝的[Visual Studio](https://www.visualstudio.com/)的版本。

2. 安裝最新版[Azure SDK.net](https://azure.microsoft.com/downloads/)。 SDK 包含 Azure 快速入門範例專案、 Azure 儲存模擬器和[Azure 儲存用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

3. 請確定您已安裝在您的電腦上的[.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653)為我們將在本教學課程中使用 Azure 快速入門範例專案所需。

    如果您不確定您的電腦中已安裝的是哪個版本的.NET Framework，請參閱[如何︰ 決定哪些.NET Framework 已安裝版本](https://msdn.microsoft.com/vstudio/hh925568.aspx)。 或者，您也可以按下**[開始**] 按鈕或 [Windows 鍵，輸入**[控制台]**。 然後按一下 [**程式** > **程式和功能**，並判斷是否已安裝的程式之間列.NET Framework 4.5。

4. 您需要的 Azure 的訂閱和 Azure 儲存體帳戶。

    - 若要取得 Azure 訂閱，請參閱[免費試用](https://azure.microsoft.com/pricing/free-trial/)、[購買選項](https://azure.microsoft.com/pricing/purchase-options/)，以及[成員提供](https://azure.microsoft.com/pricing/member-offers/)（MSDN、 Microsoft 合作夥伴網路，BizSpark 及其他 Microsoft 程式的成員）。
    - 若要建立 Azure 中的儲存空間帳戶，請參閱[如何建立儲存的帳戶](storage-create-storage-account.md#create-a-storage-account)。

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Azure 儲存在雲端上執行應用程式的第一個 Azure 儲存體

一旦您擁有的帳戶，您可以建立簡單的 Azure 儲存體應用程式，使用 Visual Studio 中的其中一個 Azure 快速啟動範例專案。 本教學課程 Azure 儲存體解釋範例專案︰ **Azure 儲存體︰ Blob**， **Azure 儲存體︰ 檔案**， **Azure 儲存體︰ 佇列**，及**Azure 儲存體︰ 表格**:

1. 啟動 [Visual Studio。
2. 從 [**檔案**] 功能表中，按一下 [**新專案**]。
3. 在 [**新專案**] 對話方塊中，按一下 [**已安裝** > **範本** > **Visual C#** > **雲端** > **快速入門** > **資料服務**。
    。 選擇其中一個下列範本︰ **Azure 儲存體︰ Blob**， **Azure 儲存體︰ 檔案**， **Azure 儲存體︰ 佇列**，或**Azure 儲存體︰ 表格**。
    b。 請確定已選取為目標架構的**.NET Framework 4.5** 。
    - 3.c。 指定專案的名稱，然後建立新的 Visual Studio 方案，如下圖所示︰

    ![Azure 快速入門][Image1]

若要執行應用程式之前，先檢閱的程式碼。 若要檢閱的程式碼，選取 [在 Visual Studio 中的 [**檢視**] 功能表上的 [**方案總管**]。 接著，按兩下 Program.cs 檔案。

接下來，請執行範例應用程式︰

1.  在 Visual Studio 中，選取 [**檢視**] 功能表上的 [**方案總管**]。 Azure 儲存模擬器中開啟的 App.config 檔案及註解的連線字串︰

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  取消連接字串 Azure 儲存服務的註解，並提供儲存體帳戶名稱和存取識別碼 App.config 檔案中︰`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    若要擷取您儲存的帳戶存取金鑰，請參閱[管理您的儲存空間便捷鍵](storage-create-storage-account.md#manage-your-storage-access-keys)。

3.  在 [提供儲存體帳戶名稱和後便捷鍵 App.config 檔案中，按一下 [**檔案**] 功能表中的按一下 [**全部儲存**儲存所有專案檔案。
4.  按一下 [**建立**] 功能表上的 [**建立的解決方案**。
5.  **偵錯**] 功能表中，請按**F11**執行解決方案逐步解說，或按**F5**執行解決方案。


## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>針對 Azure 儲存模擬器本機執行您的第一個 Azure 儲存體應用程式

[Azure 儲存模擬器](storage-use-emulator.md)提供資料列傳回 Azure Blob 與佇列中，表格服務用於開發的本機環境。 您可以使用儲存空間模擬器本機，測試儲存應用程式，但不建立 Azure 訂閱或儲存帳戶並不需產生任何成本。

若要試試看，現在就讓我們建立簡單的 Azure 儲存體應用程式，使用 Visual Studio 中的其中一個 Azure 快速啟動範例專案。 本教學課程著重於**Azure Blob 儲存體**、 **Azure 資料表儲存體**] 和**Azure 佇列中儲存**的範例專案︰

1. 啟動 [Visual Studio。
2. 從 [**檔案**] 功能表中，按一下 [**新專案**]。
3. 在 [**新專案**] 對話方塊中，按一下 [**已安裝** > **範本** > **Visual C#** > **雲端** > **快速入門** > **資料服務**。
    。 選擇其中一個下列範本︰ **Azure 儲存體︰ Blob**， **Azure 儲存體︰ 檔案**， **Azure 儲存體︰ 佇列**，或**Azure 儲存體︰ 表格**。
    b。 請確定已選取為目標架構的**.NET Framework 4.5** 。
    c。 指定專案的名稱，然後建立新的 Visual Studio 方案，如下圖所示︰

    ![Azure 快速入門][Image1]

4.  在 Visual Studio 中，選取 [**檢視**] 功能表上的 [**方案總管**]。 如果您已經加入一個，請開啟 [App.config 檔案和註解 Azure 儲存體帳戶之連線字串]。 然後取消連接字串註解 Azure 儲存模擬器︰

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

若要執行應用程式之前，先檢閱的程式碼。 若要檢閱的程式碼，選取 [在 Visual Studio 中的 [**檢視**] 功能表上的 [**方案總管**]。 接著，按兩下 Program.cs 檔案。

接下來，Azure 儲存模擬器中執行的範例應用程式︰

1.  按下**[開始**] 按鈕或 [Windows 鍵， *Microsoft Azure 儲存體模擬器*，搜尋並啟動應用程式。 模擬器啟動時，您會看到圖示及 [Windows 任務檢視] 區域中的通知。
2.  在 Visual Studio 中，按一下 [**建置**] 功能表上的 [**建立方案**]。
3.  **偵錯**] 功能表中，請按**F11**執行方案逐步解說，或按**F5**執行解決方案的完整流程。

## <a name="next-steps"></a>後續步驟

請參閱下列資源，若要進一步瞭解 Azure 儲存體︰

* [Microsoft Azure 儲存體簡介](storage-introduction.md)
* [開始使用 Azure 儲存檔案總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [使用.NET Azure Blob 儲存體快速入門](storage-dotnet-how-to-use-blobs.md)
* [使用.NET Azure 資料表儲存體快速入門](storage-dotnet-how-to-use-tables.md)
* [使用.NET Azure 佇列中儲存快速入門](storage-dotnet-how-to-use-queues.md)
* [開始使用 windows Azure 檔案儲存空間](storage-dotnet-how-to-use-files.md)
* [傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)
* [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)
* [Microsoft Azure 儲存用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Azure 儲存服務 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
