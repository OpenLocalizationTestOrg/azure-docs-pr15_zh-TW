<properties
    pageTitle="複製或移動資料與 AzCopy 存放裝置 |Microsoft Azure"
    description="您可以使用 AzCopy 公用程式來移動或複製資料，或從 blob、 表格和檔案內容。 將資料複製到 Azure 儲存體中，從本機的檔案，或複製或儲存帳戶之間的資料。 輕鬆地將您的資料移轉到 Azure 儲存體。"
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="micurd"/>

# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>傳送以 AzCopy 命令列公用程式的資料

## <a name="overview"></a>概觀

AzCopy 是專為使用簡單的命令，以獲得最佳效能的 Microsoft Azure Blob、 檔案及資料表儲存體中往返複製資料的命令列公用程式視窗。 在您儲存的帳戶，或是儲存帳戶，可以從一個物件複製資料到另一個。

> [AZURE.NOTE] 本指南假設您已熟悉[Azure 儲存體](https://azure.microsoft.com/services/storage/)。 如果沒有，閱讀[簡介 Azure 儲存體](storage-introduction.md)文件會很有幫助。 最重要的是，您必須建立[儲存帳戶](storage-create-storage-account.md#create-a-storage-account)才能開始使用 AzCopy。

## <a name="download-and-install-azcopy"></a>下載並安裝 AzCopy

### <a name="windows"></a>Windows

下載[最新版本的 AzCopy](http://aka.ms/downloadazcopy)。

### <a name="maclinux"></a>Mac/Linux

AzCopy 不適用於 Mac/Linux OSs。 不過，Azure CLI 是適當的替代方案 Azure 儲存體中往返複製資料。 若要深入瞭解的 [[使用與 Azure 儲存體 Azure CLI](storage-azure-cli.md)讀取。

## <a name="writing-your-first-azcopy-command"></a>撰寫您的第一個 AzCopy 命令

基本 AzCopy 命令的語法是︰

    AzCopy /Source:<source> /Dest:<destination> [Options]

開啟命令視窗，然後瀏覽至您的電腦-AzCopy 安裝目錄位置`AzCopy.exe`可執行的所在。 如有需要，您可以新增至您的系統路徑的 AzCopy 安裝位置。 根據預設，安裝 AzCopy`%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy`或`%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`。

下列範例會示範許多情況下，從 Microsoft Azure Blob、 檔案，以及資料表若要複製的資料。 請參閱[AzCopy 參數](#azcopy-parameters)章節的每一個範例中所使用的參數的詳細說明。

## <a name="blob-download"></a>Blob︰ 下載

### <a name="download-single-blob"></a>下載單一 blob

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

請注意，如果資料夾`C:\myfolder`不存在，會建立郵件 AzCopy，並將其下載`abc.txt `到新的資料夾。

### <a name="download-single-blob-from-secondary-region"></a>下載單一 blob 從第二個區域

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

請注意，您必須有讀取權限地理多餘的儲存，啟用。

### <a name="download-all-blobs"></a>下載所有二進位大型物件

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

假設下列 blob 位於中指定的容器︰  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

下載作業之後，目錄`C:\myfolder`會包含下列檔案︰

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

如果您不指定選項`/S`，沒有 blob 會下載。

### <a name="download-blobs-with-specified-prefix"></a>下載與指定的前置字元的二進位大型物件

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

假設下列 blob 位於中指定的容器。 所有開頭為前置字元的二進位大型物件`a`會下載︰

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

下載作業之後，資料夾`C:\myfolder`會包含下列檔案︰

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

前置詞適用於表單 blob 名稱的第一部分的虛擬目錄。 在上述範例中，虛擬目錄不符合指定的前置詞，因此就不會下載。 此外，如果選項`\S`不指定，AzCopy 將不會下載任何二進位大型物件。

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>設定匯出檔案同來源二進位大型物件的上次修改時間

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

您也可以根據其上次修改時間下載作業排除二進位大型物件。 例如，如果您想要排除二進位大型物件的上次修改時間是相同或更新的目標檔案，新增`/XN`選項︰

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

如果您想要排除二進位大型物件的上次修改時間是相同或早於目的地檔案，新增或`/XO`選項︰

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## <a name="blob-upload"></a>Blob︰ 上傳

### <a name="upload-single-file"></a>單一檔案上傳

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

如果指定的目的容器不存在，會建立 AzCopy，並將它上傳檔案。

### <a name="upload-single-file-to-virtual-directory"></a>上傳單一檔案至虛擬目錄

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

如果指定的虛擬目錄不存在，AzCopy 會上傳的檔案名稱中包含的虛擬目錄 (*例如*，`vd/abc.txt`上述範例中)。

### <a name="upload-all-files"></a>上傳的所有檔案

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

指定選項`/S`上傳至 Blob 儲存體遞迴，也就是說，所有的子資料夾及檔案會將它上傳以及的指定目錄的內容。 例如，假設下列檔案位於中資料夾`C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

上傳作業之後，容器會包含下列檔案︰

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

如果您不指定選項`/S`，AzCopy 會無法上傳遞迴。 上傳作業之後，容器會包含下列檔案︰

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>上傳符合特定的模式的檔案

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

假設下列檔案位於資料夾`C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

上傳作業之後，容器會包含下列檔案︰

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

如果您不指定選項`/S`，AzCopy 只會上傳不存在於虛擬目錄的二進位大型物件︰

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>指定目的地 blob 的 MIME 內容類型

根據預設，AzCopy 設定至目的地 blob 的內容類型`application/octet-stream`。 開始使用版本 3.1.0，您可以明確地指定透過選項的內容類型`/SetContentType:[content-type]`。 這個語法設定所有 blob 的內容類型中的上傳作業。

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

如果您指定`/SetContentType`沒有值，然後 AzCopy 會將每個 blob] 或檔案的內容類型，根據其副檔名。

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## <a name="blob-copy"></a>Blob︰ 複製

### <a name="copy-single-blob-within-storage-account"></a>複製單一 blob 儲存體帳戶內

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

當您複製的 blob 儲存體帳戶內時，[伺服器端複製](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)進行。

### <a name="copy-single-blob-across-storage-accounts"></a>複製單一 blob 儲存體帳戶之間

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

當您複製 blob 儲存體帳戶之間時，會執行[伺服器端複製](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)作業。

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>第二區域中的單一 blob 複製到主要區域

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

請注意，您必須有讀取權限地理多餘的儲存，啟用。

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>儲存帳戶之間複製單一 blob 和其快照

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

複製作業之後，目標容器會包含 blob 和其快照。 假設上述範例中的 blob 具有兩個快照，容器會包含下列 blob 和快照集︰

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>同步儲存帳戶之間複製二進位大型物件

依預設 AzCopy 非同步複製兩個儲存端點之間的資料。 因此，複製作業會執行使用快速具有龐大的方式沒有 SLA 的備用頻寬容量背景 blob 會複製，而 AzCopy 會定期檢查複製狀態，直到複製完成或失敗。

`/SyncCopy`選項可確保複製作業會收到一致的速度。 AzCopy 執行的同步複本下載至指定的來源複製到本機的記憶體，blob，然後將其上傳至 Blob 儲存體目的地。

    AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`可能會產生比較非同步複製其他出口成本，建議的方法是在您的來源儲存帳戶，以避免出口成本為相同的區域中的 Azure VM 中使用此選項。

## <a name="file-download"></a>檔案︰ 下載

### <a name="download-single-file"></a>單一檔案下載

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

如果指定的來源是 Azure 檔案共用]，然後您必須指定確切的檔案名稱 (*例如* `abc.txt`) 下載一個檔案，或指定選項`/S`下載共用遞迴中的所有檔案。 嘗試指定的檔案模式 」 和 「 選項`/S`一起會導致錯誤。

### <a name="download-all-files"></a>下載所有檔案

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

請注意，不會下載任何空白的資料夾。

## <a name="file-upload"></a>檔案︰ 上傳

### <a name="upload-single-file"></a>單一檔案上傳

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### <a name="upload-all-files"></a>上傳的所有檔案

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

請注意將不會上載任何空白的資料夾。

### <a name="upload-files-matching-specified-pattern"></a>上傳符合特定的模式的檔案

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a name="file-copy"></a>檔案︰ 複製

### <a name="copy-across-file-shares"></a>複製整個檔案共用

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### <a name="copy-from-file-share-to-blob"></a>複製檔案共用] 以 blob

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

請注意，不支援非同步複製到頁面 Blob 檔案儲存空間。

### <a name="copy-from-blob-to-file-share"></a>Blob 複製到檔案共用

    AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### <a name="synchronously-copy-files"></a>同步複製檔案

您可以指定`/SyncCopy`選項，以從檔案儲存至的檔案儲存空間、 檔案儲存至 Blob 儲存體和檔案儲存至 Blob 儲存體同步複製資料、 AzCopy 會下載至本機記憶體的來源資料，並再次上載至目的地。

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

當複製從檔案儲存空間 Blob 儲存體的預設 blob 類型區塊 blob 時，使用者可以指定選項`/BlobType:page`若要變更目的地 blob 類型。

請注意，`/SyncCopy`可能會產生其他出口成本比較非同步的複本、 建議的方法是 Azure VM 這是您的來源儲存帳戶，以避免出口成本為相同的區域中使用此選項。

## <a name="table-export"></a>表格︰ 匯出

### <a name="export-table"></a>匯出表格

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy 會寫入指定的目標資料夾中的資訊清單的檔案。 資訊清單檔案用於匯入程序，以找出所需的資料檔案，並執行資料驗證。 資訊清單檔案預設會使用下列的命名慣例︰

    <account name>_<table name>_<timestamp>.manifest

使用者也可以指定選項`/Manifest:<manifest file name>`設定的資訊清單的檔案名稱。

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### <a name="split-export-into-multiple-files"></a>分割成多個檔案匯出

    AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy 分割的資料檔案名稱中使用*大量索引*，以便區分多個檔案。 大量索引是由兩個部分、*分割索引鍵的範圍索引*和*分割檔案索引*所組成。 兩個索引是起始。

分割鍵範圍索引會為 0，如果使用者未指定選項`/PKRS`。

例如，假設 AzCopy 會產生兩個資料檔後使用者指定選項`/SplitSize`。 可能會產生的資料檔案名稱︰

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

請注意，可能的最小值] 選項`/SplitSize`為 32 MB。 AzCopy 如果 Blob 儲存體指定的目的地，分割的資料檔案，其大小達到 blob 的大小限制 (200 GB) 後，不論是否選項`/SplitSize`已指定使用者。

### <a name="export-table-to-json-or-csv-data-file-format"></a>匯出表格至 JSON 或 CSV 資料檔案格式

預設 AzCopy 會將表格匯出到 JSON 資料檔案。 您可以指定選項`/PayloadFormat:JSON|CSV`若要將表格匯出為 JSON 或 CSV。

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

當指定內容 csv、 AzCopy 也會產生檔案副檔名的結構描述檔案`.schema.csv`的每個資料檔案。

### <a name="export-table-entities-concurrently"></a>同時匯出表格項目

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy 就會開始要匯出的實體，當使用者指定選項並行作業`/PKRS`。 每一項作業︰ 匯出一個的磁碟分割範圍。

請注意，同時運算的數目也由選項`/NC`。 AzCopy 使用核心處理器數目的預設值為`/NC`當複製表格項目，即使`/NC`未指定。 當使用者指定選項`/PKRS`、 AzCopy 使用較小的兩個值-分割鍵與隱含或明確地指定範圍並行作業-以判斷同時啟動運算的數目。 如需詳細資訊，請輸入`AzCopy /?:NC`在命令列。

### <a name="export-table-to-blob"></a>匯出表格至 blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy 會以下列命名慣例 blob 容器產生 JSON 資料檔案︰

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

產生的 JSON 資料檔追蹤的最小的中繼資料的內容格式。 此內容格式的詳細資訊，請參閱[表格服務作業的內容格式](http://msdn.microsoft.com/library/azure/dn535600.aspx)。

請注意，當匯出二進位大型物件的資料表，AzCopy 會暫時儲存本機資料檔案下載表格實體然後將這些項目上傳至 blob。 這些暫存資料檔案放到筆記本的檔案資料夾的預設路徑 」<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>」，您可以指定選項/z: [日誌-檔案的資料夾]，若要變更的筆記本檔案資料夾的位置，因此變更暫時資料檔案的位置。 暫時資料檔案的大小取決表格實體的大小和選項 /SplitSize 中所指定的大小，雖然已上傳至 blob 後，會立即刪除暫存資料檔案的本機磁碟，請確定您有本機磁碟空間不足，無法在刪除前，儲存這些暫存資料檔案。

## <a name="table-import"></a>表格︰ 匯入

### <a name="import-table"></a>匯入資料表

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

選項`/EntityOperation`指出如何插入表格中的項目。 可能的值為︰

- `InsertOrSkip`︰ 跳過現有的實體，或如果不存在於資料表，請插入新的實體。
- `InsertOrMerge`︰ 合併現有的實體，或如果不存在於資料表，請插入新的實體。
- `InsertOrReplace`︰ 會取代現有的實體，或如果不存在於資料表，請插入新的實體。

請注意，您無法指定選項`/PKRS`在匯入的案例。 與匯出案例中，您必須在其中指定選項不同`/PKRS`若要開始並行作業，AzCopy 會依預設並行作業時啟動匯入資料表。 預設的並行作業開始數等於核心處理器數目;不過，您可以在其中指定不同的數字的同時選項`/NC`。 如需詳細資訊，請輸入`AzCopy /?:NC`在命令列。

請注意，AzCopy 僅支援 JSON，不 CSV 匯入。 不支援從使用者建立 JSON 資料表匯入 AzCopy 然後資訊清單檔案。 兩個這些檔案必須來自 AzCopy 表格匯出。 若要避免錯誤，請勿修改匯出的 JSON 或資訊清單檔案。

### <a name="import-entities-to-table-using-blobs"></a>若要使用二進位大型物件的 [資料表匯入項目

假設 Blob 容器包含下列︰ JSON 代表 Azure 資料表和其伴隨的資訊清單檔案的檔案。

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

您可以執行下列命令，將表格，使用該 blob 容器中的資訊清單檔案匯入項目︰

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## <a name="other-azcopy-features"></a>其他 AzCopy 功能

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>只複製的目的地不存在的資料

`/XO`和`/XN`參數可讓您從複製，分別排除較舊版本或更新版本的來源資源。 如果您只想要複製的目的地不存在的來源資源，您可以指定 AzCopy 命令中的兩個參數︰

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

注意︰ 這不是支援來源或目標是表格時。

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>使用回應檔，以指定的命令列參數

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

您可以回應檔案中包含任何 AzCopy 命令列參數。 AzCopy 處理檔案中的參數如同在命令列中，指定執行檔案內容的直接替代。

假設回應檔名為`copyoperation.txt`，其中包含下列幾行。 每個 AzCopy 參數可以指定在同一行

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

或在不同的線條︰

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy 如果將會失敗參數分成兩行，如下所示的`/sourcekey`參數︰

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>使用多個回應檔案指定命令列參數

假設回應檔名為`source.txt`，指定來源容器︰

    /Source:http://myaccount.blob.core.windows.net/mycontainer

和回應檔名為`dest.txt`，指定檔案系統中的目的地資料夾︰

    /Dest:C:\myfolder

和回應檔名為`options.txt`，指定 AzCopy 選項︰

    /S /Y

若要打電話 AzCopy 與這些回應檔案，均位於目錄`C:\responsefiles`，使用這個命令︰

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy 處理此命令，就像是如果您將包含所有命令列上的個別參數︰

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### <a name="specify-a-shared-access-signature-sas"></a>指定共用的 access 簽章 (SA)

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

您也可以指定 SA 容器 URI 上︰

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### <a name="journal-file-folder"></a>日誌檔案資料夾

每次您命令發給 AzCopy，它會檢查是否筆記本檔案中的預設資料夾，或是否存在的資料夾中，指定透過這個選項。 如果筆記本檔案不存在於任一處，AzCopy 作業視為新，並產生新的筆記本檔案。

如果不存在的筆記本檔案，AzCopy 會檢查您輸入的命令列是否符合筆記本檔案中的命令列。 如果符合並列文字] 命令，AzCopy 繼續未完成的作業。 如果不相符，系統會提示您 [覆寫的筆記本檔案，以開始新的作業，或取消目前的作業。

如果您要使用的預設位置的筆記本檔案︰

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

如果您省略選項`/Z`，或指定選項`/Z`不含資料夾的路徑，如下圖所示上述 AzCopy 建立日誌檔案預設的位置，也就是`%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`。 如果筆記本檔案已經存在，AzCopy 繼續根據日誌檔案的作業。

如果您要指定自訂筆記本檔案的位置︰

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

如果不存在，此範例會建立日誌檔案。 如果存在，AzCopy 繼續根據日誌檔案的作業。

如果您想要恢復 AzCopy 作業︰

    AzCopy /Z:C:\journalfolder\

此範例會繼續上次的作業可能會無法完成。

### <a name="generate-a-log-file"></a>產生的記錄檔

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

如果您指定選項`/V`不提供詳細資訊記錄的檔案路徑，然後 AzCopy 所建立的記錄檔預設位置，也就是`%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`。

否則，您可以建立記錄檔中的自訂的位置︰

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

請注意，如果您指定選項的相對路徑`/V`，例如`/V:test/azcopy1.log`，然後詳細資訊的記錄會建立在名為子資料夾中的目前工作目錄`test`。

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>指定開始同時運算的數目

選項`/NC`指定同時複製運算的數目。 根據預設，AzCopy 開始並行作業來增加資料傳輸處理量數目。 為表格作業，同時運算的數目等於您擁有的處理器數目。 Blob 和檔案的操作，同時運算的數目為等於 8 時間的必須的處理器數目。 如果您正在執行 AzCopy 低頻寬網路上，您可以指定較小的 /NC 若要避免資源競爭所造成的錯誤。

### <a name="run-azcopy-against-azure-storage-emulator"></a>針對 Azure 儲存模擬器執行 AzCopy

您可以針對[Azure 儲存模擬器](storage-use-emulator.md)二進位大型物件，來執行 AzCopy:

    AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

和資料表︰

    AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## <a name="azcopy-parameters"></a>AzCopy 參數

如下所述的 AzCopy 參數。 您也可以在使用 AzCopy 輸入下列命令，從命令列說明的其中一項︰

- 如詳細 AzCopy 的命令列說明︰`AzCopy /?`
- 如有任何 AzCopy 參數的詳細說明︰`AzCopy /?:SourceKey`
- 命令列的範例︰`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ 來源: 「 來源 」

指定要複製的來源資料來源。 來源可以是檔案系統目錄、 blob 容器、 blob 虛擬、 儲存檔案共用、 儲存檔案目錄或 Azure 的資料表。

**適用於︰**二進位大型物件的檔案，表格

### <a name="destdestination"></a>/ 目的地: 「 目的地 」

指定要複製到目的地。 目的地可以是檔案系統目錄、 blob 容器、 blob 虛擬目錄、 儲存檔案共用、 儲存檔案目錄或 Azure 的資料表。

**適用於︰**二進位大型物件的檔案，表格

### <a name="patternfile-pattern"></a>/ 模式: 「 檔案模式 」

指定檔案模式，指出要複製的檔案。 取決於行為 /Pattern 參數的資料來源的位置，以及 [遞迴模式] 選項的目前狀態。 透過選項 /s 指定遞迴模式

如果指定的來源檔案系統中是目錄，然後標準萬用字元，而所提供的檔案模式比對目錄中的檔案。 如果選項指定 /S，然後 AzCopy 也符合特定的模式針對目錄下的任何一個子資料夾中的所有檔案。

如果指定的來源是 blob 容器或虛擬目錄，然後不會套用萬用字元。 如果指定 /S，然後 AzCopy] 選項會將指定的檔案模式解譯 blob 前置字元。 如果未指定 /S，選項然後 AzCopy 比對檔案模式比對完全 blob 名稱。

如果指定的來源是 Azure 檔案共用]，則您必須指定確切的檔案名稱 (例如 abc.txt) 複製一個檔案，或指定選項 /S 共用遞迴中複製的所有檔案。 嘗試指定的檔案模式 」 和 「 選項 /S 一起會導致錯誤。

AzCopy 使用區分大小寫符合 /Source 是 blob 容器或 blob 的虛擬目錄，並使用 [在所有其他的情況下不區分大小寫相符。

在任何檔案模式指定的預設檔案模式是*。* 檔案系統位置或空白前置字元的位置，Azure 儲存體。 不支援指定多個檔案模式。

**適用於︰**二進位大型物件檔案

### <a name="destkeystorage-key"></a>/ DestKey: 「 儲存金鑰 」

指定目的地資源的儲存空間帳戶金鑰。

**適用於︰**二進位大型物件的檔案，表格

### <a name="destsassas-token"></a>/ DestSAS: 「 sa 權杖 」

（如果適用的話），指定目的地讀取和寫入權限與共用 Access 簽章 (SA)。 括住用雙引號，SA，因為它可能包含特殊命令列的字元。

如果目的地資源是 blob 容器、 檔案共用或表格，您可以指定後面接著 SA 權杖，此選項，或您可以指定 SA 屬於目的 blob 容器、 檔案共用或表格的 URI，沒有這個選項。

如果來源與目的地是兩個二進位大型物件，目的地 blob 必須位於相同的儲存空間帳戶為來源 blob 內。

**適用於︰**二進位大型物件的檔案，表格

### <a name="sourcekeystorage-key"></a>/ SourceKey: 「 儲存金鑰 」

指定來源資源的儲存空間帳戶金鑰。

**適用於︰**二進位大型物件的檔案，表格

### <a name="sourcesassas-token"></a>/ SourceSAS: 「 sa 權杖 」

（如果適用的話），請以閱讀和清單的權限，來源指定共用 Access 簽章。 括住用雙引號，SA，因為它可能包含特殊命令列的字元。

如果來源資源是 blob 容器，並且鍵或 SA 都不會提供，將會透過匿名存取讀取 blob 容器。

如果來源檔案共用或表格，必須提供索引鍵或 SA。

**適用於︰**二進位大型物件的檔案，表格

### <a name="s"></a>/S

指定複製作業的遞迴模式。 遞迴模式中 AzCopy 會複製所有的二進位大型物件或符合指定的檔案模式，包括子資料夾中的檔案。

**適用於︰**二進位大型物件檔案

### <a name="blobtypeblock--page--append"></a>/ BlobType: 「 封鎖 」 |「 頁 」 |「 新增 」

指定目的地 blob 區塊 blob、 頁面 blob 或新增 blob。 只有在您要上傳 blob 時，適用於此選項。 否則，則會產生錯誤。 如果目的地是 blob，不指定此選項，根據預設，AzCopy 會建立區塊 blob。

**適用於︰**二進位大型物件

### <a name="checkmd5"></a>/ CheckMD5

計算 MD5 雜湊下載的資料，並確認 MD5 雜湊儲存在 blob，或檔案的內容 MD5 屬性符合計算結果雜湊。 [MD5 檢查已關閉根據預設，所以您必須指定下載資料時，請執行 MD5 核取此選項。

Azure 儲存體不保證 MD5 雜湊 blob 的檔案儲存為最新的筆記。 是修改 blob 或檔案時，更新 MD5 客戶的責任。

AzCopy 一律會 Azure blob 或檔案的內容 MD5 屬性之後上傳至服務。  

**適用於︰**二進位大型物件檔案

### <a name="snapshot"></a>/ 快照

指出是否要傳送快照集。 這個選項只有在來源是 blob 時才有效。

此格式重新命名傳輸的 blob 快照︰ blob 名稱 （快照時間）.extension

根據預設，不會複製快照集。

**適用於︰**二進位大型物件

### <a name="vverbose-log-file"></a>/ V [詳細資訊的記錄檔]

輸出到記錄檔的詳細資訊的狀態訊息。

根據預設，名稱為詳細記錄檔中的 AzCopyVerbose.log `%LocalAppData%\Microsoft\Azure\AzCopy`。 如果您指定現有的檔案位置這個選項，詳細資訊的記錄會附加至該檔案。  

**適用於︰**二進位大型物件的檔案，表格

### <a name="zjournal-file-folder"></a>/ Z: [日誌-檔案的資料夾]

指定恢復作業的筆記本檔案資料夾。

AzCopy 永遠支援繼續如果已中斷作業。

如果不指定此選項，或將其指定的資料夾路徑沒有，AzCopy 將預設的位置，也就是 %localappdata%\microsoft\azure\azcopy 中建立日誌檔案。

每次您命令發給 AzCopy，它會檢查是否筆記本檔案中的預設資料夾，或是否存在的資料夾中，指定透過這個選項。 如果筆記本檔案不存在於任一處，AzCopy 作業視為新，並產生新的筆記本檔案。

如果不存在的筆記本檔案，AzCopy 會檢查您輸入的命令列是否符合筆記本檔案中的命令列。 如果符合並列文字] 命令，AzCopy 繼續未完成的作業。 如果不相符，系統會提示您 [覆寫的筆記本檔案，以開始新的作業，或取消目前的作業。

作業成功完成時，會刪除筆記本檔案。

請注意，不支援恢復從舊版 AzCopy 所建立的筆記本檔案操作。

**適用於︰**二進位大型物件的檔案，表格

### <a name="parameter-file"></a>/@:"parameter-file"

指定包含參數的檔案。 AzCopy 處理檔案中的參數，就如同指定命令列上。

在回應檔案中，您可以在單一行中，指定多個參數，或在其本身的列中指定每個參數。 請注意個別的參數無法橫跨多個線條。

回應檔案可以包含註解行的開頭為 # 符號。

您可以指定多個回應檔案。 不過，請注意，不支援 AzCopy 巢狀回應檔案。

**適用於︰**二進位大型物件的檔案，表格

### <a name="y"></a>/Y

隱藏所有 AzCopy 確認提示。

**適用於︰**二進位大型物件的檔案，表格

### <a name="l"></a>/L

指定清單作業複製資料。

AzCopy 會解讀使用這個選項，以執行命令列，但這不是模擬的選項 /L 和計算將複製多少物件，您可以指定 /V 同時檢查哪些物件將複製的詳細資訊的記錄檔中的選項。

這個選項的行為也會受到來源資料的位置和遞迴模式選項 /S 和檔案模式] 選項的 /Pattern 目前狀態。

使用此選項時，AzCopy 會需要此來源位置的清單和已讀取 」 權限。

**適用於︰**二進位大型物件檔案

### <a name="mt"></a>/MT

設定要相同來源 blob 或檔案的 [下載的檔案上次修改時間。

**適用於︰**二進位大型物件檔案

### <a name="xn"></a>/XN

排除較新的來源資源。 將不會複製資源，如果上次修改的時間來源的相同或較目的地。

**適用於︰**二進位大型物件檔案

### <a name="xo"></a>/XO

排除較舊的來源資源。 如果來源的上次修改的時間相同，將不會複製資源或早於目的地。

**適用於︰**二進位大型物件檔案

### <a name="a"></a>/A

上傳已設定為保存屬性的檔案。

**適用於︰**二進位大型物件檔案

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

上傳的任何指定的屬性設定的檔案。

可用的屬性包括︰

- R = 唯讀檔案
- = 準備好要封存的檔案
- S = 系統檔案
- H = 隱藏的檔案
- C = 壓縮的檔案
- N = 一般檔案
- E = 加密檔案
- T = 暫存檔案
- O = 離線檔案
- 我 = 非索引的檔案

**適用於︰**二進位大型物件檔案

### <a name="xarashcnetoi"></a>/ XA: [RASHCNETOI]

排除任何指定的屬性設定的檔案。

可用的屬性包括︰

- R = 唯讀檔案
- = 準備好要封存的檔案
- S = 系統檔案
- H = 隱藏的檔案
- C = 壓縮的檔案
- N = 一般檔案
- E = 加密檔案
- T = 暫存檔案
- O = 離線檔案
- 我 = 非索引的檔案

**適用於︰**二進位大型物件檔案

### <a name="delimiterdelimiter"></a>/ 分隔符號:"分隔符號"

指出用來分隔 blob 名稱的虛擬目錄的分隔符號字元。

根據預設，AzCopy 用途 / 做為分隔符號字元。 不過，AzCopy 支援使用任何一般的字元 (例如@,# 或 %) 做為分隔符號。 如果您需要加入其中一個命令列上這些特殊字元，請用雙引號檔案名稱。

這個選項只適用於下載 blob 的。

**適用於︰**二進位大型物件

### <a name="ncnumber-of-concurrent-operations"></a>/ NC: 」 數字的位並行-作業 」

指定一個運算的數目。

依預設 AzCopy 開始並行作業來增加資料傳輸處理量數目。 請注意，大量並行低頻寬環境中的作業可能會使不勝負荷的網路連線防止完全完成作業。 節流根據實際可用的網路頻寬並行作業。

並行作業的上限為 512。

**適用於︰**二進位大型物件的檔案，表格

### <a name="sourcetypeblob--table"></a>/ 可以: 「 Blob 」 |「 資料表 」

指定的`source`資源是 blob 的本機的開發環境，儲存在模擬器中執行。

**適用於︰**二進位大型物件的資料表

### <a name="desttypeblob--table"></a>/ DestType: 「 Blob 」 |「 資料表 」

指定的`destination`資源是 blob 的本機的開發環境，儲存在模擬器中執行。

**適用於︰**二進位大型物件的資料表

### <a name="pkrskey1key2key3"></a>/ PKRS: 「 key1 #key2 #key3 #...」

分割磁碟分割鍵範圍，才能啟用 [匯出表格資料平行，增加匯出作業的速度。

如果未指定此選項，則 AzCopy 會使用單一執行緒匯出表格項目。 例如，如果使用者指定 /PKRS: 「 aa #bb 」，然後 AzCopy 開始的三個並行作業。

每一項作業︰ 匯出其中三個分割鍵範圍，如下所示︰

  [第一個資料分割-鍵，aa）

  [aa，bb)

  [bb，最後一個分割索引鍵]

**適用於︰**表格

### <a name="splitsizefile-size"></a>/ SplitSize: 「 檔案大小]

指定分割大小 (mb)，所允許的最小值匯出的檔案為 32。

如果不指定此選項，AzCopy 會將表格資料匯出至單一檔案。

如果表格資料匯出至 blob，匯出的檔案大小到達 blob 大小的 200 GB 限制，然後 AzCopy 會分割匯出的檔案，即使未指定此選項。

**適用於︰**表格

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: 「 InsertOrSkip 」 |「 InsertOrMerge 」 |「 InsertOrReplace 」

指定表格資料匯入行為。

- InsertOrSkip-跳過現有的實體，或如果不存在於資料表，請插入新的實體。

- 合併現有的實體 InsertOrMerge-，或如果不存在於資料表，請插入新的實體。

- InsertOrReplace-會取代現有的實體，或如果不存在於資料表，請插入新的實體。

**適用於︰**表格

### <a name="manifestmanifest-file"></a>/ 資訊清單: 「 資訊清單檔案 」

表格匯出及匯入作業，請指定資訊清單的檔案。

這是選用選項匯出作業期間，AzCopy 會產生的資訊清單的檔案，以預先定義的名稱，如果未指定此選項。

尋找資料檔匯入作業期間必要時，此選項。

**適用於︰**表格

### <a name="synccopy"></a>/ SyncCopy

指出是否要同步複製二進位大型物件或兩個 Azure 儲存體端點之間的檔案。

AzCopy 依預設會使用伺服器端非同步複本。 指定此選項即可同步複本，其下載至本機記憶體的二進位大型物件或檔案，然後將它們上載至 Azure 儲存體。

複製 Blob 儲存體，檔案儲存空間，或從到檔案儲存空間或反向操作的 Blob 儲存體中的檔案時，您可以使用這個選項。

**適用於︰**二進位大型物件檔案

### <a name="setcontenttypecontent-type"></a>/ SetContentType: 「 內容類型 」

指定目的地二進位大型物件或檔案的 MIME 內容類型。

AzCopy 設定預設資料流應用程式/8 位元 blob 或檔案的內容類型。 您可以設定的所有二進位大型物件或檔案的內容類型明確指定的值，此選項。

如果您指定的值，沒有這個選項，然後將設定 AzCopy，每個 blob 或檔案的內容類型，根據其副檔名。

**適用於︰**二進位大型物件檔案

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: 「 JSON 」 |「 CSV 」

指定表格匯出的資料檔案的格式。

如果未指定此選項，則根據預設，AzCopy 會匯出 JSON 格式表格資料檔案。

**適用於︰**表格

## <a name="known-issues-and-best-practices"></a>已知的問題及最佳作法

### <a name="limit-concurrent-writes-while-copying-data"></a>複製資料時的限制並行寫入

當您複製二進位大型物件或 AzCopy 的檔案時，請記住，另一個應用程式可能會修改資料時所複製投影片。 如果可能的話，請確定不會遭到所複製的資料修改複製作業期間。 例如，當複製 VHD Azure 虛擬機器相關聯，確認沒有其他應用程式目前寫入 VHD。 若要執行此動作的好方法是租用複製資源。 或者，您可以先建立 VHD 的快照，然後複製 [快照集。

如果您無法防止其他應用程式時要複製撰寫二進位大型物件或檔案，然後請記住，工作完成時，複製的資源已不再與來源的資源完整的不一致性致歉。

### <a name="run-one-azcopy-instance-on-one-machine"></a>在一部電腦上執行 AzCopy 執行個體之一。
AzCopy 設計用來最大化的加速資料傳輸您電腦的資源，因此我們建議您執行只有一個 AzCopy 執行個體，一個在電腦上，並指定選項`/NC`如果您需要更多的同時作業。 如需詳細資訊，請輸入`AzCopy /?:NC`在命令列。

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>啟用 AzCopy MD5 fips 相容時您 「 使用 FIPS 相容演算法加密，雜湊，以及簽章 」。
預設的 AzCopy 使用.NET MD5 實作計算 MD5 複製物件時，但還有一些需要 AzCopy 啟用 FIPS 相容 MD5 設定的安全性需求。

您可以建立 app.config 檔案`AzCopy.exe.config`屬性`AzureStorageUseV1MD5`，將其放離題與 AzCopy.exe。

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

屬性 」 AzureStorageUseV1MD5 」 • True-的預設值，AzCopy 會使用.NET MD5 實作。
• False – AzCopy 會使用標準 MD5 演算法 FIPS。

請注意，在您的 Windows 電腦上的預設為停用 fips 相容，您可以在 [執行] 視窗中輸入 secpol.msc 並核取 [安全性設定這個選項]-> [本機原則]-> [安全性選項]-> [系統密碼編譯︰ 使用 fips 相容加密，雜湊，以及簽章。

## <a name="next-steps"></a>後續步驟

如需有關 Azure 儲存體和 AzCopy 的詳細資訊，請參閱下列資源。

### <a name="azure-storage-documentation"></a>Azure 儲存的文件︰

- [Azure 儲存空間的簡介](storage-introduction.md)
- [如何使用從.NET Blob 儲存體](storage-dotnet-how-to-use-blobs.md)
- [如何使用從.NET 檔案儲存空間](storage-dotnet-how-to-use-files.md)
- [如何使用.NET 從資料表儲存體](storage-dotnet-how-to-use-tables.md)
- [如何建立、 管理，或刪除儲存帳戶](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Azure 儲存部落格文章︰
- [介紹 Azure 儲存資料移動文件庫預覽](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy︰ 介紹同步複製與自訂內容類型](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy︰ 宣佈使用的 AzCopy 3.0 一般可用性加上表格和檔案的支援 AzCopy 4.0 預覽版本](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy︰ 最佳化大型複製案例](http://go.microsoft.com/fwlink/?LinkId=507682)
- [讀取權限地理多餘的儲存空間 AzCopy︰ 支援](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [重新啟動模式與 SA 權杖 AzCopy︰ 傳輸資料](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy︰ 使用跨帳戶複製 Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [Azure Blob 的 AzCopy︰ 上傳/下載檔案](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
