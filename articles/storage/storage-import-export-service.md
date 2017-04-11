<properties
    pageTitle="若要將資料傳送至 Blob 儲存體使用匯入/匯出 |Microsoft Azure"
    description="瞭解如何建立匯入及匯出傳送資料至 blob 儲存體 Azure 傳統的入口網站中的工作。"
    authors="renashahmsft"
    manager="aungoo"
    editor="tysonn"
    services="storage"
    documentationCenter=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="renash"/>


# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>使用 Microsoft Azure 匯入/匯出服務傳送資料至 Blob 儲存體

## <a name="overview"></a>概觀

Azure 匯入/匯出服務可讓您對安全地傳輸傳送硬碟 Azure 資料中心 Azure blob 儲存體的大量資料。 您也可以使用這項服務，從 Azure blob 儲存體傳輸資料到硬碟機並運送至您的內部部署網站。 這項服務適合的情況下，您想要轉接數個 TBs 的資料，或從 Azure，但上傳或下載到的網路不可行有限的頻寬或高網路成本。

服務所需的硬碟應該元鎖定加密資料的安全性。 服務支援中的公用 Azure 所有區域的傳統的儲存空間帳戶。 您必須提供硬碟至其中一個支援本文稍後所指定的位置。

在本文中，您將學習深入瞭解匯入/匯出 Azure 服務，以及如何將運送 Azure Blob 儲存體中往返複製資料的磁碟機。

> [AZURE.IMPORTANT] 您可以建立及管理匯入與匯出作業使用傳統入口網站或[匯入/匯出服務 REST Api](http://go.microsoft.com/fwlink/?LinkID=329099)傳統的儲存空間。 資源管理員儲存帳戶不支援這一次。

## <a name="when-should-i-use-the-azure-importexport-service"></a>何時應使用 Azure 匯入/匯出服務？

您可以考慮使用 Azure 匯入/匯出服務上傳或下載在網路上的資料是太慢或取得額外的網路頻寬禁止成本。

您可以在案例中使用這項服務，如下所示︰

- 移轉至雲端的資料︰ 快速移至 Azure 的大量資料，並顯示成本有效。
- 內容通訊群組︰ 快速傳送給您的客戶網站的 [資料。
- 備份︰ 製作備份您的內部部署資料的儲存 Azure blob 儲存體。
- 資料修復︰ 復原大量 blob 儲存體中儲存的資料，並讓其傳送給您內部部署的位置。

## <a name="pre-requisites"></a>必要條件

在此區段中，我們有列出使用這項服務所需的必要條件。 請檢閱其謹慎之前磁碟機。

### <a name="storage-account"></a>儲存帳戶

您必須現有 Azure 訂閱及一或多個**傳統**儲存帳戶匯入/匯出服務使用。 每項工作可能會用來傳送資料至或只有一個傳統的儲存空間的帳戶。 換句話說，單一匯入/匯出作業無法跨多個儲存的帳戶。 建立新的儲存空間帳戶資訊，請參閱[如何建立儲存的帳戶](storage-create-storage-account.md#create-a-storage-account)。

### <a name="blob-types"></a>Blob 類型

您可以將資料複製到**區塊**blob 或**頁面**blob 使用 Azure 匯入/匯出服務。 相反地，您可以僅匯出**區塊**二進位大型物件、**頁面**blob 或**新增**blob 從 Azure 使用這項服務的儲存空間。

### <a name="job"></a>工作

若要開始匯入或匯出 Blob 儲存體的程序，第一次建立工作。 工作可以匯入工作] 或 [匯出工作︰

- 當您要傳送您擁有內部部署 blob Azure 儲存體帳戶中的資料時，請建立匯入作業。
- 當您想要轉接目前儲存為 blob 儲存體帳戶運送到您的硬碟中的資料時，請建立匯出工作。

當您建立工作時，您會通知匯入/匯出服務，您將會傳送至 Azure 資料中心的一或多個硬碟機集。

- 匯入工作，您將會傳送含有您資料的硬碟。
- 匯出作業，您將會傳送空白的硬碟。
- 您可以將運送最多 10 個硬碟每個作業。

您可以建立匯入或匯出使用[傳統入口網站](https://manage.windowsazure.com/)或[Azure 儲存匯入/匯出 REST API](http://go.microsoft.com/fwlink/?LinkID=329099)的工作。

### <a name="client-tool"></a>用戶端工具

建立**匯入**作業的第一個步驟是會傳送您的磁碟機準備匯入。 若要準備您的磁碟機，您必須將其連線至本機伺服器與本機伺服器上執行 Azure 匯入/匯出用戶端工具。 此用戶端工具幫助您將資料複製到磁碟機、 加密與 BitLocker 的磁碟機上的資料以及產生的磁碟機筆記本檔案。

日誌檔案會儲存您的工作和磁碟機，例如磁碟機序列和儲存體帳戶名稱的基本資訊。 這個筆記本檔案不會儲存在磁碟機。 在建立匯入工作會使用。 逐步執行本文稍後的提供建立工作的詳細資料。

用戶端工具僅適用於 64 位元 Windows 作業系統。 請參閱特定的作業系統版本支援的[作業系統](#operating-system)區段。

下載最新版[Azure 匯入/匯出用戶端工具](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)。 如需有關使用 Azure 匯入/匯出工具的詳細資訊，請參閱[Azure 匯入/匯出工具參照](http://go.microsoft.com/fwlink/?LinkId=329032)。

### <a name="hard-disk-drives"></a>硬碟

僅限 3.5 英吋 SATA II/III 內部硬碟支援的匯入/匯出服務搭配使用。 您可以使用硬碟最多 10 個 TB。
匯入工作、 將處理磁碟機上的只第一個資料量。 必須使用 NTFS 格式化資料量。
當資料複製到您的硬碟，您可以將它直接使用 SATA 連接器附加，或您可以附加外部使用外部 SATA II/III USB 介面卡。 我們建議您使用下列的外部 SATA II/III USB 介面卡其中一項︰

- Anker 68UPSATAA-02 BU
- Anker 68UPSHHDS BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

如果您有未列於上方的轉換程式，您可以嘗試執行 Azure 匯入/匯出工具準備的磁碟機，並查看運作之前購買支援轉換程式，使用您的轉換程式。

> [AZURE.IMPORTANT] 這項服務不支援外部硬碟機的內建的 USB 介面卡。 此外，您無法使用內外部的 HDD 的大小寫磁碟;請不要傳送外部 HDDs。

### <a name="encryption"></a>加密

磁碟機上的資料必須使用 BitLocker 磁碟機加密加密。 這會保護您的資料在傳輸時會時。

匯入工作，有兩種方式執行加密。 第一個方法是使用 / 加密參數時執行的用戶端工具在磁碟機準備工作階段。 第二個的方法是啟用 BitLocker 加密，以手動方式在磁碟機，和在用戶端工具命令列中指定的加密金鑰，在磁碟機準備工作階段。

匯出工作、 將資料複製到磁碟機後, 服務會加密使用 BitLocker 之前將其傳送給您的磁碟機。 加密金鑰會提供給您，透過 [傳統] 入口網站。  

### <a name="operating-system"></a>作業系統

您可以使用下列 64 位元作業系統的其中一個準備硬碟之前 Azure 的磁碟機使用 Azure 匯入/匯出工具︰

Windows 7 企業、 Windows 7 Ultimate Windows 8 Pro Windows 8 企業、 Windows 8.1 Pro Windows 8.1 企業、 Windows 10<sup>1</sup>，Windows Server 2008 R2 Windows Server 2012，Windows Server 2012 R2。 所有這些作業系統支援 BitLocker 磁碟機加密。

> [AZURE.IMPORTANT] <sup>1</sup>如果您使用 Windows 10 電腦來準備您的硬碟，請下載最新版 Azure 匯入/匯出] 工具。

### <a name="locations"></a>位置

Azure 匯入/匯出服務支援所有公用 Azure 儲存體帳戶中往返複製資料。 您可以將運送到下列位置的其中一個硬碟機。 如果您儲存的帳戶是不在此指定在公用 Azure 位置，您可以建立使用傳統入口網站或匯入/匯出 REST API 的工作，將會提供替代的傳送位置。

支援的傳送位置︰

- 設定適用於美國

- 西美制]。

- 設定適用於美國 2

- 美國中部

- 北美美國中部

- 美國中部南美洲

- 北美歐洲

- 西歐

- 中式地址

- 東南亞

- 澳大利亞東部

- 澳大利亞 Southeast

- 日本西部

- 日本東部

- 中央印度


### <a name="shipping"></a>傳送

**資料中心的傳送磁碟機︰**

在建立匯入或匯出作業時，您將會提供其中一個支援位置隨附磁碟機郵寄地的址。 提供的郵寄地址會取決於您儲存的帳戶的位置，但它可能不相同，您儲存帳戶的位置。

您可以使用 FedEx、 DHL、 畫面或美國郵政服務等航空公司隨附郵寄地址磁碟機。

**從資料中心的運送磁碟機︰**

在建立匯入或匯出作業時，您必須提供返回您的工作完成後，傳送磁碟機時使用 microsoft 寄件地址。 請確定您提供在處理時發生延遲狀況的有效回函地址。

您也必須提供有效 FedEx 或 DHL 航空公司帳戶用於 microsoft 貨運回的磁碟機的數字。 FedEx 帳戶數字，才能傳送回從美國及歐洲位置的磁碟機。 DHL 帳戶數字，才能傳送回從亞洲及澳大利亞位置的磁碟機。 如果您的確具備，您可以建立[FedEx](http://www.fedex.com/us/oadr/) （適用於美國及歐洲） 或[DHL](http://www.dhl.com/) （亞洲及澳大利亞） 航空公司帳戶。 如果您已經有帳戶數字，請確認是有效的電話公司。

在隨附的套件，您必須遵循在[Microsoft Azure 服務合約](https://azure.microsoft.com/support/legal/services-terms/)的條款。

> [AZURE.IMPORTANT] 請注意，您所傳送的實體媒體可能需要交互國際的框線。 您負責確保，您的實體媒體及資料是匯入及/或匯出根據的相關法律。 實體媒體之前，請洽詢您 advisers 驗證，您的媒體及資料可以合法情況下出貨識別的資料中心。 這是為了確保其及時到達 Microsoft。 例如，會交互國際框線任何套件需要附上 （除了屬於如果交叉框線內歐盟） 套件商業發票。 您無法列印出填滿航空公司網站上的商業發票的複本。 範例商業發票是 [DHL 商業發票] (http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) 或[FedEx 商業發票](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf)。 請確定 Microsoft 不匯出為上所指明。

## <a name="how-does-the-azure-importexport-service-work"></a>Azure 匯入/匯出服務如何運作？

您可以在您的內部部署網站和 Azure blob 儲存體建立工作，並傳送至 Azure 資料中心的硬碟機使用 Azure 匯入/匯出服務之間轉移資料。 您將運送每一個硬碟機是單一工作相關聯。 每項工作是以單一儲存帳戶相關聯。 檢閱[必要條件區段](#pre-requisites)謹慎若要深入瞭解此服務，例如支援 blob 類型、 磁碟類型、 位置及傳送的細節。

在此區段中，我們會說明在高層級的步驟匯入及匯出作業。 稍後在[快速入門] 區段](#quick-start)中，我們會提供逐步指示建立匯入及匯出工作。

### <a name="inside-an-import-job"></a>在 [匯入工作

在高的層級，匯入工作會包含下列步驟︰

- 決定要匯入的資料，您需要的磁碟機的數目。
- 找出目的地 blob 的 Blob 儲存體中的資料。
- 若要將資料複製到一或多個硬碟，然後使用 BitLocker 加密使用 Azure 匯入/匯出] 工具。  
- 使用 [傳統] 入口網站或匯入/匯出 REST API 目標傳統的儲存空間帳戶中建立匯入作業。 如果使用傳統入口網站上, 傳的磁碟機筆記本檔案。
- 提供的寄件地址及公司帳戶號碼用於傳送給您的磁碟機。
- 將運送建立工作期間所提供的傳送地址的硬碟。
- 更新傳送追蹤匯入工作詳細資料中的數字，並提交匯入作業。
- 磁碟機會收到，且處理的 Azure 資料中心。
- 磁碟機運送使用您的公司帳戶匯入作業中所提供的寄件地址。

    ![圖 1:Import 工作流程](./media/storage-import-export-service/importjob.png)


### <a name="inside-an-export-job"></a>在匯出作業

在高的層級，匯出工作會包含下列步驟︰

- 決定要匯出的資料，您需要的磁碟機的數目。
- 識別來源二進位大型物件或容器路徑 Blob 儲存體中的資料。
- 使用 [傳統] 入口網站或匯入/匯出 REST API 來源儲存帳戶中建立的匯出作業。
- 匯出作業中指定的來源二進位大型物件或容器路徑的資料。
- 用於傳送給您的磁碟機中提供的寄件地址及的公司帳戶號碼。
- 將運送建立工作期間所提供的傳送地址的硬碟。
- 更新傳送追蹤匯出工作詳細資料中的數字，並提交匯出工作。
- 磁碟機會收到和處理的 Azure 資料中心。
- 磁碟機加密使用 BitLocker;索引鍵，可透過 [傳統] 入口網站。  
- 磁碟機運送使用您的公司帳戶匯入作業中所提供的寄件地址。

    ![圖 2:Export 工作流程](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-status"></a>檢視您的工作狀態

您可以追蹤狀態匯入或匯出傳統入口網站中的工作。 瀏覽至您在 [傳統] 入口網站中的儲存空間帳戶，然後按一下 [**匯入/匯出**] 索引標籤。 您的工作清單會顯示在頁面上。 您可以篩選工作狀態、 工作名稱、 工作類型或追蹤編號清單。

您會看到其中一個動作工作狀態磁碟機是程序中的位置而定。

| 工作狀態   | 描述                                                                                                                                                                                                                                                                                                                                                                        |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 建立     | 建立您的工作，但尚不提供您傳送的資訊。                                                                                                                                                                                                                                                                                                |
| 傳送     | 建立您的工作，並提供您傳送的資訊。 **注意**︰ 當機傳送 Azure 資料中心時，狀態可能仍會顯示 「 出貨 」 的一些時間。 後所啟動的服務複製資料，狀態會變更 「 轉移 」。 如果您想要查看更具體的磁碟機的狀態，您可以使用匯入/匯出 REST API。 |
| 轉接 | 您的資料傳輸從硬碟 （適用於匯入工作），或您的硬碟 （適用於匯出作業）。                                                                                                                                                                                                                                                                 |
| 包裝    | 您的資料傳輸已完成，並傳送給您正在準備您的硬碟。                                                                                                                                                                                                                                                                             |
| 完成     | 您的硬碟出貨給您。                                                                                                                                                                                                                                                                                                                                      |

### <a name="time-to-process-job"></a>程序的工作時間

匯入/匯出作業異不同的因素，例如傳送時間的程序所花費的時間量工作類型、 類型及大小要複製的資料，並提供的磁碟的大小。 匯入/匯出服務沒有 SLA。 您可以使用 REST API 來追蹤工作進行深入分析。 [清單工作作業讓複製進度的指示有百分比完整的參數。 連絡我們如果您需要估計完成時間的要徑匯入/匯出工作。

### <a name="pricing"></a>價格

**磁碟機處理費用**

有磁碟機處理付費每個磁碟機處理屬於您匯入或匯出作業。 [Azure 匯入/匯出價格](https://azure.microsoft.com/pricing/details/storage-import-export/)，請參閱詳細資料。

**傳送成本**

當您將運送至 Azure 的磁碟機時，傳送航空公司您付費運輸成本。 當 Microsoft 傳回給您的磁碟機時，運費負責為您提供一次建立工作的公司帳戶。

**交易的成本**

Blob 儲存體將資料匯入沒有交易成本。 從 blob 儲存體匯出資料時，皆適用的標準出口費用。 如需交易成本的詳細資訊，請參閱[資料傳輸價格。](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>快速入門

在此區段中，我們會提供逐步指示建立匯入及匯出工作。 請確定您安裝前符合所有的[必要條件](#pre-requisites)。

## <a name="how-to-create-an-import-job"></a>如何建立匯入工作？

建立，將資料複製到您的 Azure 儲存體帳戶硬碟傳送一或多個磁碟機，其中包含指定的資料中心的資料匯入作業。 匯入工作傳達硬碟和複製、 目標儲存帳戶和 Azure 匯入/匯出服務傳送資訊至資料的詳細資料。 三個步驟，建立匯入作業。 首先，準備使用 Azure 匯入/匯出用戶端工具磁碟機。 第二，送出使用 [傳統入口網站的匯入作業。 第三運送至運送建立工作期間所提供的地址的磁碟機，和更新您的工作詳細資料的傳送資訊。   

> [AZURE.IMPORTANT] 您可以提交每個儲存帳戶的單一工作。 儲存帳戶匯入您所傳送的每一個磁碟機。 例如，假設您想要將兩個儲存帳戶匯入資料。 您必須使用另一個硬碟機的每個儲存帳戶，並建立個別的工作，每個儲存帳戶。

### <a name="prepare-your-drives"></a>準備磁碟機

使用 [匯入/匯出 Azure 服務匯入資料的第一個步驟是準備使用 Azure 匯入/匯出用戶端工具磁碟機。 請遵循下列步驟來準備您的磁碟機。

1.  識別要匯入的資料。 這可能是目錄與本機伺服器或網路共用上的獨立檔案。  

2.  決定您需要根據資料的總大小的磁碟機的數目。 採購所需 3.5 英吋 SATA II/III 硬碟機的數目。

3.  找出目標儲存帳戶、 容器、 虛擬目錄，以及二進位大型物件。

4.  決定目錄及/或將會複製到每一個硬碟機的獨立檔案。

5.  若要將資料複製到一或多個硬碟使用[Azure 匯入/匯出] 工具](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)。

    - Azure 匯入/匯出] 工具建立若要將您的資料來源的資料複製到 [硬碟機的複製工作階段。 單一複本工作階段，可以將單一目錄及其子目錄或單一檔案以及複製工具。

    - 如果您的來源資料橫跨多個目錄您可能需要指定多個複本工作階段。

    - 您準備要每一個硬碟機需要至少一份複本工作階段。

6.  您可以指定 / 加密參數，以便在硬碟上的 Bitlocker 加密。 或者，您也可以啟用 Bitlocker 加密，以手動方式在硬碟上的，執行工具時提供金鑰。

7.  Azure 匯入/匯出工具會產生準備的磁碟機筆記本檔案，每個磁碟機。 磁碟機筆記本檔案儲存在本機電腦，不是在本身的磁碟機上。 當您建立匯入工作時，您將上傳的筆記本檔案。 磁碟機筆記本檔案包含的磁碟機識別碼和 BitLocker 金鑰]，以及其他磁碟機相關資訊。
**重要事項**︰ 您準備要每一個硬碟機會導致筆記本檔案。 當您建立匯入作業使用傳統入口網站時，您必須上傳的一部分的匯入工作的磁碟機的所有筆記本檔案。 磁碟機不檔案將不會處理的筆記本。

8.  請勿修改完成磁碟準備後的硬碟上的筆記本檔案的資料。

以下是命令和準備使用 Azure 匯入/匯出用戶端工具硬碟機的範例。

Azure 匯入/匯出用戶端工具 PrepImport] 命令將目錄複製第一個複製工作階段︰

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**範例︰**

在下面的範例，我們所複製投影片的所有檔案和子 H:\Video 裝載 x: 硬碟機的目錄。 指定儲存帳戶索引鍵和稱為影片存放容器至的目的地儲存帳戶會被匯入資料。 如果沒有存放容器，它將會建立。 這個命令也將格式設定，並加密目標的硬碟。

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure 匯入/匯出用戶端工具 PrepImport] 命令將目錄複製後續複製工作階段︰

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

同一個硬碟機的後續複製工作階段，指定相同的筆記本檔案名稱，並提供的新的工作階段識別碼。有不需要再次提供儲存帳戶索引鍵和目標磁碟機或設定格式或加密磁碟機。 在此範例中我們所複製投影片的 H:\Photo 資料夾及子目錄及其相同的目標磁碟機，稱為 [相片存放容器至。

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure 匯入/匯出用戶端工具 PrepImport] 命令，將檔案複製第一個複製工作階段︰

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Azure 匯入/匯出用戶端工具 PrepImport] 命令，將檔案複製到後續複製工作階段︰

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**記住**︰ 根據預設，資料要匯入封鎖二進位大型物件。 您可以使用 /BlobType 參數，為頁面 Blob 的資料匯入。 例如，如果您要匯入其會裝載為磁碟 Azure VM VHD 檔案，必須將它們匯為頁面二進位大型物件。 如果您不確定要使用哪個 blob 類型，您可以指定 /blobType:auto，我們可協助您判斷正確。 在此情況下，將匯入所有 vhd 和 vhdx 的檔案，為頁面 Blob，其餘部分將匯都入為區塊二進位大型物件。

請參閱更多詳細資料，瞭解如何使用 Azure 匯入/匯出用戶端工具中[準備匯入的磁碟機](https://msdn.microsoft.com/library/dn529089.aspx)。

此外，請參閱[準備匯入工作的硬碟機的範例工作流程](https://msdn.microsoft.com/library/dn529097.aspx)的詳細逐步指示。  

### <a name="create-the-import-job"></a>建立匯入作業

1.  當您準備好您的磁碟機時，瀏覽至您在[傳統入口網站](https://manage.windowsazure.com)中的儲存空間帳戶，然後檢視儀表板。 在 [**快速瀏覽**，按一下 [**建立匯入作業**。 檢視的步驟，以表示您已準備好您的磁碟機，而且您有可用的磁碟機日誌檔案的核取方塊。

2.  步驟 1 中，提供連絡人的資訊負責匯入的工作和有效的寄件地址的人員。 如果您想要儲存的匯入工作的詳細資訊記錄資料，核取 [儲存**在我的 「 waimportexport' blob 容器中的詳細資訊記錄檔**] 選項。

3.  在 [步驟 2 上, 傳磁碟機日誌檔案取得在磁碟機準備步驟。 您需要上傳檔案的每一個您已準備好的磁碟機。

    ![建立匯入作業-步驟 3](./media/storage-import-export-service/import-job-03.png)

4.  步驟 3 中，輸入 [匯入工作的描述性名稱。 請注意，您所輸入的名稱可能包含大小寫字母、 數字、 連字號底線，必須以字母，以開始，而且不能包含空格。 您會使用您選擇不進行中及一旦完成時，追蹤您的工作的名稱。

    接下來，從清單中選取您的資料中心區域。 資料中心和地址，您必須提供您的套件，將會指出資料中心區域。 請參閱下方的常見問題集，如需詳細資訊。

5.  在步驟 4，從清單中選取寄電信業者，輸入您的公司帳戶號碼。 Microsoft 提供給您的磁碟機，您匯入工作完成後，會使用此帳戶。

    如果您有您追蹤的號碼，從清單中選取 [傳送電信業者，然後輸入您的追蹤號碼。

    如果您執行不追蹤數目，選擇 [**我將提供我傳送的資訊匯入作業後我所提供我套件**，然後完成匯入程序。

6. 您有出貨套件，返回至**匯入/匯出]**頁面的 [傳統] 入口網站中，在您儲存的帳戶之後，請輸入您的追蹤號碼請從清單中，選取您的工作，並選擇**傳送的資訊**。 瀏覽執行精靈，然後在 [步驟 2 中輸入您追蹤的電話號碼。

    如果沒有更新追蹤號碼內 2 週的建立工作，便會到期的工作。

    如果工作是在建立、 傳送或轉移狀態，您也可以更新精靈步驟 2 中的您公司帳戶號碼。 包裝狀態工作後，您無法更新您的工作的公司帳戶號碼。

7. 您可以在入口網站的儀表板上追蹤您的工作進度。 請參閱前一節中的每個工作狀態的意義檢視[您的工作狀態](#viewing-your-job-status)。

## <a name="how-to-create-an-export-job"></a>如何建立匯出的工作？

建立通知匯入/匯出服務，您將會傳送資料中心的一或多個空白磁碟機，讓資料可以從您儲存的帳戶中匯出到磁碟和磁碟，然後傳送給您的匯出作業。

### <a name="prepare-your-drives"></a>準備磁碟機

準備匯出作業的磁碟機，建議使用下列測試檢查︰

1. 核取所需使用 [Azure 匯入/匯出] 工具 PreviewExport 命令磁碟的數目。 如需詳細資訊，請參閱[匯出工作預覽磁碟機使用方式](https://msdn.microsoft.com/library/azure/dn722414.aspx)。 協助您 blob 您選取，請根據您要使用的磁碟機大小預覽磁碟機使用方式。

2. 確認您可以讀/寫到發行匯出作業的硬碟。

### <a name="create-the-export-job"></a>建立匯出作業

1.  若要建立匯出工作，請瀏覽至您儲存的帳戶在[傳統入口網站](https://manage.windowsazure.com)，並檢視儀表板。 在 [**快速瀏覽**，按一下 [**建立匯出的工作**，繼續執行精靈。

2.  在步驟 2 中，提供連絡人的資訊負責匯出作業的人員。 如果您想要儲存匯出作業的詳細資訊記錄資料，核取 [儲存**在我的 「 waimportexport' blob 容器中的詳細資訊記錄檔**] 選項。

3.  在步驟 3 中，指定您想要從儲存帳戶匯出至空白的磁碟機或磁碟機 blob 資料。 您可以選擇要匯出所有 blob 資料中儲存的帳戶，或您可以指定哪些 blob 或匯出二進位大型物件的設定。

    若要指定要匯出的 blob，使用 [**等於**選取器，並指定 blob，以容器名稱開頭的相對路徑。 若要指定根容器使用*$root* 。

    若要指定所有的二進位大型物件的前置詞開始，請使用 [**開始使用**選取器，並指定前置詞，以斜線開頭 '/'。 前置詞可能容器名稱、 完成容器名稱或完整的容器名稱後面接著 blob 名稱的前置詞的前置字元。

    下表顯示有效 blob 路徑的範例︰

    選取器|Blob 路徑|描述
    ---|---|---
    開始使用|/|匯出所有 blob 儲存體帳戶
    開始使用|/$root /|匯出根容器中的所有二進位大型物件
    開始使用|/book|匯出所有的二進位大型物件在任何容器中的**活頁簿**的前置詞的開頭
    開始使用|/music/|匯出所有的二進位大型物件的容器**音樂**
    開始使用|/ 音樂/愛|匯出所有二進位大型物件的容器**音樂**的前置詞**愛**
    等於|$root/logo.bmp|匯出 blob **logo.bmp**根容器中
    等於|videos/story.mp4|匯出 blob **story.mp4**在容器的**視訊**

    此螢幕擷取畫面所示，您必須提供正確的格式，以在處理期間，避免發生錯誤的 blob 路徑。

    ![建立匯出作業-步驟 3](./media/storage-import-export-service/export-job-03.png)


4.  在步驟 4 中，輸入匯出作業的描述性名稱。 大小寫字母、 數字、 連字號，可能會包含您所輸入的名稱和底線，必須以字母，以開始，而且不能包含空格。

    資料中心區域會指出您必須將運送套件的資料中心。 請參閱下方的常見問題集，如需詳細資訊。

5.  在步驟 5，從清單中，選取傳回電信業者，輸入您的公司帳戶號碼。 Microsoft 提供給您所有的磁碟機，您匯出作業完成後，會使用此帳戶。

    如果您有您追蹤的號碼，從清單中選取 [傳送電信業者，然後輸入您的追蹤號碼。

    如果您執行不追蹤數目，選擇 [**我將提供匯出作業後我所提供我套件我傳送的資訊**，然後完成匯出程序。

6. 您有出貨套件，返回至**匯入/匯出]**頁面的 [傳統] 入口網站中，在您儲存的帳戶之後，請輸入您的追蹤號碼請從清單中，選取您的工作，並選擇**傳送的資訊**。 瀏覽執行精靈，然後在 [步驟 2 中輸入您追蹤的電話號碼。

    如果沒有更新追蹤號碼內 2 週的建立工作，便會到期的工作。

    如果工作是在建立、 傳送或轉移狀態，您也可以更新精靈步驟 2 中的您公司帳戶號碼。 包裝狀態工作後，您無法更新您的工作的公司帳戶號碼。

    > [AZURE.NOTE] 如果要匯出的 blob 是使用中，複製到硬碟機的時間，Azure 匯入/匯出服務會 blob 的快照，然後複製快照集。

7.  您可以在 [傳統] 入口網站中的儀表板上追蹤您的工作進度。 請參閱每個工作狀態的意義在前一節中的 「 檢視您的工作狀態 」。

8.  您收到的磁碟機匯出資料之後，您可以檢視和複製產生的磁碟機服務 BitLocker 金鑰。 瀏覽至您在 [傳統] 入口網站中的儲存空間帳戶，然後按一下 [匯入/匯出] 索引標籤。 從清單中，選取您匯出的工作，然後按一下 [檢視金鑰] 按鈕。 BitLocker 金鑰顯示，如下所示︰

    ![檢視匯出作業的 BitLocker 的按鍵](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

請為矩形使用這項服務時，會遇到客戶最常見的問題，移到常見問題集一節。

## <a name="frequently-asked-questions"></a>常見問題集 ##


**將會多久後我磁碟機達到資料中心複製我的資料？**

若要複製的時間不盡相同，根據不同的因素工作類型，例如提供類型與大小要複製的資料，磁碟的大小，與現有的工作量。 它可以而異數天的幾個星期，這些因素。 因此，很難提供一般的估計值。 服務嘗試的複製多個磁碟機平行時可能最佳化您的工作。 如果您有時間要徑匯入/匯出作業，請連絡我們的估計值。

**何時應使用 Azure 匯入/匯出服務？**
其中一個應該考慮使用 Azure 匯入匯出如果上傳或下載到網路所需的時間約為估計超過 7 天。 您可以計算將會多久使用任何線上計算機，或透過[下載](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip)位於 Azure 匯入匯出 REST API 範例在[資料傳輸速度計算機](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html)Azure 範例存放庫中的項目。 這不是完全的計算，但只要概略的指示。

**可以使用 Azure 匯入/匯出服務與資源管理員儲存帳戶嗎？**

否，您無法複製資料，或直接使用 [匯入/匯出 Azure 服務資源管理員儲存帳戶。 服務只支援傳統的儲存空間的帳戶。 資源管理員儲存帳戶支援會即將推出。 或者，您可以傳統的儲存空間的帳戶，將資料匯入，並將它移轉至您的資源管理員的儲存空間帳戶使用[AzCopy](storage-use-azcopy.md)或 CopyBlob。

**我可以複製 Azure 檔案使用 Azure 匯入/匯出服務嗎？**

否，Azure 匯入/匯出服務僅支援區塊二進位大型物件和頁面二進位大型物件。 不支援包括 Azure 檔案、 表格及佇列中其他儲存空間類型。

**為 Azure 匯入/匯出服務不適用於 CSP 訂閱？**

否，Azure 匯入/匯出服務不支援 CSP 訂閱。 未來將會新增支援。

**可以略過的匯入作業的磁碟機準備步，或可以準備的磁碟機不複製？**

必須使用 Azure 匯入/匯出用戶端工具來準備您想要將運送匯入資料的任何磁碟機。 您必須使用用戶端工具，將資料複製到的磁碟機。

**需要建立匯出工作時，請執行任何磁碟準備工作嗎？**

否，但某些預先檢查建議。 核取所需使用 [Azure 匯入/匯出] 工具 PreviewExport 命令磁碟的數目。 如需詳細資訊，請參閱[匯出工作預覽磁碟機使用方式](https://msdn.microsoft.com/library/azure/dn722414.aspx)。 協助您 blob 您選取，請根據您要使用的磁碟機大小預覽磁碟機使用方式。 此外，請您可以從讀取和寫入發行匯出作業的硬碟。

**如果我不小心傳送的不符合 HDD 支援需求，會發生什麼情況？**

Azure 資料中心的受支援的需求，您會傳回不符合的磁碟機。 如果只在封裝的磁碟機部分符合的支援需求，請將會處理這些磁碟機，並不符合需求的磁碟機會傳回給您。

**我可以取消我的工作？**

當您建立或傳送其狀態時，您可以取消作業。

**多久傳統入口網站中檢視已完成的工作的狀態？**

您可以檢視已完成工作的狀態 90 天。 已完成的工作會被刪除 90 天。

**如果我想要匯入或匯出 10 封以上的磁碟機，該怎麼辦？**

匯入或匯出作業可以參照 10 的磁碟機中匯入/匯出服務的單一工作。 如果您想要傳送 10 封以上的磁碟機，您可以建立多個工作。 與相同的工作相關聯的磁碟機必須在同一個套件一起運送。

**可以使用不在建議清單中的 USB SATA 卡嗎？**

如果您有未列於上方的轉換程式，您可以嘗試執行 Azure 匯入/匯出工具準備的磁碟機，並查看運作之前購買支援轉換程式，使用您的轉換程式。

**服務設定格式傳回之前的磁碟機？**

[否]。 使用 BitLocker 加密所有的磁碟機。

**可以向 Microsoft 購買匯入/匯出作業的磁碟機？**

[否]。 您必須將運送您自己的磁碟機，兩個匯入及匯出工作。

**匯入作業完成後，將我的資料看起來像什麼儲存帳戶？將會保留我的目錄階層嗎？**

準備時硬碟匯入工作，目的由 /dstdir 指定︰ 參數。 這是在從硬碟機的資料會複製到其中的儲存空間帳戶目的容器。 這項目的容器，從硬碟資料夾建立虛擬目錄且 blob 建立的檔案。

**如果磁碟機已存在於儲存帳戶中的檔案，將會服務覆寫現有 blob 儲存體帳戶中？**

當您準備的磁碟機，您可以指定是否要覆寫的目標檔案，或略過使用參數稱為 /Disposition: < 重新命名 | 無覆寫 | 覆寫 >。 根據預設，服務會重新命名新檔案而不是覆寫現有的二進位大型物件。

**是相容於 32 位元作業系統的 Azure 匯入/匯出用戶端工具？**
[否]。 用戶端工具僅適用於 64 位元 Windows 作業系統。 請參閱支援的作業系統版本的作業系統] 區段中[必要條件](#pre-requisites)的完整清單。

**應該硬碟以外的任何項目中加入我的套件？**

請將運送只有您的硬碟。 不包含 power 供應纜線或 USB 纜線等項目。

**我是否必須運送我使用 FedEx 或 DHL 的磁碟機？**

您可以將運送使用任何已知的航空公司，例如 FedEx、 DHL、 畫面或美國郵政服務資料中心的磁碟機。 不過，傳送給您的資料中心的磁碟機，您必須提供 FedEx 帳戶數字，在 [美國和歐盟或在亞洲及澳大利亞區域內 DHL 帳戶數字。

**是否有與傳送我的磁碟機國際任何限制？**

請注意，您所傳送的實體媒體可能需要交互國際的框線。 您負責確保，您的實體媒體及資料是匯入及/或匯出根據的相關法律。 之前傳送實體媒體]，請確認您為顧問媒體和資料可以合法情況下傳送識別的資料中心。 這是為了確保其及時到達 Microsoft。

**建立工作，郵寄地址時，我儲存帳戶位置不同的位置。我該怎麼辦？**

某些儲存帳戶位置會對應到替代的傳送位置。 先前可用的傳送位置也可以暫時對應至其他位置。 隨時檢查運送之前磁碟機建立工作期間所提供的地址。

**為何我的工作狀態，在 [傳統入口網站說 「 貨運 」 時的航空公司網站顯示我的套件已傳送嗎？**

在 [傳統] 入口網站中的狀態從變更傳送轉接何時開始處理的磁碟機。 如果您的磁碟機達到設備，但尚未啟動處理，您的工作狀態會顯示為傳送。

**當我的磁碟機，航空公司要求資料中心的連絡人名稱和電話號碼。我應該提供什麼？**

建立工作期間的電話號碼提供給您。 如果您需要的連絡人名稱，請與我們連絡waimportexport@microsoft.com，我們會為您提供這項資訊。

**可以使用 Azure 匯入/匯出服務 PST 信箱和 SharePoint 資料複製到 O365 嗎？**

請參閱[匯入 PST 檔案或 SharePoint 資料至 Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)。

**可以使用 Azure 匯入/匯出服務，將我的備份離線複製到 Azure 備份服務嗎？**

請參閱[Azure 備份離線備份工作流程](../backup/backup-azure-backup-import-export.md)。

## <a name="see-also"></a>另請參閱︰

- [設定 Azure 匯入/匯出用戶端工具](https://msdn.microsoft.com/library/dn529112.aspx)

- [傳送含有 AzCopy 命令列公用資料](storage-use-azcopy.md)

- [Azure 匯入匯出 REST API 範例](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
