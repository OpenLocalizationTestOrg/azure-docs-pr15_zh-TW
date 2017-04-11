<properties
    pageTitle="Azure AD Connect 同步處理︰ 如何進行的變更為預設設定 |Microsoft Azure"
    description="引導您如何在 Azure AD Connect 同步處理設定進行變更。"
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


# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD Connect 同步處理︰ 如何進行的變更為預設設定
本主題的目的是引導您瞭解如何變更中 Azure AD Connect 同步處理的預設設定。 它會提供一些常見的案例的步驟。 使用此知識，您應該可以根據自己的商務規則自己設定做一些簡單的變更。

## <a name="synchronization-rules-editor"></a>同步處理規則編輯器
同步處理規則編輯器用來查看和變更預設的設定。 您可以找到它**Azure AD Connect**群組] 底下的 [開始] 功能表中。  
![開始使用同步處理規則編輯器] 功能表](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

當您開啟它時，您會看到預設現成的規則。

![同步處理規則編輯器](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>在編輯器] 中瀏覽
按一下頂端的編輯器] 下拉式清單可讓您快速找到特定規則。 例如，如果您想要查看屬性 proxyAddresses 哪裡包含的規則，您要變更下拉式清單下列︰  
![SRE 篩選](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
若要重設篩選並載入全新的設定，請在鍵盤上按**F5** 。

若要右上角，必須**新增新規則**] 按鈕。 此按鈕用來建立您自己的自訂規則。

在底部，您所選的同步處理規則有作用的按鈕。 **編輯**和**刪除**執行您希望他們。 **匯出**會重新建立同步處理規則的 PowerShell 指令碼。 此程序可讓您從一個伺服器的同步處理規則移動到另一個。

## <a name="create-your-first-custom-rule"></a>建立您的第一個自訂規則
最常見的變更是屬性流程的變更。 Azure AD 與可能不到您的來源目錄中的資料。 在此區段中範例中，您會想要確保使用者的名字永遠**適當**的大小寫。

### <a name="disable-the-scheduler"></a>停用排程器
[排程器](active-directory-aadconnectsync-feature-scheduler.md)程式預設執行每 30 分鐘。 您想要確定無法啟動時進行變更，及疑難排解新規則。 若要暫時停用排程器，啟動 PowerShell，並執行`Set-ADSyncScheduler -SyncCycleEnabled $false`

![停用排程器](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>建立規則

1. 按一下 [**新增新規則**]。
2. 在 [**描述**] 頁面上輸入下列資訊︰  
![輸入規則的篩選](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
    - 名稱︰ 為這個規則的描述性名稱。
    - 描述︰ 某些釐清以便其他人可以瞭解規則的功能。
    - 連線系統︰ 物件，請參閱的系統。 在此案例中，我們會選取 Active Directory 連接器。
    - 連線的系統/Metaverse 物件類型︰ 分別選取 [**使用者**和**人員**。
    - 連結類型︰ 會將此值變更為**加入**。
    - 優先順序︰ 提供的系統中唯一值。 較低的數字值會指出較高的優先順序。
    - 標記︰ 留白。 僅限現成的規則 microsoft 應該已填入值，此方塊。
3. 在 [**領域設定篩選器**] 頁面中，輸入**givenName ISNOTNULL**。  
![輸入範圍設定篩選的規則](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
本節會用來定義的物件應套用規則。 如果保留空白，規則會套用到所有的使用者物件中。 但是，包括會議室有空、 服務帳戶及其他非人員使用者物件。
4. 在**加入規則**，請將它保留空白。
5. 在 [**轉換**] 頁面中，變更 FlowType**運算式**。 選取目標屬性**givenName**，然後在 [來源輸入`PCase([givenName])`。
![輸入的規則的轉換](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
同步處理引擎是區分大小寫，同時在函數名稱與屬性的名稱。 如果您輸入錯誤，您會看到一則警告當您新增規則。 編輯器] 可讓您儲存並繼續，，您必須重新開啟規則，並更正規則。
6. 按一下 [**新增**] 以儲存規則]。

系統中，將新的自訂規則應該會看到其他同步處理規則的。

### <a name="verify-the-change"></a>確認變更
此新的變更，您會想要確定其如預期般運作正常，並不會擲回任何錯誤。 根據您的物件數目，有兩種不同的方式，請執行此步驟。

1. 所有物件上執行完整的同步處理
2. 執行 [單一物件上的 [預覽和完整的同步處理

從 [開始] 功能表開始**同步處理服務**。 本節中的步驟都是這項工具。

1. **在 「 所有 」 物件的完整同步處理**  
選取頂端的 [**連接器**]。 找出您在前一個區段中，在此例 Active Directory 網域服務所做變更的連接器並加以選取。 選取 [**執行**]，從動作並選取**完整的同步處理**及**[確定]**。
![完整的同步處理](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
物件會立即更新斷。 您可以查看斷物件。

2. **[預覽] 和 [單一物件上完整的同步處理**  
選取頂端的 [**連接器**]。 找出您在前一個區段中，在此例 Active Directory 網域服務所做變更的連接器並加以選取。 選取 [**搜尋連接器空間**]。 若要尋找您要用來測試變更的物件使用範圍。 選取物件，然後按一下 [**預覽**]。 在 [新增] 畫面中，選取 [**認可預覽**]。
![確認預覽](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
變更立即承諾 metaverse。

**看看斷物件**  
您可以選擇，請確定需要值的一些範例物件和規則套用。 從頂端選取 [ **Metaverse 搜尋**]。 新增任何您要尋找相關的物件的篩選。 從搜尋結果中，開啟 [物件]。 查閱屬性值，並為套用規則預期的**同步處理規則**欄中也驗證。  
![Metaverse 搜尋](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  
### <a name="enable-the-scheduler"></a>啟用排程器
如果所有項目會如預期般，您可以再次啟用排程器。 從 PowerShell 執行`Set-ADSyncScheduler -SyncCycleEnabled $true`。

## <a name="other-common-attribute-flow-changes"></a>其他常見的屬性流程變更
上一節說明如何變更屬性流程。 在此區段中，會提供一些其他範例。 如何建立同步處理規則的步驟縮寫，但您可以在上一節中尋找完整的步驟。

### <a name="use-another-attribute-than-the-default"></a>使用預設的另一個屬性
在 Fabrikam，有樹系本機字母名字、 姓氏及顯示名稱的使用位置。 可以擴充屬性中找到這些屬性的英文字元表示。 Azure AD 中建置全域通訊清單時，Office 365 組織想要使用這些屬性。

使用預設設定時，從本機樹系物件看起來像這樣︰  
![屬性流程 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

若要與其他屬性流建立規則，請執行下列動作︰

- 從 [開始] 功能表開始**同步處理規則編輯器**。
- **輸入**仍選取左側，按一下 [**新增新規則**] 按鈕。
- 請為這個規則的名稱和描述。 選取 [內部部署的 Active Directory 和相關的物件類型]。  在 [**連結類型**] 中，選取 [**加入**]。 針對優先順序，選擇 [不是由其他規則的數字]。 現成的規則開頭 100，因此可以在此範例使用 50。
![屬性流程 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- 留範圍 （也就是，應該套用到樹系中的所有使用者物件）。
- 留加入規則 （也就是讓處理任何連接的現成的規則）。
- 在轉換，建立下列流程︰  
![屬性流程 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- 按一下 [**新增**] 以儲存規則]。
- 移至**同步處理服務管理員**。 在 [**連接器**] 中，選取我們用來新增規則的連接器。 選取 [**執行**]，及**完整的同步處理**]。 完整的同步處理會重新計算所有物件使用目前的規則。

這是相同的物件，這個自訂規則的結果︰  
![屬性流程 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>屬性的長度
字串屬性設定為 [索引的預設而長度上限為 448 的字元。 如果您正在使用其他可能包含的字串屬性，然後確認屬性流程中包含下列動作︰  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>變更 userPrincipalSuffix
Active Directory 中的 userPrincipalName 屬性永遠未知的使用者，且可能不適合作為登入 id。 Azure AD Connect 同步處理安裝精靈可讓挑選不同的屬性，例如郵件。 但必須在某些情況下計算屬性。 例如，公司 Contoso 有兩個 Azure AD 目錄，一個用於生產，一個以進行測試。 他們想要登入 ID 中使用其他的尾碼其測試租用戶中的使用者  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

在這個運算式，讓所有的第一個左@-sign(Word) 和使用固定的字串串連。

### <a name="convert-a-multi-value-to-a-single-value"></a>將多個值轉換為單一值
在 Active Directory 中的某些屬性是多重值的結構描述中，雖然看起來單一值的 Active Directory 使用者和電腦。 例如，描述屬性。  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

在此運算式以防屬性的值，我們需要屬性中的第一個項目 （項目），移除前置及結尾空格 (Trim)，，然後保留 （左） 的第一次 448 字元字串中。

### <a name="do-not-flow-an-attribute"></a>不流程屬性
在這個區段的分析藍本背景，請參閱[控制項的屬性流程程序](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process)。

有兩種方式不傳送屬性。 第一個安裝精靈] 中，並可讓您[移除所選的屬性](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)。 如果您從未同步處理之前屬性這個選項可搭配使用。 不過，如果您已開始同步處理此屬性，以及稍後再將它移除這項功能，管理屬性與現有的值的同步處理引擎停駐點會保留在 Azure AD。

如果您想要移除的屬性值，並確定不在未來流程，您需要改為建立自訂規則。

在 Fabrikam，我們知道，我們同步處理至雲端的屬性部分不應該有。 我們想要確定這些屬性會從 Azure AD 移除。  
![不正確的擴充屬性](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- 建立新的連入同步處理規則，並填入描述![描述](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- 建立屬性流類型**運算式**的與來源**AuthoritativeNull**。 常值**AuthoritativeNull**表示值應該是空的 MV 即使較低的優先順序同步處理規則會以填入值。
![轉換擴充屬性](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- 儲存同步處理規則。 啟動**同步處理服務**、 尋找連接器，選取 [**執行**]，並**完整的同步處理**。 此步驟會重新計算所有屬性的流程。
- 確認要匯出的搜尋連接器空間想做的變更。
![刪除分段](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="next-steps"></a>後續步驟

- 了解更多關於設定模型中[瞭解宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)的資訊。
- 進一步[瞭解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)的運算式語言。

**概觀主題**

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
