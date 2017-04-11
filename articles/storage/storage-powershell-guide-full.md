<properties
    pageTitle="Azure PowerShell 使用 Azure 儲存 |Microsoft Azure"
    description="瞭解如何使用適用於 Azure 儲存體 Azure PowerShell 指令程式來建立和管理儲存空間的帳戶。使用二進位大型物件、 表格、 佇列和檔案。設定查詢儲存分析和建立共用的 access 簽章。"
    services="storage"
    documentationCenter="na"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="using-azure-powershell-with-azure-storage"></a>Azure PowerShell 使用 Azure 儲存體

## <a name="overview"></a>概觀

Azure PowerShell 是一個模組，可提供 cmdlet 來管理透過 Windows PowerShell 的 Azure。 它位於工作為基礎的命令列殼層指令碼語言專門用來系統管理。 使用 PowerShell，您可以輕鬆地控制，並自動管理您的 Azure 服務及應用程式。 例如，您可以使用 cmdlet 來執行相同的工作，您可以透過[Azure 入口網站](https://portal.azure.com)執行。

本指南中，我們會探索︰ 使用[Azure 儲存體 Cmdlet](https://msdn.microsoft.com/library/azure/mt269418.aspx)來執行各種不同的 Azure 儲存體開發及管理工作。

本指南假設您已使用[Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/)和[Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx)的前一個體驗。 快速入門提供指令碼，示範使用 PowerShell 的 Azure 儲存體。 您應該更新根據您的設定，才能執行每個指令碼的指令碼變數。

本指南中的第一個區段提供 Azure 儲存體和 PowerShell 快速檢視。 如需詳細的資訊與指示，開始[使用 Azure PowerShell 的 Azure 儲存體的必要條件](#prerequisites-for-using-azure-powershell-with-azure-storage)。


## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>快速入門 5 分鐘 PowerShell 與 Azure 儲存體

本節說明如何在 5 分鐘存取透過 PowerShell 的 Azure 儲存體。

**Azure 的新︰**取得 Microsoft Azure 訂閱，與該訂閱相關聯的 Microsoft 帳戶。 Azure 購買選項的相關資訊，請參閱[免費試用](https://azure.microsoft.com/pricing/free-trial/)、[購買選項](https://azure.microsoft.com/pricing/purchase-options/)，以及[成員提供](https://azure.microsoft.com/pricing/member-offers/)（MSDN、 Microsoft 合作夥伴網路，BizSpark 及其他 Microsoft 程式的成員）。

Azure 訂閱的相關資訊，請參閱[指派管理員角色中 Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) 。

**建立 Microsoft Azure 訂閱與帳戶︰ 之後**

1.  下載並安裝[PowerShell 的 Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409)。
2.  啟動 Windows PowerShell 整合式指令碼環境 （ise [以系統）︰ 在您的本機電腦，移至**[開始**] 功能表。 輸入**系統管理工具**] 和 [按一下以執行。 在 [**系統管理工具**] 視窗中，以滑鼠右鍵按一下**Windows PowerShell ise [以系統**，按一下 [**以系統管理員身分執行**]。
3.  在**Windows PowerShell ise [以系統**，按一下 [**檔案** > **新增**]，建立新的指令碼檔。
4.  現在，我們會告訴您一個簡單的指令碼顯示基本的 PowerShell 命令，以存取 Azure 儲存體。 指令碼首先會要求您 Azure 帳戶認證，以新增您 Azure 帳戶本機 PowerShell 環境。 然後，指令碼會設定預設 Azure 訂閱並 Azure 中建立新的儲存空間帳戶。 接下來，指令碼會在此新的儲存空間帳戶中建立新的容器，並將現有的圖像檔案 (blob) 上傳至該容器。 指令碼會列出該容器中的所有 blob 之後，它會在您的本機電腦上建立新目的地目錄，並下載圖像檔案。
5.  在下列程式碼區段中，選取 [註解之間的指令碼**#begin**和**#end**。 按 CTRL + C 複製到剪貼簿。

        #begin
        # Update with the name of your subscription.
        $SubscriptionName = "YourSubscriptionName"

        # Give a name to your new storage account. It must be lowercase!
        $StorageAccountName = "yourstorageaccountname"

        # Choose "West US" as an example.
        $Location = "West US"

        # Give a name to your new container.
        $ContainerName = "imagecontainer"

        # Have an image file and a source directory in your local computer.
        $ImageToUpload = "C:\Images\HelloWorld.png"

        # A destination directory in your local computer.
        $DestinationFolder = "C:\DownloadImages"

        # Add your Azure account to the local PowerShell environment.
        Add-AzureAccount

        # Set a default Azure subscription.
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

        # Create a new storage account.
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

        # Set a default storage account.
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

        # Create a new container.
        New-AzureStorageContainer -Name $ContainerName -Permission Off

        # Upload a blob into a container.
        Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

        # List all blobs in a container.
        Get-AzureStorageBlob -Container $ContainerName

        # Download blobs from the container:
        # Get a reference to a list of all blobs in a container.
        $blobs = Get-AzureStorageBlob -Container $ContainerName

        # Create the destination directory.
        New-Item -Path $DestinationFolder -ItemType Directory -Force  

        # Download blobs into the local destination directory.
        $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
        #end

5.  在**Windows PowerShell ise [以系統**，按下 CTRL + V 以複製指令碼。 按一下 [**檔案** > **儲存**。 在 [**另存新檔**] 對話方塊視窗中，輸入名稱的指令碼檔，例如 「 mystoragescript 」。 按一下 [**儲存**]。

6.  現在，您需要更新指令碼變數根據您設定的設定。 您必須與您的訂閱更新**$SubscriptionName**變數。 您可以將指令碼中所指定的其他變數或隨意進行更新。

    - **$SubscriptionName:**您必須更新此變數與您的訂閱名稱。 執行下列其中一個，找出您的訂閱名稱的下列三種方式︰

        。 在**Windows PowerShell ise [以系統**，按一下 [**檔案** > **新增**]，建立新的指令碼檔。 將下列指令碼複製到新的指令碼檔，然後按一下 [**偵錯** > **執行**。 下列指令碼首先會要求您 Azure 帳戶認證，以新增您的 Azure 帳戶本機 PowerShell 環境，然後再顯示 [已連線到本機的 PowerShell 工作階段的所有訂閱。 記下您想要遵循此教學課程時使用的訂閱名稱︰

            Add-AzureAccount
                Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

        b。 若要找出並複製您的訂閱名稱，在[Azure 入口網站](https://portal.azure.com)，在中心左側功能表上，按一下 [**訂閱**]。 複製您想要執行本指南中的指令碼時使用的訂閱名稱。

        ![Azure 入口網站][Image2]

        c。 若要找出並複製您的訂閱名稱[Azure 傳統入口網站](https://manage.windowsazure.com/)中，向下捲動，然後按一下 [**設定**]，在入口網站的左側。 按一下 [**訂閱**]，以查看您的訂閱的清單。 複製您想要執行本指南中的指定的指令碼時使用的訂閱名稱。

        ![Azure 傳統入口網站][Image1]

    - **$StorageAccountName:**在 [指令碼中使用指定的名稱，或輸入您儲存的帳戶的新名稱。 **重要︰**儲存體帳戶名稱必須是唯一 Azure 中。 它必須小寫太 ！

    - **$Location:**在 [指令碼中使用指定的 「 西美國 」 或選擇其他 Azure 的位置，例如東亞美國，北美歐洲等等。

    - **$ContainerName:**在 [指令碼中使用指定的名稱，或輸入您的容器的新名稱。

    - **$ImageToUpload:**輸入的路徑圖片本機電腦，例如: 「 C:\Images\HelloWorld.png 」。

    - **$DestinationFolder:**輸入路徑至本機目錄來儲存檔案下載從 Azure 儲存空間，例如: 「 C:\DownloadImages 」。

7.  在更新之後指令碼變數 「 mystoragescript.ps1 」 檔案中的，按一下 [**檔案** > **儲存**。 然後按一下 [**偵錯** > **執行**]，或按**F5**執行指令碼。  

執行指令碼之後，您應該包含下載的影像檔案的本機的目的地資料夾。 以下螢幕擷取畫面顯示輸出範例︰

![下載二進位大型物件][Image3]


> [AZURE.NOTE] 「 快速入門 Azure 儲存與 PowerShell 5 分鐘 」 一節會提供如何使用 PowerShell 的 Azure 與 Azure 儲存體簡單。 如需詳細的資訊與指示，建議您閱讀下列各節。

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>使用 PowerShell 的 Azure 與 Azure 儲存體的先決條件
您需要 Azure 的訂閱，如上述執行本指南中提供的 PowerShell 指令程式的帳戶。

Azure PowerShell 是一個模組，可提供 cmdlet 來管理透過 Windows PowerShell 的 Azure。 安裝和設定 PowerShell 的 Azure 詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 我們建議您下載及安裝或使用本指南之前升級至最新的 PowerShell 的 Azure 模組。

在標準的 Windows PowerShell 主控台或 Windows PowerShell 整合式指令碼環境 （ise [以系統） 中，您可以執行指令程式。 例如，若要開啟**Windows PowerShell ise [以系統**，移至 [開始] 功能表、 輸入系統管理工具] 然後按一下以執行。 在 [系統管理工具] 視窗中，以滑鼠右鍵按一下 Windows PowerShell ise [以系統，按一下 [以系統管理員身分執行]。

## <a name="how-to-manage-storage-accounts-in-azure"></a>如何管理 Azure 儲存體帳戶

### <a name="how-to-set-a-default-azure-subscription"></a>如何設定預設 Azure 訂閱
若要管理使用 Azure PowerShell 的 Azure 儲存空間，您需要驗證您的用戶端環境與 Azure 透過 Azure Active Directory 驗證或憑證驗證。 如詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)教學課程。 本指南使用 Azure Active Directory 驗證。

1.  在 Windows PowerShell ise [以系統，輸入下列命令以新增您 Azure 帳戶本機 PowerShell 環境︰

    `Add-AzureAccount`

2.  在 」 登入至 Microsoft Azure 」 視窗中，輸入電子郵件地址和密碼與您的帳戶相關聯。 Azure 送給儲存的認證資訊，並關閉視窗。

3.  接下來，請執行下列命令以檢視您的本機 PowerShell 環境的 Azure 帳戶，並確認您的帳戶列︰

    `Get-AzureAccount`

4.  然後，執行下列 cmdlet，若要檢視所有訂閱連接至本機的 PowerShell 工作階段，並確認已列出您的訂閱︰

    `Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.  若要設定預設 Azure 訂閱，請執行選取 AzureSubscription 指令程式︰

        $SubscriptionName = 'Your subscription Name'
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.  確認執行取得 AzureSubscription 指令程式的預設訂閱的名稱︰

    `Get-AzureSubscription -Default`

7.  若要查看所有可用的 PowerShell cmdlet Azure 儲存空間，請執行︰

    `Get-Command -Module Azure -Noun *Storage*`

### <a name="how-to-create-a-new-azure-storage-account"></a>如何建立新的 Azure 儲存體帳戶
若要使用 Azure 儲存空間，您必須儲存帳戶。 設定您的電腦連線到您的訂閱之後，您可以建立新的 Azure 儲存體帳戶。

1.  執行找到所有可用的資料中心位置取得 AzureLocation cmdlet:

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.  接下來，請執行新增 AzureStorageAccount 指令程式來建立新的儲存空間帳戶。 下列範例會建立新的儲存空間帳戶 」 西美國 」 資料中心。

        $location = "West US"
        $StorageAccountName = "yourstorageaccount"
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] 儲存帳戶的名稱 Azure 中必須是唯一的而且必須小寫。 命名慣例及限制，請參閱[關於 Azure 儲存體帳戶](storage-create-storage-account.md)及[命名及參照容器、 二進位大型物件，以及中繼資料](http://msdn.microsoft.com/library/azure/dd135715.aspx)。

### <a name="how-to-set-a-default-azure-storage-account"></a>如何設定預設 Azure 儲存體帳戶
您可以在您的訂閱中有多個儲存的帳戶。 您可以選擇其中一個圖形，並將其設為預設儲存帳戶的所有儲存命令中相同的 PowerShell 工作階段。 這可讓您執行的 PowerShell 的 Azure 儲存體命令不明確指定的儲存空間的內容。

1.  若要設定預設儲存空間的訂閱的帳戶，您可以執行設定 AzureSubscription 指令程式。

        $SubscriptionName = "Your subscription name"
        $StorageAccountName = "yourstorageaccount"  
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.  接下來，請執行取得 AzureSubscription cmdlet 來確認儲存帳戶是與您的預設訂閱帳戶相關聯。 這個命令傳回目前的訂閱，包括其目前的儲存空間帳戶的訂閱內容。

        Get-AzureSubscription –Current

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>如何在訂閱中的所有 Azure 儲存體帳戶] 清單
每個 Azure 的訂閱可能為 100 儲存帳戶。 限制在最新的資訊，請參閱[Azure 訂閱及服務限制，配額和限制式](../azure-subscription-service-limits.md)。

執行下列 cmdlet，找出 [名稱] 和 [目前的訂閱中的儲存空間帳戶的狀態︰

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="how-to-create-an-azure-storage-context"></a>如何建立 Azure 儲存的內容
Azure 儲存的內容是在 PowerShell 封裝儲存認證中的物件。 執行任何後續指令程式可讓您驗證您的要求，但未明確指定儲存帳戶和其便捷鍵時，請使用儲存的內容。 您可以在許多方面，使用儲存體帳戶名稱和存取鍵、 共用的 access 簽章 (SA) 權杖連線字串，例如建立儲存內容或匿名。 如需詳細資訊，請參閱[新增 AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx)。  

使用下列三種方式，來建立儲存內容的其中一個︰

- 執行[取得 AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) cmdlet，找出您的 Azure 儲存體帳戶的主要儲存便捷鍵。 接下來，呼叫[新增 AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) cmdlet 來建立儲存內容︰

        $StorageAccountName = "yourstorageaccount"
        $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
        $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- 產生 Azure 儲存容器的共用的存取簽章權杖，並使用它來建立的儲存空間內容︰

        $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
        $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

    如需詳細資訊，請參閱[新增 AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx)和[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)。

- 在某些情況下，您可能會想要指定服務端點，當您建立新的儲存空間內容。 當您已使用 Blob 服務登錄將自訂網域名稱用於您儲存的帳戶或您想要使用共用的 access 簽章，以存取儲存資源時，這可能是必要。 在 [連線字串中設定服務端點並使用它來建立新的儲存空間內容，如下所示︰

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
        $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

如需有關如何設定儲存連線字串的詳細資訊，請參閱[設定連線字串](storage-configure-connection-string.md)。

現在，您有設定您的電腦，並瞭解如何管理訂閱並使用 PowerShell 的 Azure 儲存體帳戶，請移至下一節，瞭解如何管理 Azure blob 及 blob 的快照。

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>如何擷取及重新產生金鑰 Azure 儲存體

Azure 儲存體帳戶隨附兩個帳戶金鑰。 您可以使用下列 cmdlet，以擷取索引鍵。

    Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

您可以使用下列 cmdlet 以擷取特定的金鑰。 有效的值是主要及次要。  

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

如果您想要重新產生金鑰，請使用下列指令程式。 -KeyType 有效值為 「 主要 」 和 「 次要 」

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## <a name="how-to-manage-azure-blobs"></a>如何管理 Azure blob
Azure Blob 儲存體] 是儲存大量的非結構化資料，例如文字或二進位資料，可透過 HTTP 或 HTTPS 世界從任何位置存取服務。 本節假設您已熟悉 Azure Blob 儲存體服務概念。 詳細資訊，請參閱[開始使用 Blob 儲存體使用.NET](storage-dotnet-how-to-use-blobs.md)與[Blob 服務的概念](http://msdn.microsoft.com/library/azure/dd179376.aspx)。

### <a name="how-to-create-a-container"></a>如何建立容器
Azure 儲存體中每個 blob 必須容器中。 您可以建立私人容器使用新增 AzureStorageContainer 指令程式︰

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] 有三個匿名讀取權限層級︰**關閉** **Blob**，與**容器**。 若要避免匿名存取 blob，權限將參數設定為**關閉**。 根據預設，新的容器是私人且只能由帳戶擁有者可以存取。 若要允許匿名公用讀取權限 blob 的資源，但無法容器中繼資料或 blob 容器中的清單，權限將參數設定為**Blob**。 若要允許完整公用 blob 資源的讀取權限，容器中繼資料和清單的二進位大型物件在容器中，權限將參數設定為**容器**。 如需詳細資訊，請參閱[管理匿名的讀取權限容器及二進位大型物件](storage-manage-access-to-resources.md)。

### <a name="how-to-upload-a-blob-into-a-container"></a>如何上傳至容器的 blob
Azure Blob 儲存體支援區塊二進位大型物件及頁面二進位大型物件。 如需詳細資訊，請參閱[瞭解區塊 Blob、 附加二進位大型物件和頁面二進位大型物件](http://msdn.microsoft.com/library/azure/ee691964.aspx)。

二進位大型物件在其上傳到容器，您可以使用[設定 AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx)指令程式。 根據預設，這個命令上傳至區塊 blob 的本機檔案。 若要指定 blob 的類型，您可以使用-BlobType 參數。

下列範例執行[Get-childitem](http://technet.microsoft.com/library/hh849800.aspx) cmdlet，以取得指定的資料夾中的所有檔案，然後將它們傳送到下一個指令程式，使用管線運算子。 [設定 AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx)指令程式上傳至您的容器的本機檔案︰

    Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="how-to-download-blobs-from-a-container"></a>如何從容器下載二進位大型物件
下列範例會示範如何從容器下載二進位大型物件。 範例第一次建立連接至使用儲存的帳戶內容，包括儲存體帳戶名稱和其主要便捷鍵 Azure 儲存體。 然後，範例擷取 blob 參照使用[取得 AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx)指令程式。 接下來，這個範例使用[取得 AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) cmdlet 下載到本機的目的地資料夾的二進位大型物件。

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>二進位大型物件複製到另一個存放容器的方式
您可以非同步複製 blob 儲存體帳戶以及區域。 下列範例會示範如何將二進位大型物件複製到兩個不同的儲存帳戶中的另一個存放容器。 範例先設定 [來源與目的地儲存帳戶的變數，，然後建立針對每個帳戶的儲存空間內容。 接下來，範例將複製 blob 從來源容器至目的容器使用[開始 AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx)指令程式。 假設的來源與目的地儲存帳戶與容器已存在於。

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

請注意，此範例會執行非同步複本。 您可以監視每個複本的狀態執行[取得 AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx)指令程式。

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>如何將複製的第二個位置的二進位大型物件
您可以複製 blob 從次要 RA GRS 啟用帳戶的位置。

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### <a name="how-to-delete-a-blob"></a>如何刪除 blob
若要刪除 blob，先取得 blob 的參考，然後呼叫移除 AzureStorageBlob cmdlet 它。 下列範例會刪除指定容器中的所有二進位大型物件。 範例第一次設定儲存帳戶的變數，，然後建立的儲存空間的內容。 接下來，範例擷取 blob 參照使用[取得 AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx)指令程式，並執行 blob 移除容器 Azure 儲存體中[移除 AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx)指令程式。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## <a name="how-to-manage-azure-blob-snapshots"></a>如何管理 Azure blob 快照集
Azure 可讓您建立 blob 的快照。 快照是唯讀版本的時間點會採取 blob。 一旦建立快照，它可以讀取、 複製或刪除，但無法修改。 快照提供備份 blob 時間內所顯示的樣子。 如需詳細資訊，請參閱[建立 Blob 的快照](http://msdn.microsoft.com/library/azure/hh488361.aspx)。

### <a name="how-to-create-a-blob-snapshot"></a>如何建立 blob 快照
若要建立的 blob snaphot，先取得 blob 的參考，然後呼叫`ICloudBlob.CreateSnapshot`它的方法。 下列範例會先設定 [變數儲存帳戶，然後建立 [儲存內容。 接下來，範例擷取 blob 參照使用[取得 AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx)指令程式，並執行建立快照的[ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx)方法。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### <a name="how-to-list-a-blobs-snapshots"></a>如何列出 blob 的快照集
您可以建立任何數量的快照，為您想要用於 blob。 您可以列出您要追蹤您目前的快照集 blob 相關聯的快照集。 下列範例會使用預先定義的 blob，而且通話清單的 blob 的快照[取得 AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx)指令程式。  

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>如何複製 blob 的快照
您可以複製要還原的快照的 blob 的快照。 如需詳細的資訊與限制，請參閱[建立 Blob 的快照](http://msdn.microsoft.com/library/azure/hh488361.aspx)。 下列範例會先設定 [變數儲存帳戶，然後建立 [儲存內容。 接下來，此範例會定義容器和 blob 名稱變數。 範例擷取 blob 參照使用[取得 AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx)指令程式，並執行[ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx)方法建立快照。 然後，範例執行[開始 AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) cmdlet 複製 ICloudBlob 物件用來源 blob blob 的快照。 請務必一併更新根據您的設定，才能執行此範例的變數。 請注意，下列範例會假設的來源和目的容器來源 blob 已存在於。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

現在，您已經學會如何管理 Azure blob 以及 blob 使用 PowerShell 的 Azure 的快照，請移至下一節，瞭解如何管理表格、 佇列和檔案。

## <a name="how-to-manage-azure-tables-and-table-entities"></a>如何管理 Azure 表格及表格項目
Azure 資料表儲存體服務是 NoSQL 資料存放區，您可以用來儲存及查詢大量資料集的結構化、 非關聯性。 服務的主要元件是資料表、 實體和內容。 資料表是實體的集合。 實體是一組的屬性。 每個實體可以有最 252 屬性，也就是所有名稱 / 值組。 本節假設您已熟悉 Azure 資料表儲存體服務概念。 詳細資訊，請參閱[瞭解表格服務的資料模型](http://msdn.microsoft.com/library/azure/dd179338.aspx)，並[開始使用.NET Azure 資料表儲存體](storage-dotnet-how-to-use-tables.md)。

下面子區段中，在您將學習如何管理使用 PowerShell 的 Azure Azure 資料表儲存體服務。 涵蓋的案例包含**建立**、**刪除**和**擷取****資料表**，以及**新增**、**查詢**，和**刪除表格的實體**。

### <a name="how-to-create-a-table"></a>如何建立表格
每個資料表必須位於 Azure 儲存體帳戶。 下列範例會示範如何 Azure 儲存體中建立表格。 範例第一次建立連接至使用儲存的帳戶內容，包括儲存體帳戶名稱和其便捷鍵 Azure 儲存體。 接下來，其用於[新增 AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) cmdlet Azure 儲存體中建立表格。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-retrieve-a-table"></a>如何以擷取資料表
您可以查詢，並擷取一個或所有資料表儲存體帳戶。 下列範例會示範如何擷取給定的資料表，使用[取得 AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx)指令程式。

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

如果您呼叫取得 AzureStorageTable cmdlet 不具任何參數，它會取得所有的儲存空間資料表儲存體帳戶。

### <a name="how-to-delete-a-table"></a>如何刪除表格
您可以刪除資料表儲存體帳戶使用[移除 AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx)指令程式。  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-manage-table-entities"></a>如何管理表格項目
目前，PowerShell 的 Azure 不提供 cmdlet 來管理表格實體直接。 若要執行表格實體的作業，您可以使用[Azure 儲存用戶端文件庫的.NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)中指定的類別。

#### <a name="how-to-add-table-entities"></a>新增表格的實體的方式
若要新增至資料表的實體，請先建立物件，定義您的實體屬性。 實體可以有 255 個屬性，包括 3 系統內容︰ **PartitionKey**、 **RowKey**和**時間戳記**。 您負責插入和更新**PartitionKey**和**RowKey**的值。 伺服器管理**時間戳記**，無法修改的值。 在一起的**PartitionKey**及**RowKey**唯一識別每個表格中的實體。

-   **PartitionKey**︰ 決定實體儲存在磁碟分割。
-   **RowKey**︰ 可以唯一識別磁碟分割內的實體。

您可能會定義實體最 252 自訂的屬性。 如需詳細資訊，請參閱[瞭解表格服務的資料模型](http://msdn.microsoft.com/library/azure/dd179338.aspx)。

下列範例會示範如何新增至資料表的項目。 範例會示範如何擷取員工資料表，並將其新增數個項目。 首先，它會建立連線至使用儲存的帳戶內容，包括儲存體帳戶名稱和其便捷鍵 Azure 儲存體。 接下來，它會擷取給定的資料表使用[取得 AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx)指令程式。 如果資料表不存在，[新增 AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx)指令程式會使用 Azure 儲存體中建立表格。 接下來，此範例會定義自訂函數新增項目新增至資料表的項目，指定每個實體磁碟分割與列鍵。 新增實體函數呼叫[Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx)類別以建立實體物件[新增物件](http://technet.microsoft.com/library/hh849885.aspx)指令程式。 之後，就會將其新增至表格此實體物件上呼叫[Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx)方法。

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### <a name="how-to-query-table-entities"></a>如何查詢表格項目
若要查詢的資料表，請使用[Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx)類別。 下列範例會假設您已執行指令碼中如何將本指南的實體一節。 範例第一次建立連接至使用儲存的內容，包括儲存體帳戶名稱和其便捷鍵 Azure 儲存體。 接下來，則會擷取先前建立的 [員工] 表格，使用[取得 AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx)指令程式。 呼叫 [Microsoft.WindowsAzure.Storage.Table.TableQuery 類別上的 [[新增物件](http://technet.microsoft.com/library/hh849885.aspx)cmdlet 建立新的查詢物件。 此範例會尋找有其值為字串篩選中所指定的 1 'ID' 資料行的實體。 詳細資訊，請參閱[查詢的資料表和項目](http://msdn.microsoft.com/library/azure/dd894031.aspx)。 當您執行查詢時，則會傳回符合的篩選準則的所有項目。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### <a name="how-to-delete-table-entities"></a>若要刪除表格的實體的方式
您可以刪除使用其分割及列的實體。 下列範例會假設您已執行指令碼中如何將本指南的實體一節。 範例第一次建立連接至使用儲存的內容，包括儲存體帳戶名稱和其便捷鍵 Azure 儲存體。 接下來，則會擷取先前建立的 [員工] 表格，使用[取得 AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx)指令程式。 如果資料表存在，就會呼叫[Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx)方法來擷取實體根據其分割和資料列的關鍵值。 然後，將[Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx)方法若要刪除的實體。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>如何管理 Azure 佇列和佇列中的郵件
Azure 佇列中的儲存空間是儲存大量郵件的可透過 HTTP 或 HTTPS 經過驗證的來電世界從任何地方存取服務。 本節假設您已熟悉 Azure 佇列中儲存服務概念。 詳細資訊，請參閱[快速入門使用.NET Azure 佇列中儲存空間](storage-dotnet-how-to-use-queues.md)。

本節會顯示如何管理使用 PowerShell 的 Azure Azure 佇列中儲存的服務。 涵蓋的案例包含**插入**及**刪除**佇列中的郵件，以及**建立**、**刪除**和**擷取佇列**。

### <a name="how-to-create-a-queue"></a>如何建立佇列
下列範例第一次建立連接至使用儲存的帳戶內容，包括儲存體帳戶名稱和其便捷鍵 Azure 儲存體。 接下來，它會呼叫[新增 AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) cmdlet 來建立名為 「 queuename' 佇列。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Azure 佇列中服務的命名慣例資訊，請參閱[命名佇列和中繼資料](http://msdn.microsoft.com/library/azure/dd179349.aspx)。

### <a name="how-to-retrieve-a-queue"></a>如何擷取佇列中
您可以查詢，並擷取特定的佇列或儲存帳戶的所有佇列的清單。 下列範例會示範如何以擷取指定的佇列使用[取得 AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx)指令程式。

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

如果您呼叫[取得 AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) cmdlet 不具任何參數，它會取得所有佇列的清單。

### <a name="how-to-delete-a-queue"></a>如何刪除佇列
若要刪除佇列和其包含的所有郵件，請連絡移除 AzureStorageQueue 指令程式。 下列範例會示範如何刪除指定的佇列使用移除 AzureStorageQueue 指令程式。

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### <a name="how-to-insert-a-message-into-a-queue"></a>如何插入佇列中的郵件
若要插入的現有佇列中的郵件，請先建立新類別的執行個體[Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) 。 接下來，呼叫[AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx)方法。 從格式的字串 （utf-8） 或位元組陣列可以建立 CloudQueueMessage。

下列範例會示範如何將佇列中的郵件。 範例第一次建立連接至使用儲存的帳戶內容，包括儲存體帳戶名稱和其便捷鍵 Azure 儲存體。 接下來，它會擷取指定佇列中使用[取得 AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx)指令程式。 如果佇列中，[新增物件](http://technet.microsoft.com/library/hh849885.aspx)指令程式會用來建立[Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx)類別的執行個體。 之後，就會將其新增至佇列此郵件物件上呼叫[AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx)方法。 以下是程式碼會擷取佇列中，並會插入 「 MessageInfo 」 的訊息︰

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### <a name="how-to-de-queue-at-the-next-message"></a>如何取消佇列在下一封郵件
您的程式碼就能取消佇列中兩個步驟的佇列中的訊息。 當您呼叫[Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx)方法時，您會取得佇列中的下一封郵件。 傳回從**GetMessage**訊息會變成看不到任何其他佇列中讀取郵件的程式碼。 若要完成移除佇列中的郵件，您也必須呼叫[Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx)方法。 移除郵件的兩個步驟程序可確保如果程式碼，就無法處理的訊息，因為硬體或軟體失敗，另一個程式碼執行個體可以取得相同的訊息並再試一次。 處理郵件後，程式碼呼叫**DeleteMessage**權限。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## <a name="how-to-manage-azure-file-shares-and-files"></a>如何管理 Azure 檔案共用和檔案
Azure 檔案儲存空間提供使用標準 SMB 通訊協定的應用程式共用的儲存空間。 Microsoft Azure 虛擬機器和雲端服務可以透過裝載共用的應用程式元件之間共用檔案的資料與內部部署的應用程式可存取檔案共用透過檔案儲存空間 API 或 PowerShell 的 Azure 中的資料。

如需有關 Azure 檔案儲存空間的詳細資訊，請參閱[開始使用 windows Azure 檔案儲存](storage-dotnet-how-to-use-files.md)與[檔案服務 REST API](http://msdn.microsoft.com/library/azure/dn167006.aspx)。

## <a name="how-to-set-and-query-storage-analytics"></a>如何設定和查詢儲存狀況分析
您可以使用[Azure 儲存體分析](storage-analytics.md)收集 Azure 儲存體帳戶及記錄有關您儲存的帳戶傳送要求資料的指標。 您可以使用 [儲存指標監控儲存帳戶] 中，然後儲存記錄診斷和疑難排解問題使用您儲存的帳戶的狀況。
根據預設，儲存指標無法進行儲存服務。 您可以啟用監視使用 Windows PowerShell 的 Azure 入口網站，或使用的儲存空間用戶端文件庫，以程式設計方式。 儲存記錄發生伺服器端，並可讓您記錄的詳細資料的儲存空間帳戶中的成功或失敗要求。 這些記錄檔可讓您看到的詳細資料請閱讀、 撰寫及刪除作業表格、 佇列和二進位大型物件，以及失敗要求的原因。

若要瞭解如何啟用並檢視使用 PowerShell 儲存指標資料，請參閱[如何啟用使用 PowerShell 儲存指標](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell)。

若要瞭解如何啟用並擷取使用 PowerShell 儲存記錄資料，請參閱[如何啟用 [儲存記錄使用 PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell)及[尋找您的 [儲存記錄的記錄資料。](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata)
使用儲存指標，並儲存記錄存放問題進行疑難排解的詳細資訊，請參閱[監控、 Diagnosing 及疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>如何管理共用 Access 簽章 (SA) 和儲存存取原則
共用的 access 簽章會使用 Azure 儲存體任何應用程式的安全性模型很重要的一部分。 很有幫助以提供有限的權限，您儲存的帳戶不應該帳戶金鑰的用戶端。 根據預設，只有儲存帳戶擁有者可以存取二進位大型物件、 表格和佇列中該帳戶。 如果您的服務或應用程式需要這些資源可用其他用戶端不共用您的便捷鍵，您會有三個選項︰

- 設定以允許匿名讀取權限的容器，其二進位大型物件的容器的權限。 這不是允許的資料表或佇列。
- 使用共用的 access 簽名授與，限制特定時間間隔容器、 二進位大型物件、 佇列和資料表的存取權限。
- 使用儲存的 access 原則，以取得額外的層級控制共用的 access 簽章容器或其二進位大型物件、 佇列中，或表格。 將儲存的存取原則可讓您變更的開始時間、 到期時間或權限的簽名，或發行撤銷其後面。

共用的 access 簽章可在其中一個兩個表單︰

- **臨機操作 SA**︰ 當您建立臨機操作的 SA，開始時間]、 [到期時間 SA 的權限已指定 SA URI 上。 此類型的 SA 可能會在 [容器]、 [blob、 [表格]，建立或佇列中，而且非 revokable。
- **儲存的存取原則 SA**: 資源容器 blob 容器、 表格或佇列-上定義儲存的存取原則，您可以使用它來管理一或多個共用的 access 簽章的限制式。 當您建立 SA 關聯與儲存的存取原則時，SA 會繼承限制-的開始時間、 到期時間和權限-儲存的存取原則定義。 此類型的 SA 是 revokable。

如需詳細資訊，請參閱[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)和[管理匿名的讀取權限容器及二進位大型物件](storage-manage-access-to-resources.md)。

在下一個區段中，您將學習如何建立 Azure 資料表的共用的存取簽章權杖及儲存存取原則。 Azure PowerShell 提供容器與二進位大型物件，以及佇列類似的 cmdlet。 若要執行此區段中的指令碼，下載[PowerShell 的 Azure 0.8.14 版本](http://go.microsoft.com/?linkid=9811175&clcid=0x409)或更新版本。

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>如何建立的原則型共用 Access 簽章權杖
您可以使用 [新增 AzureStorageTableStoredAccessPolicy cmdlet 來建立新的儲存的存取原則。 然後，呼叫若要建立新的原則型共用的存取簽章憑證 Azure 儲存體表格[新增 AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx)指令程式。

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="how-to-create-an-ad-hoc-non-revokable-shared-access-signature-token"></a>如何建立臨機操作 (非 revokable) 共用 Access 簽章權杖
若要建立新臨機操作 (非 revokable) 共用 Access 簽章的憑證 Azure 儲存體表格中使用[新增 AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) cmdlet:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="how-to-create-a-stored-access-policy"></a>如何建立儲存的存取原則
您可以使用 [新增 AzureStorageTableStoredAccessPolicy cmdlet 來建立新的儲存的存取原則 Azure 儲存體表格︰

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="how-to-update-a-stored-access-policy"></a>如何更新儲存的存取原則
若要更新現有的儲存的 access 原則 Azure 儲存體表格中使用設定 AzureStorageTableStoredAccessPolicy cmdlet:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="how-to-delete-a-stored-access-policy"></a>如何刪除儲存的存取原則
若要刪除儲存的存取原則 Azure 儲存體資料表上使用移除 AzureStorageTableStoredAccessPolicy cmdlet:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>如何使用 Azure 儲存體美國政府版和 Azure china （中國）
Azure 環境是 Microsoft Azure，例如[Azure 政府版美國政府](https://azure.microsoft.com/features/gov/)、 [AzureCloud 的全域 Azure](https://portal.azure.com)及[AzureChinaCloud Azure 中國的 21Vianet 所營運的](http://www.windowsazure.cn/)獨立部署。 您可以部署新的 Azure 環境美國政府版和 Azure china （中國）。

若要使用 AzureChinaCloud Azure 儲存空間，您需要建立 AzureChinaCloud 與相關聯的儲存空間內容。 遵循下列步驟可協助您著手︰

1.  執行以查看可用的 Azure 環境[取得 AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) cmdlet:

    `Get-AzureEnvironment`

2.  新增至 Windows PowerShell 的 Azure china （中國） 帳戶︰

    `Add-AzureAccount –Environment AzureChinaCloud`

3.  建立儲存內容的 AzureChinaCloud 帳戶︰

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

若要使用[美國 Azure 政府](https://azure.microsoft.com/features/gov/)Azure 儲存空間，您應該定義新的環境，而且再此環境中建立新的儲存空間內容︰

1.  執行以查看可用的 Azure 環境[取得 AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) cmdlet:

    `Get-AzureEnvironment`

2.  新增至 Windows PowerShell 的 Azure 美國政府帳戶︰

    `Add-AzureAccount –Environment AzureUSGovernment`

3.  建立儲存內容的 AzureUSGovernment 帳戶︰

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

如需詳細資訊，請參閱︰

- [Microsoft Azure 政府開發人員指南](../azure-government-developer-guide.md)。
- [差異 china （中國） 服務上建立應用程式時的概觀](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>後續步驟
本指南中，您學到如何管理與 Azure PowerShell 的 Azure 儲存體。 以下是一些相關的文章和資源，進一步瞭解這些。

- [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)
- [Azure 儲存體 PowerShell Cmdlet](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Windows PowerShell 參照](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
