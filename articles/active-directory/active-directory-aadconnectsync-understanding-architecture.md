<properties
   pageTitle="Azure AD Connect 同步處理︰ 了解架構 |Microsoft Azure"
   description="本主題說明架構 Azure AD Connect 同步處理，並說明所用的字詞。"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/31/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect 同步處理︰ 了解架構
本主題會說明 Azure AD Connect 同步處理的基本架構。 在許多方面，很類似 MIIS 2003 ILM 2007 與 FIM 2010 其前置任務。 Azure AD Connect 同步處理是項技術功用。 如果您熟悉這些舊版技術，本主題內容會以您所熟悉以及。 如果您是新同步處理，然後本主題適用於您。 不過不是，必須知道要成功地進行 Azure AD Connect （稱為本主題中的同步處理引擎） 的同步處理的自訂本主題的詳細資料。

## <a name="architecture"></a>架構
同步處理引擎建立會儲存在多個連線的資料來源的物件的整合式的檢視，並管理這些資料來源中的身分識別資訊。 從連線的資料來源及一組規則，以決定如何處理此資訊擷取的身分識別資訊取決於此整合式的檢視。

### <a name="connected-data-sources-and-connectors"></a>連接的資料來源與連接器
同步處理引擎處理來自不同的資料存放庫，例如 Active Directory 或 SQL Server 資料庫的身分識別資訊。 每個資料存放庫的組織其資料資料庫類似的格式，以及提供標準的資料存取方法是潛在資料來源的同步處理引擎。 同步處理引擎會同步處理資料存放庫稱為**連接資料來源**或**連線目錄**(CD)。

同步處理引擎封裝稱為**連接器**模組中連接的資料來源的互動。 每個連接的資料來源類型有特定的連接器。 連接器翻譯成連接的資料來源瞭解的格式必要的作業。

連接器打電話 API 交換身分識別資訊 （讀取和寫入） 連線的資料來源。 您也可新增使用可延伸連線架構的自訂連接器。 下圖顯示連接器同步處理引擎連線連線的資料來源的方式。

![Arch1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

資料流程可以在 [方向]，但它不能同時流程中兩個方向。 換句話說，連接器可以設定為允許資料流動資料來源的連線至同步處理引擎，或從連線的資料來源，同步處理引擎，但只有下列其中一項作業可能發生一次一個物件和屬性。 方向可能不同的不同物件，以及使用不同的屬性。

若要設定連接器，您可以指定您要同步處理的物件類型。 指定的物件類型定義範圍的同步處理程序中所包含的物件。 下一步是選取的屬性，若要同步處理，這就是屬性包含清單。 可以回應變更隨時變更這些設定您的商務規則。 當您使用 Azure AD Connect 安裝精靈時，為您設定這些設定。

若要匯出至連線的資料來源的物件，屬性內含清單必須包含至少需要建立特定的物件類型連接的資料來源中的最小屬性。 例如， **sAMAccountName**屬性必須包含屬性內含清單匯出到 Active Directory 使用者物件，因為在 Active Directory 中的所有使用者物件都必須都有定義的**sAMAccountName**屬性中。 同樣地，安裝精靈執行這項設定，您。

如果連線的資料來源使用結構化的元件，例如分割或容器組織物件，您可以限制指定的解決方案使用連接的資料來源中的區域。

### <a name="internal-structure-of-the-sync-engine-namespace"></a>同步處理引擎命名空間內部結構
整個同步處理引擎命名空間包含兩個命名空間儲存的身分識別的資訊。 兩個命名空間是︰

- 連接器空間 (CS)
- Metaverse (MV)

**連接器空間**是臨時區域包含的格式指定的物件從連線的資料來源和屬性要包含在清單中指定的屬性。 同步處理引擎使用連接器空間，來決定連接的資料來源中變更的功能，並階段內送的變更。 同步處理引擎也會使用連接器空間階段輸出匯出至連線的資料來源的變更。 同步處理引擎會針對每個連接線維持為臨時區域的空間，以不同的連接器。

藉由使用臨時區域，同步處理引擎仍會保留獨立的連接的資料來源，並不會受到其可用性和協助工具。 如此一來，您可以處理身分識別資訊隨時臨時區域中使用的資料。 同步處理引擎可以要求僅所做的變更連線的資料來源內自最後一個終止通訊工作階段或僅變更推入身分識別的資訊尚未收到連接的資料來源，以減少同步處理引擎之間連線的資料來源的網路流量。

此外，同步處理引擎儲存該階段連接器空間中的所有物件的狀態資訊。 收到新的資料時，同步處理引擎一律會評估是否已同步處理資料。

**Metaverse**是包含多個連線的資料來源，提供所有合併的物件的單一全域管理員、 整合檢視中的彙總的身分識別資訊的儲存區。 根據擷取自連接的資料來源及一組規則，允許您自訂的同步處理程序的身分識別資訊建立 Metaverse 物件。

下圖顯示的連接器空間命名空間和同步處理引擎內的 metaverse 命名空間。

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>同步處理引擎身分識別的物件
同步處理搜尋引擎中的物件的 [連線的資料來源中的物件格式或同步處理引擎整合式的檢視具有這些物件。 同步處理引擎的每個物件都必須具備全域唯一識別碼 (GUID)。 Guid 提供資料完整性與快速物件之間的關聯。

### <a name="connector-space-objects"></a>連接器空間物件
當同步處理引擎通訊的連接的資料來源時，它會讀取連接的資料來源中的身分識別資訊，並使用這項資訊來建立的身分識別物件表示連接器空間中。 您無法建立或個別刪除這些物件。 不過，您可以手動刪除連接器空間中的所有物件。

連接器空間中的所有物件都有兩個屬性︰

- 全域唯一識別碼 (GUID)
- 辨別的名稱也稱為

如果連線的資料來源指定唯一的屬性的物件，物件連接器空間中的可以也會有錨點屬性。 錨點屬性可唯一識別中連接的資料來源的物件。 同步處理引擎使用錨點連接的資料來源中找出此物件的相對應表示。 同步處理引擎假設物件的錨點不會再變更物件的存留時間。

連接線許多產生自動為每個物件的錨點，將其匯入時使用已知的唯一識別碼。 例如，Active Directory 連接器使用**objectguid 資訊**屬性的錨點。 針對不提供明確定義的唯一識別碼的連接的資料來源，您可以指定錨點產生連接器設定的一部分。

情況下，從錨點內建或輸入物件的多個唯一的屬性，都不的修訂，並可唯一識別連接器空間 （例如，員工編號或使用者識別碼） 中的物件。

連接器空間物件可以是下列其中一項︰

- 暫存的物件
- 版面配置區

### <a name="staging-objects"></a>臨時物件
暫存的物件代表指定的物件類型的執行個體連接的資料來源的資料。 除了 GUID，辨別的名稱暫存的物件一律會有一個值，指出物件類型。

已永遠匯入臨時物件都有錨點屬性的值。 新佈建同步處理引擎及程序建立連線的資料來源中的臨時物件沒有錨點屬性的值。

臨時物件也包含目前值的商務屬性和操作引擎所需的同步處理進行同步處理程序的資訊。 操作資訊包含旗標，表示分段暫存的物件安裝的更新的類型。 如果暫存的物件有收到新的身分識別資訊尚未處理連接的資料來源，則會將物件標示為**擱置匯入**。 如果暫存的物件會有新的身分識別資訊不尚未已匯出至連線的資料來源，就會標示為**擱置匯出**。

匯入物件或匯出物件，可以是暫存的物件。 同步處理引擎會建立使用從連線的資料來源收到的物件資訊匯入物件。 當同步處理引擎收到的新物件符合其中一個連接器中選取的物件類型存在相關資訊時，它會建立匯入物件連接器空間中連接的資料來源中的物件代表。

下圖顯示匯入物件，表示中連接的資料來源的物件。

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

同步處理引擎建立匯出斷使用物件的資訊。 在下一個通訊工作階段，匯出的物件會匯出至連線的資料來源。 從同步處理引擎的觀點來看，匯出物件還不存在連線的資料來源中。 因此，匯出物件的錨點屬性無法使用。 從同步處理引擎接收物件之後，連線的資料來源所建立物件的錨點屬性的唯一值。

下圖顯示如何建立匯出物件斷使用身分識別的資訊。

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

同步處理引擎確認匯出的物件，重新匯入連線的資料來源的物件。 同步處理引擎收到其下一個從連線的資料來源匯入期間，匯出的物件就會匯入物件。

### <a name="placeholders"></a>版面配置區
同步處理引擎會使用一般命名空間來儲存物件。 不過，例如 Active Directory 某些連線的資料來源使用階層式命名空間。 階層式命名空間資訊轉換為一般命名空間，同步處理引擎會使用版面配置區，若要保留的階層。

每個預留位置表示物件的階層式名稱不匯入同步處理引擎，但需建構階層的名稱的元件 （例如，組織單位）。 填寫連接的資料來源中的不在執行中的連接器空格的物件的物件參照所建立的間距。

同步處理引擎也會使用版面配置區來儲存參考不匯入已的物件。 如果同步處理設定為包含管理員屬性*Abbie Spencer*物件及收到的值，例如是否已匯入之，例如物件*CN = 煜 Sperry，CN = 使用者，亦即 = fabrikam，亦即 = com*，管理員資訊會儲存為連接器空間中的版面配置區。 如果管理員物件稍後匯入，版面配置區物件會覆寫暫存代表之物件的管理員]。

### <a name="metaverse-objects"></a>Metaverse 物件
Metaverse 物件包含彙總的檢視該同步處理引擎具有的連接器空間中暫存的物件。 同步處理引擎建立 metaverse 物件使用匯入物件中的資訊。 數個連接器空間物件可以連結至單一 metaverse 物件，但連接器空間物件無法連結至多個 metaverse 物件。

Metaverse 物件無法手動建立或刪除。 同步處理引擎自動刪除 metaverse 物件連接器空間中沒有任何連接器空間物件的連結。

若要對應的物件類型中 metaverse 對應中連接的資料來源的物件，同步處理引擎會提供可延伸的結構描述，以預先定義的物件類型與相關聯的屬性。 您可以建立新的物件類型和 metaverse 物件的屬性。 屬性可以是單一值或多重值，而且屬性類型可以是字串、 參照、 數字及布林值。

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>暫存物件及 metaverse 物件之間的關聯
在同步處理引擎命名空間，資料流程會啟用連結之間的關聯性暫存物件及 metaverse 物件。 連結至 metaverse 物件的暫存物件稱為**加入物件**（或**連接器物件**）。 不會連結到 metaverse 物件的暫存物件稱為**退出物件**（或**斷路器物件**）。 字詞加入，退出偏好負責匯入及匯出資料的連線目錄的連接器與不令人混淆。

版面配置區永遠不會連結至 metaverse 物件

聯結的物件組成的暫存的物件和單一 metaverse 物件其連結的關聯性。 聯結的物件用來同步處理之間連接器空間物件和 metaverse 物件的屬性值。

當暫存的物件會變成聯結的物件同步處理期間時，可以暫存的物件和 metaverse 物件之間的流程屬性。 屬性流程雙向，且使用匯入屬性和匯出屬性規則設定。

單一連接器空間物件可以連結至只有一個 metaverse 物件。 不過，每個 metaverse 物件可以連結至多個連接器空間物件在相同或不同的連接器空格，如下圖所示。

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

連結和之間的關係暫存物件 metaverse 物件常設，而您指定的規則可以只移除。

Disjoined 的物件是暫存的物件，不會連結到任何 metaverse 物件。 屬性值 disjoined 物件的不被處理任何進一步 metaverse 內。 同步處理引擎，不會更新連線的資料來源中的對應物件的屬性值。

藉由使用退出物件，您可以將身分識別資訊儲存在同步處理引擎，並稍後處理程序。 保留暫存的物件 disjoined 物件連接器空間中有許多優點。 因為系統已有分段安裝此物件的必要的資訊，並不需要建立表示這個物件的下一步匯入過程中再次連接的資料來源的資料。 如此一來，同步處理引擎具有完成快照的連接的資料來源，即使沒有目前連線的資料來源連線。 退出物件轉換到聯結的物件，反之亦然，根據您指定的規則。

匯入物件會建立 disjoined 物件。 匯出物件必須連接的物件。 系統邏輯會強制執行這項規則，並刪除每個匯出的物件的不是聯結的物件。

## <a name="sync-engine-identity-management-process"></a>同步處理引擎身分識別管理程序
身分識別管理程序控制身分識別的資訊更新之間連線的不同資料來源的方式。 身分識別管理會發生三個程序︰

- 匯入
- 同步處理
- 匯出

匯入期間，同步處理引擎會評估內送郵件的身分識別資訊，從連線的資料來源。 當未偵測到的變更時，它會建立新的臨時物件或更新現有暫存空間中的物件連接器的同步處理。

同步處理，同步處理引擎會更新以反映連接器空間中所發生的變更 metaverse，並更新以反映斷所發生的變更連接器空間。

在匯出程序，同步處理引擎推入查看變更的物件上分段安裝並的會加註標示為 「 擱置中匯出。

下圖顯示每一個處理程序發生身分識別的資訊流程為資料來源的一個連線到另一個。

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>匯入程序
匯入期間，同步處理引擎會評估身分識別的資訊更新。 同步處理引擎比較資料來源的連線身分識別相關資訊的暫存物件收到的身分識別資訊，並判斷暫存的物件是否需要更新。 如果需要新的資料更新暫存的物件，暫存的物件就會標示擱置中匯入。

接中同步處理連接器空格的物件，同步處理引擎處理只身分識別的資訊已變更。 此程序提供下列優點︰

- **有效的同步處理**。 最小化的同步處理期間處理的資料量。
- **有效重新同步處理**。 您可以變更同步處理引擎不想要重新連線到資料來源的同步處理引擎處理身分識別資訊的方式。
- **若要預覽同步處理的商機**。 您可以預覽同步處理，以確認您的身分識別管理程序假設正確無誤。

針對每個連接器中指定的物件，同步處理引擎第一次嘗試接點的連接器空間中尋找物件的表示。 同步處理引擎會檢查連接器空間中的所有暫存物件，然後嘗試尋找具有相符的錨點屬性對應的暫存物件。 如果沒有現有的暫存物件具有相符的錨點屬性，同步處理引擎會尋找具有相同的辨別名稱對應暫存物件。

當同步處理引擎找到符合的辨別名稱，但不是由錨點的暫存物件時，會發生下列特殊行為︰

- 如果物件位於連接器空間中有未錨點，然後同步處理引擎連接器空間會移除此物件並標示連結至為**再試一次 [下一步執行的同步處理佈建**metaverse 物件。 然後，建立新的匯入物件。
- 如果物件位於連接器空間中有錨點，同步處理引擎會假設，這個物件可能已經重新命名或刪除連線目錄中。 它會指派連接器空間物件的暫存的新辨別的名稱，讓它可以階段內送的物件。 舊的物件就會變成**暫時性**，等待連接器，以匯入重新命名或刪除解決這種情況。

如果同步處理引擎會找出暫存的物件對應至連接器中指定的物件，它會決定哪一類的要套用變更。 例如，同步處理引擎可能會重新命名或刪除連接的資料來源中的物件或它可能只更新物件的屬性值。

更新資料的臨時物件標示擱置中匯入]。 等待匯入的不同類型的可供使用。 根據匯入程序的結果，暫存空間中的物件連接器具有等待匯入類型下列其中一項︰

- **無**。 沒有變更任何暫存物件的屬性可供使用。 同步處理引擎不會標示此類型擱置中匯入。
- [**新增**]。 暫存的物件是新匯入物件連接器空間中。 同步處理引擎旗標此類型的擱置中進行其他處理斷匯入。
- **更新**。 同步處理引擎連接器空間中找到的相對應的暫存物件，並使其屬性的更新可以處理斷標幟為此類型，等待匯入。 更新包含物件重新命名。
- [**刪除**]。 同步處理引擎連接器空間中找到的相對應的暫存物件，並會標示為此類型，等待匯入，以便可以刪除聯結的物件。
- **刪除/加入**。 同步處理引擎尋找對應的暫存物件連接器空間，但不是相符的物件類型。 在此情況下，刪除新增的分段修改。 A 刪除新增修改指出同步處理引擎，因為不同的規則集套用到此物件，物件輸入變更時，必須會發生這個物件的完整重新同步處理。

藉由設定擱置匯入的狀態暫存的物件，則可能會大幅降低處理同步處理期間，因為這麼做，因此可讓系統處理程序只有在已更新資料的物件的資料量。

### <a name="synchronization-process"></a>同步處理程序
同步處理包含兩個相關程序︰

- 連接器空間中使用的資料更新 metaverse 內容時，請輸入的同步處理。
- 輸出同步處理時的連接器空間的內容會更新斷使用資料。

藉由使用分段連接器空間中的資訊，輸入同步處理程序會建立斷整合式的檢視的儲存在連接的資料來源的資料。 暫存的所有物件或只有擱置匯入資訊會彙總，根據規則的設定方式而定。

變更 metaverse 物件時，輸出的同步處理程序更新匯出的物件。

輸入的同步處理會從連線的資料來源收到的身分識別資訊斷建立整合式的檢視。 同步處理引擎可以處理隨時身分識別資訊連接的資料來源的資料使用有最新身分識別的資訊。

**輸入的同步處理**

輸入的同步處理會包含下列處理程序︰

- **佈建**（也稱為**預測**很重要，若要區別此程序輸出的同步處理佈建的話）。 同步處理引擎建立新的 metaverse 物件根據暫存的物件，並連結。 佈建是物件層級的作業。
- [**加入**]。 同步處理引擎會連結至現有的 metaverse 物件暫存的物件。 聯結是物件層級的作業。
- **匯入屬性流程**。 同步處理引擎更新屬性值，稱為屬性流程，斷的物件。 匯入屬性流程是屬性層級作業所需的暫存的物件和 metaverse 物件之間的連結。

佈建是斷建立物件的唯一程序。 佈建會影響只匯入的 [退出物件的物件。 在佈建，同步處理引擎會建立 metaverse 物件的對應到匯入物件的物件類型，並建立兩個物件，如此會建立聯結的物件之間的連結。

加入程序也會建立匯入物件和 metaverse 物件之間的連結。 加入與佈建之間的差異是加入程序，需要匯入物件會連結至現有的 metaverse 物件，佈建程序建立新的 metaverse 物件的位置。

同步處理引擎會加入至 metaverse 物件的匯入物件使用同步處理規則設定中指定的準則。

期間佈建與加入程序，同步處理引擎會連結至 metaverse 物件，讓您加入 disjoined 的物件。 這些物件層級作業完成後，同步處理引擎可以更新相關聯的 metaverse 物件的屬性的值。 此程序稱為匯入屬性流程。

在含有新的資料，並會連結至 metaverse 物件的所有匯入物件上時所發生匯入屬性流量。

**輸出的同步處理**

輸出的同步處理更新匯出的物件，當 metaverse 物件變更，但不是會刪除。 輸出的同步處理的目標是評估是否 metaverse 物件的變更需要更新暫存物件連接器空格。 在某些情況下，所做的變更可能需要該臨時中所有的連接器空格的物件會更新。 變更臨時物件會加註標示，擱置中匯出]，讓您匯出的物件。 這些匯出的物件稍後發送給連接的資料來源在匯出程序。

輸出的同步處理具有三個程序︰

- **佈建**
- **取消提供**
- **匯出屬性流程**

佈建和取消是兩個物件層級的操作。 取消提供取決於佈建因為只佈建可以啟動它。 佈建移除 metaverse 物件匯出物件之間的連結時，會觸發取消提供。

佈建是一律會觸發時變更會套用至斷的物件。 時的變更 metaverse 物件，同步處理引擎可以執行下列工作的任何佈建的程序的一部分︰

- 建立聯結的物件，metaverse 物件連結至新建立的匯出物件的位置。
- 重新命名的連結的物件。
- Disjoin metaverse 物件和臨時物件，建立 disjoined 的物件之間的連結。

如果佈建需要建立新的連接器物件的同步處理引擎，但暫存物件 metaverse 物件連結的永遠是匯出物件，因為物件尚不存在連線的資料來源中。

如果佈建需要同步處理引擎 disjoin 聯結的物件，建立 disjoined 的物件，就會觸發取消提供。 Deprovisioning 程序中刪除物件。

在進行，刪除匯出物件不實際刪除物件。 物件標示為**刪除的郵件**，這表示刪除作業分段的物件。

匯出屬性流程也會發生輸出的同步處理，匯入屬性流程時發生輸入同步處理的方式很類似。 匯出屬性流程之間會發生只 metaverse 及匯出連結的物件。

### <a name="export-process"></a>匯出程序
在匯出程序，同步處理引擎會檢查所有會擱置中匯出連接器空格中，加註標示，並傳送更新給連接的資料來源的匯出物件。

同步處理引擎可以決定匯出的成功，但無法完全判斷身分識別管理程序已完成。 連線的資料來源中的物件永遠可以變更由其他處理程序。 同步處理引擎沒有連接的資料來源的持續的連線，因此不足以讓物件的屬性假設只在成功匯出通知的連接的資料來源。

例如，在連接的資料來源中的程序可能會變更物件的屬性回其原始的值 （也就是連線的資料來源可能會覆寫值資料是由同步處理引擎發送和套用連接的資料來源中的成功後，立即）。

同步處理引擎儲存匯出及匯入暫存的每個物件的狀態資訊。 如果屬性屬性內含清單中所指定的值已經變更自上次匯出，儲存空間的匯入及匯出做出適當的狀態啟用同步處理引擎。 同步處理引擎會使用匯入程序，以確認匯出至連線的資料來源的屬性值。 匯入及匯出詳細資訊，之間的比較，如下圖所示，在下圖中，可讓同步處理引擎，以決定是否匯出已順利完成，或如果需要重複。

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

例如，如果同步處理引擎匯出屬性 C，連接的資料來源中的值為 5、，它會儲存 C = 5 在匯出狀態記憶體中。 在嘗試匯出 C = 5 連線的資料來源一次同步處理引擎假設，此值尚未持續套用至物件 （也就是，除非不同的值從連線的資料來源最近匯入），因為此物件結果每個其他匯出。 在物件上匯入作業期間收到 C = 5 時，會清除匯出記憶體。

## <a name="next-steps"></a>後續步驟
進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。