<properties
    pageTitle="Azure AD Connect 同步處理︰ 了解的預設設定 |Microsoft Azure"
    description="本文將說明中 Azure AD Connect 同步處理的預設設定。"
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect 同步處理︰ 了解的預設設定
本文說明的現成設定規則。 將其文件規則，而且這些規則如何影響設定。 也會引導您透過 Azure AD Connect 同步處理的預設設定。 目標是讀者了解設定模型，名為宣告式佈建在真實世界範例中的運作。 本文假設您已安裝並設定 Azure AD Connect 同步處理，使用安裝精靈。

若要瞭解設定模型的詳細資訊，請閱讀[瞭解宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)。

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Azure AD 來自內部部署現成的規則
下列運算式可以找到的現成可用的設定。

### <a name="user-out-of-box-rules"></a>使用者現成的規則
需要物件類型也會套用這些規則。

使用者物件必須符合下列同步處理︰

- 必須 sourceAnchor。
- Azure AD 中建立物件之後，sourceAnchor 無法變更。 如果該值為內部部署的變更，物件會停止同步處理，直到 sourceAnchor 變更回到先前的值。
- 必須有 accountEnabled (userAccountControl) 屬性填入。 與內部部署的 Active Directory，永遠的這個屬性會出現並填入。

下列使用者物件**未**同步處理至 Azure AD:

- `IsPresent([isCriticalSystemObject])`. 請確定在 Active Directory 中許多的現成的物件，例如內建的系統管理員帳戶、 未同步處理。
- `IsPresent([sAMAccountName]) = False`. 請確定未同步處理使用者物件沒有 sAMAccountName 屬性。 這種情況下只實際上會發生的網域，從 NT4 升級。
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. 不同步處理 Azure AD Connect 同步處理和其舊版所使用的服務帳戶。
- 無法同步處理不會執行在 Exchange Online 的 Exchange 帳戶。
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- 無法同步處理不會執行在 Exchange Online 的物件。
`CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
此遮罩 (& H21C07000) 會篩選出下列物件︰
    - 具備郵件功能的公用資料夾
    - 系統服務員信箱
    - 信箱資料庫信箱 （系統信箱）
    - 通用安全性群組 （不會套用，為使用者，但有舊版的原因）
    - 非標準群組 （可適用於使用者，但有舊版的原因）
    - 信箱計劃
    - 探索信箱
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. 無法同步處理任何複寫受害者物件。

適用於下列屬性規則︰

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. SourceAnchor 屬性不提供連結信箱中。 假設，如果已經找到連結的信箱，稍後加入實際的帳戶。
- Exchange 相關屬性如果屬性**mailNickName**的值只同步處理。
- 當有多個樹系時，屬性會耗用以下列順序︰
    1. 登入相關的屬性 (例如 userPrincipalName) 會產生樹系啟用帳戶。
    2. Exchange GAL （全域通訊清單） 中的屬性會提供與 Exchange 信箱樹系。
    3. 如果找不到信箱，這些屬性可以來自任何樹系。
    4. Exchange 相關屬性 （在 GAL 中看不到技術屬性） 會從樹系提供位置`mailNickname ISNOTNULL`。
    5. 如果有多個要符合這些規則的樹系，來判斷哪些樹系佔屬性所用的連接器 （樹系） 建立順序 （日期/時間）。

### <a name="contact-out-of-box-rules"></a>連絡人的現成的規則
連絡人的物件必須符合下列同步處理︰

- 必須具備郵件功能的連絡人。 它會驗證使用下列規則︰
    - `IsPresent([proxyAddresses]) = True)`. 必須填入 proxyAddresses 屬性。
    - 可以在 proxyAddresses 屬性或郵件屬性中找到的主要電子郵件地址。 目前狀態的@會用於驗證的內容是電子郵件地址。 這兩個規則的其中一個必須評估為 True。
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. 有有的項目 」 SMTP: 」，如果有的話，可以@字串中找到？
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. 已填妥郵件屬性，且如果，則可以@字串中找到？

下列連絡人物件**未**同步處理至 Azure AD:

- `IsPresent([isCriticalSystemObject])`. 請確定已同步處理連絡人標示為重要的物件。 不應是任何的預設設定。
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. 這些物件不會使用在 Exchange Online。
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. 無法同步處理任何複寫受害者物件。

### <a name="group-out-of-box-rules"></a>群組的現成的規則
群組物件必須符合下列同步處理︰

- 必須小於 50000 的成員。 計數為內部部署] 群組中的成員數目。
    - 如果同步處理開始的第一次之前，它會有更多成員，群組不同步。
    - 如果的成員數目成長從一開始建立，然後到了 50000 成員時停止同步處理，直到成員資格計數低於 50000 一次。
    - 附註︰ Azure AD 也強制 50000 的成員資格計數。 您無法群組與更多成員同步處理，即使您修改或移除此規則。
- 如果群組是**通訊群組**，然後將其也必須啟用的郵件。 請參閱會強制執行此規則的[連絡人現成的規則](#contact-out-of-box-rules)。

下列群組物件**未**同步處理至 Azure AD:

- `IsPresent([isCriticalSystemObject])`. 請確定在 Active Directory 中許多的現成的物件，例如 [內建的系統管理員] 群組、 未同步處理。
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. 使用 DirSync 舊版的群組。
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. 角色群組。
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. 無法同步處理任何複寫受害者物件。

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal 現成的規則
FSPs 加入 「 任何 」 (\*) 斷的物件。 實際上，此加入只會發生使用者與安全性群組。 此設定可確保跨樹系的成員資格會解析，和 Azure AD 中正確表示。

### <a name="computer-out-of-box-rules"></a>電腦現成的規則
電腦物件必須符合下列同步處理︰

- `userCertificate ISNOTNULL`. 只有在 Windows 10 電腦填入這個屬性。 在這個屬性中值的所有電腦物件會同步都處理。

## <a name="understanding-the-out-of-box-rules-scenario"></a>了解現成的規則案例
在此範例中，我們使用一個帳戶樹系 (A) 的部署、 一個資源樹系 (R) 及一個 Azure AD 目錄。

![案例說明的圖片](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

在此設定，則會假設有帳戶樹系中的啟用的帳戶與資源樹系與連結信箱中停用的帳戶。

我們的預設設定的目標是︰

- 從樹系啟用帳戶同步處理有關登入的屬性。
- 從樹系與信箱同步處理，請參閱 GAL （全域通訊清單） 的屬性。 如果找不到信箱，則會使用任何其他樹系。
- 如果找不到連結的信箱，連結的啟用的帳戶必須找不到要匯出至 Azure AD 的物件。

### <a name="synchronization-rule-editor"></a>同步處理規則編輯器
可以檢視和使用工具的同步處理規則編輯器 (SRE) 變更設定，可以在 [開始] 功能表中找到的捷徑。

![同步處理規則編輯器圖示](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

SRE 是資源套件的工具，安裝 Azure AD Connect 同步處理。 若要啟動它，您必須是 ADSyncAdmins 群組的成員。 啟動時將它，您會看到像這樣︰

![同步處理規則連入](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

在窗格中，您會看到所有的同步處理規則建立您的設定。 在資料表中的每行是一個同步處理規則。 在規則類型] 下、 左列出兩種不同類型︰ 傳入和傳出。 輸入與輸出是來自 metaverse 的檢視。 您主要將焦點移至本概觀中輸入的規則。 實際的同步處理規則清單定 AD 中偵測到的結構描述。 在上方的圖片，帳戶樹系 (fabrikamonline.com) 並沒有任何服務，例如 Exchange 和 Lync，然後沒有同步處理規則建立這些服務。 不過，在資源樹系 (res.fabrikamonline.com) 您找到同步處理規則使用這些服務。 規則的內容是偵測到的版本而有所不同。 例如，使用 Exchange 2013 部署中有比設定 Exchange 2010/2007年中的多個屬性流。

### <a name="synchronization-rule"></a>同步處理規則
同步處理規則是一組的書寫方向滿足如下的條件時的屬性設定物件。 它也用來說明如何相關斷，稱為 [**加入**或**符合**物件連接器空間中的物件。 同步處理規則有指出整體彼此的優先順序值。 同步處理規則較低值，較高的優先順序，屬性流程衝突，較高的優先順序贏得衝突解決程序。

例如，查看 [同步處理規則**中從 AD – 使用者 AccountEnabled**。 標示 SRE 這一行，然後選取 [**編輯**]。

由於此規則的現成的規則，您收到警告，當您開啟的規則。 您不應該讓任何[變更現成的規則](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)，，詢問您要的是。 在此情況下，您只想要檢視規則。 選取 [**無**]。

![同步處理規則警告](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

同步處理規則有四個設定區段︰ 範圍的篩選、 加入規則及轉換的描述。

#### <a name="description"></a>描述
第一節提供基本資訊，例如的名稱和描述。

![描述] 索引標籤上的同步處理規則編輯器 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

您也尋找這項規則，與相關的連線系統相關的資訊的物件，適用於連線系統中的輸入與 metaverse 物件類型。 Metaverse 物件類型時，一律人員無論來源物件類型是使用者、 需要或連絡人。 使其建立為一般類型，永遠不應該變更 metaverse 物件類型。 若要加入、 StickyJoin 或佈建，可以設定連結類型。 這項設定運作方式與加入規則] 區段，然後稍後涵蓋。

您也可以看到此同步處理規則用於密碼同步處理。 如果使用者在同步處理規則的範圍，就是從內部部署密碼同步至雲端 （假設您已啟用密碼同步處理功能）。

#### <a name="scoping-filter"></a>範圍的篩選
設定的範圍篩選] 區段會用來設定何時套用同步處理規則。 AD 屬性**userAccountControl**不得元 2，因為您查看同步處理規則的名稱會指出應該僅會套用啟用的使用者，請設定範圍設定。 同步處理引擎 AD 中找到的使用者， **userAccountControl**設定為 [小數位數的值 （啟用標準的使用者） 512 時適用於此同步處理規則。 當使用者擁有**userAccountControl**設定為 [514 （停用標準的使用者），不適用於規則。

![範圍中同步處理規則編輯器] 索引標籤 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

[範圍] 篩選具有群組 」 和 「 可以巢狀的子句。 群組內的所有子句須滿足都如下的同步處理規則以套用。 當定義多個群組時，然後至少有一個群組須滿足如下的套用規則的。 就是 OR 邏輯群組與邏輯之間評估，並且會評估內群組。 在 [輸出同步處理規則**外出 AAD – 群組加入**找這項設定的範例。 有數種同步處理篩選群組，例如一個安全性群組 (`securityEnabled EQUAL True`)，另一個通訊群組 (`securityEnabled EQUAL False`)。

![範圍中同步處理規則編輯器] 索引標籤 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

此規則用來定義哪些群組應該 Azure ad 佈建後。 通訊群組必須啟用會與 Azure AD，同步處理的郵件，但安全性群組的電子郵件不需要。

#### <a name="join-rules"></a>加入規則
第三個區段用來設定連接器空間中的物件與整體斷的物件。 您所看較舊版本的規則沒有任何設定加入規則，因此請改為您要查看**中從 AD – 使用者加入**。

![在 [同步處理規則編輯器中加入規則] 索引標籤 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

加入規則的內容在安裝精靈中選取的相符選項而定。 如果輸入的規則，請評估第一句中的來源連接器空格的物件，加入規則中的每個群組評估順序。 如果來源物件會以符合一個物件斷使用其中一個加入規則評估，連接物件。 如果在評估的所有規則不相符，則會使用 [描述] 頁面中的連結類型。 **佈建**設定此設定，如果在目標，metaverse 建立新的物件。 佈建到 metaverse 新的物件是也稱為**專案**metaverse 物件。

加入規則只會評估一次。 當連線連接器空間物件和 metaverse 物件時，他們會保留連接同步處理規則的範圍是沒有問題仍為。

評估時同步處理規則，只有一個同步處理規則，以加入規則定義必須是範圍中。 如果找不到單一物件的多個同步處理規則，以加入規則，會發生錯誤。 因此，最佳作法是只有一個同步處理規則有定義的物件的範圍中多個同步處理規則時加入。 在 Azure AD Connect 同步處理的現成可用的設定，這些規則可以找到查看名稱，然後找出在名稱結尾處的 [**加入**的單字。 同步處理規則不含已定義之任何加入規則適用於屬性流時另一個同步處理規則結合在一起的物件，或提供新物件在目標。

如果您看看上方的圖片，就可以看見規則嘗試加入**objectSID** **msExchMasterAccountSid** (Exchange) 與**受影響 OriginatorSid** (Lync)，這是我們預期帳戶資源樹系拓撲中。 您找到所有樹系上相同的規則。 假設是每個樹系可能的帳戶 」 或 「 資源樹系。 如果您有帳戶，在單一樹系並不需要聯結，此設定也有效。

#### <a name="transformations"></a>轉換
[轉換] 區段中定義所有連結的物件，然後滿足如下的範圍篩選套用至目標物件的屬性流程。 回到**中從 AD – 使用者 AccountEnabled**同步處理規則，請尋找下列轉換︰

![轉換] 索引標籤上的同步處理規則編輯器 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

若要在內容中，在帳戶資源樹系部署中，將此設定預期，使用 Exchange 和 Lync 設定資源樹系尋找啟用的帳戶的帳戶樹系和停用的帳戶。 您正在查看的同步處理規則包含所需的登入的屬性，這些屬性應該從樹系是啟用的帳戶。 所有這些屬性流程都放在一個同步處理規則。

轉換可以有不同類型︰ 常數，直接、 運算式。

- 固定一律會接續硬式編碼值。 在最上面的情況下，其永遠設定值，**則為 True**斷屬性名稱為**accountEnabled**。
- 直接流量的一律會接續到目標屬性為來源中的屬性值-是。
- 第三個流程類型是運算式，並可讓更多進階設定。

運算式語言是 VBA (Visual Basic for Applications，)，因此人員使用的 Microsoft Office 體驗或 VBScript 就會辨識出格式。 以方括弧括住，[attributeName] 括住屬性。 屬性名稱及的函數名稱會區分大小寫，但同步處理規則編輯器評估運算式，並提供的運算式不是有效的警告。 所有運算式會在同一行的巢狀函數來都表示。 若要顯示的功能設定語言，以下是流程 pwdLastSet，但插入其他註解︰

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

如需有關運算式語言的屬性，請參閱[瞭解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)。

### <a name="precedence"></a>優先順序
您現在看一些個別的同步處理規則，但在設定中共同作業的規則。 在某些情況下，屬性值是從多個同步處理規則 aggregate 相同的目標屬性。 在此情況下，屬性的優先順序用來決定贏得哪些屬性。 例如，查看屬性 sourceAnchor。 這個屬性會很重要，才能登入 Azure AD 屬性。 您可以找到屬性流程中兩個不同的同步處理規則，這個屬性**中從 AD – 使用者 AccountEnabled**和**中從 AD – 使用者常見**。 同步處理規則的優先順序，因為 sourceAnchor 屬性被提供啟用帳戶樹系從第一次加入至 metaverse 物件的多個物件時。 如果沒有啟用帳戶，然後同步處理引擎使用捕捉所有同步處理規則**中從 AD – 使用者常見**。 此設定可確保即使的已停用的帳戶，仍 sourceAnchor。

![同步處理規則連入](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

同步處理規則的優先順序設定] 群組中安裝精靈。 在群組中的所有規則都有相同的名稱，但連接至不同的連線目錄。 安裝精靈可讓規則**中從 AD – 使用者加入**最高優先順序，並將其反覆運算移轉所有連線 AD 目錄。 再繼續執行下一個預先定義的順序中的規則的群組。 群組內，規則會新增連接器加入精靈中的順序。 如果精靈新增另一個連接器時，重新排列同步處理規則，然後每個群組中最後一個插入新的連接器規則。

### <a name="putting-it-all-together"></a>全部整合
我們現在知道充分同步處理規則，若要瞭解如何設定不同的同步處理規則與搭配使用。 如果您查看使用者與所提供給 metaverse 屬性，以下列順序套用規則︰

名稱 | 註解
:------------- | :-------------
在 [從 AD – 使用者加入 | 加入與 metaverse 連接器空間物件的規則。
在 [從 AD – UserAccount 啟用 | 所需的登入 Azure AD 屬性和 Office 365。 我們想要啟用帳戶從這些屬性。
在 [從 AD – 從 Exchange 使用者常見 | 全域通訊清單中找到的屬性。 我們假設資料品質最樹系我們已在此找到使用者信箱中。
在 [從 AD – 使用者常見 | 全域通訊清單中找到的屬性。 找不到信箱，以防其他任何連接的物件可以參與屬性值。
在 [從 AD – Exchange 使用者 | 如果已偵測到 Exchange，只存在。 跨越基礎結構的所有 Exchange 屬性。
在 [從 AD – Lync 使用者 | 如果 Lync 偵測到，只存在。 跨越所有的基礎結構 Lync 屬性。

## <a name="next-steps"></a>後續步驟

- 了解更多關於設定模型中[瞭解宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)的資訊。
- 進一步[瞭解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)的運算式語言。
- 繼續閱讀現成可用的設定[了解使用者和連絡人](active-directory-aadconnectsync-understanding-users-and-contacts.md)] 中的運作方式
- 瞭解如何變更實際使用宣告式佈建如何[進行的變更為預設設定](active-directory-aadconnectsync-change-the-configuration.md)。

**概觀主題**

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
