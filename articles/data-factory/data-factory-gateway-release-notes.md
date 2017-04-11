<properties 
    pageTitle="資料管理閘道器的版本資訊 |Azure 資料工廠" 
    description="資料管理閘道器 tory 版本資訊" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="spelluru"/>

# <a name="release-notes-for-data-management-gateway"></a>資料管理閘道器的版本資訊
新式資料整合的挑戰是與內部部署雲端流暢地移動資料。 資料工廠讓此整合順暢的資料管理閘道，也就是您可以安裝內部部署啟用混合式資料移動代理程式。

請參閱以下文章中的資料管理閘道器及使用方式的詳細資訊︰ 

- [資料管理閘道器](data-factory-data-management-gateway.md)
- [移動之間的內部部署與雲端使用 Azure 資料工廠](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2260721"></a>目前的版本 (2.2.6072.1)

- 設定 [HTTP proxy]，使用 [閘道器組態管理員閘道器的支援。 如果設定，也可以透過 HTTP proxy] 存取 Azure Blob、 Azure 資料表、 Azure 資料湖和文件 DB。
- 支援標題處理 TextFormat 當複製資料從/Azure Blob，Azure 資料湖存放區，內部部署檔案系統與內部部署 HDFS。
- 支援從附加 Blob 和頁面 Blob，以及已支援區塊 Blob 複製資料。
- 會介紹新的閘道器狀態**Online （限制）**，亦即指出閘道器的主要功能的運作除了複製精靈的互動式作業支援。
- 增強的穩定性的閘道器註冊使用登錄機碼。

## <a name="earlier-versions"></a>舊版

## <a name="2160401"></a>2.1.6040.1

- DB2 驅動程式現在包含在閘道器安裝套件。 您不需要另行安裝它。 
- DB2 驅動程式現在支援 z/OS 和 DB2 的我 (另存新檔 / 400) 以及支援的平台已經 （Linux、 Unix 及 Windows）。 
- 使用內部部署資料存放區做為來源或目標 DocumentDB 的支援
- 支援複製資料來源，/冷/熱 blob 儲存空間，以及已支援用途儲存帳戶。 
- 可讓您連線到內部部署的 SQL Server 透過遠端登入] 權限的閘道器。  

## <a name="2060131"></a>2.0.6013.1

- 您可以選取語言/文化特性手動安裝期間使用閘道器。
- 當閘道器無法運作正常時，您可以選擇過去七天的閘道器記錄傳送至 Microsoft，以利於進行問題的疑難排解。 如果閘道無法連線到雲端服務，您可以選擇儲存封存閘道器的記錄。  
- 閘道器組態管理員的使用者介面改良功能︰
    - 請在 [常用] 索引標籤上看起來更清楚閘道器狀態。
    - 重新組織並簡化控制項。
- 您可以將資料複製從使用[無複製預覽工具的程式碼的](data-factory-copy-data-wizard-tutorial.md)儲存空間。 請參閱[分段複製](data-factory-copy-activity-performance.md#staged-copy)此功能的詳細資料的一般。 
- 您可以將 Azure 電腦學習使用輸入資料直接從內部部署的 SQL Server 資料庫的資料管理閘道。
- 改善效能
    - 改善效能無程式碼的複製預覽工具中檢視結構描述或預覽針對 SQL Server。



## <a name="11259531"></a>1.12.5953.1
- 錯誤修正

## <a name="11159181"></a>1.11.5918.1

- 已從 1 MB 增加 40 mb 的閘道器的事件記錄檔大小上限。
- 重新啟動是需要在閘道器的自動更新，會顯示警告對話方塊。 您可以選擇重新啟動右然後或更新版本。 
- 自動更新失敗，以防重試閘道器安裝程式，自動更新的三次，最大值。
- 改善效能
    - 改善效能載入大型表格從內部部署伺服器無程式碼的複製情境中。
- 錯誤修正

## <a name="11058921"></a>1.10.5892.1

- 改善效能
- 錯誤修正

## <a name="1958652"></a>1.9.5865.2

- 零觸控自動更新功能
- 新的系統匣圖示閘道器狀態指標
- [立即更新] 可從用戶端
- 若要設定更新排程時間的功能
- 切換開啟或關閉自動更新的 PowerShell 指令碼
- 支援 JSON 格式  
- 改善效能
- 錯誤修正

## <a name="1858221"></a>1.8.5822.1

- 改善疑難排解體驗
- 改善效能
- 錯誤修正

### <a name="1757951"></a>1.7.5795.1

- 改善效能
- 錯誤修正

### <a name="1757641"></a>1.7.5764.1

- 改善效能
- 錯誤修正

### <a name="1657351"></a>1.6.5735.1

- 支援內部部署 HDFS 來源/接收
- 改善效能
- 錯誤修正

### <a name="1656961"></a>1.6.5696.1

- 改善效能
- 錯誤修正

### <a name="1656761"></a>1.6.5676.1

- 支援在組態管理員的診斷工具
- 支援 Azure 資料工廠表格式資料來源的資料表資料行
- 支援 DW SQL Azure 資料工廠
- 支援此出沒 BlobSource 和 FileSource Azure 資料工廠
- 支援 CopyBehavior – MergeFiles PreserveHierarchy，與 FlattenHierarchy BlobSink 和 FileSink Azure 資料工廠的二進位複本
- 支援複製活動 Azure 資料工廠報告進度
- 支援的資料來源連線驗證 Azure 資料工廠
- 錯誤修正


### <a name="1656721"></a>1.6.5672.1

- Azure 資料工廠支援 ODBC 資料來源的資料表名稱
- 改善效能
- 錯誤修正

### <a name="1656581"></a>1.6.5658.1

- Azure 資料工廠的接收的支援檔案
- 支援 Azure 資料工廠二進位複製保留階層
- Azure 資料工廠支援複製活動等冪性
- 錯誤修正

### <a name="1656401"></a>1.6.5640.1

- 支援更多 3 的資料來源 Azure 資料工廠 ODBC、 OData （HDFS）
- Azure 資料工廠的引號字元 csv 剖析器中的支援
- 壓縮支援 (BZip2)
- 錯誤修正

### <a name="1556121"></a>1.5.5612.1

- Azure 資料工廠 （MySQL、 PostgreSQL、 DB2、 Teradata 及 Sybase） 支援五個關聯式資料庫
- 壓縮支援 （Gzip 和結實）
- 改善效能
- 錯誤修正


### <a name="1455491"></a>1.4.5549.1

- 新增 Azure 資料工廠 Oracle 資料來源支援
- 改善效能
- 錯誤修正

### <a name="1454921"></a>1.4.5492.1

- 支援 Microsoft Azure 資料工廠和 Office 365 Power BI 服務的整合的二進位
- 調整設定 UI 和註冊程序
- Azure 資料工廠 – Azure 輸入與輸出支援的 SQL Server 資料來源

### <a name="1253031"></a>1.2.5303.1

-   修復] 會出現逾時問題，以支援更多耗時的資料來源連線。 
    
### <a name="1155268"></a>1.1.5526.8

- 在安裝期間，成必要條件需要.NET Framework 4.5.1。

### <a name="1051442"></a>1.0.5144.2

- 不會影響 Azure 資料工廠案例的變更。 
