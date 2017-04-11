<properties
   pageTitle="Lotus Domino 連接器 |Microsoft Azure"
   description="本文將說明如何設定 Microsoft 的 Lotus Domino 連接器。"
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

# <a name="lotus-domino-connector-technical-reference"></a>Lotus Domino 連接器技術參照
本文將說明 Lotus Domino 連接器。 本文適用於下列產品︰

- Microsoft 身分識別管理員 2016 (MIM2016)
- Forefront 識別管理員 2010 R2 (FIM2010R2)
    -   必須使用 hotfix 4.1.3671.0 或更新版本[KB3092178](https://support.microsoft.com/kb/3092178)。

如需 MIM2016 與 FIM2010R2，連接線即從[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=717495)下載。

## <a name="overview-of-the-lotus-domino-connector"></a>Lotus Domino 連接器的概觀
Lotus Domino 連接器可讓您的同步處理服務整合 IBM 的 Lotus Domino 伺服器。

從高層級的觀點來看，目前版本的連接器支援下列功能︰

功能 | 支援
--- | ---
連接的資料來源 | 伺服器︰ <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>用戶端︰<li>Lotus Notes 9.x</li>
案例 | <li>物件生命週期管理</li><li>群組管理</li><li>密碼管理</li>
作業 | <li>完整和 Delta 匯入</li><li>匯出</li><li>設定，變更密碼 HTTP 密碼</li>
結構描述 | <li>人員 （漫遊使用者，連絡人 （具備 「 沒有憑證））</li><li>群組</li><li>資源 （會議室，資源的線上會議）</li><li>郵件中的資料庫</li><li>動態探索的支援物件的屬性</li>

Lotus Domino 連接器使用 Lotus Notes 用戶端與 Lotus Domino 伺服器通訊。 執行此相依性，必須安裝支援的 Lotus Notes 用戶端同步處理伺服器上。 用戶端和伺服器之間的通訊實作透過 Lotus 備忘稿.NET Interop (Interop.domino.dll) 介面。 這個介面有助於 Microsoft.NET 平台和 Lotus Notes 用戶端之間的通訊，以及支援 Lotus Domino 文件和檢視的存取權。 Delta 匯入，您也可 c + + 原生介面用 （根據所選的 delta 匯入的方法）。

### <a name="prerequisites"></a>必要條件
使用連接器之前，請確定您有下列同步處理伺服器上︰

- 4.5.2 的 Microsoft.NET Framework 或更新版本
- 在您同步處理的伺服器上必須安裝 Lotus Notes 用戶端
- Lotus Domino 連接器需要預設 Lotus Domino LDAP 結構描述資料庫 (schema.nsf) 會出現在伺服器上 Domino 目錄。 如果您不存在，您可以執行或重新啟動 LDAP 伺服器上的服務 Domino 來進行安裝。

### <a name="connected-data-source-permissions"></a>連線的資料來源權限
若要執行任何支援的工作 Lotus Domino 連接器，您必須是下列群組的成員︰

- 完整存取系統管理員
- 系統管理員
- 資料庫系統管理員

下表列出的權限所需的每一項作業︰

作業 | 存取權限
--- | ---
匯入 | <li>閱讀公用文件</li><li> 完整存取系統管理員 （當您的完整存取管理員群組的成員時，您的有效的存取權在中自動擁有 ACL。）</li>
匯出並設定密碼 | 有效的存取權︰ <li>建立文件</li><li>刪除文件</li><li>閱讀公用文件</li><li>撰寫公用文件</li><li>複寫或複製的文件</li>匯出作業，您也需要下列角色︰ <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### <a name="direct-operations-and-adminp"></a>直接作業和 AdminP
作業是直接前往 Domino 目錄或完成 AdminP 程序。 下列表格列出所有支援的物件，作業，若適用，相關的實作方法︰

**主要通訊錄**

物件 | 建立 | 更新 | 刪除
--- | --- | --- | ---
人員 | AdminP | 直接 | AdminP
群組 | AdminP | 直接 | AdminP
MailInDB | 直接 | 直接 | 直接
資源 | AdminP | 直接 | AdminP

**第二個通訊錄**

物件 | 建立 | 更新 | 刪除
--- | --- | --- | ---
人員 | N/A | 直接 | 直接
群組 | 直接 | 直接 | 直接
MailInDB | 直接 | 直接 | 直接
資源 | N/A | N/A | N/A

建立資源時，會建立備忘稿文件。 同樣地，刪除資源時，會刪除備忘稿文件。

### <a name="ports-and-protocols"></a>連接埠和通訊協定
IBM Lotus Notes 用戶端和 Domino 伺服器通訊使用備忘稿遠端程序呼叫 (NRPC) NRPC 使用 TCP/IP 的位置。 預設連接埠號碼 1352，但可以變更 Domino 系統管理員。

### <a name="not-supported"></a>不支援
Lotus Domino 連接器的目前版本不支援下列作業︰

- 伺服器間移動信箱。

## <a name="create-a-new-connector"></a>建立新的連接器

### <a name="client-software-installation-and-configuration"></a>用戶端軟體安裝和設定
Lotus Notes 必須安裝在伺服器**之前**安裝的連接器。

當您安裝時，請確定您執行的**單一使用者安裝**。 預設**多使用者安裝**無法運作。  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

在 [功能] 頁面上安裝的所需的 Lotus Notes 功能和**用戶端單一登入**。 單一登入，才能無法登入的 Domino 伺服器的連接。  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**附註︰**從 Lotus Notes 開始一次位於相同的伺服器使用為連接器的服務帳戶的帳戶的使用者。 也請務必關閉 Lotus Notes 用戶端在伺服器上。 其無法執行一次嘗試連線到 Domino 伺服器的連接器。

### <a name="create-connector"></a>建立連接器
若要建立 Lotus Domino 連接器，**同步處理服務**中選取 [**管理代理程式**並**建立**。 選取**Lotus Domino (Microsoft)**連接器。  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

如果您的同步處理服務版本可讓您設定**架構**，請確定連接器設定為預設值為**程序**中執行。

### <a name="connectivity"></a>連線
在 [連線] 頁面中，您必須指定 Lotus Domino 伺服器名稱，然後輸入的登入認證。  
![連線](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

Domino 伺服器屬性支援的伺服器名稱的兩種格式︰

- 伺服器名稱
- 伺服器名稱/DirectoryName

**伺服器名稱/DirectoryName**格式是慣用的格式，此屬性，因為連接器連絡人 Domino 伺服器時，它會提供更快獲得回應。

提供的使用者識別碼檔案會儲存於設定資料庫的同步處理服務。

**Delta**匯入您有以下選項︰

- **無**。 連接器不會執行任何 delta 匯入。
- **加入/更新**。 連接器用途 delta 匯入新增和更新動作。 [刪除]，是必要的**完整匯入**作業。 這項作業使用.Net interop。
- **新增/更新/移除**]。 連接器用途 delta 匯入新增、 更新及刪除作業。 這項作業使用原生 c + + 介面。

在 [**結構描述] 選項**中，您有下列選項︰

- **預設結構描述**。 連接線會偵測從 Domino 伺服器的結構描述。 這是預設的選項。
- **DSML 結構描述**。 只有使用 Domino 伺服器不會揭露結構描述。 然後您可以建立 DSML 檔案的結構描述，然後請改為匯入。 如需有關 DSML 的詳細資訊，請參閱[OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml)。

當您按一下 [下一步] 時，會驗證的使用者識別碼和密碼設定參數。

### <a name="global-parameters"></a>全域的參數
全域參數在頁面上，您可以設定的時區與匯入及匯出作業選項。  
![全域的參數](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

**Domino 伺服器時區**參數定義 Domino 伺服器的位置。

此設定選項是必要的支援**delta 匯入**作業，因為它會啟用同步處理服務決定之間的最後兩個匯入的變更。

#### <a name="import-settings-method"></a>匯入設定方法
**執行完整匯入由**具有這些選項︰

- 搜尋
- 檢視 （建議使用）

使用**搜尋**索引中 Domino 但一般即時不會更新索引，並從伺服器所傳回的資料永遠不正確。 使用許多變更為系統，這個選項，通常不佳，並提供在某些情況下會刪除 false。 不過，**搜尋**是較快，**檢視**。

**檢視**是建議的選項，因為它所提供的資料正確的狀態。 則稍微低於搜尋。

#### <a name="creation-of-virtual-contact-objects"></a>建立的虛擬連絡人的物件
**啟用建立\_連絡人物件**具有這些選項︰

- 無
- 非參考的值
- 參考及非參考的值

在 Domino，參考屬性包含許多不同的格式，參考其他物件。 若要能夠代表不同的變化，連接器實作\_連絡物件，也就是**虛擬連絡人**(VC)。 這些物件建立，讓他們可以加入現有 MV 物件或投射為新的物件。 如此一來，可以保留屬性參照。

啟用此設定，如果參照屬性的內容沒有 DN 格式，\_建立連絡人物件。 例如，群組的成員屬性可以包含 SMTP 位址。 您也可簡短名稱和其他參考屬性中的屬性。 這個案例中，選取**非參考的值**。 此設定是最常見的 Domino 實作設定。

當 Lotus Domino 設定為不同的通訊錄有不同的辨別名稱代表相同的物件時，則可能也建立\_連絡物件在通訊錄中找到的所有參照值。 這個案例中，選取 [**參照及非參照值**] 選項。

如果您有多個值屬性中 Domino**全名**中時，然後也想要啟用的虛擬連絡人建立，以便可以解決參照。 例如，這個屬性可以結婚或離婚後有多個值。 選取的核取方塊**啟用...]。全名有多個值**用這種情況。

以正確的屬性，加入\_連絡人物件會加入至 MV 的物件。

這些物件都有 VC =\_連絡人新增至其 DN。

#### <a name="import-settings-conflict-object"></a>匯入的設定，會發生衝突物件
**排除衝突物件**

在大型的 Domino 實作中，可能是多個物件都有相同的 DN 因為複寫問題。 在下列情況下，連接器就會看到兩個不同的 UniversalIDs 但相同 DN 的物件。 這種衝突可能會造成的暫時性連接器空間中建立的物件。 連接器可以略過] 中已選取 Domino 為複寫受害者的物件。 建議您保留選取此核取方塊。

#### <a name="export-settings"></a>匯出的設定
如果未選取 [**使用 AdminP，更新參照**] 選項，然後匯出參照屬性，例如 [成員] 是直接電話，而不使用 AdminP 程序。 只有當 AdminP 尚未設定以維護參考完整性時，才能使用這個選項。

#### <a name="routing-information"></a>傳遞資訊
在 Domino，可能是參照屬性具有內嵌 dn 結尾的傳遞資訊。 在群組中的成員屬性，例如，可能包含**CN=example/organization@ABC**。 後置字元@ABC路由的資訊。 路由資訊的 Domino 用於電子郵件傳送到正確的 Domino 系統，這可能是不同的組織中的系統。 在路由資訊] 欄位中，您可以指定傳遞連接器的範圍中組織內使用的尾碼。 如果其中一個值的參照屬性中找到為結尾，路由資訊會移除參考。 如果參照值路由後的置字元無法對應到其中一個指定，這些值\_建立連絡人物件。 這些\_建立連絡人物件，用**RO=@**插入 DN。 這些\_連絡人物件允許加入實際物件，必要時也會新增下列屬性︰ \_routingName，\_連絡人姓名，\_顯示名稱，並 UniversalID。

#### <a name="additional-address-books"></a>其他通訊錄
如果您不需要**協助**安裝，提供的次要通訊錄的名稱，您可以手動輸入這些通訊錄。

#### <a name="multivalued-transformation"></a>多重值的轉換
在 Lotus Domino 許多屬性是多重值。 相對應的 metaverse 屬性是通常是單一值。 藉由設定匯入及匯出作業選項，您可以啟用連接器，以協助進行必要的轉換的受影響的屬性。

**匯出**  
匯出作業選項支援兩種模式︰

- 新增項目
- 取代項目

**取代項目**– 當您選取此選項時，連接線永遠 Domino 中移除目前的屬性值，並提供的值取代。 提供預可以是單一值或多重值。

範例︰ 小幫手物件的屬性人員具有下列的值︰

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

如果命名**David 亞歷山大**新增助理指派給此人物件，則結果為︰

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**附加的項目**– 當您選取此選項時，連接線會保留 Domino 中的屬性上現有的值，並按一下頂端的 [資料] 清單中插入新的值。

範例︰ 小幫手物件的屬性人員具有下列的值︰

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

如果命名**David 亞歷山大**新增助理指派給此人物件，則結果為︰

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**匯入**  
匯入作業選項支援兩種模式︰

- 預設值
- 單一值的多重值

**預設**– 當您選取的預設選項時，所有屬性的所有值都會匯入。

**單一值的 Multivalued** – 當您選取這個選項，多重值屬性會轉換成單一值的屬性。 如果有一個以上的值，則會使用的頂端 （此值，通常是也是最新） 上的值。

範例︰ 小幫手物件的屬性人員具有下列的值︰

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

最新的更新，這個屬性會**David 亞歷山大**。 匯入作業選項設定為 Multivalued 單一值，因為連接器只匯入**David 亞歷山大**連接器空間。

若要將多重值的屬性轉換成單一值屬性的邏輯不適用於群組成員屬性及人全名屬性。

也可以設定匯入及匯出轉換為全域規則的例外狀況規則每個屬性，多重值屬性。 若要設定此選項，請輸入 [save]。[attributename]**排除屬性清單匯入**及**匯出排除屬性清單**的 [文字] 方塊中。 例如，如果您輸入 Person.Assistant 全域旗標設定為 [匯入所有的值，只的第一個值匯入小幫手。

#### <a name="certifiers"></a>Certifiers
連接器列出所有組織/組織單位。 若要將人員物件匯出至主要的通訊錄，以其密碼發證者需要。

如果所有 certifiers 都有相同的密碼，可以使用**所有 Certifers 密碼**。 您可以輸入的密碼，並只指定發證者檔案。

如果您只要匯入，然後您沒有指定任何 certifiers。

### <a name="configure-provisioning-hierarchy"></a>設定佈建的階層
當您設定的 Lotus Domino 連接器時，略過這個對話方塊的頁面。 Lotus Domino 連接器不支援佈建的階層。  
![佈建階層](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>設定磁碟分割區與階層
當您設定磁碟分割區和階層時，您必須先選取稱為 NAB=names.nsf 主要通訊錄。 除了主要的通訊錄中，您可以選取次要通訊錄，如果有的話。  
![磁碟分割區](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>選取的屬性
當您設定您的屬性時，您必須先選取所有屬性的前面加上**\_MMS\_**。 當您提供新物件 Lotus Domino 時，這些屬性所需

![屬性](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>物件生命週期管理
本節提供 Domino 的不同物件的概觀。

### <a name="person-objects"></a>人員物件
人員物件代表組織和組織單位的使用者。 預設的屬性，除了 Domino 系統管理員可以新增的人員物件的自訂屬性。 至少人員物件必須包含所有必要的屬性。 強制屬性的完整清單，請參閱[Lotus 筆記內容](#lotus-notes-properties)。 若要註冊人員物件，必須符合下列先決條件︰

- 通訊錄 (names.nsf) 必須已定義且應為主要的通訊錄。
- 您必須註冊特定使用者在組織中的 [O/OU 發證者識別碼和密碼 / 組織單位。
- 您必須設定一組特定的人員物件的 Lotus Notes 屬性。 這些屬性適用於佈建人員物件。 如需詳細資訊，請參閱本文稍後稱為[Lotus 筆記內容](#lotus-notes-properties)。
- 人員的初始 HTTP 密碼時將屬性和設定佈建。
- 人員物件必須下列三種支援類型其中一項︰
    1. 一般使用者有郵件檔案和使用者識別碼檔案
    2. 漫遊使用者 （包含所有漫遊資料庫檔案標準使用者）
    3. 連絡人 （沒有識別碼檔案的使用者）

（除了屬於連絡人） 的人員可進一步分為美國以及國際使用者所定義的值\_MMS\_IDRegType 屬性。 這些人員使用備忘稿用戶端存取 Lotus Domino 伺服器對備忘稿識別碼，以及人員文件。 如果他們使用備忘稿郵件，然後他們也可以將郵件檔案。 使用者必須註冊至會變成作用中。 如需詳細資訊，請參閱︰

- [設定 [備忘稿使用者](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [使用者註冊](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [管理使用者](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [重新命名的使用者](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

所有這些作業是 Lotus Domino 中執行，然後再匯入的同步處理服務。

### <a name="resources-and-rooms"></a>資源與聊天室
資源是資料庫的其他類型中 Lotus Domino。 資源可與各種類型的設備，例如投影機會議室有空。 有支援 Lotus Domino 連接器的資源所定義的資源類型屬性的子類型︰

資源類型 | 資源類型屬性
--- | ---
聊天室 | 1
資源 （其他） | 2
線上會議 | 3

若要使用的資源物件類型，以下是必要︰

- 在連線的 Domino 伺服器應該已存在於資源的預約功能資料庫
- 網站已定義的資源

資源的預約功能資料庫包含三種類型的文件︰

- 網站的設定檔
- 資源
- 保留項目

如需資源的預約功能資料庫的設定的詳細資訊，請參閱[預約資源資料庫的設定](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html)。

**建立、 更新和刪除資源**  
建立、 更新及刪除作業的執行 Lotus Domino 連接器資源的預約功能資料庫中。 建立資源，則為 Names.nsf （也就是主要通訊錄） 中的文件。 如需編輯和刪除資源的詳細資訊，請參閱[編輯和刪除資源文件](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html)。

**匯入及匯出作業的資源**  
資源可以匯入和匯出從同步處理服務，例如其他類型的物件。 選取 [物件類型設為資源在設定。 成功的匯出作業，您必須有資源類型、 會議資料庫及網站名稱的詳細資料。

### <a name="mail-in-databases"></a>郵件中的資料庫
郵件中資料庫是資料庫是設計用來接收郵件。 它是與任何特定的 Lotus Domino 使用者帳戶相關聯的 Lotus Domino 信箱 （也就是它沒有自己的識別碼檔案及密碼）。 郵件中的資料庫有與相關它自己的電子郵件地址專屬使用者識別碼 （「 簡短名稱 」）。

是否需要使用另一個信箱中有不同的使用者能共用自己電子郵件地址 (例如，group@contoso.com),建立郵件的資料庫。 這個信箱存取控制透過其存取控制清單 (ACL)，其中包含可開啟信箱備忘稿使用者名稱。

必要的屬性的清單，請參閱本文稍後的一部分[必要屬性](#mandatory-attributes)。

資料庫設計用來接收郵件，在郵件中資料庫文件中建立一個 Lotus Domino。 資料庫的複本儲存每個伺服器的 Domino 目錄中，必須有這份文件。 如需建立郵件中資料庫文件的詳細說明，請參閱[建立郵件中資料庫文件](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html)。

建立郵件的資料庫之前, 資料庫應該已經存在 （應都由 Lotus 管理員） 的 Domino 伺服器。

### <a name="group-management"></a>群組管理
您可以從下列資源，以取得詳細的 Lotus Domino 群組管理概觀︰

- [使用群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [建立群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [建立與修改群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [管理群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [重新命名群組](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>密碼管理
Lotus Domino 註冊的使用者，有兩種類型的密碼︰

1. 使用者密碼 （使用者識別碼檔案中的預存）
2. 網際網路 / HTTP 密碼

Lotus Domino 連接器支援只以 HTTP 密碼的作業。

若要執行密碼管理，您應該啟用連接器管理代理程式設計工具中的密碼管理。 若要啟用密碼管理，**啟用密碼管理**頁面上選取 [**設定的副檔名**] 對話方塊。  
![設定副檔名](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Lotus Domino 連接器支援追蹤網際網路密碼作業︰

- 設定密碼︰ 設定密碼設定使用者的 Domino 新 HTTP/網際網路密碼。 預設的帳戶也是已解除鎖定。 解除鎖定標幟公開同步處理引擎的 WMI 介面上。
- 變更密碼︰ 在這個案例中，使用者可能會想要變更的密碼或指定的時間之後變更密碼的提示。 這項作業，才能將] （和舊的新密碼） 是必要。 一旦變更，會更新 Lotus Domino 的新密碼。

如需詳細資訊，請參閱︰

- [使用網際網路鎖定功能](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [管理網際網路密碼](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>參考資訊
此區段列出屬性描述等 Lotus Domino 連接器屬性需求。

### <a name="lotus-notes-properties"></a>Lotus Notes 屬性
當您以 Lotus Domino 目錄提供人員物件時，物件必須一組特定內容的填入的特定值。 只需這些值的建立作業。

下表列出這些屬性，並提供他們的描述。

屬性 | 描述
--- | ---
\_MMS_AltFullName | 使用者的替代的完整名稱。
\_MMS_AltFullNameLanguage | 用於指定使用者的完整名稱替代語言。
\_MMS_CertDaysToExpire | 目前的日期之前憑證的天數到期。 如果未指定預設的日期是目前日期兩年數。
\_MMS_Certifier | 包含發證者的組織階層名稱的屬性。 例如︰ OU = OrganizationUnit，O = 組織，C = 國家/地區。
\_MMS_IDPath | 如果屬性是空值，沒有使用者識別碼檔案建立本機同步處理伺服器上。 如果屬性包含檔案名稱，則使用者識別碼檔案會建立 madata 資料夾中。 屬性也可以包含的完整路徑。
\_MMS_IDRegType | 具備 「 可以分類為連絡人、 美國使用者及國際使用者。 下表列出可能的值︰ <li>0-連絡人</li><li>1-美國使用者</li><li>2-國際使用者</li>
\_MMS_IDStoreType | 美國和國際使用者的必要的屬性。 這個屬性會包含指定的使用者識別碼是否儲存在備忘稿的通訊錄，或在該人員的郵件檔案附件的整數值。 如果使用者識別碼檔案是在通訊錄中的附件，您也可以在建立另存為檔案與\_MMS_IDPath。 <li>清空-識別碼保存庫，沒有識別檔案 （連絡人） 中的儲存區識別碼檔案。</li><li> 1-備忘稿通訊錄中的附件。 \_MMS_Password 屬性必須設定的使用者識別碼檔案附件</li><li>2-存放在人員的郵件檔案的識別碼。 \_MMS_UseAdminP 必須設定為 false，讓人員註冊期間會建立郵件檔案。 \_MMS_Password 屬性必須設定的使用者識別碼檔案。</li>
\_MMS_MailQuotaSizeLimit | 電子郵件檔案資料庫所允許的 mb 數。
\_MMS_MailQuotaWarningThreshold | 電子郵件檔案資料庫之前，會發出警告所允許的 mb 數。
\_MMS_MailTemplateName | 用來建立使用者的電子郵件檔案的電子郵件範本檔案。 如果指定的範本時，郵件檔案會建立使用指定的範本。 如果有不指定任何範本，預設範本檔案用來建立的檔案。
\_MMS_OU | 選擇性 OU 名稱底下發證者的屬性。 這個屬性應該是空的連絡人。
\_MMS_Password | 使用者的必要的屬性。 這個屬性會包含物件的識別檔案的密碼。
\_MMS_UseAdminP | 屬性會設定為 true，則如果郵件檔應該是 AdminP 程序建立的 Domino 伺服器上 （非同步匯出程序）。 如果屬性設定為 false 時，郵件檔案會以建立 Domino 使用者 （同步匯出程序中）。

與相關聯的識別檔案，為使用者\_MMS_Password 屬性必須包含的值。 透過 Lotus Notes 用戶端的電子郵件存取，請使用者 MailServer 和 MailFile 屬性必須包含的值。

若要透過網頁瀏覽器存取電子郵件，下列屬性必須包含的值︰

- MailFile-必要] 屬性包含儲存郵件檔案的 Lotus Domino 伺服器上的路徑。
- [MailServer-必要] 屬性，包含 Lotus Domino 伺服器的名稱。 此值會使用當您建立的 Domino 伺服器 Lotus 郵件檔案的名稱。
- HTTPPassword-選擇性包含 Web access 的密碼的物件的屬性。

若要存取 Domino 伺服器不具備郵件功能，HTTPPassword 屬性必須包含的值。 [MailFile 屬性和 MailServer 屬性可以是空的。

使用\_MMS_ IDStoreType = 2 （郵件檔案中儲存區識別碼，） NotesRegistrationclass MailSystem 屬性設定為 REG_MAILSYSTEM_INOTES (3)。

### <a name="mandatory-attributes"></a>必要屬性
Lotus Domino 連接器主要支援這些類型的物件 （文件類型）︰

- 群組
- 郵件中的資料庫
- 人員
- 連絡人 （沒有發證者人員）
- 資源

本節會列出的每個要匯出的 Domino 伺服器的支援物件的屬性。

物件類型 | 必要屬性
--- | ---
群組 | <li>ListName</li>
主要的資料庫 | <li>全名</li><li>MailFile</li><li>[MailServer</li><li>MailDomain</li>
人員 | <li>[姓氏]</li><li>MailFile</li><li>簡短名稱</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li>
連絡人 （沒有發證者人員） | <li>\_MMS_IDRegType</li>
資源 | <li>全名</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>網站</li><li>顯示名稱</li><li>MailFile</li><li>[MailServer</li><li>MailDomain</li>

## <a name="common-issues-and-questions"></a>常見的問題與問題

### <a name="schema-detection-does-not-work"></a>結構描述偵測無法運作
若要偵測結構描述，則必須 schema.nsf 檔案會出現在 Domino 伺服器上。 如果在 LDAP 伺服器上安裝，才會出現此檔案。 如果沒有偵測到的結構描述，請檢查下列各項︰

- 檔案 schema.nsf 存在於 Domino 伺服器的根資料夾
- 使用者可以看到 schema.nsf 檔案的權限。
- 強制重新啟動 LDAP 伺服器。 開啟**Lotus Domino 主控台**，並使用**告訴 LDAP ReloadSchema** ] 命令來重新載入結構描述。

### <a name="not-all-secondary-address-books-are-visible"></a>並非所有次要通訊錄會顯示
Domino 連接器依賴功能**協助**能夠尋找次要通訊錄。 如果第二個通訊錄遺失，請確認[協助](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html)如果已啟用及設定 Domino 伺服器上。

### <a name="custom-attributes-in-domino"></a>在 [Domino 的自訂屬性
Domino 延伸架構，使其顯示為連接器使用的自訂屬性中有數種方式。

**方法 1︰ 將延伸 Lotus Domino 結構描述**

1. 建立一份 Domino 目錄範本 {PUBNAMES。NTF} （您不應該自訂範本的預設 IBM Lotus Domino 目錄） 的下列[步驟執行](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html)︰
2. 開啟 [複本的 Domino 目錄範本 {CONTOSO。NTF} 範本建立 Domino 設計工具] 中，請遵循下列步驟︰
    - 按一下 [共用的項目，然後展開子表單
    - 按兩下 {從} $InheritableSchema 子表單 (其中 {從} 是預設結構化物件類別的名稱，例如︰ 人員)。
    - 名稱您想要新增結構描述 {MyPersonAtrribute}，而且對應到該屬性的屬性。 建立欄位以選取 [**建立**] 功能表，然後從功能表選取**欄位**。
    - 在新增欄位，請選取其類型、 樣式、 大小、 字型和欄位屬性] 視窗上的其他相關的參數來設定其內容。
    - 保留預設值相同指定該屬性的名稱為屬性 （例如，如果屬性名稱是 MyPersonAttribute，繼續具有相同名稱的預設值）。
    - ${從} InheritableSchema 子表單儲存更新的值。
3. 取代 Domino 目錄範本 {PUBNAMES。NTF} 使用新的自訂範本 {CONTOSO。NTF} 依下列[步驟執行](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html)。
4. 關閉 Domino 管理員，並開啟 Domino 主控台重新啟動 LDAP 服務，然後重新載入 LDAP 結構描述︰
    - 在 Domino 主控台插入 [重新啟動 LDAP 服務-[重新啟動任務 LDAP]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)歸檔的**Domino 命令**文字] 下的命令]。
    - 若要重新載入 LDAP 結構描述使用告訴 LDAP 命令-告訴 LDAP ReloadSchema
5. 開啟 Domino 管理員和選取人員與群組] 索引標籤以查看 [新增的屬性會反映在 domino 新增人員。
6. 開啟 Schema.nsf 從**檔案**] 索引標籤，然後查看 [新增的屬性會反映到 dominoPerson LDAP 物件類別。

**方法 2︰ 建立 auxClass 使用的自訂屬性，然後以物件類別建立關聯**

1. 建立一份 Domino 目錄範本 {PUBNAMES。NTF} 依下列[步驟](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html)（永不自訂預設 IBM Lotus Domino 目錄範本）︰
2. 開啟 [複本的 Domino 目錄範本 {CONTOSO。建立、 Domino 設計工具中的 NTF} 範本。
3. 在左窗格中，選取 [共用的程式碼，然後子表單。
4. 按一下 [新的子表單
5. 請執行下列動作，指定新的子表單的屬性︰
    - 新的子表單開啟時，請選擇 [設計]-子表單屬性
    - 旁名稱] 屬性中，輸入輔助物件類別，例如 TestSubform 的名稱。
    - 保留 」 中包含插入子表單...] 對話方塊 [選取的 [選項] 屬性
    - 取消選取的選項屬性 」 轉譯傳遞到筆記中的 HTML。 」
    - 預留其他屬性相同，並關閉子表單] 屬性方塊。
    - 儲存並關閉新的子表單。
6. 執行下列動作以新增欄位，以定義輔助物件類別︰
    - 開啟您建立子表單。
    - 選擇 [建立-欄位。
    - 名稱旁的 [欄位] 對話方塊的 [基本功能] 索引標籤上，指定任何的名稱，例如: {MyPersonTestAttribute}。
    - 在新增欄位，請選取其類型、 樣式、 大小、 字型和相關的內容來設定其內容。
    - 保留預設值相同指定該屬性的名稱為屬性 （例如，如果屬性名稱是 MyPersonTestAttribute，繼續具有相同名稱的預設值）。
    - 子表單儲存更新的值，然後執行下列動作︰
        - 在左窗格中，選取 [共用的程式碼然後子表單
        - 選取新的子表單，然後選擇設計-設計內容。
        - 按一下 [從左至右，第三個索引標籤，然後選取 [**將這項限制的設計變更**。
7. 開啟 ${從} ExtensibleSchema 子表單，（{從} 是預設的結構化物件類別，例如 – 人員的名稱）。
8. 插入資源選取子表單 （您建立的例如 – TestSubform） 並儲存 ${從} ExtensibleSchema 子表單。
9. 取代 Domino 目錄範本 {PUBNAMES。NTF} 使用新的自訂範本 {CONTOSO。NTF} 依下列[步驟執行](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html)。
10. 關閉 Domino 管理員，並開啟 Domino 主控台重新啟動 LDAP 服務，然後重新載入 LDAP 結構描述︰
    - 在 Domino 主控台插入 [重新啟動 LDAP 服務-[重新啟動任務 LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)歸檔的**Domino 命令**文字] 下的命令]。
    - 若要重新載入 LDAP 結構描述，請使用告訴 LDAP 命令**告訴 LDAP ReloadSchema**。
11. 開啟 Domino 管理員，然後選取 [使用者和群組以查看 [新增的屬性會反映在 domino 新增人員] 索引標籤 （在其他人] 索引標籤）。
12. 開啟 [**檔案**] 索引標籤上的 [Schema.nsf，請參閱新增的屬性會反映在 TestSubform LDAP 輔助物件類別下。

**方法 3︰ 新增至 ExtensibleObject 類別的自訂屬性**

1. 開啟 {Schema.nsf} 檔案放在根目錄
2. 從**所有的結構描述文件**] 下的左側功能表中選取 LDAP 物件類別，然後按一下 [**新增物件類別**] 按鈕︰
3. 提供 LDAP 形式的 {zzzExtensibleSchema} （zzz 是預設的結構化物件類別，例如人員的名稱） 的名稱。 例如，延伸人員物件類別架構，提供 LDAP 名稱 {PersonExtensibleSchema}。
4. 提供您要將架構的上層物件類別名稱。 例如，來擴充人員物件類別的結構描述，提供上司物件類別名稱 {dominoPerson}:
5. 提供正確的 OID 對應至物件類別。
6. 選取 [強制] 或 [選用的屬性類型的欄位，依照需求] 下的擴充/自訂屬性︰
7. 後新增 ExtensibleObjectClass 必要的屬性，按一下 [**儲存並關閉**]。
8. ExtensibleObjectClass 會建立個別的預設物件課程擴充屬性。

## <a name="troubleshooting"></a>疑難排解

-   如何啟用疑難排解連接器記錄的資訊，請參閱[如何啟用 ETW 追蹤連接器的](http://go.microsoft.com/fwlink/?LinkId=335731)。
