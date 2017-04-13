<properties
   pageTitle="設定 Visual Studio Azure 雲端服務的角色 |Microsoft Azure"
   description="瞭解如何設定和使用 Visual Studio 設定 Azure 雲端服務的角色。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configure-the-roles-for-an-azure-cloud-service-with-visual-studio"></a>設定 Visual Studio Azure 雲端服務的角色

Azure 雲端服務可以有一或多個背景工作或網頁的角色。 每一個角色必須先定義該角色的設定方式及也設定 [該角色的執行方式。 若要進一步瞭解在雲端服務中的角色，請參閱[Azure 雲端服務的簡介](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services)。 您的雲端服務的資訊會儲存在下列檔案︰

- **ServiceDefinition.csdef**

    服務定義檔案定義執行階段設定，包括何種角色所需的雲端服務，結束點，然後虛擬機器大小。 儲存此檔案中的資料時，可以變更您的角色正在執行。

- **ServiceConfiguration.cscfg**

    服務設定檔設定要執行的角色的執行個體數目，以及角色所定義的設定值。 執行您的角色時，可以變更此檔案中儲存的資料。

若要能夠為您的角色的執行方式儲存這些設定不同的值，您可以將多個服務設定。 您可以使用不同的服務設定為每個部署環境。 例如，您可以設定使用本機 Azure 儲存模擬器本機服務設定，並建立另一個服務設定，若要使用 Azure 儲存在雲端儲存空間帳戶連線字串。

當您在 Visual Studio 中建立新的 Azure 雲端服務時，預設會建立兩個服務設定。 這些設定會新增至 Azure 專案。 命名設定︰

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## <a name="configure-an-azure-cloud-service"></a>設定 Azure 雲端服務

下圖所示，您可以設定 Visual Studio 中，在方案總管 Azure 雲端服務。

![設定雲端服務](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### <a name="to-configure-an-azure-cloud-service"></a>若要設定 Azure 雲端服務

1. 若要設定每一個角色 Azure 專案**方案**總管] 中，開啟角色的快顯功能表中 Azure 專案，然後選擇**屬性**。

    使用該角色名稱的頁面會顯示在 Visual Studio 編輯器。 此頁面會顯示 [**設定**] 索引標籤的欄位。

1. 在 [**服務設定**] 清單中，選擇您想要編輯的服務設定的名稱。

    如果您想要對此角色的服務設定的所有變更，您可以選擇**所有的設定**。

    >[AZURE.IMPORTANT] 如果您選擇特定的服務設定，有些屬性已停用，因為只可以設定的所有設定。 若要編輯這些屬性，您必須選擇所有的設定。

    您現在可以選擇要更新該檢視任何啟用的內容] 索引標籤。

## <a name="change-the-number-of-role-instances"></a>變更角色執行個體數目

若要改善您的雲端服務的效能，您可以變更的角色執行的，根據數使用者或載入特定角色的預期的執行個體數目。 Azure 中執行雲端服務之角色的每個執行個體會建立個別的虛擬機器。 這會影響此雲端服務的部署帳單。 如需有關帳單的詳細資訊，請參閱[瞭解為 Microsoft Azure 帳單](billing/billing-understand-your-bill.md)。

### <a name="to-change-the-number-of-instances-for-a-role"></a>若要變更角色的執行個體數目

1. 選擇 [**設定**] 索引標籤。

1. 在 [**服務設定**] 清單中，選擇 [您想要更新的服務設定]。

    >[AZURE.NOTE] 您可以設定的執行個體計算特定的服務設定，或針對所有的服務設定。

1. 在 [**執行個體計數**文字] 方塊中，輸入您要開始此角色的執行個體數目。

    >[AZURE.NOTE] 當您將您的雲端服務發佈至 Azure 個別的虛擬機器上執行每個執行個體。

1. 選擇 [將這些變更儲存至 [服務設定檔] 工具列上的 [**儲存**] 按鈕。

## <a name="manage-connection-strings-for-storage-accounts"></a>管理儲存空間帳戶的連線字串

您可以新增、 移除或修改您的服務設定的連接字串。 您可以針對不同的服務設定不同的連接字串。 例如，您可能會想本機的連線字串的值是本機的服務設定`UseDevelopmentStorage=true`。 您也可以設定 Azure 中使用儲存帳戶的雲端服務設定。

>[AZURE.WARNING] 當您輸入的儲存空間帳戶連線字串 Azure 儲存體帳戶重要資訊時，這項資訊儲存在本機服務設定檔中。 不過，這項資訊目前未儲存為加密的文字。

藉由使用不同的值，每個服務設定，您不必您雲端服務中使用不同的連接字串或修改您的程式碼，當您將您的雲端服務發佈至 Azure。 您可以使用相同的名稱，連接字串的程式碼中，值可能會不同，根據您選取 [當您建立您的雲端服務，或將它發佈服務設定。

### <a name="to-manage-connection-strings-for-storage-accounts"></a>若要管理儲存空間帳戶的連線字串

1. 選擇 [**設定**] 索引標籤。

1. 在 [**服務設定**] 清單中，選擇 [您想要更新的服務設定]。

    >[AZURE.NOTE] 您可以更新特定的服務設定] 的連接字串，但如果您需要新增或刪除連接字串，您必須先選取所有的設定。

1. 若要新增的連接字串，選擇 [**新增設定**] 按鈕。 新的項目會新增至清單。

1. 在 [**名稱**] 文字方塊中，輸入您想要使用的連線字串的名稱。

1. 在 [**類型**] 下拉式清單中，選擇 [**連線字串**]。

1. 若要變更的連接字串] 的值，請選擇 [省略符號 （...）] 按鈕。 **建立儲存連線字串**] 對話方塊隨即出現。

1. 若要使用本機存放區帳戶模擬器，選擇 [ **Microsoft Azure 儲存模擬器**選項] 按鈕，然後選擇**[確定**] 按鈕。

1. 若要使用的儲存空間帳戶 Azure 中，選擇**您的訂閱**選項] 按鈕，選取您要的儲存帳戶。

1. 若要使用自訂的認證，請選擇 [**手動輸入認證**選項] 按鈕。 輸入儲存體帳戶名稱及主要或第二個鍵。 如需有關如何建立資訊儲存帳戶，以及如何儲存帳戶中**建立的儲存空間連線字串**] 對話方塊中，輸入的詳細資料，請參閱[發佈或部署 Visual Studio Azure 應用程式的準備](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)。

1. 若要刪除的連接字串，選取的連接字串，然後選擇 [**移除的設定**] 按鈕。

1. 選擇 [將這些變更儲存至 [服務設定檔] 工具列上的 [**儲存**] 圖示。

1. 若要存取服務設定檔中的連接字串，您必須取得組態設定的值。 下列程式碼會顯示的範例建立 blob 儲存體的位置和資料上傳使用連接字串`MyConnectionString`從當使用者在 web 角色 Azure 雲端服務中的 [Default.aspx 頁面上選擇**Button1**服務設定檔。 新增下列使用陳述式以 Default.aspx.cs:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. 在 [設計] 檢視中開啟 Default.aspx.cs，並從工具箱] 新增按鈕。 新增下列程式碼，`Button1_Click`方法。 此程式碼使用`GetConfigurationSettingValue`以取得的值從 [連線字串的服務設定檔。 然後 blob 會建立連線字串中參照的儲存空間帳戶中`MyConnectionString`，最後程式新增至 blob 的文字。

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>新增您的 Azure 雲端服務中使用自訂設定

在 [服務設定檔中的自訂設定可讓您新增的名稱和特定的服務設定字串的值。 您可以選擇使用此設定來設定您的雲端服務中的功能，閱讀的設定值，並使用這個值來控制您的程式碼中的邏輯。 您可以變更這些服務設定值，而不必重新建立您的服務套件，或您的雲端服務正在執行時。 設定變更時的通知可以檢查您的程式碼。 請參閱[RoleEnvironment.Changing 事件](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)。

您可以新增、 移除或修改自訂設定您的服務設定。 您可以針對不同的服務設定這些字串不同的值。

藉由使用不同的值，每個服務設定，您不必雲端服務中使用不同的字串或修改您的程式碼，當您以 Azure 發佈您的雲端服務。 您可以使用相同名稱的字串的程式碼中，值可能會不同，根據您選取 [當您建立您的雲端服務，或將它發佈服務設定。

### <a name="to-add-custom-settings-to-use-in-your-azure-cloud-service"></a>若要新增至您的 Azure 雲端服務中使用自訂的設定

1. 選擇 [**設定**] 索引標籤。

1. 在 [**服務設定**] 清單中，選擇 [您想要更新的服務設定]。

    >[AZURE.NOTE] 您可以更新特定的服務設定的字串，但如果您要新增或刪除字串時，您必須先選取**所有的設定**。

1. 若要新增一個字串，選擇 [**新增設定**] 按鈕。 新的項目會新增至清單。

1. 在 [**名稱**] 文字方塊中，輸入您想要使用字串的名稱。

1. 在 [**類型**] 下拉式清單中，選擇 [**字串**]。

1. 若要新增或變更字串的值，請在 [**值**] 文字方塊中輸入新的值。

1. 若要刪除的字串，選取字串，然後選擇 [**移除的設定**] 按鈕。

1. 選擇 [將這些變更儲存至 [服務設定檔] 工具列上的 [**儲存**] 按鈕。

1. 若要存取服務設定檔中的字串，您必須取得組態設定的值。

    您需要請確定下列使用陳述式已新增至 Default.aspx.cs 所做的一樣在上述程序。

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. 新增下列程式碼，`Button1_Click`方法存取連線字串的相同方式來存取此字串。 您的程式碼然後可以執行某些特定的程式碼，根據 [服務設定檔所使用的設定字串的值。

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>管理本機的儲存空間，每個角色執行個體

您可以新增本機檔案系統儲存空間，每個執行個體的角色。 您可以儲存本機資料，不需要其他角色的存取。 在此可以儲存任何您不需要將儲存為表格、 blob 或 SQL 資料庫儲存的資料。 例如，您可以使用此可能需要一次使用的快取資料的本機存放區。 其他執行個體的角色無法存取此儲存的資料。 

本機存放區設定套用至所有服務設定。 您只可以新增、 移除或修改本機的儲存空間的所有服務設定。

### <a name="to-manage-local-storage-for-each-role-instance"></a>若要管理本機的儲存空間，每個角色執行個體

1. 選擇 [**本機存放裝置**] 索引標籤。

1. 在 [**服務設定**] 清單中，選擇 [**所有設定**]。

1. 若要新增的本機存放區項目，選擇 [**新增本機的儲存空間**] 按鈕。 新的項目會新增至清單。

1. 在 [**名稱**] 文字方塊中，輸入您想要使用此本機的儲存空間的名稱。

1. 在 [**大小**] 文字方塊中，輸入 mb 的本機的儲存空間，您需要的大小。

1. 若要回收此角色的虛擬機器時，請在此本機存放區中移除資料，請選取**資源回收角色清除**核取方塊。

1. 若要編輯現有的本機存放區項目，請選擇您要更新的資料列。 然後您可以編輯欄位中的上一個步驟所述。

1. 若要刪除的本機存放區項目，選擇儲存項目清單中，然後選擇 [**移除本機存放區**] 按鈕。

1. 若要將這些變更儲存至 [服務設定檔，選擇工具列] 上的 [**儲存**] 圖示。

1. 若要存取您已新增的服務設定檔案在本機儲存空間，您必須取得本機資源設定的設定的值。 使用下列幾行程式碼來存取此值，建立稱為**MyStorageTest.txt**檔案並測試資料行寫入到該檔案。 您可以新增到此程式碼`Button_Click`在先前的程序中所使用的方法︰

1. 您需要請確定下列使用陳述式會新增至 Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. 新增下列程式碼，`Button1_Click`方法。 這會建立本機存放區中的檔案，並將該檔案會寫入測試資料。

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. （選用）若要檢視此本機執行您的雲端服務時所建立的檔案，請使用下列步驟︰

  1. 執行網頁角色，並選取，請確定**Button1**內部的程式碼`Button1_Click`呼叫。

  1. 在通知區域中，開啟 [Azure] 圖示的捷徑功能表並選擇**顯示計算模擬器 ui**。 **Azure 計算模擬器**] 對話方塊隨即出現。

  1. 選取 [網頁角色]。

  1. 在功能表列中，選擇 [**工具**]**開啟本機存放區**]。 Windows 檔案總管] 視窗隨即出現。

  1. 在功能表列中，[**搜尋**] 方塊中輸入**MyStorageTest.txt** ，然後選擇**enter 鍵**啟動搜尋。

    檔案會顯示在搜尋結果。

  1. 若要檢視檔案的內容，請開啟檔案的快顯功能表，然後選擇 [**開啟**。

## <a name="collect-cloud-service-diagnostics"></a>收集雲端服務診斷程式

您可以收集診斷資料 Azure 雲端服務。 此資料會新增到儲存的帳戶。 您可以針對不同的服務設定不同的連接字串。 例如，您可能會想本機存放區帳戶 UseDevelopmentStorage 值本機服務設定 = true。 您也可以設定 Azure 中使用儲存帳戶的雲端服務設定。 如需有關 Azure 診斷的詳細資訊，請參閱收集依據使用 Azure 診斷記錄的資料。

>[AZURE.NOTE] 本機的服務設定已設定為使用本機資源。 如果您使用的雲端服務設定發佈您的 Azure 雲端服務，您可以指定當您發佈的連接字串也用於診斷連線字串除非您已指定連線字串。 如果您封裝使用 Visual Studio 您雲端服務，不會變更的服務設定中的連接字串。

### <a name="to-collect-cloud-service-diagnostics"></a>收集雲端服務診斷程式

1. 選擇 [**設定**] 索引標籤。

1. 在 [**服務設定**] 清單中，選擇 [服務設定您要更新，或選擇 [**所有組態**]。

    >[AZURE.NOTE] 您可以更新特定的服務設定，儲存帳戶，但如果您想要啟用或停用診斷您必須選擇所有的設定。

1. 若要啟用診斷程式，請選取 [**啟用診斷**] 核取方塊。

1. 若要變更的值的儲存空間的帳戶，請選擇 [省略符號 （...）] 按鈕。

    **建立儲存連線字串**] 對話方塊隨即出現。

1. 若要使用本機的連線字串，選擇 Azure 儲存體模擬器選項，然後選擇**[確定**] 按鈕。

1. 若要使用與您 Azure 訂閱相關聯的儲存空間帳戶，請選擇 [**訂閱**] 選項。

1. 若要使用本機的連接字串儲存帳戶，請選擇 [**手動輸入認證**] 選項。

    如需有關如何建立儲存帳戶以及如何輸入**建立儲存連線字串**] 對話方塊中的儲存空間帳戶的詳細資料的詳細資訊，請參閱[準備發佈或部署 Visual Studio Azure 應用程式](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)。

1. 選擇您想要使用在 [**帳戶名稱**的儲存空間帳戶。

    如果您是手動輸入您儲存的帳戶認證，複製，或在 [**帳戶金鑰]**中輸入您的主索引鍵。 從[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)，就可以複製這個索引鍵。 若要複製此鍵，從[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)中的 [**儲存帳戶**] 檢視中執行下列步驟︰
    
  1. 選取您想要使用雲端服務的儲存空間帳戶。

  1. 選取位於畫面底部的 [**管理便捷鍵**按鈕。 [**管理便捷鍵**] 對話方塊隨即出現。

  1. 若要複製的便捷鍵，選擇 [**複製至剪貼簿**] 按鈕。 現在您可以將此按鍵貼入 [**帳戶金鑰**] 欄位。

1. 若要使用的儲存空間帳戶所提供的作為連線字串，當您將您的雲端服務發佈至 Azure 診斷 （和快取） 選取 [**更新開發儲存空間的連接字串診斷和快取與 Azure 儲存體帳戶認證時發佈至 Azure**核取方塊。

1. 選擇 [將這些變更儲存至 [服務設定檔] 工具列上的 [**儲存**] 按鈕。

## <a name="change-the-size-of-the-virtual-machine-used-for-each-role"></a>變更用於每一個角色的虛擬機器的大小

您可以設定每一個角色的虛擬機器大小。 您只可以設定所有服務設定的大小。 如果您選取較小的電腦，然後較不 CPU 核心、 記憶體和本機磁碟儲存空間配置。 配置的頻寬也會比較小。 如需有關這些大小和分派的資源的詳細資訊，請參閱[雲端服務的大小](cloud-services/cloud-services-sizes-specs.md)。

每個虛擬機器中 Azure 所需的資源將會影響 Azure 中執行您的雲端服務的成本。 如需有關 Azure 帳單的詳細資訊，請參閱[瞭解為 Microsoft Azure 帳單](billing/billing-understand-your-bill.md)。

### <a name="to-change-the-size-of-the-virtual-machine"></a>若要變更的虛擬機器大小

1. 選擇 [**設定**] 索引標籤。

1. 在 [**服務設定**] 清單中，選擇 [**所有設定**]。

1. 若要選取此角色的虛擬機器的大小，請從**虛擬記憶體大小**清單中選擇適當的大小。

1. 選擇 [將這些變更儲存至 [服務設定檔] 工具列上的 [**儲存**] 按鈕。

## <a name="manage-endpoints-and-certificates-for-your-roles"></a>管理結束點和您的角色的憑證

依指定的通訊協定的連接埠號碼，並為 HTTPS 的 SSL 憑證資訊，您可以設定網路的結束點。 2012 年 6 月之前的版本支援 HTTP HTTPS，與 TCP。 年 6 月 2012年版本支援的通訊協定和 UDP。 您無法使用 UDP 用於計算模擬器中輸入的結束點。 您可以使用該通訊協定僅適用於內部結束點。

若要改善您 Azure 雲端服務的安全性，您可以建立使用 HTTPS 通訊協定的結束點。 例如，如果您有客戶用來購買訂單的雲端服務，您要確認他們的資訊安全使用 SSL。

您也可以新增可用的內部或外部的結束點。 外部端點稱為輸入結束點。 輸入的結束點 」 可讓您的雲端服務的使用者的其他存取點。 如果您有 WCF 服務，您可能會想要公開內部 web 角色来用來存取這項服務的端點。

>[AZURE.IMPORTANT] 您只可以更新所有服務設定的結束的點。

如果您新增 HTTPS 結束點，您需要使用 SSL 憑證。 若要這麼做可以與您的角色，所有的服務設定建立關聯的憑證，並使用這些端點。

>[AZURE.IMPORTANT] 這些憑證不會封裝服務。 您必須以透過[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)的 Azure 分別上傳您的憑證。

僅在您的雲端服務執行 Azure 中時，就會套用任何您與您的服務設定建立關聯的管理憑證。 在本機的開發環境中，執行您的雲端服務，會使用標準憑證管理 Azure 計算模擬器。

### <a name="to-add-a-certificate-to-a-role"></a>若要新增至角色的憑證

1. 選擇 [**憑證**] 索引標籤。

1. 在 [**服務設定**] 清單中，選擇 [**所有設定**]。

    >[AZURE.NOTE] 若要新增或移除的憑證，您必須先選取所有的設定。 如果有需要，您可以更新名稱] 和 [特定的服務設定指紋。

1. 若要新增此角色的憑證，請選擇 [**新增憑證**] 按鈕。 新的項目會新增至清單。

1. 在 [**名稱**] 文字方塊中，輸入憑證的名稱。

1. 在 [**儲存位置**] 清單中，選擇您想要新增的憑證的位置。

1. 在 [**儲存名稱**] 清單中，選擇您要用來選取憑證存放區。

1. 若要新增憑證，請選擇 [省略符號 （...）] 按鈕。 **Windows 安全性**] 對話方塊隨即出現。

1. 選擇您想要使用的清單，然後選擇 [**確定**] 按鈕的憑證。

    >[AZURE.NOTE] 當您新增的憑證存放區的憑證時，中繼憑證會自動新增至您的組態設定。 這些中繼憑證必須也上傳至 Azure 才能正確地設定您的服務為 SSL。

1. 若要刪除憑證，請選擇憑證，然後選擇 [**移除憑證**] 按鈕。

1. 若要將這些變更儲存至 [服務設定檔] 工具列中選擇 [**儲存**] 圖示。

### <a name="to-manage-endpoints-for-a-role"></a>管理角色的端點

1. 選擇 [**結束點**] 索引標籤。

1. 在 [**服務設定**] 清單中，選擇 [**所有設定**]。

1. 若要新增端點，選擇 [**新增結束點**] 按鈕。 新的項目會新增至清單。

1. 在 [**名稱**] 文字方塊中，輸入您想要使用這個端點的名稱。

1. 從 [**類型**] 清單中選擇您需要的結束點類型。

1. 從 [**通訊協定**] 清單選擇您所需要的端點的通訊協定。

1. 如果輸入的結束點，在**公用連接埠**] 文字方塊中，輸入要使用的公用連接埠。

1. 在 [**私人連接埠**] 文字方塊中輸入要使用的私人連接埠。

1. 如果端點需要 https 通訊協定， **SSL 憑證名稱**] 清單中選擇要使用的憑證。

    >[AZURE.NOTE] 此清單會顯示您已新增的 [**憑證**] 索引標籤中的此角色的憑證。

1. 選擇 [將這些變更儲存至 [服務設定檔] 工具列上的 [**儲存**] 按鈕。

## <a name="next-steps"></a>後續步驟
進一步瞭解在 Visual Studio 中 Azure 專案閱讀[設定 Azure 專案](vs-azure-tools-configuring-an-azure-project.md)。 進一步瞭解雲端服務結構描述閱讀[結構描述參考](https://msdn.microsoft.com/library/azure/dd179398)。
