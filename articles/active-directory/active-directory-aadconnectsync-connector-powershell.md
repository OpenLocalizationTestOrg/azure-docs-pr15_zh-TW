<properties
   pageTitle="PowerShell 連接器 |Microsoft Azure"
   description="本文將說明如何設定 Microsoft Windows PowerShell 連接器。"
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

# <a name="windows-powershell-connector-technical-reference"></a>Windows PowerShell 連接器技術參照
本文將說明在 Windows PowerShell 連接器。 本文適用於下列產品︰

- Microsoft 身分識別管理員 2016 (MIM2016)
- Forefront 識別管理員 2010 R2 (FIM2010R2)
    -   必須使用 hotfix 4.1.3671.0 或更新版本[KB3092178](https://support.microsoft.com/kb/3092178)。

如需 MIM2016 與 FIM2010R2，連接線即從[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=717495)下載。

## <a name="overview-of-the-powershell-connector"></a>PowerShell 連接器的概觀
PowerShell 連接器可讓您使用 Windows PowerShell 以 Api 所提供的外部系統整合同步處理服務。 連接線會提供通話為基礎的可延伸連線管理代理程式的功能橋樑，2 (ECMA2) 架構和 Windows PowerShell。 如需有關 ECMA 架構的詳細資訊，請參閱[可延伸連線 2.2 管理代理程式參照](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx)。

### <a name="prerequisites"></a>必要條件
使用連接器之前，請確定您有下列同步處理伺服器上︰

- 4.5.2 的 Microsoft.NET Framework 或更新版本
- 2.0、 3.0 或 4.0 的 Windows PowerShell

同步處理服務伺服器上的執行原則必須設定為允許連接器執行 Windows PowerShell 指令碼。 除非連接器就會執行經過數位簽章，指令碼執行這個命令來設定執行原則︰  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>建立新的連接器
若要建立 Windows PowerShell 連接器同步處理服務中，您必須提供一系列的 Windows PowerShell 指令碼執行同步處理服務要求的步驟。 根據您連線至資料來源與您所需的功能，您必須執行的指令碼不盡相同。 本節描述每個可以實作與它們是必要的指令碼。

Windows PowerShell 連接器是設計用來儲存每個同步處理服務資料庫內的指令碼。 雖然可以執行的儲存在檔案系統上的指令碼，很容易插入本文的每個指令碼直接在連接器的設定。

若要建立 PowerShell 連接器，**同步處理服務**中選取 [**管理代理程式**並**建立**。 選取**PowerShell (Microsoft)**連接器。

![建立連接器](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>連線
提供連線到遠端系統設定的參數。 這些值安全地儲存同步處理服務，提供您的 Windows PowerShell 指令碼時執行的連接器。

![連線](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

您可以設定下列連線參數︰

**連線**

參數 | 預設值 | 用途
--- | --- | ---
伺服器 | <Blank> | 連接器必須連接到的伺服器名稱。
網域 | <Blank> | 要執行連接器時，使用儲存的認證的網域。
使用者 | <Blank> | 執行連接器時，使用儲存的認證 Username。
密碼 | <Blank> | 要執行連接器時，使用儲存的認證的密碼。
模擬連接器帳戶 | False | 為 true 時，同步處理服務會提供的認證的內容中執行的 Windows PowerShell 指令碼。 可能的話，建議的**$Credentials**參數傳遞至每個指令碼，而不是模擬可用。 如需其他的權限，才能使用此選項的詳細資訊，請參閱[模擬的其他設定](#additional-configuration-for-impersonation)。
當模擬載入使用者設定檔 | False | 指示 Windows 模擬時載入使用者設定檔，連接器的認證。 如果模擬的使用者會有漫遊設定檔，連接器就不會載入漫遊的設定檔。 如需其他的權限，才能使用此參數的詳細資訊，請參閱[模擬的其他設定](#additional-configuration-for-impersonation)。
當模擬的登入類型 | 無 | 模擬時登入類型。 如需詳細資訊，請參閱[dwLogonType] [dw]文件。
已簽署指令檔 | False | 如果為 true，Windows PowerShell 連接器驗證的每個指令碼都有有效的數位簽章。 如果是 false，請確定同步處理服務伺服器的 Windows PowerShell 執行原則 RemoteSigned 或 [沒有限制。

**常見的模組**  
連接器可讓您共用的 Windows PowerShell 模組儲存設定。 當連接器執行指令碼時，Windows PowerShell 模組擷取檔案系統，讓它可以匯入每個指令碼。

匯入、 匯出和密碼同步處理的指令碼，一般模組擷取至連接器的 MAData 資料夾。 結構描述、 驗證、 階層和分割探索指令碼，一般模組擷取至 %TEMP%資料夾。 在這兩種情況下，解壓縮的常見模組指令碼名為根據的一般模組指令碼名稱設定

若要載入 MAData 資料夾中稱為 FIMPowerShellConnectorModule.psm1 模組，請使用下列陳述式︰`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

若要載入 %TEMP%資料夾從呼叫 FIMPowerShellConnectorModule.psm1 模組，請使用下列陳述式︰`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**參數驗證**  
驗證指令碼是選擇性的 Windows PowerShell 指令碼，可確保由系統管理員所提供的連接器設定參數是有效的。 驗證伺服器連線認證與連線參數是驗證指令碼的常見使用方式。 驗證指令碼稱為之後下列索引標籤，並修改對話方塊︰

- 連線
- 全域的參數
- 分割區設定

驗證指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | 設定] 索引標籤或觸發驗證邀請的對話方塊。
ConfigParameters | [KeyedCollection] [ keyk] [字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。

驗證指令碼應該管線傳回單一 ParameterValidationResult 物件。

**結構描述探索**  
結構描述探索指令碼是必要欄位。 物件類型、 屬性和屬性設定屬性流程規則時，同步處理服務所使用的限制式，則會傳回這個指令碼。 結構描述探索指令碼連接器建立期間執行，並填入連接器的結構描述。 在同步處理服務管理員中的 [重新整理的結構描述] 動作也使用它。

結構描述探索指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection] [ keyk] [字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。

指令碼必須傳回單一的[結構描述][schema]管線的物件。 結構描述物件組成[SchemaType] [schemaT]代表物件類型的物件 (例如︰ 使用者和群組)。 SchemaType 物件保留集合[SchemaAttribute] [schemaA]代表屬性的物件 (例如︰ 名字、 姓氏及郵寄地址) 的類型。

**額外的參數**  
除了標準設定的設定，您可以定義其他自訂設定的設定的特定連接器的執行個體。 這些參數可以指定在連接線的磁碟分割，或執行的步驟層級，並從相關的 Windows PowerShell 指令碼存取。 自訂設定的設定可儲存在同步處理服務資料庫以純文字格式，或可能加密。 同步處理服務會自動加密並解密安全設定所需的設定。

若要指定自訂的設定，請以逗號 （，） 分隔每個參數的名稱。

若要從指令碼存取自訂設定的設定，您必須後置詞的名稱，以底線 ( \_ ) 和參數 （全域、 分割或 RunStep） 的範圍。 例如，若要存取全域 FileName 參數，請使用此程式碼片段︰`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>功能
管理代理人設計工具] 的 [功能] 索引標籤定義的行為和功能的連接器。 連接器建立之後，就無法修改此索引標籤上所做的選擇。 下表列出的功能設定。

![功能](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

功能 | 描述 |
--- | --- |
[辨別的名稱樣式][dnstyle] | 表示如果連接器支援辨別的名稱，但如果是這樣，什麼樣式。
[匯出的類型][exportT] | 決定呈現匯出指令碼的物件的類型。 <li>屬性變更時，AttributeReplace – 包含完整的多重值屬性的值。</li><li>屬性變更時，AttributeUpdate – 包含多重值屬性差異。</li><li>MultivaluedReferenceAttributeUpdate-包含完整的值-參考多重值屬性只針對多重值的參照屬性的差異。</li><li>ObjectReplace – 當任何屬性變更，包括所有屬性的物件</li>
[資料正常化][DataNorm] | 指示要標準化錨點屬性，只要是指令碼之前同步處理服務。
[物件確認][oconf] | 設定同步處理服務擱置匯入行為。 <li>標準 – 預期匯出所有的變更，才能透過匯入的預設行為</li><li>NoDeleteConfirmation – 當刪除物件時，就會產生的沒有等待匯入。</li><li>NoAddAndDeleteConfirmation – 當物件建立或刪除，就會產生的沒有等待匯入。</li>
DN 作為錨點 | 如果 LDAP 設定辨別名稱樣式，連接器空間的錨點屬性也是辨別的名稱。
並行作業的幾個連接線 | 核取時，就可以同時執行多個 Windows PowerShell 的連接器。
磁碟分割區 | 核取時，連接線支援多個的磁碟分割區與分割探索。
階層圖 | 核取時，連接線支援 LDAP 樣式的階層式結構。
啟用匯入 | 核取時，連接線匯入資料透過匯入指令碼。
啟用 Delta 匯入 | 核取時，連接器就可以從匯入的指令碼要求的差異。
啟用匯出 | 核取時，連接線會將資料匯出指令碼透過匯出。
啟用完整的匯出 | 核取時，匯出指令碼支援匯出整個連接器空間。 若要使用這個選項，啟用匯出必須也會檢查。
沒有參考第一個匯出階段中的值 | 核取時，在第二個匯出行程中匯出參考屬性。
啟用物件重新命名 | 核取時，則可以修改辨別的名稱。
刪除新增如下圖所取代 | 核取時，刪除新增作業會匯出為單一替代方案。
啟用密碼作業 | 核取時，支援密碼同步處理指令碼。
啟用第一次匯出密碼 | 核取時，建立物件時，會匯出期間佈建設定的密碼。

### <a name="global-parameters"></a>全域的參數
管理代理人設計工具中的 [全域參數] 索引標籤可讓您設定 Windows PowerShell 指令碼所執行的連接器。 您也可以設定全域的自訂設定定義在 [連線] 索引標籤上的 [設定] 的值。

**分割探索**  
分割是一個共用的結構描述中不同的命名空間。 例如，在 Active Directory 中每個網域是一個樹系的磁碟分割。 磁碟分割是邏輯群組匯入及匯出作業。 匯入及匯出有內容，並所有作業會發生這個內容中的磁碟分割。 磁碟分割區應該要代表 LDAP 中的階層。 磁碟分割的辨別的名稱在匯入用於驗證所傳回的所有物件都都分割的資料範圍內。 分割辨別的名稱在也會使用佈建從 metaverse 連接器空間來決定物件應該匯出期間與相關聯的磁碟分割。

分割探索指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters  | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。

指令碼必須傳回單一的[磁碟分割][part]物件或磁碟分割管線物件的 [T] 清單。

**階層探索**  
LDAP 的辨別名稱樣式功能時，才會使用階層探索指令碼。 指令碼用來讓您瀏覽並選取容器一組被視為近或拉範圍匯入及匯出作業。 指令碼應該僅提供所提供的指令碼的根節點的直接子系節點的清單。

階層探索指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。
ParentNode | [HierarchyNode][hn] | 指令碼應該要傳回直接子系階層的根節點。

指令碼必須傳回其中一個子 HierarchyNode 物件或子 HierarchyNode 物件的 [T] 清單管線。

#### <a name="import"></a>匯入
支援匯入作業的連接器必須實作三個指令碼。

**開始匯入**  
開始匯入的指令碼執行匯入執行步驟的開頭。 在此步驟中，您可以連線到來源系統，並執行匯入資料連線的系統前的準備步驟。

開始匯入的指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | 通知指令碼匯入執行 （delta 或完整版），磁碟分割、 階層、 浮水印]，以及預期的頁面大小的類型。
類型 | [結構描述][schema] | 匯入的連接器空間的結構描述。

指令碼必須傳回單一[OpenImportConnectionResults] [oicres]物件至管線，例如︰`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**匯入資料**  
匯入資料指令碼稱為連接器，直到指令碼表示有沒有其他資料匯入。 Windows PowerShell 連接線具有 9999 物件的頁面大小。 如果您的指令碼會傳回匯入多個 9999 物件，您必須支援分頁。 自訂資料屬性時，您可以使用存放區浮水印讓每次匯入資料指令碼稱為連接器公開，您的指令碼會繼續進行匯入的物件停止的地方。

匯入資料指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。
GetImportEntriesRunStep | [ImportRunStep][irs] | 保留期間可使用浮水印 (CustomData) 分頁匯入與 delta 匯入。
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | 通知指令碼匯入執行 （delta 或完整版），磁碟分割、 階層、 浮水印]，以及預期的頁面大小的類型。
類型 | [結構描述][schema] | 匯入的連接器空間的結構描述。

匯入資料指令碼必須撰寫清單 [[CSEntryChange][csec]] 管線的物件。 此集合被由 CSEntryChange 代表匯入的每個物件的屬性。 完整匯入執行]，在此集合應該有一組完整的每個物件的所有屬性的 CSEntryChange 物件。 期間 Delta 匯入，請 CSEntryChange 物件可能包含每個物件要匯入或變更物件的 （取代模式） 的完整表示屬性層級的差異。

**結束匯入**  
在匯入執行結束時，結束匯入的指令碼執行。 這個指令碼應該執行任何清理工作所需 （例如，系統關閉連線） 並失敗的回應。

結束匯入的指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | 通知指令碼匯入執行 （delta 或完整版），磁碟分割、 階層、 浮水印]，以及預期的頁面大小的類型。
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | 匯入已結束的原因的相關通知指令碼。

指令碼必須傳回單一[CloseImportConnectionResults] [cicres]物件至管線，例如︰`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>匯出
相同的連接器匯入架構，支援匯出的連接器必須實作三個指令碼。

**開始匯出**  
開始匯出指令碼執行匯出執行步驟的開頭。 在此步驟中，您可以連線到來源系統，並連線的系統匯出資料之前先進行任何準備的步驟。

開始匯出指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | 通知指令碼匯出執行 （delta 或完整版），磁碟分割、 階層和預期的頁面大小的類型。
類型 | [結構描述][schema] | 連接器空間匯出的結構描述。

指令碼不應傳回任何輸出管線。

**匯出資料**  
同步處理服務呼叫匯出資料指令碼，則需要處理所有的擱置匯出的次數。 如果連接器空間有更多的擱置匯出比連接器的頁面大小，匯出資料指令碼可能相同的物件的多個時間和可能多次呼叫。

匯出資料指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。
CSEntries | IList[CSEntryChange][csec] | 所有連接器空間物件已在此階段期間處理匯出擱置的清單。
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | 通知指令碼匯出執行 （delta 或完整版），磁碟分割、 階層和預期的頁面大小的類型。
類型 | [結構描述][schema] | 連接器空間匯出的結構描述。

匯出資料指令碼必須傳回[PutExportEntriesResults] [peeres]管線的物件。 不需要此物件不會包含結果的資訊，針對每個匯出的連接線，除非發生錯誤或錨點屬性變更。 例如，若要返回管線 PutExportEntriesResults 物件︰`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**結束匯出**  
在執行匯出結束時，若要執行的結束匯出指令碼。 這個指令碼應該執行任何清理工作所需 （例如，系統關閉連線） 並失敗的回應。

結束匯出指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | 通知指令碼匯出執行 （delta 或完整版），磁碟分割、 階層和預期的頁面大小的類型。
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | 已結束匯出的原因的相關通知指令碼。

指令碼不應傳回任何輸出管線。

#### <a name="password-synchronization"></a>密碼同步處理
Windows PowerShell 連接器可以作為密碼變更重設的目標。

密碼指令碼會從連接器收到下列參數︰

名稱 | 資料類型 | 描述
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][字串， [ConfigParameter][cp]] | 連接器設定參數的資料表。
認證 | [PSCredential][pscred] | 包含 [連線] 索引標籤上的 [由系統管理員輸入任何認證。
分割 | [分割][part] | CSEntry 位於目錄磁碟分割。
CSEntry | [CSEntry][cse] | 變更密碼或重設，就會收到的物件連接器空間項目。
OperationType | 字串 | 指出作業是否為 (**SetPassword**) 重設或變更 (**變更密碼**)。
PasswordOptions | [PasswordOptions][pwdopt] | 旗標，指定預定的密碼重設行為。 只使用 OperationType **SetPassword**如果此參數。
OldPassword | 字串 | 填入變更密碼的物件的舊密碼。 這個參數僅適用於 OperationType 時**變更密碼**的。
NewPassword | 字串 | 填入的指令碼應該設定物件的新密碼。

密碼指令碼不到 Windows PowerShell 管線傳回任何結果。 如果發生錯誤的密碼指令碼，指令碼應該會擲回其中一個問題的相關通知同步處理服務下列例外狀況︰

- [PasswordPolicyViolationException] [ pwdex1] – 如果密碼不符合連線系統中的密碼原則擲回 Expression.error。
- [PasswordIllFormedException] [ pwdex2] – 如果密碼不接受連線系統擲回 Expression.error。
- [PasswordExtension] [ pwdex3] – 擲回密碼指令碼中的所有其他錯誤。

## <a name="sample-connectors"></a>範例連接器
可用的範例連接器需完成的概觀，請參閱[Windows PowerShell 連接器範例連接器集合][samp]。

## <a name="other-notes"></a>其他備忘稿

### <a name="additional-configuration-for-impersonation"></a>模擬的其他設定
授與使用者的模擬同步處理服務伺服器上的下列權限︰

下列登錄機碼的讀取權限︰

- HKEY_USERS\\[SynchronizationServiceServiceAccountSID] \Software\Microsoft\PowerShell
- HKEY_USERS\\[SynchronizationServiceServiceAccountSID] \Environment

若要判斷安全性 （識別碼） 的同步處理服務服務帳戶，請執行下列 PowerShell 命令︰

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

下列檔案系統資料夾的讀取權限︰

- %ProgramFiles%\Microsoft forefront 身分識別 Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft forefront 身分識別 Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront 身分識別 Manager\2010\Synchronization Service\MaData\\{ConnectorName}

{ConnectorName} 版面配置區以替代 Windows PowerShell 連接器名稱。

## <a name="troubleshooting"></a>疑難排解

-   如何啟用疑難排解連接器記錄的資訊，請參閱[如何啟用 ETW 追蹤連接器的](http://go.microsoft.com/fwlink/?LinkId=335731)。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
