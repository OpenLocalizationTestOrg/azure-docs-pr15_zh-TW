<properties 
   pageTitle="設定和使用 Visual Studio 中的儲存空間模擬器 |Microsoft Azure"
   description="設定和使用 Visual Studio 中的儲存空間模擬器"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>設定和使用 Visual Studio 中的儲存空間模擬器

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>概觀
Azure SDK 的開發環境包含的儲存空間模擬器模擬 Blob 佇列中，與資料表儲存體可用的服務 Azure 中本機開發電腦上的公用程式。 如果您是建置雲端服務，採用 Azure 儲存服務，或撰寫通話儲存服務任何外部應用程式，您可以測試您的程式碼本機針對儲存模擬器。 Microsoft Visual Studio Azure 工具整合 Visual Studio 中的儲存空間模擬器管理。 Azure 工具初始化第一次使用儲存空間模擬器資料庫、 啟動儲存模擬器服務，當您執行或偵錯 Visual Studio 中，從程式碼，並提供唯讀儲存模擬器資料存取透過 Azure 儲存檔案總管。

如的詳細資訊的儲存空間模擬器，包括系統需求與自訂的設定指示，請參閱[使用 Azure 儲存模擬器開發和測試](./storage/storage-use-emulator.md)。

>[AZURE.NOTE] 有一些功能儲存模擬器模擬和 Azure 儲存服務之間的差異。 Azure SDK 的文件中的特定差異的詳細資訊，請參閱[儲存模擬器之間的差異和 Azure 儲存服務](./storage/storage-use-emulator.md)。

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>儲存模擬器設定連線字串

若要存取儲存模擬器從角色中的程式碼，您要設定的儲存空間模擬器與的稍後可以變更為指向 Azure 儲存體帳戶的連線字串。 連線字串是設定的設定可連線到儲存帳戶執行階段讀取您的角色。 如需有關如何建立連線字串的詳細資訊，請參閱[設定 Azure 應用程式](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage)。

>[AZURE.NOTE] 您可以使用**DevelopmentStorageAccount**屬性，以傳回程式碼中的儲存空間模擬器帳戶的參照。 如果您想要從您的程式碼，存取儲存模擬器，但如果您打算要發佈至 Azure 應用程式，您必須建立連線字串存取 Azure 儲存體帳戶及修改您的程式碼，若要使用的連線字串之前，請在發佈，此方法均正確運作。 如果您會經常切換儲存模擬器帳戶和 Azure 儲存體帳戶之間，連線字串會簡化此程序。

## <a name="initializing-and-running-the-storage-emulator"></a>初始化及執行的儲存空間模擬器

您可以指定，當您執行或偵錯 Visual Studio 中的服務時，Visual Studio 會自動啟動的儲存空間模擬器。 在方案總管中開啟**Azure**專案的快顯功能表，然後選擇 [**內容]**。 在 [**開發**] 索引標籤**啟動 Azure 儲存模擬器**] 清單中，選擇**，則為 True** （如果已未設定的值）。

第一次執行，或偵錯 Visual Studio 中，從服務的儲存空間模擬器啟動初始化處理程序。 此程序儲存模擬器保留本機的連接埠，並建立儲存模擬器資料庫。 完成之後，此程序不需要執行一次，除非儲存模擬器資料庫會被刪除。

>[AZURE.NOTE] 開始使用 Azure 工具 2012 年 6 月發行，儲存模擬器執行時，根據預設，在 SQL Express LocalDB。 儲存模擬器執行針對預設的執行個體的 SQL Express 2005 或 2008年的 Azure 工具先前的版本類似，您必須安裝之前，您可以安裝 Azure SDK。 您也可以執行防止具名的執行個體的 SQL Express 或具名或預設 Microsoft SQL Server 執行個體的儲存空間模擬器。 如果您需要設定儲存模擬器對非預設的執行個體執行個體執行，請參閱[使用 Azure 儲存模擬器開發和測試](./storage/storage-use-emulator.md)。

儲存模擬器提供檢視本機存放區服務的狀態和啟動，請停止，以及重設使用者介面。 已啟動儲存模擬器服務，您可以顯示的使用者介面或啟動或停止服務，以滑鼠右鍵按一下 [Microsoft Azure 模擬器 Windows 工作列中的 [通知區域圖示。

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>檢視伺服器總管] 中的儲存空間模擬器資料

伺服器總管] 中的 [Azure 儲存體] 節點可讓您檢視資料，並變更 blob 和資料表的資料，在您儲存的帳戶，包括儲存空間模擬器中的設定。 如需詳細資訊，請參閱[瀏覽和管理伺服器檔案總管的儲存空間資源](https://msdn.microsoft.com/library/azure/ff683677.aspx)。
