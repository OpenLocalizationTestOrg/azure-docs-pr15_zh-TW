<properties
    pageTitle="如何建立、 管理，或刪除儲存帳戶 Azure 傳統入口網站中的 |Microsoft Azure"
    description="建立新的儲存空間帳戶、 管理您的帳戶便捷鍵，或刪除 Azure 入口網站中的儲存空間帳戶。 深入了解標準和付費儲存帳戶。"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>關於 Azure 儲存體帳戶

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>概觀

Azure 儲存體帳戶可讓您存取 Azure 儲存體中 Azure Blob、 佇列、 表格和檔案的服務。 您儲存的帳戶提供的 Azure 儲存體資料物件的唯一的命名空間。 根據預設，您的帳戶中的資料時，才可使用您的帳戶擁有者。

有兩種類型的儲存空間帳戶︰

- 標準的儲存空間] 帳戶包含 Blob、 表格、 佇列中，以及檔案儲存空間。
- 進階版儲存帳戶目前支援 Azure 虛擬機器磁碟才。 請參閱[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](storage-premium-storage.md)概略在您採取進階的進階版儲存空間。

## <a name="storage-account-billing"></a>儲存帳戶帳單

是向您收費 Azure 儲存體使用量根據您儲存的帳戶。 儲存空間的成本會根據四個因素︰ 儲存容量、 複寫配置、 儲存空間交易和資料出口。

- 儲存容量是指您儲存帳戶分配中有多少您是使用儲存的資料。 只要將資料儲存成本會決定資料量，您儲存，及如何複製。
- 複寫會決定您資料的份數會維持一次，並在什麼位置。
- 交易，請參閱所有讀取和寫入到 Azure 儲存體的作業。
- 資料出口指的是傳輸登出 Azure 的地區資料。 雲端服務或應用程式的一些其他類型的應用程式是否不在執行相同的區域] 中的應用程式存取您儲存的帳戶中的資料，當您要支付的資料出口。 （如需 Azure 服務，您可以採取步驟以群組您的資料和縮小或排除資料出口費用相同的資料中心的服務）。  

[Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage)] 頁面會提供詳細的儲存容量、 複寫和交易的價格資訊。 [資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)] 頁面會提供詳細的資料出口價格資訊。

如需儲存帳戶容量和效能目標的詳細資訊，請參閱[Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。

> [AZURE.NOTE] 當您建立 Azure 虛擬機器時，儲存帳戶會為您自動建立部署位置如果您不在該位置已有儲存帳戶。 讓您不需要遵循下列步驟來建立的虛擬機器磁碟儲存帳戶。 儲存體帳戶名稱會根據的虛擬機器名稱。 請參閱更多詳細資料的[Azure 虛擬機器文件](https://azure.microsoft.com/documentation/services/virtual-machines/)。

## <a name="create-a-storage-account"></a>建立儲存的帳戶

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。

2. 按一下 [在頁面底部的工作列中的 [**新增**]。 選擇 [**資料服務** | **儲存空間**，然後按一下 [**快速建立**。

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. 在 [ **URL**] 中，輸入您儲存帳戶的名稱。

    > [AZURE.NOTE] 儲存帳戶名稱必須介於 3 和 24 個字元的長度，而且可能包含數字和只有大小寫字母。
    >  
    > 您儲存體帳戶名稱中必須是唯一 Azure。 將會指出 Azure 傳統入口網站，是否您選取儲存體帳戶名稱已經存在。

    如需如何儲存體帳戶名稱會使用地址 Azure 儲存體中的物件詳細資訊，請參閱下方的[儲存空間帳戶結束點](#storage-account-endpoints)。

4. 在**位置/相關性] 群組**中，選取您儲存的帳戶是靠近您或您的客戶的位置。 如果從另一個 Azure 服務的詳細資訊，例如 Azure 虛擬機器或雲端服務，可存取您儲存的帳戶中的資料您可能會想要從群組中相同的資料中心，與您用來改善效能與降低成本其他 Azure 服務您儲存的帳戶清單中選取相關性群組。

    請注意，建立您儲存帳戶時，您必須先選取相關性群組。 您無法移動現有帳戶至相關性的群組。 如需有關相關性群組的詳細資訊，請參閱[服務與相關性群組共同的位置](#service-co-location-with-an-affinity-group)下方。

    >[AZURE.IMPORTANT] 若要判斷哪些位置可供您的訂閱，您可以呼叫的[所有資源的提供者] 都清單中](https://msdn.microsoft.com/library/azure/dn790524.aspx)的作業。 清單中的 PowerShell 的提供者，請撥[取得 AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx)。 從.NET，使用[清單](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx)的方法 ProviderOperationsExtensions 類別。
    >
    >如需有關所提供的服務中的區域，此外，請參閱[Azure 區域](https://azure.microsoft.com/regions/#services)。


5. 如果您有一個以上的 Azure 訂閱時，就會顯示 [**訂閱**] 欄位。 在 [**訂閱**]，輸入您想要使用的儲存空間帳戶 Azure 訂閱。

6. 在**複寫**中，選取所要的等級複寫儲存帳戶。 建議的複寫選項是地理重複試驗，提供您資料的最大持續性。 如需 Azure 儲存體複寫選項的詳細資訊，請參閱[Azure 儲存體複寫](storage-redundancy.md)。

6. 按一下 [**建立儲存帳戶**]。

    可能需要幾分鐘，才能建立您儲存的帳戶。 若要查看狀態，您可以監視底部 Azure 傳統入口網站的通知。 建立儲存帳戶之後，您的新儲存帳戶有**線上**狀態，而且是可供使用。

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### <a name="storage-account-endpoints"></a>儲存帳戶端點

您儲存在 Azure 儲存體中的每個物件都有唯一的 URL 位址。 儲存體帳戶名稱表單的地址的子網域。 子網域和網域名稱，，每個服務的特定的組合表單*端點*儲存帳戶。

例如，如果您儲存的帳戶名稱為*mystorageaccount*，然後預設端點，為您儲存的帳戶是︰

- Blob 服務︰ http://*mystorageaccount*。 blob.core.windows.net

- 表格服務︰ http://*mystorageaccount*。 table.core.windows.net

- 佇列服務︰ http://*mystorageaccount*。 queue.core.windows.net

- 檔案服務︰ http://*mystorageaccount*。 file.core.windows.net

建立帳戶之後，您可以看到您儲存的帳戶[Azure 傳統入口網站](https://manage.windowsazure.com)中的儲存空間儀表板上的端點。

將物件的位置，在儲存帳戶附加至端點內建的 URL，以存取儲存帳戶中的物件。 Blob 地址，例如可能此格式︰ http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

您也可以設定自訂網域名稱用於您儲存的帳戶。 如需詳細資訊，請參閱[設定將自訂網域名稱用於您 blob 儲存體結束點](storage-custom-domain-name.md)。

### <a name="service-co-location-with-an-affinity-group"></a>服務共同位置與相關性群組

為*相關性群組*是以 Azure 儲存體帳戶 Azure 服務及 Vm 地理群組。 相關性群組可以尋找電腦負載，在相同的資料中心或附近目標使用者對象，以改善服務效能。 此外，沒有帳單的費用是所產生的出口從屬於相同的相關性群組的其他服務存取儲存帳戶中的資料。

> [AZURE.NOTE]  若要建立相關性] 群組中，開啟[Azure 傳統入口網站](https://manage.windowsazure.com)的 [<b>設定</b>] 區域，按一下<b>相關性群組</b>]，，然後按一下<b>新增相關性群組</b>] 或 [<b>新增</b>] 按鈕。 您也可以建立及使用 Azure 服務管理 API 管理相關性群組。 如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">相關性群組的操作</a>。

## <a name="view-copy-and-regenerate-storage-access-keys"></a>檢視、 複製與重新產生儲存便捷鍵

當您建立的儲存空間帳戶時，Azure 會產生兩個 512 位元儲存便捷鍵，在儲存帳戶存取時，可用來進行驗證。 提供兩個儲存便捷鍵，Azure 可讓您重新產生金鑰不會中斷您儲存的服務或該服務的存取權。

> [AZURE.NOTE] 我們建議您避免與其他人共用您的儲存空間便捷鍵。 若要允許儲存資源的存取權，但不提供您便捷鍵，您可以使用*共用的 access 簽章*。 共用的 access 簽名提供存取您的帳戶中的資源，您所定義的時間間隔與您所指定的權限。 如需詳細資訊，請參閱[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md) 。

在[Azure 傳統入口網站](https://manage.windowsazure.com)中，使用**管理鍵**在儀表板或 [**儲存**] 頁面上檢視、 複製與重新產生存取 Blob、 表格和佇列中服務所使用的儲存空間便捷鍵。

### <a name="copy-a-storage-access-key"></a>複製儲存便捷鍵  

您可以使用 [**管理鍵**複製連線字串中使用儲存空間便捷鍵。 連接字串需要儲存體帳戶名稱與金鑰] 以使用在 [驗證]。 設定存取 Azure 儲存服務的連線字串的相關資訊，請參閱[設定 Azure 儲存空間的連接字串](storage-configure-connection-string.md)。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [**儲存**]，然後按一下以開啟儀表板儲存體帳戶名稱。

2. 按一下 [**管理鍵**。

    隨即會開啟 [**管理便捷鍵**。

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. 若要複製的儲存空間便捷鍵，選取 [文字]。 按滑鼠右鍵，然後按一下 [**複製**]。

### <a name="regenerate-storage-access-keys"></a>重新產生儲存便捷鍵
我們建議您定期以協助保護您的儲存空間連接您儲存的帳戶變更便捷鍵。 兩個 access 的快速鍵，好讓您可以使用一個便捷鍵，當您重新產生其他便捷鍵以維護儲存帳戶的連線。

> [AZURE.WARNING] 重新產生您便捷鍵時，可能會影響 Azure，以及自己取決於儲存帳戶的應用程式的服務。 使用便捷鍵，以存取儲存帳戶的所有用戶端必須更新以使用新的金鑰。

**媒體服務**-如果您有取決於您儲存的帳戶的媒體服務，您必須重新同步處理便捷鍵媒體服務重新產生金鑰。

**應用程式**-如果您有 web 應用程式或使用的儲存空間帳戶的雲端服務，就會失去連線如果您重新產生金鑰，除非您回復索引鍵。 

**儲存探險者**-如果您使用的任何[儲存檔案總管] 應用程式](storage-explorers.md)，您可能必須更新這些應用程式所使用的儲存空間鍵。

以下是旋轉儲存 access 索引鍵的程序︰

1. 更新您要參照的儲存空間帳戶的次要便捷鍵的應用程式碼的連線字串。

2. 重新產生您儲存的帳戶的主要便捷鍵。 在[Azure 傳統入口網站](https://manage.windowsazure.com)，從儀表板或 [**設定**] 頁面上，按一下 [**管理索引鍵**]。 按一下 [主要便捷鍵，] 底下的 [**重新產生**，然後按一下**[是]**以確認您想要產生新的金鑰。

3. 更新您的程式碼參照的新的主要便捷鍵的連線字串。

4. 重新產生次要便捷鍵。

## <a name="delete-a-storage-account"></a>刪除儲存帳戶

若要移除您不再使用的儲存空間帳戶，請在儀表板或 [**設定**] 頁面上使用**刪除**。 **刪除**刪除整個儲存帳戶，包括所有二進位大型物件、 表格和佇列中的帳戶。

> [AZURE.WARNING] 無法還原已刪除的儲存空間帳戶或擷取任何它包含刪除之前的內容。 請務必備份任何您想要刪除帳戶之前儲存的項目。 這也適用於任何資源在帳戶，當您刪除 blob、 表格、 佇列中或檔案，將會永久刪除。
>
> 如果您儲存的帳戶包含 Azure 虛擬機器 VHD 檔案，就必須刪除任何圖像，可以刪除儲存帳戶之前，先使用這些 VHD 檔案的磁碟。 首先，如果它正在執行，停止虛擬機器，並將其刪除。 若要刪除的磁碟，瀏覽到**磁碟**] 索引標籤，然後刪除任何的磁碟。 若要刪除的圖像，瀏覽至 [**圖像**] 索引標籤，然後刪除儲存的帳戶中的任何影像。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [**儲存**]。

2. 按一下名稱，除了儲存帳戶項目中的任一處，然後按一下 [**刪除**。

     -或者-

    按一下以開啟儀表板，儲存體帳戶名稱，然後按一下 [**刪除**。

3. 按一下 [**是**] 以確認您要刪除的儲存空間的帳戶。

## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解 Azure 儲存空間，請參閱[Azure 儲存體文件](https://azure.microsoft.com/documentation/services/storage/)。
- 請造訪[Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)。
- [傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)
