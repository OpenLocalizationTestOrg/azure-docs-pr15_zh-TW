<properties
   pageTitle="一般 SQL 連接器 |Microsoft Azure"
   description="本文將說明如何設定 Microsoft 的一般 SQL 連接器。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-technical-reference"></a>一般的 SQL 連接器技術參照

本文將說明一般 SQL 連接器。 本文適用於下列產品︰

- Microsoft 身分識別管理員 2016 (MIM2016)
- Forefront 識別管理員 2010 R2 (FIM2010R2)
    -   必須使用 hotfix 4.1.3671.0 或更新版本[KB3092178](https://support.microsoft.com/kb/3092178)。

如需 MIM2016 與 FIM2010R2，連接線即從[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=717495)下載。

若要查看此動作的連接器，請參閱[逐步的一般 SQL 連接器](active-directory-aadconnectsync-connector-genericsql-step-by-step.md)。

## <a name="overview-of-the-generic-sql-connector"></a>一般 SQL 連接器的概觀

一般的 SQL 連接器可讓您與使用者提供 ODBC 連接的資料庫系統整合同步處理服務。  

從高層級的觀點來看，目前版本的連接器支援下列功能︰

功能 | 支援
--- | ---
連接的資料來源 | 連接線會支援所有 64 位元 ODBC 驅動程式。 以下列測試︰ <li>Microsoft SQL Server 與 SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 和 11 g</li><li>MySQL 5.x</li>
案例   | <li>物件生命週期管理</li><li>密碼管理</li>
作業 | <li>完整的匯入及 Delta 匯入、 匯出</li><li>匯出︰ 新增、 更新、 刪除及取代</li><li>設定密碼、 變更密碼</li>
結構描述 | <li>動態探索的物件和屬性</li>

### <a name="prerequisites"></a>必要條件
使用連接器之前，請確定您有下列同步處理伺服器上︰

- 4.5.2 的 Microsoft.NET Framework 或更新版本
- 64 位元 ODBC 用戶端驅動程式

### <a name="permissions-in-connected-data-source"></a>連線的資料來源中的權限
若要建立或執行任何支援的工作的一般 SQL 連接器中，您必須︰

- db_datareader
- db_datawriter

### <a name="ports-and-protocols"></a>連接埠和通訊協定
若要使用的 ODBC 驅動程式所需的連接埠，請參閱資料庫供應商的文件。

## <a name="create-a-new-connector"></a>建立新的連接器
若要建立一般 SQL 連接器，**同步處理服務**中選取 [**管理代理程式**並**建立**。 選取 [**一般 SQL (Microsoft)**連接器]。

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>連線
連接線會使用 ODBC 資料來源名稱檔案的連線。 建立使用**ODBC 資料來源****系統管理工具**] 底下的 [開始] 功能表中的 DSN 檔案。 系統管理工具中建立**檔案 DSN** ，讓它可提供給連接器。

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

當您建立新的一般 SQL 連接器時，[連線] 畫面會是第一個。 您必須先提供下列資訊︰

- DSN 檔案路徑
- 驗證
    - 使用者名稱
    - 密碼

資料庫應該支援這些驗證方法之一︰

- **Windows 驗證**︰ 驗證資料庫驗證使用者使用 Windows 認證。 指定使用者名稱/密碼會用於驗證與資料庫。 此帳戶必須資料庫的權限。
- **SQL 驗證**︰ 驗證使用者名稱/密碼定義一個連線至資料庫的 [連線] 畫面的資料庫用途。 如果您儲存在 DSN 檔案中的使用者名稱/密碼，在 [連線] 畫面上提供的認證優先。
- **Azure SQL 資料庫驗證**︰ 如需詳細資訊，請參閱[連線至 SQL 資料庫來使用 Azure Active Directory 驗證](..\sql-database\sql-database-aad-authentication.md)。

**DN 是錨點**︰ 如果您選取這個選項，DN 也會使用錨點屬性。 可用於簡單的實作，但也有下列限制︰

-   連接器支援只有一個物件類型。 因此任何參照屬性只能參考相同的物件類型。

**匯出類型︰ 物件取代**︰ 在匯出期間，當變更某些屬性，所有屬性的整個物件匯出及會取代現有的物件。

### <a name="schema-1-detect-object-types"></a>結構描述 1 （偵測物件類型）
在此頁面上，您要設定要如何尋找資料庫中的不同物件類型連接器。

每個物件類型呈現為磁碟分割及設定上**設定的磁碟分割區和階層**。

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**物件類型偵測方法**︰ 連接器支援這些物件類型偵測方法。

- **固定的值**︰ 提供清單以逗號分隔的物件類型清單。 例如︰ `User,Group,Department`。  
![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **表格/檢視/預存程序**︰ 提供表格/檢視/預存程序的名稱，然後提供的物件類型清單的資料行名稱。 如果您是使用預存程序，則也提供參數，格式**[名稱]: [方向]: [值]**。 在各行 （使用 Ctrl + Enter 來取得新的一行） 提供每個參數。  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **SQL 查詢**︰ 這個選項可讓您提供的 SQL 查詢會傳回單一資料行與物件類型，例如`SELECT [Column Name] FROM TABLENAME`。 傳回的資料行必須是字串類型 (varchar)。

### <a name="schema-2-detect-attribute-types"></a>結構描述 2 （偵測屬性類型）
在此頁面上，您要設定如何移至偵測到的屬性名稱與類型。 [設定] 選項會列出每個偵測到前一頁上的物件類型。

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**屬性類型偵測方法**︰ 連接器支援這些屬性類型偵測方法與每個偵測到的物件類型結構描述 1] 畫面中。

- **表格/檢視/預存程序**︰ 提供的名稱來尋找屬性名稱的表格/檢視/預存程序。 如果您是使用預存程序，則也提供參數，格式**[名稱]: [方向]: [值]**。 在各行 （使用 Ctrl + Enter 來取得新的一行） 提供每個參數。 若要偵測屬性名稱中的多重值屬性，提供逗點分隔的資料表或檢視清單。 不支援多重值的案例父子表格當有相同的資料行名稱。
- **SQL 查詢**︰ 這個選項可讓您提供的 SQL 查詢會傳回單一資料行屬性名稱，例如`SELECT [Column Name] FROM TABLENAME`。 傳回的資料行必須是字串類型 (varchar)。

### <a name="schema-3-define-anchor-and-dn"></a>結構描述 3 （定義錨點和 DN）
此頁面可讓您設定定位點和 DN 針對每個偵測到的物件類型的屬性。 您可以選取多個屬性，使錨點具唯一性。

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- 多重值和布林值屬性不會列出。
- 除非**DN 錨點**選取 [連線] 頁面上，不能 DN 和錨點] 使用相同的屬性。
- 如果**DN 錨點**選取 [連線] 頁面上，此頁面需要 DN 屬性。 這個屬性也想要使用錨點屬性。
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>結構描述 4 （定義屬性類型、 參照、 和方向）
此頁面可讓您設定的屬性，例如整數、 二進位或布林值和每個屬性的方向。 頁面**結構描述 2**的所有屬性會都列出包括多重值的屬性。

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **資料類型**︰ 用來對應已知同步處理引擎這些類型的屬性類型。 預設會偵測到 SQL 結構描述中使用相同的類型，但 DateTime 與參照不是很容易偵測。 對於，必須先指定**DateTime**或**參照**。
- **方向**︰ 您可以將匯入、 匯出或 ImportExport 屬性方向。 ImportExport 是預設值。
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

附註︰

- 如果不是偵測連接器屬性類型，則會使用字串資料類型。
- **巢狀表格**可以視為單一資料行的資料表。 Oracle 儲存任何特定順序排列的巢狀表格的列。 不過，當您擷取到 PL/SQL 變數的巢狀的表格，列可以從 1 開始的連續註標。 可讓您陣列形式存取個別資料列。
- **VARRYS**不支援的連接器。

### <a name="schema-5-define-partition-for-reference-attributes"></a>結構描述 5 （參考屬性的定義磁碟分割）
在此頁面上，您設定的磁碟分割 （物件類型） 屬性指的所有參考屬性。

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

如果您使用**DN 是錨點**，然後您就必須使用相同的物件類型為從所參照的項目。 您無法參照另一個物件類型。

### <a name="global-parameters"></a>全域的參數
全域參數頁面用來設定 Delta 匯入、 日期/時間格式，以及密碼方法。

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

一般的 SQL 連接器 Delta 匯入支援以下兩種方法︰

- **觸發程序**︰ 請參閱[產生 Delta 檢視使用引動程序](https://technet.microsoft.com/library/cc708665.aspx)。
- **浮水印**︰ 可以使用任何資料庫的一般方法。 根據資料庫供應商，浮水印查詢並預先填入。 浮水印欄必須使用每個資料表/檢視簡報。 此欄必須追蹤隨後便會插入並為資料表和其相關更新 (多重值或子系) 資料表。 必須同步處理之間的同步處理服務與資料庫伺服器的時鐘。 如果沒有出現，可能會省略中 delta 匯入的某些項目。  
限制︰
    - 浮水印策略時不支援刪除物件。
- **快照**: （適用於 Microsoft SQL Server 只）[產生 Delta 檢視表使用快照](https://technet.microsoft.com/library/cc720640.aspx)
- **追蹤修訂**: （適用於 Microsoft SQL Server 只）[瞭解追蹤修訂](https://msdn.microsoft.com/library/bb933875.aspx)  
限制︰
    - 錨點與 DN 屬性必須屬於主索引鍵的資料表中選取的物件。
    - SQL 查詢時不支援匯入及匯出的追蹤修訂。

**其他參數**︰ 指定資料庫伺服器時區指出您的資料庫伺服器的位置。 此值用來支援各種不同的格式的日期與時間的屬性。

連接線永遠儲存日期和日期-時間 UTC 格式。 若要能夠正確轉換日期與時間、 資料庫伺服器及使用的檔案格式的時區必須指定。 格式應該.Net 格式來表示。

匯出期間每個日期時間屬性必須提供給 UTC 時間格式的連接器。

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**密碼設定**︰ 連接器提供密碼同步處理功能和支援的設定，並變更密碼。

連接器提供兩種方法可以支援密碼同步處理︰

- **預存程序**︰ 此方法需要兩個預存程序支援設定及變更密碼。 輸入所有參數新增及變更**設定密碼預存程序**和**變更密碼 SP**分別為每一個參數下方的範例中的 [密碼] 作業。
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **密碼副檔名**︰ 此方法需要密碼擴充 DLL （您需要提供實作[IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx)介面擴充功能 DLL 名稱）。 密碼擴充功能組件必須放在副檔名資料夾，使連接器可以載入 DLL 在執行階段。
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

您也必須啟用密碼管理**設定副檔名**頁面上。
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>設定磁碟分割區與階層
分割和階層在頁面上，選取 [所有的物件類型]。 每個物件類型是它自己的磁碟分割。

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

您也可以覆寫 [**連線**] 或 [**全域參數**的頁面上定義的值。

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>設定定位點
此頁面是唯讀，因為已經定義錨點。 選取的錨點屬性一定會附加的物件類型，以確定資料維持在物件類型唯一。

![錨點](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>設定執行的步驟參數
下列步驟設定連接器上執行的設定檔。 這些設定，請執行匯入及匯出資料的實際工時。

### <a name="full-and-delta-import"></a>完整和 Delta 匯入
一般的 SQL 連接器支援全與 Delta 匯入使用這些方法︰

- 表格
- 檢視
- 預存程序
- SQL 查詢

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**在表格/檢視**  
若要匯入物件的多重值的屬性，您必須提供**名稱的多表格/檢視**中的逗點分隔表格/檢視名稱] 和 [**聯結條件**中的個別聯結條件，父資料表。

範例︰ 您想要匯入員工物件和所有多重值的屬性。 有兩個資料表，「 員工 （主資料表） 部門 （多重值）。
執行下列動作︰

- 在 [**表格/檢視/預存程序**輸入**員工**。
- 輸入部門**名稱的多表格**檢視畫面。
- 輸入員工與部門之間的聯結條件中**聯結條件**，例如`Employee.DEPTID=Department.DepartmentID`。
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**預存程序**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- 如果您有多資料時，建議實作分頁與您預存程序。
- 如您預存程序支援分頁，您需要提供開始索引和結束索引。 請參閱︰[有效率地分頁大量的資料](https://msdn.microsoft.com/library/bb445504.aspx)。
- @StartIndex與@EndIndex，來取代在執行階段**設定的步驟**] 頁面上設定的個別頁面大小值。 例如，當連接器擷取第一頁和頁面大小設定 500，在這種情況下@StartIndex會是 1 和@EndIndex500。 連接器擷取後續頁面和變更時，這些值增加@StartIndex&@EndIndex值。
- 若要執行參數化預存程序，提供的參數`[Name]:[Direction]:[Value]`格式。 在各行 （使用 Ctrl + Enter 來取得新的一行） 中輸入每個參數。
- 一般的 SQL 連接器也支援 Microsoft SQL Server 匯入作業連結伺服器。 如果從連結伺服器中的表格，應該擷取資訊，表格應提供的格式︰`[ServerName].[Database].[Schema].[TableName]`
- 一般的 SQL 連接器支援只有有類似結構 （同時別名名稱與資料類型） 之間執行步驟資訊和結構描述偵測的物件。 如果不同結構描述和提供的資訊，在執行步驟，從選取的物件，然後 SQL 連接線即不支援這種情況。

**SQL 查詢**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- 多個結果設定不受支援的查詢。
- 支援的分頁的 SQL 查詢，並提供開始索引和結束索引為變數支援分頁。

### <a name="delta-import"></a>Delta 匯入
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Delta 匯入設定需要相較於完整匯入一些其他設定。

- 如果您選擇的觸發程序或快照的方法，以追蹤 delta 變更，然後提供在**歷程記錄的資料表或快照資料庫名稱**] 方塊中的歷程記錄的資料表或快照資料庫。
- 您也需要提供歷程記錄的資料表和父資料表之間的聯結條件，例如`Employee.ID=History.EmployeeID`
- 若要追蹤父資料表中的交易記錄資料表中，您必須提供資料行名稱包含作業資訊 （新增/更新/刪除）。
- 如果您選擇浮水印追蹤 delta 的變更，然後提供包含作業資訊**水標記欄名稱**中的資料行名稱。
- **變更類型屬性**的資料行是必要的變更類型。 此欄對應至 [差異] 檢視中的變更類型主要資料表或多重值] 表格中的變更。 此資料行中包含 Modify_Attribute 變更類型的屬性層級的變更] 或 [新增]，[修改]，或刪除變更類型的物件層級的變更類型。 如果這是預設值新增以外，修改]，或刪除]，然後您可以定義當您使用這個選項，這些值。

### <a name="export"></a>匯出
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

一般的 SQL 連接器支援匯出使用支援的四種方法，例如︰

- 表格
- 檢視
- 預存程序
- SQL 查詢

**在表格/檢視**  
如果您選擇 [表格/檢視] 選項，連接器就會產生執行匯出個別的查詢。

**預存程序**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

如果您選擇 [預存程序] 選項，匯出需要三個不同的預存程序，以執行插入/更新刪除作業。

- **新增預存程序名稱**︰ 如果任何物件都有各自資料表中插入的連接器，此預存程序執行。
- **更新預存程序名稱**︰ 如果任何物件都有各自資料表中的更新的連接器，此預存程序執行。
- **刪除預存程序名稱**︰ 如果任何物件都有各自資料表中刪除的連接器，此預存程序執行。
- 用來做為參數值預存程序的結構描述從選取的屬性。 例如， `EmployeeName: INPUT: @EmployeeName` （EmployeeName 已在連接器結構描述和連接器時執行匯出取代的個別值）
- 若要執行參數化預存程序，提供中的參數`[Name]:[Direction]:[Value]`格式。 在各行 （使用 Ctrl + Enter 來取得新的一行） 中輸入每個參數。

**SQL 查詢**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

如果您選擇的 SQL 查詢的選項，匯出需要三個不同的查詢執行插入/更新刪除作業。

- **插入查詢**︰ 如果任何物件都有各自資料表中插入的連接器，此查詢會執行。
- **更新查詢**︰ 如果任何物件都有各自資料表中的更新的連接器，此查詢會執行。
- **刪除查詢**︰ 如果任何物件都有各自資料表中刪除的連接器，此查詢會執行。
- 結構描述，例如用來做為參數值至查詢，從選取的屬性`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>疑難排解

-   如何啟用疑難排解連接器記錄的資訊，請參閱[如何啟用 ETW 追蹤連接器的](http://go.microsoft.com/fwlink/?LinkId=335731)。
