<properties
   pageTitle="一般 LDAP 連接器 |Microsoft Azure"
   description="本文將說明如何設定 Microsoft 的一般 LDAP 連接器。"
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

# <a name="generic-ldap-connector-technical-reference"></a>一般 LDAP 連接器技術參照
本文將說明一般 LDAP 連接器。 本文適用於下列產品︰

- Microsoft 身分識別管理員 2016 (MIM2016)
- Forefront 識別管理員 2010 R2 (FIM2010R2)
    -   必須使用 hotfix 4.1.3671.0 或更新版本[KB3092178](https://support.microsoft.com/kb/3092178)。

如需 MIM2016 與 FIM2010R2，連接線即從[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=717495)下載。

這份文件時參照 IETF Rfc，使用格式 (RFC [RFC 編號] / [區段 RFC 文件])，例如 (RFC 4512/4.3)。
您可以在 http://tools.ietf.org/html/rfc4500 （您需要以正確的 RFC 編號取代 4500），以找到更多的資訊。

## <a name="overview-of-the-generic-ldap-connector"></a>一般 LDAP 連接器的概觀
一般 LDAP 連接器可讓您的同步處理服務整合 v3 LDAP 伺服器。

IETF Rfc 中未指定特定作業和結構描述元素，例如與執行 delta 匯入]。 這些作業支援明確指定唯一的 LDAP 目錄。

從高層級的觀點來看，目前版本的連接器支援下列功能︰

功能 | 支援
--- | --- |
連接的資料來源 | 連接線會支援所有 LDAP v3 伺服器 (相容 RFC 4510)。 以下列測試︰ <li>Microsoft Active Directory 輕量型目錄服務 (AD LDS)</li><li>Microsoft Active Directory 通用類別目錄 (AD GC)</li><li>389 目錄伺服器</li><li>Apache 目錄伺服器</li><li>IBM 役 DS</li><li>Isode 目錄</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>開啟 DJ</li><li>開啟 DS</li><li>開啟 LDAP (openldap.org)</li><li>Oracle （先前週日） 目錄 Server 企業版</li><li>RadiantOne 虛擬目錄伺服器 (VDS)</li><li>太陽一個目錄伺服器</li>**不支援的主要目錄︰** <li>Microsoft Active Directory 網域服務 (AD DS) [改為使用內建的 Active Directory 連接器]</li><li>Oracle 網際網路目錄 (OID)</li>
案例   | <li>物件生命週期管理</li><li>群組管理</li><li>密碼管理</li>
作業 |LDAP 目錄上支援下列作業︰ <li>完整匯入</li><li>匯出</li>指定目錄上僅支援下列作業︰<li>Delta 匯入</li><li>設定密碼、 變更密碼</li>
結構描述 | <li>LDAP 結構描述 （RFC3673 和 RFC4512/4.2） 中偵測到結構描述</li><li>支援結構化類別、 aux 類別，以及 extensibleObject 物件類別 (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Delta 匯入及密碼管理支援
支援 Delta 匯入及密碼管理目錄︰

- Microsoft Active Directory 輕量型目錄服務 (AD LDS)
    - 支援 delta 匯入的所有作業
    - 支援設定密碼
- Microsoft Active Directory 通用類別目錄 (AD GC)
    - 支援 delta 匯入的所有作業
    - 支援設定密碼
- 389 目錄伺服器
    - 支援 delta 匯入的所有作業
    - 支援設定密碼] 和 [變更密碼
- Apache 目錄伺服器
    - 不支援 delta 匯入，因為此目錄沒有常設變更記錄
    - 支援設定密碼
- IBM 役 DS
    - 支援 delta 匯入的所有作業
    - 支援設定密碼] 和 [變更密碼
- Isode 目錄
    - 支援 delta 匯入的所有作業
    - 支援設定密碼] 和 [變更密碼
- Novell eDirectory 和 NetIQ eDirectory
    - 支援新增、 更新和重新命名 delta 匯入作業
    - 不支援刪除作業 delta 匯入
    - 支援設定密碼] 和 [變更密碼
- 開啟 DJ
    - 支援 delta 匯入的所有作業
    - 支援設定密碼] 和 [變更密碼
- 開啟 DS
    - 支援 delta 匯入的所有作業
    - 支援設定密碼] 和 [變更密碼
- 開啟 LDAP (openldap.org)
    - 支援 delta 匯入的所有作業
    - 支援設定密碼
    - 不支援變更密碼
- Oracle （先前週日） 目錄 Server 企業版
    - 支援 delta 匯入的所有作業
    - 支援設定密碼] 和 [變更密碼
- RadiantOne 虛擬目錄伺服器 (VDS)
    - 必須使用 7.1.1 版本或更新版本
    - 支援 delta 匯入的所有作業
    - 支援設定密碼] 和 [變更密碼
-  太陽一個目錄伺服器
    - 支援 delta 匯入的所有作業
    - 支援設定密碼] 和 [變更密碼

### <a name="prerequisites"></a>必要條件
使用連接器之前，請確定您有下列同步處理伺服器上︰

- 4.5.2 的 Microsoft.NET Framework 或更新版本

### <a name="detecting-the-ldap-server"></a>偵測 LDAP 伺服器
連接器依賴各種技巧，以偵測並識別 LDAP 伺服器。 連接器使用根 DSE、 廠商名稱/版本，並將其檢查尋找唯一的物件和屬性存在於中某些 LDAP 伺服器已知的結構描述。 此資料中，如果找到，請用來預先填入 [連接器中的設定選項。

### <a name="connected-data-source-permissions"></a>連線的資料來源權限
若要執行匯入及匯出作業連線目錄中的物件，連接器帳戶必須具備足夠的權限。 連接器必須寫入權限，才能匯出，並讀取權限，才能匯入。 權限設定被執行中的目標目錄本身管理體驗。

### <a name="ports-and-protocols"></a>連接埠和通訊協定
連接線會使用指定的設定，依預設為 389 LDAP 及 636 LDAPS 的連接埠號碼。

LDAPS，則必須使用 SSL 3.0 或 TLS。 SSL 2.0 不支援，而且無法啟動。

### <a name="required-controls-and-features"></a>所需的控制項和功能
下列 LDAP 控制項/功能必須才能正常運作連接器 LDAP 伺服器上︰  
`1.3.6.1.4.1.4203.1.5.3`True/False 篩選

True/False 篩選不常用報告為 LDAP 目錄支援與可能會顯示在下**強制功能找不到****全域] 頁面**上。 它會用來**或**篩選查詢中建立 LDAP，例如，當匯入多個物件類型。 您可以匯入多個物件類型，如果您的 LDAP 伺服器支援此功能。

如果您使用目錄的唯一識別碼是錨點的位置下列也必須使用 （請參閱本文稍後的詳細資訊的 [[設定錨點](#configure-anchors)] 區段）︰  
`1.3.6.1.4.1.4203.1.5.1`所有的操作屬性

如果目錄有所能容納一個呼叫目錄中的多個物件，然後則建議使用分頁。 搭配使用分頁，您必須針對下列選項之一︰

**選項 1:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**選項 2:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

如果連接器設定中啟用這兩個選項，則會使用 pagedResultsControl。

`1.2.840.113556.1.4.417`ShowDeletedControl

若要查看已刪除的物件，ShowDeletedControl 只會用 USNChanged delta 匯入的方法。

連接線會偵測伺服器上出現的選項。 如果無法偵測的選項，警告存在於 [全域] 頁面上的連接器屬性。 他們所支援並非所有的 LDAP 伺服器簡報的所有控制項/功能和連接器即使此警告，則可能會使用順利。

### <a name="delta-import"></a>Delta 匯入
已偵測到支援目錄時，只使用 delta 匯入。 目前使用下列方法︰

- LDAP Accesslog。 請參閱[http://www.openldap.org/doc/admin24/overlays.html#Access 記錄](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- LDAP Changelog。 請參閱[http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- 時間戳記。 針對 Novell/NetIQ eDirectory 連接器，請使用取得的最後一個日期/時間建立和更新的物件。 Novell/NetIQ eDirectory 不提供相等值表示擷取已刪除的物件。 如果沒有其他 delta 匯入方法是在 LDAP 伺服器上作用，也可以使用這個選項。 這個選項，不能刪除匯入物件。
- USNChanged。 請參閱︰ [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>不支援
不支援下列 LDAP 功能︰

- 伺服器 (RFC 4511/4.1.10) 之間方面

## <a name="create-a-new-connector"></a>建立新的連接器
若要建立一般 LDAP 連接器，**同步處理服務**中選取 [**管理代理程式**並**建立**。 選取 [**一般 LDAP (Microsoft)**連接器]。

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>連線
在 [連線] 頁面中，您必須指定 host （主機）、 連接埠與繫結的資訊。 根據繫結已選取，其他則可能會提供下列各節中的資訊。

![連線](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- 偵測結構描述時，連接逾時設定只會用第一個連線至伺服器。
- 如果繫結是匿名，這兩個使用者名稱 / 使用密碼，也不憑證。
- 其他繫結中，輸入資訊任一的使用者名稱 / 密碼或選取憑證。
- 如果您使用 Kerberos 驗證，也提供領域/使用者的網域。

[**屬性別名**] 文字方塊中用於 RFC4522 語法的結構描述中定義的屬性。 結構描述偵測期間無法偵測到這些屬性和連接器需要說明]，找出那些屬性。 例如下列屬性別名] 方塊正確識別為二進位屬性 userCertificate 屬性中輸入需要︰

`userCertificate;binary`

此設定可以外觀的範例如下︰

![連線](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

選取**包含結構描述中的操作屬性**] 核取方塊也包含伺服器所建立的屬性。 包括屬性，例如物件時所建立和上次更新時間。

如果使用可延伸物件 (RFC4512/4.3)，並啟用此選項可讓所有的物件上使用的每個屬性，請選取 [**包含結構描述中的可延伸屬性**]。 選取這個選項可讓您的結構描述很大，除非連線的目錄會使用這項功能，否則建議您保留未選取的選項。

### <a name="global-parameters"></a>全域的參數
在全域參數頁面上，您可以設定 DN delta 變更記錄和其他 LDAP 功能。 頁面是預先填入 LDAP 伺服器所提供的資訊。

![連線](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

[上方] 區段會顯示所提供的伺服器本身，例如名稱伺服器的資訊。 連接器也確認強制控制項中根 DSE。 如果不會列出這些控制項，則會顯示警告。 一些 LDAP 目錄待辦事項清單根 DSE 中的所有功能，就可能即使警告訊息出現時，連接器就不會出現問題。

**支援控制項**的核取方塊控制某些作業的行為︰

- 已選取 [樹狀目錄中刪除，就會刪除階層 LDAP 呼叫。 未選取 [刪除] 樹狀結構，連接線會遞迴刪除如有需要。
- 選取分頁的結果，連接線會使用指定的執行步驟的大小會分頁匯入。
- VLVControl 和 SortControl 是從 LDAP 目錄讀取資料 pagedResultsControl 的替代方案。
- 如果未選取 [所有三個選項 (pagedResultsControl，VLVControl 及 SortControl) 連接器匯入一項作業，可能會失敗，如果是大型的目錄中的所有物件。
- USNChanged Delta 匯入方法時，只會用 ShowDeletedControl。

在變更記錄 DN 已命名的內容，例如使用 delta 變更記錄**cn = changelog**。 此值必須指定能夠執行 delta 匯入。

以下是預設變更記錄 DNs 的清單︰

目錄 | Delta 變更記錄
--- | ---
Microsoft AD LDS 和 AD GC | 自動偵測。 USNChanged。
Apache 目錄伺服器 | 無法使用。
目錄 389 | 變更記錄。 預設要使用的值︰ **cn = changelog**
IBM 役 DS | 變更記錄。 預設要使用的值︰ **cn = changelog**
Isode 目錄 | 變更記錄。 預設要使用的值︰ **cn = changelog**
Novell/NetIQ eDirectory | 無法使用。 時間戳記。 連接器用途上次更新日期/時間，以取得新增及更新記錄。
開啟的 DJ DS | 變更記錄。  預設要使用的值︰ **cn = changelog**
開啟 LDAP | Access 記錄檔。 預設要使用的值︰ **cn = accesslog**
Oracle DSEE | 變更記錄。 預設要使用的值︰ **cn = changelog**
RadiantOne VDS | 虛擬目錄。 取決於連線到 VDS 的目錄。
太陽一個目錄伺服器 | 變更記錄。 預設要使用的值︰ **cn = changelog**

密碼屬性是連接器應該使用以設定密碼中變更密碼的屬性名稱和密碼設定作業。
此值會依預設設定為**userPassword** ，但需要為特定的 LDAP 系統時，可以變更。

在其他的磁碟分割區] 清單中，可能是將不會自動偵測到的其他命名空間。 例如，如果數個伺服器構成邏輯叢集，應該匯入所有同時，可以使用此設定。 如同在 Active Directory 可以一個樹系中有多個網域，不過所有網域來都共用一個結構描述，可以在此方塊中輸入其他的命名空間模擬相同。 每個命名空間可以匯入從不同的伺服器，進一步設定在設定的磁碟分割區和階層] 頁面上。 若要取得新的一行，使用 Ctrl + Enter。

### <a name="configure-provisioning-hierarchy"></a>設定佈建的階層
此頁面用來將 DN 元件，例如 OU 對應到的物件類型應該會佈建後，例如 organizationalUnit。

![佈建階層](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

藉由設定佈建的階層，您可以設定連接器，以自動建立時所需的結構。 例如，如果有命名空間 dc = contoso，dc = com 與新的物件 cn = 王，ou = 西雅圖、 c = 美國，dc = contoso，dc = com 佈建後，然後連接器可以建立美國類型國家/地區和 organizationalUnit 西雅圖的物件，如果這些不是已在目錄。

### <a name="configure-partitions-and-hierarchies"></a>設定磁碟分割區與階層
分割和階層在頁面上，選取您要匯入及匯出的物件的所有命名空間。

![磁碟分割區](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

每個命名空間，則也設定會覆寫 [連線] 畫面上指定的值的連線設定。 如果這些值從左到其預設空白值，則會使用 [連線] 畫面中的資訊。

您也可選取哪些容器和 Ou 連接器應該匯入及匯出至。

### <a name="configure-anchors"></a>設定定位點
此頁面一定預先設定的值，且無法變更。 如果已識別伺服器的廠商，可能會不變的屬性，例如物件 GUID 填入錨點。 如果偵測不到或已知會有不變的屬性，連接線會使用 dn （辨別名稱） 作為錨點。

![錨點](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

以下是 LDAP 伺服器和所用錨點的清單︰

目錄 | 錨點屬性
--- | ---
Microsoft AD LDS 和 AD GC | objectguid 資訊
389 目錄伺服器 | dn
Apache 目錄 | dn
IBM 役 DS | dn
Isode 目錄 | dn
Novell/NetIQ eDirectory | GUID
開啟的 DJ DS | dn
開啟 LDAP | dn
Oracle ODSEE | dn
RadiantOne VDS | dn
太陽一個目錄伺服器 | dn

## <a name="other-notes"></a>其他備忘稿
本節提供專屬於此連接器，或因為其他原因是重要事項方面的資訊。

### <a name="delta-import"></a>Delta 匯入
在開啟的 LDAP delta 浮水印是 UTC 日期/時間。 因此，必須同步處理 FIM 同步處理服務與開啟的 LDAP 之間的時鐘。 如果沒有出現，可能會省略 delta 變更記錄中的某些項目。

Novell eDirectory delta 匯入沒有偵測的任何物件刪除。 因此，則必須執行完整匯入定期找出所有已刪除的物件。

Delta 變更記錄的日期/時間為基礎的目錄，它是強烈建議您定期時間點執行完整匯入。 若要尋找的同步處理引擎和還是介於 LDAP 伺服器功能目前連接器空間，可讓此程序。

## <a name="troubleshooting"></a>疑難排解

-   如何啟用疑難排解連接器記錄的資訊，請參閱[如何啟用 ETW 追蹤連接器的](http://go.microsoft.com/fwlink/?LinkId=335731)。
