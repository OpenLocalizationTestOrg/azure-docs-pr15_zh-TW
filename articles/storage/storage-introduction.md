<properties
    pageTitle="Access 2007 中的儲存 |Microsoft Azure"
    description="Azure 儲存體，Microsoft 線上的資料儲存在雲端的概觀。 瞭解如何使用您的應用程式中的最佳使用雲端儲存空間方案。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Microsoft Azure 儲存體簡介

## <a name="overview"></a>概觀

Azure 儲存空間是現代依賴持續性、 可用性和延展性以符合其客戶的需求的應用程式的雲端儲存空間方案。 請閱讀此文章、 開發人員]、 [IT 專業人員及 [商務決策製造商可以進一步瞭解︰

- Azure 儲存體是什麼，並如何您可以利用您雲端行動、 伺服器，以及桌面應用程式
- 您可以儲存哪些類型的資料與 Azure 儲存體服務︰ blob （物件） 資料、 NoSQL 表格資料、 佇列中的郵件，並在之後歸檔共用。
- 如何管理 Azure 儲存體中資料的存取
- 如何 Azure 儲存體資料進行永久性透過重複性和複寫
- 請前往下一步來建立您的第一個 Azure 儲存體應用程式

執行與 Azure 儲存體快速，請參閱[開始使用 5 分鐘內 Azure 儲存體](storage-getting-started-guide.md)。

工具、 文件庫及其他資源的 [使用 Azure 儲存體的詳細資訊，請參閱[後續步驟](#next-steps)。

## <a name="what-is-azure-storage"></a>什麼是 Azure 儲存空間？

雲端運算的其資料 – 正是為什麼 Microsoft 開發 Azure 儲存體需要調整、 長期，及高度可用儲存空間的應用程式的可讓新案例。 除了讓大型的應用程式，以支援新的情況，Azure 儲存體也提供儲存基礎的 Azure 虛擬機器，其強固進一步建築。

Azure 儲存體是人連線擴充，您可以儲存，以及處理程序數百 tb 支援大型資料的資料的所需的科學、 財務分析和媒體應用程式。 或者您可以儲存少量的小型企業版網站所需的資料。 落在您的需求，無論您是付款僅適用於已儲存的資料。 Azure 儲存體目前儲存數十 trillions 的唯一的客戶物件，以及平均處理數百萬秒的要求。

Azure 儲存體是彈性，您可以設計應用程式的大型的全域對象，並不按比例縮放這些應用程式，視需要-從儲存的資料量，對該要求的數目。 您可以使用僅適用於使用什麼方法，並使用時，只。

Azure 儲存體使用自動分割系統的自動負載平衡資料的流量。 這表示，為您的應用程式放大的需求，Azure 儲存體自動配置以符合其適當的資源。

Azure 儲存為可從全球，從任何類型的應用程式中的任何位置存取是否在雲端，在桌面上，在內部部署伺服器上，或在行動電話上執行或平板電腦的裝置。 在行動應用程式儲存在裝置上的資料子集及處理一組完整的資料儲存在雲端的位置的案例中，您可以使用 Azure 儲存體。

Azure 儲存支援使用 （包括 Windows 和 Linux） 的作業系統與各種不同的程式設計語言 （包括.NET，Java、 Node.js、 Python、 [注音標示、 PHP 與 c + + 和行動裝置的程式設計語言） 一組方便的開發的用戶端。 Azure 儲存體也公開資料資源透過簡單的 REST Api，可提供給任何用戶端可以傳送及接收透過 HTTP/HTTPS 的資料。

Azure Premium 儲存空間提供高效能、 低延遲磁碟支援 I/O 大量負載執行 Azure 虛擬機器上的商務連絡人。 與 Azure 進階版的儲存空間，您可以將多個常設資料磁碟附加至虛擬機器，並將其設定為符合您的效能需求。 Azure 進階版儲存區中的最大 I/O 效能 SSD 磁碟所支援的每個資料磁碟。 請參閱[Premium 儲存空間︰ Azure 虛擬機器負載高效能儲存空間](storage-premium-storage.md)如需詳細資訊。

## <a name="introducing-the-azure-storage-services"></a>介紹 Azure 儲存服務

Azure 儲存體會提供下列四個服務︰ Blob 儲存體、 資料表儲存體、 佇列中儲存和檔案儲存空間。

- 非結構化的物件資料儲存 blob 儲存體。 Blob 可以是任何類型的文字或二進位資料，例如文件、 媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為 「 物件儲存空間。
- 資料表儲存體儲存結構化資料集。 資料表儲存體是 NoSQL 鍵屬性的資料存放區，可快速開發及快速存取大量資料。
- 佇列中的儲存空間提供可靠的訊息的工作流程處理和雲端服務的元件之間的通訊。
- 檔案儲存空間提供使用標準 SMB 通訊協定的舊版應用程式共用的儲存空間。 Azure 虛擬機器和雲端服務可以透過裝載共用的應用程式元件之間共用檔案的資料與內部部署的應用程式可存取檔案共用檔案的服務 REST API 透過中的資料。

Azure 儲存體帳戶是安全的帳戶，可讓您存取 Azure 儲存體的服務。 您儲存的帳戶提供您儲存資源唯一的命名空間。 下圖顯示在儲存帳戶中的 Azure 儲存體資源之間的關聯︰

![Azure 儲存體資源](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Blob 儲存體

要讓使用者使用大量的非結構化的物件資料儲存在雲端，Blob 儲存體提供效益和擴充的解決方案。 您可以使用 Blob 儲存體來儲存內容，例如︰

- 文件
- 社交的資料，例如相片、 影片、 音樂和部落格
- 備份的檔案，電腦、 資料庫及裝置
- 圖像與文字的 web 應用程式
- 設定資料的雲端應用程式
- 顯示較大的資料，例如記錄檔和其他大型資料集

每個 blob 會組織成容器。 容器也會提供有用的方式來指派給群組物件的安全性原則。 儲存帳戶最多可以包含任何數目的容器，然後容器可以包含的儲存空間帳戶的 500 TB 容量限制二進位大型物件的任何數字。  

Blob 儲存體提供三種類型的二進位大型物件、 封鎖二進位大型物件、 新增二進位大型物件，然後頁面二進位大型物件 （磁碟）。

- 封鎖 blob 適用於串流和儲存雲端的物件，且儲存的文件、 媒體檔案備份等不錯的選擇。
- 附加 blob 區塊 blob 類似，但適用於附加作業。 新增 blob 都可以更新只能由結尾加入新的區塊。 附加 blob 的記錄，新的資料必須只寫入 blob 的結束位置，例如案例不錯的選擇。
- 針對代表 IaaS 磁碟最佳化頁面二進位大型物件並支援隨機寫入，可能是 1 TB 的大小。 Azure 虛擬機器網路附加的 IaaS 磁碟是儲存為網頁 blob VHD。

網路限制讓上傳或下載至 Blob 儲存體的資料連線的不實際的大型資料集，您可以發行至 Microsoft，以匯入或匯出資料直接從資料中心的硬碟。 請參閱[使用 Microsoft Azure 匯入/匯出服務傳送資料至 Blob 儲存體](storage-import-export-service.md)。

## <a name="table-storage"></a>資料表儲存體

新的應用程式通常會要求延展性與彈性的軟體需求的上一個產生的資料存放區。 資料表儲存體提供可用性、 人連線可調整儲存空間，讓您的應用程式可以自動調整，以符合使用者的需求。 資料表儲存體是 Microsoft 的 NoSQL 主要/屬性存放區 – 有 schemaless 設計和建立傳統的關聯式資料庫的不同。 與 schemaless 資料儲存區，您可以輕鬆為您的應用程式 evolve 的需求，調整您的資料。 資料表儲存體是易於使用，開發人員可以快速建立應用程式。 存取資料是快速且所有類型的應用程式的效益。  資料表儲存體通常是大幅低於成本傳統的 SQL 類似的大量資料內容。

資料表儲存體是主要屬性存放區，表示資料表中的每個值儲存為輸入的屬性名稱。 屬性名稱可以用於篩選和指定選取準則。 屬性和其值的集合組成的實體。 由於資料表儲存體 schemaless，之相同資料表中的兩個實體最多可以包含的內容，不同的集合，而這些屬性可為不同類型。

您可以使用資料表儲存體儲存彈性的資料集，例如使用者資料的 web 應用程式、 通訊錄、 裝置的詳細資訊及其他任何類型的中繼資料，您的服務要求。  您可以儲存任何數目的項目在資料表中，並且儲存帳戶可能包含表格的儲存空間帳戶的容量限制的任何數字。

二進位大型物件和佇列，例如開發人員可以管理並 access 的資料表使用標準的其餘部分的儲存空間通訊協定，但是資料表儲存體也支援的 OData 通訊協定子集簡化進階查詢功能與啟用 JSON 和 AtomPub (基礎 」 XML) 格式。

今天以網際網路為基礎的應用程式 NoSQL 資料庫，例如資料表儲存體提供傳統關聯式資料庫常用替代方案。

## <a name="queue-storage"></a>佇列中的儲存空間

在設計應用程式的小數位數，應用程式元件是通常分離，讓他們可以不按比例縮放獨立。 佇列中的儲存空間會提供可靠的訊息解決方案非同步元件間的通訊應用程式，無論在雲端，在桌面上，在內部部署伺服器上，或在行動裝置上執行。 佇列中的儲存空間也支援管理非同步工作，以及建立程序工作流程。

儲存帳戶可以包含佇列的任何數字。 佇列可以包含任何數字的儲存空間帳戶的容量限制的郵件。 個別郵件可能會最多 64 KB。

## <a name="file-storage"></a>檔案儲存空間

Azure 檔案儲存空間提供雲端 SMB 檔案共用，讓您可以將移轉依賴檔案共用 Azure 來快速且不高重的舊版應用程式。 與 Azure 檔案儲存空間，如同在桌面應用程式裝載一般的 SMB 共用 Azure 虛擬機器或雲端服務中執行的應用程式時，可以裝載在雲端，檔案共用。 任何數目的應用程式元件可以然後裝載，同時存取的檔案儲存空間共用。

由於檔案儲存空間共用標準 SMB 檔案共用，在 Azure 中執行的應用程式可以存取透過檔案系統 I/O Api 共用中的資料。 開發人員時，因此可以使用他們的現有的程式碼和技巧來移轉現有的應用程式。 IT 專業人員可以使用 PowerShell cmdlet 來建立、 裝載，及管理檔案儲存空間共用的管理 Azure 應用程式的一部分。

其他 Azure 儲存的服務，例如檔案儲存空間會公開 REST API 來存取共用中的資料。 內部部署的應用程式可以呼叫檔案儲存空間 REST API 來存取檔案共用中的資料。 如此一來，企業版，可以選擇移轉 Azure 一些舊版應用程式，並繼續執行其他人在自己的組織內。 請注意，裝載檔案共用只適用於 Azure; 中執行應用程式內部部署應用程式可能只能存取 REST API 透過檔案共用。

分散式應用程式也可以使用檔案儲存空間來儲存和共用實用的應用程式的資料和開發和測試工具。 例如，應用程式可能會儲存設定檔案，和診斷的資料，例如記錄、 標準，以及當機傾印儲存共用，讓他們可提供給多個虛擬機器或角色檔案中。 開發人員和系統管理員可以儲存公用程式需要建立或管理應用程式中的檔案儲存空間共用所有元件，可供使用，而不是安裝於每虛擬機器或角色例項。

## <a name="access-to-blob-table-queue-and-file-resources"></a>Blob、 表格、 佇列中，以及檔案資源的存取權

根據預設，只有儲存帳戶擁有者可以存取的儲存空間帳戶中的資源。 如需資料的安全性，對您的帳戶中的資源的每個要求必須經過驗證。 驗證依賴共用金鑰模型。 二進位大型物件也可以設定為支援匿名驗證。

您儲存的帳戶會被指派兩個建立用於驗證的私人便捷鍵。 有兩個機碼，可確保您定期重新產生金鑰做為一般的安全性金鑰管理練習時，仍然可以使用您的應用程式。

如果您需要讓使用者能控制存取您儲存空間的資源，然後您可以建立共用的 access 簽章]。 共用的 access 簽章 (SA) 是可以附加到可讓您委派的存取儲存資源的 URL 的權杖。 建立權杖的任何人可以存取指向具有它會指定，權限的時間就是有效的資源。 開始使用版本 2015年-04-05，Azure 儲存體支援共用的 access 簽章的兩種︰ 服務 SA 並帳戶 SA。

服務 SA 委派存取剛才的其中一種儲存服務資源︰ Blob、 佇列、 表格或檔案的服務。

帳戶 SA 委派資源在一或多個儲存服務的存取權。 您可以委派存取服務等級無法使用的服務 SA 的操作。 您也可以委派存取讀取、 撰寫及刪除 blob 容器、 表格、 佇列和與服務 SA 不允許的檔案共用上的作業。

最後，您可以指定容器和其二進位大型物件或特定 blob，可供公開存取。 當您表示的容器或 blob 公用時，任何人都可以讀取以匿名方式;都不需要驗證。  公用容器及 blob 有助公開資源，例如媒體和裝載在網站上的文件。  若要降低網路延遲全球的使用者，您可以快取 Azure CDN 網站所用的 blob 資料。

共用的 access 簽章的詳細資訊，請參閱[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md) 。 如需詳細資訊安全存取您儲存的帳戶，請參閱[管理匿名的讀取權限容器及二進位大型物件](storage-manage-access-to-resources.md)和[驗證 Azure 儲存服務](https://msdn.microsoft.com/library/azure/dd179428.aspx)。

## <a name="replication-for-durability-and-high-availability"></a>持續性及高可用性的複寫

您的 Microsoft Azure 儲存體帳戶中的資料永遠會以確保持續性及高可用性複寫。 複寫複製資料，在相同的資料中心，或第二個資料中心，請在您選擇複寫選項而定。 複寫會保護您的資料，並保留您的應用程式-時間發生暫時性硬體失敗。 如果您的資料複製到第二個資料中心，，也會保護您的資料嚴重失敗的主索引的位置。

複寫可確保您儲存的帳戶符合[服務等級協定 (SLA) 的儲存空間](https://azure.microsoft.com/support/legal/sla/storage/)，甚至面對失敗。 請參閱 Azure 儲存體有關 SLA 保證持續性及可用性。 

當您建立的儲存空間帳戶時，您可以選取下列複寫選項的其中一個︰  

- **在本機上多餘的儲存空間 (LRS)。** 在本機上多餘的儲存空間維護三份資料。 LRS 複製一個區域中的單一資料中心內三次。 從標準的硬體失敗，而不是從失敗的單一資料中心，LRS 保護您的資料。  

    在 [折扣提供 LRS。 最大的持續性，我們建議您使用地理多餘的儲存空間，如下所述。


- **區域多餘的儲存空間 (ZRS)。** 區域多餘的儲存空間維護您的資料的三個複本。 ZRS 複寫三次跨三功能，在單一的區域內或跨兩個區域，提供更高的持續性比 LRS。 ZRS 可確保您的資料是長期一個區域中。  

    ZRS 提供較高層級的持續性比 LRS;不過的最大的持續性，我們建議您使用地理多餘的儲存空間，如下所述。  

    > [AZURE.NOTE] ZRS 是目前僅適用於區塊 blob，只支援的版本 2014年-02-14 及更新版本。
    >
    > 當您在建立儲存帳戶並選取 ZRS 時，您無法將它轉換成使用其他任何類型的試驗，或反向操作。

- **地理多餘的儲存空間 (GRS)**。 GRS 維護您的資料的六個複本。 GRS，與您的資料內的主要區域中，複製三次，也會提供最高層級的持續性的主要區域，遠離英哩的次要區域數百中複寫三次。 在 [主要區域失敗，Azure 儲存體會容錯移轉至第二個區域。 GRS 可確保您的資料是長期中兩個不同的區域。

    依地區的主要和次要配對的相關資訊，請參閱[Azure 區域](https://azure.microsoft.com/regions/)。

- **讀取權限地理多餘的儲存空間 (RA GRS)**。 讀取權限地理多餘的儲存會將您的資料複製到第二個地理位置，並提供您的資料中的第二個位置的 「 讀取 」 權限。 讀取權限地理多餘的儲存空間可讓您從 [主要] 或 [次要的位置，存取您的資料的一個位置就無法使用。 讀取權限地理多餘的儲存空間是依預設您儲存的帳戶的預設選項，您所建立。 

    > [AZURE.IMPORTANT] 您可以變更如何將資料複製之後您儲存的帳戶建立之後，除非您在建立帳戶時，指定 ZRS。 不過，請注意，您可能需支付如果您切換從 LRS GRS 或 RA GRS 成本一次性的其他資料傳輸。

如需儲存複寫選項的其他詳細資訊，請參閱[Azure 儲存體複寫](storage-redundancy.md)。

價格儲存帳戶複寫的資訊，請參閱[Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)。 如需所提供的服務中每個區域的相關資訊，請參閱[Azure 區域](https://azure.microsoft.com/regions/#services)。

架構與 Azure 儲存體持續性的詳細資訊，請參閱[SOSP 紙張-Azure 儲存體︰ 高度使用雲端儲存服務強式一致性](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。


## <a name="transferring-data-to-and-from-azure-storage"></a>傳送資料，以及從 Azure 儲存體

您可以使用 AzCopy 命令列公用程式複製 blob、 檔案及表格資料，在您儲存的帳戶或跨儲存帳戶。 如需詳細資訊，請參閱[將資料以 AzCopy 命令列公用程式傳送](storage-use-azcopy.md)。

AzCopy 內建的上方[Azure 資料移動文件庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)中，這是目前可在預覽中。

匯入/匯出 Azure 服務會提供將 blob 資料匯入或匯出您儲存的帳戶透過 Azure 資料中心寄送給硬碟磁碟 blob 資料。 如需有關匯入/匯出服務的詳細資訊，請參閱[使用 Microsoft Azure 匯入/匯出服務傳送資料 Blob 儲存體](storage-import-export-service.md)。

## <a name="pricing"></a>價格

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>儲存 Api、 文件庫及工具

Azure 儲存體資源可以透過 HTTP/HTTPS 要求任何語言。 此外，Azure 儲存體提供數種常見的語言的程式設計文件庫。 這些文件庫簡化各種處理詳細資料，例如同步和非同步引動，等批次處理作業、 例外狀況管理，自動重試操作行為的使用 Azure 儲存體。 文件庫會目前提供下列語言與平台，與其他人管線中︰

### <a name="azure-storage-data-services"></a>Azure 儲存資料服務

- [儲存服務 REST API](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [儲存用於.NET Windows Phone 與 Windows 執行階段的用戶端文件庫](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [儲存空間 c + + 的用戶端文件庫](https://github.com/Azure/azure-storage-cpp)
- [儲存 Java/Android 的用戶端文件庫](/develop/java/)
- [儲存 Node.js 的用戶端文件庫](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [儲存空間 PHP 的用戶端文件庫](/develop/php/)
- [儲存空間 [注音標示的用戶端文件庫](/develop/ruby/)
- [儲存 Python 的用戶端文件庫](/develop/python/)
- [針對 PowerShell 1.0 的儲存空間 Cmdlet](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Azure 儲存體管理服務

- [儲存資源提供者 REST API 參考](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [儲存資源提供者用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [針對 PowerShell 1.0 的儲存空間資源提供者 Cmdlet](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [儲存服務管理 REST API （傳統）](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Azure 儲存資料移動服務

- [儲存匯入/匯出服務 REST API](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [儲存空間的.NET 資料移動用戶端庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>工具和公用程式

- [Azure 儲存檔案總管](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Azure 儲存用戶端工具](storage-explorers.md)
- [Azure Sdk 和工具](https://azure.microsoft.com/tools/)
- [Azure 儲存模擬器](http://www.microsoft.com/download/details.aspx?id=43709)
- [Azure PowerShell](../powershell-install-configure.md)
- [AzCopy 命令列公用程式](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>後續步驟

若要進一步瞭解 Azure 儲存空間，請探索這些資源︰

### <a name="documentation"></a>文件

- [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>適用於系統管理員

- [Azure PowerShell 使用 Azure 儲存體](storage-powershell-guide-full.md)
- [Azure CLI 使用 Azure 儲存體](storage-azure-cli.md)

### <a name="for-net-developers"></a>.NET 開發人員

- [使用.NET Azure Blob 儲存體快速入門](storage-dotnet-how-to-use-blobs.md)
- [使用.NET Azure 資料表儲存體快速入門](storage-dotnet-how-to-use-tables.md)
- [使用.NET Azure 佇列中儲存快速入門](storage-dotnet-how-to-use-queues.md)
- [開始使用 windows Azure 檔案儲存空間](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Java/Android 開發人員

- [如何使用從 Java Blob 儲存體](storage-java-how-to-use-blob-storage.md)
- [如何使用從 Java 資料表儲存體](storage-java-how-to-use-table-storage.md)
- [如何使用從 Java 佇列中的儲存空間](storage-java-how-to-use-queue-storage.md)
- [如何使用從 Java 檔案儲存空間](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Node.js 開發人員

- [如何使用從 Node.js Blob 儲存體](storage-nodejs-how-to-use-blob-storage.md)
- [如何使用從 Node.js 資料表儲存體](storage-nodejs-how-to-use-table-storage.md)
- [如何使用從 Node.js 佇列中的儲存空間](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>PHP 開發人員

- [如何使用從 PHP Blob 儲存體](storage-php-how-to-use-blobs.md)
- [如何使用從 PHP 資料表儲存體](storage-php-how-to-use-table-storage.md)
- [如何使用從 PHP 佇列中的儲存空間](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>並列文字的開發人員

- [如何使用從 [注音標示的 Blob 儲存體](storage-ruby-how-to-use-blob-storage.md)
- [如何使用從 [注音標示的資料表儲存體](storage-ruby-how-to-use-table-storage.md)
- [如何使用佇列中的儲存空間，從 [注音標示](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Python 開發人員

- [如何使用從 Python Blob 儲存體](storage-python-how-to-use-blob-storage.md)
- [如何使用從 Python 資料表儲存體](storage-python-how-to-use-table-storage.md)
- [如何使用從 Python 佇列中的儲存空間](storage-python-how-to-use-queue-storage.md)
- [如何使用從 Python 檔案儲存空間](storage-python-how-to-use-file-storage.md)
