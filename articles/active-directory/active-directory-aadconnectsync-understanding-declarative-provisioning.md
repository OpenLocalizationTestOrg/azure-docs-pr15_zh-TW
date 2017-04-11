<properties
    pageTitle="Azure AD Connect 同步處理︰ 了解宣告式佈建 |Microsoft Azure"
    description="說明中 Azure AD Connect 的宣告式佈建設定模型。"
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
    ms.date="08/29/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect 同步處理︰ 了解宣告式佈建
本主題說明中 Azure AD Connect 設定模型。 模型稱為宣告式佈建並可讓您輕鬆變更設定。 本主題中描述的許多進階而不需要的大部分的客戶案例。

## <a name="overview"></a>概觀
宣告式佈建處理來自來源的連線目錄的物件，並決定如何的物件和屬性轉換從來源至目標。 同步處理管線來處理物件和管線是相同的輸入與輸出規則。 輸入的規則的連接器空間到 metaverse，而輸出規則是從 metaverse 連接器空間。

![同步處理的管線](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

管線有數種不同的模組。 每個負責一個物件同步的概念。

![同步處理的管線](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

- 來源]，[來源物件
- [範圍](#scope)，找到所有範圍中的同步處理規則
- [加入](#join)，請決定連接器空間和 metaverse 之間的關聯
- [轉換](#transform)、 計算轉換屬性的方式與流量
- [優先順序](#precedence)，解決衝突屬性捐贈
- 目標，目標物件

## <a name="scope"></a>範圍
範圍模組會評估物件，然後決定範圍中，應包含在處理的規則。 在物件上的屬性值，根據不同的同步處理規則的評估的範圍。 例如，沒有 Exchange 信箱已停用的使用者沒有啟用的使用者信箱與不同的規則。  
![範圍](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

為群組和子句定義範圍。 子句會將群組中。 在群組中的所有子句之間，則使用邏輯 AND。 例如，(部門 = IT 及國家/地區 = 丹麥)。 使用邏輯 OR 群組之間。

![範圍](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
如此圖片中的範圍應為讀取 (部門 = IT 及國家/地區 = 丹麥) 或者 (國家/地區 = 瑞典)。 如果群組 1 或 2 群組會評估為 true，然後規則是範圍中。

[範圍] 模組支援下列作業。

作業 | 描述
--- | ---
等 NOTEQUAL | 字串比較的值等於值屬性中。 多重值屬性，請參閱 ISIN 和 ISNOTIN。
LESSTHAN LESSTHAN_OR_EQUAL | 如果該值會評估字串比較小於屬性中的值。
包含，NOTCONTAINS | 如果值，請參閱位置內值屬性會評估字串比較。
STARTSWITH NOTSTARTSWITH | 如果值是以屬性中的值開頭會評估字串比較。
ENDSWITH NOTENDSWITH | 如果值是以結束屬性中的值會評估字串比較。
GREATERTHAN GREATERTHAN_OR_EQUAL | 字串比較的值是否大於屬性中的值。
ISNULL ISNOTNULL | 評估屬性時不存在的物件。 如果屬性不存在，因此 null，規則就是範圍中。
ISIN ISNOTIN | 評估如果該值為簡報中的已定義的屬性。 這項作業是等和 NOTEQUAL 的多重值的變化。 屬性應該是多重值的屬性，如果值，請參閱任何屬性的值，然後規則是範圍。
ISBITSET ISNOTBITSET | 如果已設定特定的位元會評估。 例如，可用來評估 userAccountControl，查看使用者已啟用或停用的位元。
ISMEMBEROF ISNOTMEMBEROF | DN 連接器空間中的群組時，應該包含的值。 如果該物件是群組的指定的成員，規則會是群組的範圍中。

## <a name="join"></a>加入
同步處理管線中的加入模組負責在目標尋找來源中的物件和物件之間的關係。 輸入規則，在此關聯會尋找物件關聯斷連接器空間中的物件。  
![加入 cs 和 mv 之間](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
目標是要看到 [是否有物件已在 metaverse，建立另一個連接器，它應該會與相關聯。 例如帳戶資源樹系中的使用者帳戶樹系應該聯結與資源樹系使用者。

大多輸入規則會用於連接至相同的 metaverse 物件一起加入連接器空格的物件。

聯結會定義為一或多個群組。 群組內，您必須子句。 在群組中的所有子句之間，則使用邏輯 AND。 使用邏輯 OR 群組之間。 從上到下的順序來處理群組。 一個群組已在目標找到物件完全相符項目，會評估沒有其他加入規則。 如果零個或多個找到一個物件下, 一組規則會繼續處理。 因此，規則應該要建立順序最明確的第一個且更模糊的結尾。  
![加入定義](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
如此圖片中的聯結處理從上到下。 第一次同步處理管線會看到是否為數相符。 如果沒有，第二個規則會看到帳戶名稱如果可以用來結合在一起的物件。 如果不相符的項目可能是，第三個和最後一個規則是更模糊相符項目使用使用者的名稱。

如果在評估加入的所有規則，並不完全相符項目會使用 [**描述**] 頁面上的**連結類型**。 如果**佈建**設定此選項，就會建立新的物件，在目標。  
![佈建或連接](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

範圍中，物件應該只有一個單一同步處理規則，以加入規則。 如果有多個同步處理規則定義聯結的位置，會發生錯誤。 優先順序不用來解決衝突加入。 物件必須擁有加入規則中的屬性，以相同的連入/連方向的流程的範圍。 如果您需要流程屬性，輸入與輸出至相同的物件，您必須輸入與輸出的同步處理規則，以加入。

嘗試佈建目標連接器空間物件時，輸出加入有特殊行為。 第一次嘗試反向加入使用 DN 屬性。 如果以相同的 DN 目標連接器空間中已經有物件，物件連接。

新的同步處理規則進入範圍後，只會評估加入模組。 當物件加入時，不退出即使不再滿足加入準則。 如果您想要 disjoin 物件，加入物件的同步處理規則必須超出範圍。

### <a name="metaverse-delete"></a>Metaverse 刪除
Metaverse 物件將維持現狀時間有多長，有一個**連結類型**與範圍中的同步處理規則設為**佈建**或**StickyJoin**。 StickyJoin 連接器不允許佈建到 metaverse，新的物件，但它已加入，則它必須先刪除來源中刪除 metaverse 物件。

刪除 metaverse 物件時，會標示刪除已標示為**佈建**輸出的同步處理規則相關聯的所有物件。

## <a name="transformations"></a>轉換
轉換用來定義如何屬性應該流程從來源至目標。 流可以有下列其中一下列**流程類型**︰ 行銷、 常數或運算式。 直接流量的接續屬性值-沒有其他轉換功能。 為常數設定指定的值。 運算式表達轉換的應該使用宣告式佈建的運算式語言。 [了解宣告式佈建運算式語言](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)主題中找運算式語言的詳細資料。

![佈建或連接](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

**套用一次**] 核取方塊定義的一開始建立物件時，應只設定的屬性。 例如，此設定可以用於設定新的使用者物件初始密碼。

### <a name="merging-attribute-values"></a>合併的屬性值
在屬性流沒有設定，以判斷多重值的屬性是否應該合併來自多個不同的連接器。 預設值是**更新**，表示最高優先順序的同步處理規則應該 win。

![合併列印類型](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

此外還有**合併**與**MergeCaseInsensitive**。 這些選項可讓您合併來自不同來源的值。 例如，它可用來合併多個不同的樹系成員或 proxyAddresses 屬性。 當您使用此選項時，所有的物件的範圍中的同步處理規則必須使用相同的合併列印類型。 您無法定義從一個連接器的**更新**及**合併**來自另一個。 如果您嘗試，您會收到錯誤。

**合併**與**MergeCaseInsensitive**之間的差異是如何處理重複的屬性值。 同步處理引擎可確保重複的值不會插入到目標屬性。 使用**MergeCaseInsensitive**，重複的值只差異，以防不會出現。 例如，就不會看到兩"SMTP:bob@contoso.com"和"smtp:bob@contoso.com"目標屬性中。 **合併**的確切的值，多個值只尋找只是不同的大小寫可能會出現。

[**取代**] 選項會**更新**，相同，但不是會使用它。

### <a name="control-the-attribute-flow-process"></a>控制項的屬性流程程序
多個輸入同步處理規則設定時相同的 metaverse 屬性限，來決定輸贏所用的優先順序。 最高優先順序 （最小數值） 的同步處理規則要參與的值。 輸出規則會發生相同情況。 同步處理最高優先順序獲勝規則，而且參與連線目錄的值。

在某些情況下，而非參與值，同步處理規則應該決定其他規則的行為方式。 有某些特殊的常值，這種情況下使用。

輸入的同步處理規則的常值**NULL**可用來表示流程具有參與沒有值。 低優先順序的另一個規則可以參與一個值。 如果沒有規則提供值，則會移除 metaverse 屬性。 輸出規則，如果**NULL**是最後一個值，同步處理的所有規則都處理之後, 再的值會移除連線目錄中。

常值**AuthoritativeNull**很類似為**NULL** ，但沒有較低的優先順序規則可以參與一個值的差異。

屬性流程也可以使用**IgnoreThisFlow**。 則表示沒有任何參與，因為類似為 NULL。 不同的是它不會在目標移除現有的值。 這是屬性流程永不已有等。

以下是範例︰

在*外出 AD-使用者 Exchange 混合式*下列流程，請參閱︰  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
這個運算式應為讀取︰ 如果使用者信箱位於 Azure AD，然後流程從 Azure AD 至 AD 屬性。 如果沒有的話，未流程回到 Active Directory 中的任何項目。 在此情況下，它會保留現有的值 AD 中。

### <a name="importedvalue"></a>ImportedValue
由於屬性名稱必須以引號，而不是方括號括住，函數 ImportedValue 是與所有其他函數不同︰  
`ImportedValue("proxyAddresses")`.

同步處理期間通常屬性使用預期的值，即使其尚未尚未匯出，或在匯出 （「 頂端塔 」） 時收到錯誤。 輸入的同步處理假設還未最後到達連線的目錄的屬性達到它。 在某些情況，請務必只同步處理已經確認連線目錄 （「 全像和 delta 匯入塔 」） 的值。

此函數的範例，請參閱現成的同步處理規則*中從 AD – 使用者常見的 Exchange*。 在混合式部署 Exchange 價值的 Exchange online 應只同步處理時，已經確認值匯出成功︰  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>優先順序
當參與相同的屬性值至目標嘗試幾個同步處理規則時，[優先順序] 的值用來決定輸贏。 具有高優先順序，最低數字的值，規則會參與衝突的屬性。

![合併列印類型](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

此順序可以用來定義的物件的一小部分的更精確地屬性流量。 例如出的-方塊-規則確定屬性從已啟用帳戶 (**使用者 AccountEnabled**) 已從其他帳戶的優先順序。

您可以定義優先順序連接器之間。 允許連接器與較佳的資料，若要先提供的值。

### <a name="multiple-objects-from-the-same-connector-space"></a>從相同的連接器空間的多個物件
如果您有多個物件在相同的連接器空間加入至相同的 metaverse 物件時，必須調整的優先順序。 如果數個物件在相同的同步處理規則的範圍，然後同步處理引擎不能決定優先順序。 這是不明確的來源物件應該參與 metaverse 的值。 此設定會報告為不明確，即使在來源屬性有相同的值。  
![加入至相同的 mv 物件的多個物件](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

此案例中，您需要變更同步處理規則的範圍，因此來源物件範圍中有不同的同步處理規則。 可讓您定義不同的優先順序。  
![加入至相同的 mv 物件的多個物件](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>後續步驟

- 進一步[瞭解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)的運算式語言。
- 請參閱如何宣告式佈建是使用的出方塊了解[的預設設定](active-directory-aadconnectsync-understanding-default-configuration.md)。
- 瞭解如何變更實際使用宣告式佈建如何[進行的變更為預設設定](active-directory-aadconnectsync-change-the-configuration.md)。
- 繼續閱讀如何使用者及連絡人共同合作[瞭解使用者](active-directory-aadconnectsync-understanding-users-and-contacts.md)和連絡人。

**概觀主題**

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)

**參考主題**

- [Azure AD Connect 同步處理︰ 函數參考](active-directory-aadconnectsync-functions-reference.md)
