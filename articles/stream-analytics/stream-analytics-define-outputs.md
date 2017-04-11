<properties
    pageTitle="串流分析輸出︰ 儲存空間，分析選項 |Microsoft Azure"
    description="深入了解目標串流分析資料輸出選項，包括 Power BI 的分析結果。"
    keywords="資料轉換、 分析結果、 資料儲存選項"
    services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
    documentationCenter="" 
    authors="jeffstokes72"
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>串流分析輸出︰ 儲存空間，分析選項

撰寫串流分析工作，請考慮將會如何用產生的資料。 如何將檢視資料流分析工作的結果，其中會您將其儲存嗎？

才能啟用各種不同的應用程式模式，請 Azure 資料流分析會有不同的選項，可用來儲存 [輸出及檢視分析結果。 這可讓您更容易檢視工作輸出，並讓您具彈性的消耗及儲存空間的工作輸出資料倉儲和其他用途。 開始工作和事件開始進行之前，必須存在於設定工作中的任何輸出。 例如，如果您使用 Blob 儲存體作為輸出時，工作不會建立儲存帳戶自動。 需要先建立使用者 ASA 工作開始。

## <a name="azure-data-lake-store"></a>Azure 資料 Lake 存放區

資料流分析支援[Azure 資料湖存放區](https://azure.microsoft.com/services/data-lake-store/)。 此儲存空間，可讓您儲存的任何大小、 類型及 ingestion 速度作業與探分析的資料。 此時，建立及設定資料湖存放輸出只支援 Azure 傳統入口網站。 此外，串流分析需要授權存取資料湖存放區。 [資料湖輸出文章](stream-analytics-data-lake-output.md)討論的授權和註冊資料湖市集預覽 （如有需要） 的方式的詳細資訊。

### <a name="authorize-an-azure-data-lake-store"></a>授權 Azure 資料湖存放區

作為輸出 Azure 管理入口網站中選取資料湖儲存時，系統會提示您授權至現有的資料湖存放區連線。  

![授權資料湖存放區](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

然後填寫資料湖存放輸出的屬性，如下所示︰

![授權資料湖存放區](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

下表列出屬性名稱與建立資料湖存放輸出所需的描述。

<table>
<tbody>
<tr>
<td><B>屬性名稱</B></td>
<td><B>描述</B></td>
</tr>
<tr>
<td>輸出別名</td>
<td>這是在查詢中使用，將此資料湖存放查詢輸出記的名稱。</td>
</tr>
<tr>
<td>帳戶名稱</td>
<td>傳送您的輸出資料湖儲存體帳戶名稱。 您會看到的下拉式清單的資料湖存放帳戶的登入入口網站的使用者擁有的存取權。</td>
</tr>
<tr>
<td>[<I>選擇性</I>] 路徑加上字首圖樣</td>
<td>用來撰寫您的檔案中指定的資料湖儲存帳戶檔案路徑。 <BR>{日期}，{time}<BR>範例 1︰ 資料夾 1/記錄 / {日期} / {時間}<BR>範例 2︰ 資料夾 1/記錄 / {日期}</td>
</tr>
<tr>
<td>[<I>選擇性</I>] 的日期格式</td>
<td>如果前置詞路徑中使用日期權杖，您可以選取組織檔案的 [日期] 格式。 範例︰ YYYY/MM/DD</td>
</tr>
<tr>
<td>[<I>選擇性</I>] 時間格式</td>
<td>如果時間權杖使用前置詞路徑中，指定組織檔案的時間格式。 目前唯一支援的值是 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>輸出資料序列化格式。 支援 JSON、 CSV 和 Avro。</td>
</tr>
<tr>
<td>編碼</td>
<td>如果 CSV 或 JSON 設定格式，請編碼必須指定。 Utf-8 這次是只支援編碼格式。</td>
</tr>
<tr>
<td>分隔符號</td>
<td>只適用於 CSV 序列化。 資料流分析支援序列化 CSV 資料常見的分隔符號的數字。 支援的值為逗號、 分號、 空格、] 索引標籤及垂直線。</td>
</tr>
<tr>
<td>格式</td>
<td>只適用於 JSON 序列化。 線條分隔指定的輸出格式化由以新的一行分隔每個 JSON 物件。 陣列指定的輸出格式化為 JSON 物件的陣列。</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>更新資料 Lake 存放授權

您必須重新驗證您的資料湖存放帳戶之後建立或上一次驗證您的工作,，已變更其密碼。

![授權資料湖存放區](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  


## <a name="sql-database"></a>SQL 資料庫

[Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)可作為輸出資料是關聯式性質或裝載在關聯式資料庫中的內容而定的應用程式。 資料流分析工作會寫入 Azure SQL 資料庫中現有的資料表。  請注意資料表結構描述必須完全符合的欄位，且正從您的工作輸出其類型。 [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)也可以指定作為輸出透過 [SQL 資料庫輸出選項以及 （這是預覽功能）。 下表列出的屬性名稱與建立 SQL 資料庫輸出的描述。

| 屬性名稱 | 描述 |
|---------------|-------------|
| 輸出別名 | 這是在查詢中使用，將此資料庫查詢輸出記的名稱。 |
| 資料庫 | 傳送您的輸出的資料庫名稱 |
| 伺服器名稱 | SQL 資料庫伺服器名稱 |
| 使用者名稱 | 有資料庫寫入存取權的使用者名稱 |
| 密碼 | 若要連線至資料庫密碼 |
| 表格 | 資料表名稱輸出寫入的位置。 表格名稱會區分大小寫，此表格的結構描述應該完全符合的欄位和由您的工作輸出產生其類型數。 |

> [AZURE.NOTE] 目前 Azure SQL 資料庫提供支援工作中輸出資料流分析。 不過，不支援使用附加資料庫執行 SQL Server Azure 虛擬機器。 這是有所變更未來的版本。

## <a name="blob-storage"></a>Blob 儲存體

Blob 儲存體提供將大量的非結構化資料儲存在雲端的效益和擴充解決方案。  Azure Blob 儲存體和及其用法簡介資訊，請參閱[如何使用二進位大型物件](../storage/storage-dotnet-how-to-use-blobs.md)的文件。

下表列出的屬性名稱與建立 blob 輸出的描述。

<table>
<tbody>
<tr>
<td>屬性名稱</td>
<td>描述</td>
</tr>
<tr>
<td>輸出別名</td>
<td>這是在查詢中使用，將此 blob 儲存體查詢輸出記的名稱。</td>
</tr>
<tr>
<td>儲存帳戶</td>
<td>傳送您的輸出儲存體帳戶名稱。</td>
</tr>
<tr>
<td>儲存帳戶金鑰</td>
<td>儲存帳戶相關聯私密金鑰。</td>
</tr>
<tr>
<td>儲存容器</td>
<td>容器提供 blob 儲存在 Microsoft Azure Blob 服務中的邏輯群組。 當您上載 blob Blob 服務時，您必須指定為該 blob 的容器。</td>
</tr>
<tr>
<td>[選擇性] 路徑加上字首圖樣</td>
<td>用來撰寫您的二進位大型物件指定容器中的檔案路徑。<BR>內路徑，您可以選擇指定寫入 blob 的頻率使用下列 2 變數一或多個執行個體︰<BR>{日期}，{time}<BR>範例 1: cluster1 記錄檔 / {日期} / {時間}<BR>範例 2: cluster1 記錄檔 / {日期}</td>
</tr>
<tr>
<td>[選擇性] 的日期格式</td>
<td>如果前置詞路徑中使用日期權杖，您可以選取組織檔案的 [日期] 格式。 範例︰ YYYY/MM/DD</td>
</tr>
<tr>
<td>[選擇性] 時間格式</td>
<td>如果時間權杖使用前置詞路徑中，指定組織檔案的時間格式。 目前唯一支援的值是 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>輸出資料序列化格式。  支援 JSON、 CSV 和 Avro。</td>
</tr>
<tr>
<td>編碼</td>
<td>如果 CSV 或 JSON 設定格式，請編碼必須指定。 Utf-8 這次是只支援編碼格式。</td>
</tr>
<tr>
<td>分隔符號</td>
<td>只適用於 CSV 序列化。 資料流分析支援序列化 CSV 資料常見的分隔符號的數字。 支援的值為逗號、 分號、 空格、] 索引標籤及垂直線。</td>
</tr>
<tr>
<td>格式</td>
<td>只適用於 JSON 序列化。 線條分隔指定的輸出格式化由以新的一行分隔每個 JSON 物件。 陣列指定的輸出格式化為 JSON 物件的陣列。</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>事件中心

[事件集線器](https://azure.microsoft.com/services/event-hubs/)是彈性發行-訂閱事件 ingestor。 也可以收集數百萬秒的事件。  使用一事件中心作為輸出時，資料流分析工作的成果都的另一個串流工作輸入。

有幾個參數所需作為輸出設定事件中樞資料流。

| 屬性名稱 | 描述 |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 輸出別名 | 這是在查詢中使用，將此事件中心查詢輸出記的名稱。 |
| 服務匯流排命名空間 | 服務匯流排命名空間是一組之訊息的實體容器。 當您建立新的事件中心時，您也可以建立服務匯流排命名空間 |
| 事件中心 | 您的事件中心輸出的名稱 |
| 事件中心原則名稱 | 共用的 access 原則，可以建立在 [事件中心設定] 索引標籤。 每個共用的存取原則會有一個名稱，在您的設定，權限] 和 [便捷鍵 |
| 事件中心原則索引鍵 | 用來驗證服務匯流排命名空間存取共用便捷鍵 |
| [選擇性] 的磁碟分割索引鍵資料行 | 此資料行包含事件中心輸出的磁碟分割索引鍵。 |
| 事件序列化格式 | 輸出資料序列化格式。  支援 JSON、 CSV 和 Avro。 |
| 編碼 | Utf-8 CSV 和 JSON，這次是只支援編碼格式 |
| 分隔符號 | 只適用於 CSV 序列化。 資料流分析序列化中的資料 CSV 格式支援常見的分隔符號的數字。 支援的值為逗號、 分號、 空格、] 索引標籤及垂直線。 |
| 格式 | 只適用於 JSON 類型。 線條分隔指定的輸出格式化由以新的一行分隔每個 JSON 物件。 陣列指定的輸出格式化為 JSON 物件的陣列。 |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/)可以當做資料流分析工作的成果，提供的分析結果的豐富視覺效果的體驗。 此功能可用於操作儀表板、 報表產生器與公制導向報告。

### <a name="authorize-a-power-bi-account"></a>授權的 Power BI 帳戶

1.  當選取 [Power BI Azure 管理入口網站中輸出時，系統會提示現有的 Power BI 使用者的授權，或建立新的 Power BI 帳戶。  

    ![Power BI 使用者的授權](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2.  如果您不還有一個，然後按一下 [立即授權，請建立一個新的帳戶。  如下所示的畫面會顯示。  

    ![Azure 帳戶 Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3.  在此步驟中，提供授權 Power BI 輸出的公司或學校帳戶。 如果您不已註冊 Power BI，選擇 [登現在。 使用 Power BI 用的公司或學校帳戶可能不同於您目前登入 Azure 訂閱帳戶。

### <a name="configure-the-power-bi-output-properties"></a>設定 Power BI 輸出屬性

已驗證的 Power BI 帳戶之後，您可以在您的 Power BI 輸出設定屬性。 下表是屬性名稱清單，其設定您的 Power BI 輸出的描述。

| 屬性名稱 | 描述 |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 輸出別名 | 這是在查詢中使用，將此中輸出至查詢輸出記的名稱。 |
| 群組工作區 | 若要啟用與其他 Power BI 的使用者共用資料您可以選取您的 Power BI 帳戶內的 [群組]，或選擇 「 我的工作區]，如果您不想寫入到群組。  更新現有的群組，您必須更新 Power BI 驗證。 | 
| 資料集的名稱 | 提供資料集的名稱，為想要使用 Power BI 輸出 |
| 資料表名稱 | 提供的資料表名稱底下的 [Power BI 輸出資料集。 目前，從資料流分析作業的 Power BI 輸出只能有一個資料表中資料集 |

針對設定 Power BI 輸出和儀表板的逐步解說，請參閱[Azure 資料流分析及 Power BI](stream-analytics-power-bi-dashboard.md) 。

> [AZURE.NOTE] 未明確建立的資料集及表格中的 Power BI 儀表板。 資料集和資料表會自動填入開始工作和工作 Power bi 中開始幫浦輸出的時間。 請注意，是否工作查詢不會產生任何結果，資料集和資料表將不會建立。 也請注意，如果 Power BI 已經有的資料集和使用此串流分析工作中提供的相同名稱的表格，現有資料將會覆寫。

### <a name="renew-power-bi-authorization"></a>更新 Power BI 授權

您必須重新驗證您的 Power BI 帳戶之後建立或最後驗證您的工作,，已變更其密碼。 如果您的 Azure Active Directory (AAD) 租用戶，您也必須更新 Power BI 授權每個 2 週設定多重因素驗證 (MFA)。 此問題的症狀是沒有工作輸出是 「 驗證使用者錯誤 」 作業記錄中︰

  ![Power BI 重新整理 token 時發生錯誤](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

若要解決此問題，請停止您執行的工作並移至您的 Power BI 輸出。  按一下 [更新授權 」 連結，並重新啟動您的工作，以避免資料遺失的最後一個停止時間。

  ![Power BI 更新授權](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>資料表儲存體

[Azure 資料表儲存體](../storage/storage-introduction.md)提供可用性、 人連線可調整儲存空間，讓應用程式可自動調整成符合使用者的需求。 資料表儲存體是哪一種較少的結構描述的限制，可以使用結構化資料的 Microsoft NoSQL 主要/屬性存放區。 Azure 資料表儲存體可用來儲存持續與有效擷取資料。

下表列出的屬性名稱與建立表格輸出其描述。

| 屬性名稱 | 描述 |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 輸出別名 | 這是在查詢中使用，將此資料表儲存體查詢輸出記的名稱。 |
| 儲存帳戶 | 傳送您的輸出儲存體帳戶名稱。 |
| 儲存帳戶金鑰 | 儲存帳戶相關聯便捷鍵。 |
| 資料表名稱 | 資料表的名稱。 如果不存在，會取得建立資料表。 |
| 分割索引鍵 | 包含分割索引鍵的輸出資料行名稱。 分割索引鍵是磁碟分割表單的實體主索引鍵的第一個部分指定資料表中的唯一識別碼。 是字串值，可能是 1 KB 的大小。 |
| 資料列識別碼 | 包含的資料列索引鍵的輸出資料行名稱。 資料列識別碼是指定的磁碟分割內的實體的唯一識別碼。 它表單實體的主索引鍵的第二部分。 列索引鍵是字串值，可能是 1 KB 的大小。 |
| 批次大小 | 批次作業的記錄數目。 通常是預設不足，大部分的工作，請參閱[表格批次作業規格](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)如需詳細資訊修改這項設定。 |

## <a name="service-bus-queues"></a>服務匯流排佇列

[服務匯流排佇列](https://msdn.microsoft.com/library/azure/hh367516.aspx)提供第一個在第一個查看 (FIFO) 訊息傳送給一或多個競爭消費者。 一般而言，郵件應該接收和處理中暫時順序加入佇列中，每一封郵件接收，並處理只有一封郵件消費者接收器。

下表列出屬性名稱與建立佇列中輸出的描述。

| 屬性名稱 | 描述 |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 輸出別名 | 這是在查詢中使用，將本服務匯流排佇列中的查詢輸出記的名稱。 |
| 服務匯流排命名空間 | 服務匯流排命名空間是一組之訊息的實體容器。 |
| 佇列名稱 | 服務匯流排佇列中的名稱。 |
| 佇列原則名稱 | 當您建立佇列中時，您也可以在佇列中設定] 索引標籤上，建立共用的 access 原則。 每個共用的存取原則會有一個名稱，在您的設定，權限，並便捷鍵。 |
| 佇列中原則機碼 | 用來驗證服務匯流排命名空間存取共用便捷鍵 |
| 事件序列化格式 | 輸出資料序列化格式。  支援 JSON、 CSV 和 Avro。 |
| 編碼 | Utf-8 CSV 和 JSON，這次是只支援編碼格式 |
| 分隔符號 | 只適用於 CSV 序列化。 資料流分析序列化中的資料 CSV 格式支援常見的分隔符號的數字。 支援的值為逗號、 分號、 空格、] 索引標籤及垂直線。 |
| 格式 | 只適用於 JSON 類型。 線條分隔指定的輸出格式化由以新的一行分隔每個 JSON 物件。 陣列指定的輸出格式化為 JSON 物件的陣列。 |

## <a name="service-bus-topics"></a>服務匯流排主題

雖然服務匯流排佇列提供收件者寄件者傳來一對一通訊方法，[服務匯流排主題](https://msdn.microsoft.com/library/azure/hh367516.aspx)會提供一一對多表單的通訊。

下表列出的屬性名稱與建立表格輸出其描述。

| 屬性名稱 | 描述 |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 輸出別名 | 這是在查詢中使用，將本服務匯流排主題查詢輸出記的名稱。 |
| 服務匯流排命名空間 | 服務匯流排命名空間是一組之訊息的實體容器。 當您建立新的事件中心時，您也可以建立服務匯流排命名空間 |
| 主題名稱 | 主題訊息實體，類似於事件集線器和佇列。 它們是設計用來收集事件資料流時，從不同的裝置與服務的數字。 建立主題時，也會給予特定的名稱。 無法使用主題傳送的郵件，除非訂閱已建立，因此請確定有主題下的一個或多個訂閱 |
| 主題原則名稱 | 當您建立主題時，您也可以建立主題設定] 索引標籤上的共用的存取原則。 每個共用的存取原則會有一個名稱，在您的設定，權限] 和 [便捷鍵 |
| 主題原則機碼 | 用來驗證服務匯流排命名空間存取共用便捷鍵 |
| 事件序列化格式 | 輸出資料序列化格式。  支援 JSON、 CSV 和 Avro。 |
| 編碼 | 如果 CSV 或 JSON 設定格式，請編碼必須指定。 Utf-8 這次是唯一的支援編碼格式 |
| 分隔符號 | 只適用於 CSV 序列化。 資料流分析序列化中的資料 CSV 格式支援常見的分隔符號的數字。 支援的值為逗號、 分號、 空格、] 索引標籤及垂直線。 |

## <a name="documentdb"></a>DocumentDB

[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)是完全管理 NoSQL 文件資料庫服務，提供在沒有結構描述的資料，如預期呈現且可靠的效能，並快速開發的查詢和交易。

下表列出的屬性名稱與建立 DocumentDB 輸出的描述。

<table>
<tbody>
<tr>
<td>屬性名稱</td>
<td>描述</td>
</tr>
<tr>
<td>帳戶名稱</td>
<td>DocumentDB 帳戶的名稱。  這也是帳戶的端點。</td>
</tr>
<tr>
<td>[帳戶金鑰]</td>
<td>DocumentDB 帳戶共用的便捷鍵。</td>
</tr>
<tr>
<td>資料庫</td>
<td>DocumentDB 資料庫名稱。</td>
</tr>
<tr>
<td>集合名稱模式</td>
<td>若要使用的集合集合名稱模式。 您可使用選擇性 {分割} 權杖，從 0 的磁碟分割區位置開始建構集合姓名格式。<BR>例如︰ 下列是有效的輸入︰<BR>MyCollection {分割}<BR>MyCollection<BR>請注意串流分析工作開始，並將不會自動建立之前，必須存在集合。</td>
</tr>
<tr>
<td>分割索引鍵</td>
<td>用來指定按鍵分割集合輸出的輸出事件中的欄位名稱。</td>
</tr>
<tr>
<td>文件識別碼</td>
<td>根據中用來指定主索引鍵插入或更新作業輸出事件欄位的名稱。</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
您已經知道什麼是資料流分析，受管理的服務串流分析的資料是來自網際網路的項目。 若要進一步瞭解這項服務，請參閱︰

- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
