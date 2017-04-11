<properties
   pageTitle="將您的資料移轉到 SQL Data Warehouse |Microsoft Azure"
   description="適用於開發解決方案時，將您的資料移轉到 Azure SQL Data Warehouse 的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="migrate-your-data"></a>移轉您的資料
資料可以使用各種不同工具您 SQL Data Warehouse 將來自不同來源移。  ADF 複製]、 [SSIS 及 [bcp 所有可用來達到這個目的。 不過，為資料量增加您應該考慮分解資料移轉程序，為步驟。 這會提供您機會將最佳化效能和復原能力，以確保平滑資料移轉每一個步驟。

這份文件第一次探討的簡單移轉案例 ADF 複製、 SSIS，以及 bcp。 然後將最佳化移轉的方式稍有更深入的外觀。

## <a name="azure-data-factory-adf-copy"></a>Azure 資料工廠 (ADF) 複製
[ADF 複製][]是[Azure 資料工廠][]的一部分。 若要將資料匯出到一般檔案位於遠端的一般檔案保留在 Azure blob 儲存體，或直接將 SQL Data Warehouse 本機的儲存空間，您可以使用 ADF 複本。

如果一般檔案，以啟動您的資料，那麼您必須先初始化載入之前將其轉移到儲存 Azure blob 的 SQL Data Warehouse。 一旦將資料傳送到 Azure blob 儲存體您可以選擇將資料發送到 SQL Data Warehouse 再次使用[ADF 複製][]。

PolyBase 也會提供載入資料的高效能選項。 不過的意思，其中一個而不使用兩種工具。 如果您需要的最佳效能，請使用 PolyBase。 如果您想單一工具體驗 （而不是大量的資料） ADF 是答案。

> [AZURE.NOTE] PolyBase 需要您的資料檔案中 utf-8。 這是 ADF 複製預設編碼，沒有任何變更。 這是提醒您無法變更 ADF 複製的預設行為。

某些很棒的[ADF 範例][]向透過下列文章。

## <a name="integration-services"></a>整合服務 ##
整合服務 (SSIS) 是一個功能強大且彈性擷取轉換]，並且載入 (ETL) 工具的支援複雜的工作流程、 資料轉換及多個資料載入選項。 使用 SSIS 只傳送資料至 Azure 或更大的移轉的一部分。

> [AZURE.NOTE] SSIS 可以匯出到 utf-8，而不位元組順序標記檔案中。 若要設定中，您必須先執行這可以使用衍生的資料行元件轉換字元資料中使用 65001 utf-8 字碼頁。 一旦轉換成欄之後，寫入到一般檔案目的地介面卡確保 65001 也為檔案的 [程式碼] 頁面上已選取的資料。

SSIS 連線至 SQL Data Warehouse 一樣想連線至 SQL Server 部署。 不過，您的連線必須使用 ADO.NET 連線管理員。 您也應該採取想要設定 「 使用大量插入可用時 「 最大化處理量的設定。 請若要進一步瞭解此屬性的[ADO.NET 目的地介面卡][]文章，參閱

> [AZURE.NOTE] 使用 OLEDB 連線到 Azure SQL Data Warehouse 不受支援。

此外，一定會有套件節流或網路問題，可能會失敗到期日的可能性。 設計套件，他們可以繼續在失敗，而不讓重做失敗之前完成的工作。

如需詳細資訊請參閱[SSIS 文件][]。

## <a name="bcp"></a>bcp
bcp 是命令列公用程式專為一般檔案資料匯入及匯出]。 某些轉換資料匯出時可以進行。 若要執行簡單的轉換使用查詢選取及轉換資料。 一旦匯出，一般檔案可以再載入直接目標 SQL Data Warehouse 資料庫。

> [AZURE.NOTE] 它，通常會建議您先封裝來源系統上檢視中的資料匯出時使用的轉換。 這樣會保留邏輯，並有可重複程序。

Bcp 的優點包括︰

- 簡單。 bcp 命令會建立並執行簡單
- 重新啟動載入程序。 一次匯出載入可執行任何次數

Bcp 的限制是︰

- bcp 適用於 [僅限即形成表格一般檔案。 使用檔案，例如 xml 或 JSON 無法運作
- bcp 不支援匯出至 utf-8。 這可能會造成使用 PolyBase bcp 匯出資料
- 資料轉換功能匯出階段僅限於且簡單性質
- 尚未適用於在網際網路上載入資料時，會強大 bcp。 任何的網路不穩定可能會導致載入錯誤。
- bcp 依賴正在載入之前的目標資料庫中的結構描述

如需詳細資訊，請參閱[使用 bcp 載入到 SQL Data Warehouse 的資料][]。

## <a name="optimizing-data-migration"></a>最佳化資料移轉
SQLDW 資料移轉程序可以有效率地細分為三個不連續的步驟︰

1. 來源資料的匯出
2. 傳輸至 Azure 資料
3. 載入目標 SQLDW 資料庫

若要建立會在每個步驟的效能最大化健全、 重新啟動和彈性的移轉程序，可以個別最佳化每一個步驟。

## <a name="optimizing-data-load"></a>最佳化資料載入
查看這些以相反順序的一些時間。載入資料最快方法是透過 PolyBase。 最佳化 PolyBase 載入程序會放先決條件在先前的步驟，最好了解這個些預先。 它們是︰

1. 資料檔案的編碼方式
2. 資料檔案的格式
3. 資料檔案的位置

### <a name="encoding"></a>編碼
PolyBase 需要資料檔案 utf-8 編碼。 這表示，當您將資料匯出時必須符合這項需求。 如果您的資料僅包含基本 ASCII 字元 （不延伸 ASCII） 然後這些地圖直接 utf-8 標準和您不必擔心編碼。 不過，如果您的資料包含任何特殊字元，例如 umlauts、 重音符號或您的資料支援非拉丁文語言表示您必須，以確保您匯出的檔案都正確 utf-8 編碼。

> [AZURE.NOTE] bcp 不支援將資料匯出至 utf-8。 因此您最好是使用 [Integration Services] 或 [ADF 複製資料匯出。 則值得 utf-8 位元組順序標記 (BOM) 並不需要的資料檔案中。

使用 utf-16 編碼任何檔案必須重新寫入***先前***的資料傳輸。

### <a name="format-of-data-files"></a>資料檔案的格式
PolyBase 規定固定的列終端子 \n 或新行。 這項標準必須符合您的資料檔案。 沒有任何限制字串或資料行終端子。

您必須在檔案中的每個資料行定義為 PolyBase 外部表格的一部分。 請確定所有匯出的資料行是必要的類型符合需求的標準。

請參閱回 [移轉您的結構描述] 文件，在支援的資料類型的詳細資料。

### <a name="location-of-data-files"></a>資料檔案的位置
SQL Data Warehouse 使用 PolyBase 獨佔載入資料從 Azure Blob 儲存體。 因此，資料必須已先轉移到 blob 儲存體。

## <a name="optimizing-data-transfer"></a>最佳化的資料傳輸
資料移轉的最慢的組件的內容之一是 Azure 資料的轉接。 不只可以網路頻寬會有問題，但也網路可靠性可以嚴重阻礙進度。 依預設將資料移轉到 Azure 發生了傳輸錯誤可能是相當可能是在網際網路上。 不過，這些錯誤可能需要重新傳送全部或部分的資料。

所幸，您有幾個選項，以改善的速度與此程序的彈性︰

### <a name="expressroute"></a>[ExpressRoute][]
若要請考慮使用[ExpressRoute][]若要加快傳輸。 [ExpressRoute][]提供您建立的私人連線至 Azure 讓連線不會繼續公用網際網路上。 這是不是必要的步驟。 不過，它將提高處理時將資料以 Azure 推入內部部署或代管設備。

使用[ExpressRoute][]的優點是︰

1. 增加的可靠性
2. 更快速的網路速度
3. 降低網路延遲
4. 網路較高的安全性

[ExpressRoute][]的好處案例; 數不只是移轉。

想要？ 如需詳細資訊，請價格請造訪[ExpressRoute 文件][]。

### <a name="azure-import-and-export-service"></a>Azure 匯入及匯出服務
Azure 匯入及匯出服務是專為大型 （GB + +），以大量 （TB + +） 傳送資料至 Azure 資料傳輸程序。 它涉及磁碟撰寫您的資料和 Azure 資料中心傳送它們。 然後將代表您將 Azure 儲存體 Blob 載入磁碟內容。

匯入匯出程序的高層級檢視如下所示︰

1. 設定要接收資料 Azure Blob 儲存體容器
2. 將資料匯出到本機存放區
2. 將資料複製到 3.5 英吋 SATA II/III 硬碟機使用 [Azure 匯入/匯出工具]
3. 建立 [Azure 匯入及匯出的服務，提供產生 [Azure 匯入/匯出工具] 的筆記本檔案匯入作業
4. 將磁碟運送您指定的 Azure 資料中心
5. 您的資料會傳送到您的 Azure Blob 儲存體容器
6. 將資料載入到 SQLDW 使用 PolyBase

### <a name="azcopy-utility"></a>[AZCopy][]公用程式
[AZCopy][]公用程式是取得您的資料傳輸至 Azure 儲存體 Blob 的絕佳工具。 大型 （GB + +） 資料傳輸至設計的小型 （MB + +）。 [AZCopy]也設計提供建議同時處理量時傳送資料至 Azure，因此是非常適合的資料傳輸的步驟。 您可以載入到 SQL Data Warehouse 使用 PolyBase 的資料傳輸一次。 您也可以使用 「 執行程序 」 任務將 SSIS 封包到加入 AZCopy。

若要使用 AZCopy 您需要下載並安裝。 有[生產版本][]與[preview 版本][]。

若要上傳檔案系統中的檔案，您必須像下列命令︰

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

高層級的程序摘要可能是︰

1. 設定 Azure 儲存 blob 容器接收的資料
2. 將資料匯出到本機存放區
3. AZCopy Azure Blob 儲存體容器中的資料
4. 將資料載入到 SQL Data Warehouse 使用 PolyBase

完整的可用文件︰ [AZCopy][]。

## <a name="optimizing-data-export"></a>最佳化的資料匯出
除了確保匯出符合需求配置 PolyBase 您也可以搜尋最佳化匯出的資料以改善進一步的程序。

> [AZURE.NOTE] PolyBase 要求資料位於 utf-8 格式不太您會使用 bcp 執行資料匯出。 bcp 不支援 utf-8 輸出資料檔案。 SSIS 或 ADF 複製會比較適合執行這種類型的資料匯出。

### <a name="data-compression"></a>資料壓縮
PolyBase 可以讀取 gzip 壓縮資料。 如果您可以壓縮資料 gzip 檔案然後您會最小化發送在網路上的資料的量。

### <a name="multiple-files"></a>多個檔案
分割成數個檔案的大型表格不僅協助改善匯出速度，也有助於傳輸 re-startability 與整體的管理能力，一次在 Azure blob 儲存體中的資料。 PolyBase 許多不錯的功能是會讀取資料夾內的所有檔案並將其視為一個資料表。 則因此建議您先將它自己的資料夾隔離的每個資料表的檔案。

PolyBase 也支援稱為 「 遞迴資料夾經過 」 的功能。 若要進一步加強您匯出的資料以改善您的資料管理組織，您可以使用這項功能。

若要進一步瞭解載入 PolyBase 的資料，請參閱[使用 PolyBase 載入到 SQL Data Warehouse 的資料][]。


## <a name="next-steps"></a>後續步驟
如需移轉相關資訊，請參閱[移轉您的方案 SQL Data Warehouse][]。
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[ADF 複本]: ../data-factory/data-factory-data-movement-activities.md 
[ADF 範例]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[開發概觀]: sql-data-warehouse-overview-develop.md
[將您的方案移轉到 SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[若要將資料載入至 SQL Data Warehouse 使用 bcp]: sql-data-warehouse-load-with-bcp.md
[若要將資料載入至 SQL Data Warehouse 使用 PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure 資料工廠]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute 文件]: http://azure.microsoft.com/documentation/services/expressroute/

[產品版本]: http://aka.ms/downloadazcopy/
[預覽版本]: http://aka.ms/downloadazcopypr/
[ADO.NET 目的地介面卡]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS 文件]: https://msdn.microsoft.com/library/ms141026.aspx
