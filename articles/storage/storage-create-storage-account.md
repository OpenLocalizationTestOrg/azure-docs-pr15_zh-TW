<properties
    pageTitle="如何建立、 管理，或刪除儲存帳戶 Azure 入口網站中的 |Microsoft Azure"
    description="建立新的儲存空間帳戶、 管理您的帳戶便捷鍵，或刪除 Azure 入口網站中的儲存空間帳戶。 深入了解標準和付費的儲存空間帳戶。"
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

Azure 儲存體帳戶提供儲存和存取您的 Azure 儲存體資料物件的唯一命名空間。 儲存帳戶中的所有物件都放在一起都付費群組。 根據預設，您的帳戶中的資料時，才可使用您的帳戶擁有者。

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>儲存帳戶帳單

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] 當您建立 Azure 虛擬機器時，儲存帳戶會為您自動建立部署位置如果您不在該位置已有儲存帳戶。 讓您不需要遵循下列步驟來建立的虛擬機器磁碟儲存帳戶。 儲存體帳戶名稱會根據的虛擬機器名稱。 請參閱更多詳細資料的[Azure 虛擬機器文件](https://azure.microsoft.com/documentation/services/virtual-machines/)。

## <a name="storage-account-endpoints"></a>儲存帳戶端點

您儲存在 Azure 儲存體中的每個物件都有唯一的 URL 位址。 儲存體帳戶名稱表單的地址的子網域。 子網域和網域名稱，，每個服務的特定的組合表單*端點*儲存帳戶。

例如，如果您儲存的帳戶名稱為*mystorageaccount*，然後預設端點，為您儲存的帳戶是︰

- Blob 服務︰ http://*mystorageaccount*。 blob.core.windows.net

- 表格服務︰ http://*mystorageaccount*。 table.core.windows.net

- 佇列服務︰ http://*mystorageaccount*。 queue.core.windows.net

- 檔案服務︰ http://*mystorageaccount*。 file.core.windows.net

> [AZURE.NOTE] Blob 儲存體帳戶只公開 Blob 服務端點。

將物件的位置，在儲存帳戶附加至端點內建的 URL，以存取儲存帳戶中的物件。 Blob 地址，例如可能此格式︰ http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

您也可以設定自訂網域名稱用於您儲存的帳戶。 對於傳統的儲存空間帳戶，請參閱[設定自訂網域名稱，為您 Blob 儲存體端點](storage-custom-domain-name.md)如需詳細資訊。 資源管理員儲存帳戶，這項功能已不新增至[Azure 入口網站](https://portal.azure.com)，但是您可以設定它使用 PowerShell。 如需詳細資訊，請參閱[設定 AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx)指令程式。  

## <a name="create-a-storage-account"></a>建立儲存的帳戶

1. [Azure 入口網站](https://portal.azure.com)登入。

2. 在中樞] 功能表中，選取 [**新增** -> **資料 + 的儲存空間** -> **儲存的帳戶**。

3. 輸入您儲存帳戶的名稱。 如需如何儲存體帳戶名稱會使用地址 Azure 儲存體中的物件詳細資訊，請參閱[儲存帳戶結束點](#storage-account-endpoints)。

    > [AZURE.NOTE] 儲存帳戶名稱必須介於 3 和 24 個字元的長度，而且可能包含數字和只有大小寫字母。
    >  
    > 您儲存體帳戶名稱中必須是唯一 Azure。 將會指出 Azure 入口網站，是否您選取儲存體帳戶名稱已在使用。

4. 指定要使用的部署模型︰**資源管理員**] 或 [**傳統**。 **資源管理員**是建議的部署模型。 如需詳細資訊，請參閱[瞭解資源管理員部署及傳統部署](../resource-manager-deployment-model.md)。

    > [AZURE.NOTE] Blob 儲存體帳戶只能建立使用資源管理員部署模型。

5. 選取的儲存空間的帳戶類型︰**一般用途**或**Blob 儲存體**。 **一般用途**是預設值。

    如果已選取 [**一般用途**，然後指定效能層︰**標準**或**進階版**。 預設為**標準**。 如需標準和付費儲存帳戶的詳細資訊，請參閱[Microsoft Azure 儲存體簡介](storage-introduction.md)和[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](storage-premium-storage.md)。

    如果選取 [ **Blob 儲存體**]，然後指定存取層︰**作用**或**製作出酷炫**。 預設值為**作用**。 請參閱[Azure Blob 儲存體︰ 很棒和熱層](storage-blob-storage-tiers.md)如需詳細資訊。

6. 選取的儲存空間帳戶複寫選項︰ **LRS**、 **GRS**、 **RA GRS**或**ZRS**。 預設值為**RA GRS**。 如需 Azure 儲存體複寫選項的詳細資訊，請參閱[Azure 儲存體複寫](storage-redundancy.md)。

7. 選取您要建立新的儲存空間帳戶的訂閱。

8. 指定新的資源群組，或選取現有的資源群組。 如需有關資源群組的詳細資訊，請參閱[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。

9. 選取您儲存的帳戶的地理位置。 如需有關所提供的服務中的區域，請參閱[Azure 區域](https://azure.microsoft.com/regions/#services)。

10. 按一下 [**建立**]，建立儲存帳戶]。

## <a name="manage-your-storage-account"></a>管理您儲存的帳戶

### <a name="change-your-account-configuration"></a>變更您的帳戶設定

建立您儲存的帳戶後，您可以修改其設定，例如，變更帳戶是使用複寫選項或變更 Blob 儲存體帳戶存取層。 在[Azure 入口網站](https://portal.azure.com)，瀏覽至您儲存的帳戶，按一下 [**所有設定**，然後都按一下要檢視及/或變更帳戶設定的**設定**。

> [AZURE.NOTE] 根據您所建立的儲存空間帳戶時選擇的效能層，可能無法使用某些複寫選項。

變更 [複製] 選項，將會變更您的價格。 如需詳細資訊，請參閱[Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)的頁面。

Blob 儲存體帳戶的變更存取層可能需支付費用除了變更您的價格，變更。 請[Blob 儲存體帳戶-價格與帳單](storage-blob-storage-tiers.md#pricing-and-billing)如需詳細資訊，參閱。

### <a name="manage-your-storage-access-keys"></a>管理您的儲存空間便捷鍵

當您建立的儲存空間帳戶時，Azure 會產生兩個 512 位元儲存便捷鍵，在儲存帳戶存取時，可用來進行驗證。 提供兩個儲存空間便捷鍵，Azure 可讓您重新產生金鑰不會中斷您儲存的服務或該服務的存取權。

> [AZURE.NOTE] 我們建議您避免與其他人共用您的儲存空間便捷鍵。 若要允許儲存資源的存取權，但不提供您便捷鍵，您可以使用*共用的 access 簽章*。 共用的 access 簽名提供存取您的帳戶中的資源，您所定義的時間間隔與您所指定的權限。 如需詳細資訊，請參閱[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md) 。

#### <a name="view-and-copy-storage-access-keys"></a>檢視和複製儲存便捷鍵

在[Azure 入口網站](https://portal.azure.com)，瀏覽至您儲存的帳戶，按一下 [**所有設定**]，然後都按一下**便捷鍵**以檢視、 複製與重新產生您的帳戶便捷鍵。 **便捷鍵**刀也包含使用主要和次要索引鍵，您可以在應用程式中複製要使用的預先設定的連接字串。

#### <a name="regenerate-storage-access-keys"></a>重新產生儲存便捷鍵

我們建議您定期以協助保護您的儲存空間連接您儲存的帳戶變更便捷鍵。 兩個 access 的快速鍵，好讓您可以使用一個便捷鍵，當您重新產生其他便捷鍵維護儲存帳戶的連線。

> [AZURE.WARNING] 重新產生您便捷鍵時，可能會影響 Azure，以及自己取決於儲存帳戶的應用程式的服務。 使用便捷鍵，以存取儲存帳戶的所有用戶端必須更新以使用新的金鑰。

**媒體服務**-如果您有取決於您儲存的帳戶的媒體服務，您必須重新同步處理便捷鍵媒體服務之後您重新產生金鑰。

**應用程式**-如果您有 web 應用程式或使用的儲存空間帳戶的雲端服務，就會失去連線如果您重新產生金鑰，除非您回復索引鍵。

**儲存探險者**-如果您使用的任何[儲存檔案總管] 應用程式](storage-explorers.md)，您可能必須更新這些應用程式所使用的儲存空間鍵。

以下是旋轉儲存 access 索引鍵的程序︰

1. 更新您要參照的儲存空間帳戶的次要便捷鍵的應用程式碼的連線字串。

2. 重新產生您儲存的帳戶的主要便捷鍵。 在**便捷鍵**刀中，按一下 [**重新產生 Key1**，然後再按一下**[是]**以確認您想要產生新的金鑰。

3. 更新您的程式碼，若要參照的新的主要便捷鍵的連線字串。

4. 以相同的方式來重新產生次要便捷鍵。

## <a name="delete-a-storage-account"></a>刪除儲存帳戶

若要移除您不再使用的儲存空間帳戶，請瀏覽至儲存的帳戶[Azure 入口網站](https://portal.azure.com)中，然後按一下 [**刪除**]。 刪除儲存帳戶刪除整個帳戶，包括在帳戶的所有資料。

> [AZURE.WARNING] 無法還原已刪除的儲存空間帳戶或擷取任何它包含刪除之前的內容。 請務必備份任何您想要儲存之前刪除帳戶。 這也適用於任何資源在帳戶，當您刪除 blob、 表格、 佇列中或檔案，將會永久刪除。

若要刪除 Azure 虛擬機器與相關聯的儲存空間帳戶，您必須先確定已刪除任何虛擬機器磁碟。 如果您沒有先刪除虛擬機器磁碟，然後當您嘗試刪除您儲存的帳戶，您會看到類似的錯誤訊息︰

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

如果儲存帳戶使用傳統部署模型，您可以遵循下列步驟，在[Azure 入口網站](https://manage.windowsazure.com)中移除虛擬機器磁碟︰

1. 瀏覽至[傳統 Azure 入口網站](https://manage.windowsazure.com)。
2. 瀏覽至 [虛擬機器] 索引標籤。
3. 按一下 [磁碟] 索引標籤。
4. 選取您資料的磁碟，然後按一下 [刪除的磁碟。
5. 若要刪除磁碟映像，瀏覽至 [圖像] 索引標籤，然後刪除儲存的帳戶中的任何影像。

如需詳細資訊，請參閱[Azure 虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)。

## <a name="next-steps"></a>後續步驟

- [Azure Blob 儲存體中︰ 炫和作用層](storage-blob-storage-tiers.md)
- [Azure 儲存複寫](storage-redundancy.md)
- [設定 Azure 儲存空間的連接字串](storage-configure-connection-string.md)
- [傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)
- 請造訪[Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)。
