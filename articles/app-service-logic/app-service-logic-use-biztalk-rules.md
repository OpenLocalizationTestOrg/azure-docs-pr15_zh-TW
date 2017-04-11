<properties
   pageTitle="深入了解與邏輯應用程式中建立 BizTalk 規則 API 應用程式 |Microsoft Azure"
   description="本主題說明 BizTalk 規則連接器的功能，並提供及其用法指示"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="andalmia"/>

#<a name="biztalk-rules"></a>BizTalk 規則

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

商務規則封裝的原則和控制商務程序的決策。 這些原則可能正式定義程序手冊、 合約或協議，或為知識庫或專業知識的員工具體呈現存在。 這些原則動態而有所變更透過時間到期變更哪些企業方案、 法規或因為其他原因。

在傳統的程式設計語言中實作這些原則需要大量的時間和協調，，並不會啟用非程式設計人員參與建立和維護商務原則。 BizTalk 商務規則能快速地執行這些原則，並將分離其餘的商務程序。 如此一來對商務原則進行必要的變更，而不影響其他的商務程序。

##<a name="why-rules"></a>為什麼規則

商業程序中使用 BizTalk 商務規則 3 重要的原因有︰

* 將分離應用程式碼的商務邏輯
- 讓商務分析師有更多的控制商務邏輯管理
+ 變更商務邏輯移至 [實際執行速度更快

##<a name="rules-concepts"></a>規則的概念

##<a name="vocabulary"></a>詞彙

_詞彙_是定義所組成的好記的名稱，用於規則條件和動作運算物件的集合。 詞彙定義讓規則更容易閱讀，瞭解與特定的商業網域中的人員共用。

詞彙專為商業語意和實作之間的差距。 例如核准狀態的資料繫結可能會指向某個資料庫，表示為 SQL 查詢中的特定資料列中的特定資料行]。 而不是插入這類複雜的規則中，您可能會改為建立詞彙定義，相關聯的資料繫結時，記的名稱的 「 狀態 」。 您可以在接下來的規則，任何數字中包含 「 狀態 」 與規則引擎可以擷取資料表中的相對應的資料。

##<a name="rule"></a>規則

商務規則是宣告式控管進行的商務程序的陳述式。 規則包含條件和動作。 此條件會評估，和規則引擎如果其評估為 true，起始一或多個動作。
使用下列格式來定義商務規則架構中的規則︰

_IF__條件__然後__巨集指令_

請考慮下列範例︰

*如果量小於或等於可用為止*  
*然後進行交易和列印回條*

此規則會決定是否會套用商務邏輯，比較的兩個金額，交易金額及提供資金的表單中的表單中進行交易。
您可以使用商務規則來建立、 修改及部署商務規則。 或者，您可以以程式設計方式執行上述作業。

###<a name="conditions"></a>條件

條件為 true/false 組成的一或多個述詞 （布林） 運算式。
在我們的範例，述詞小於或等於會套用至數量與可用為止。 此條件會永遠評估為 true 或 false。
AND、 OR 邏輯運算子，而非表單的邏輯運算式可能很大，可以結合述詞，但一定會評估為 true 或 false。

###<a name="actions"></a>動作

動作是功能條件評估的結果。 如果規則條件符合時，會啟動對應的動作。
在此範例中，「 進行交易 」 及 「 列印回條 」 是執行時，並只時，條件 （在此情況下，「 若小於或等於可用資金 」），則為 true 的動作。
動作會以商務規則架構中設定上執行的作業 XML 文件。

##<a name="policy"></a>原則

原則是規則邏輯群組。 您撰寫原則、 將其儲存，測試，當您滿意結果，生產環境中使用它。

###<a name="policy-composition"></a>原則撰寫

您可以撰寫規則入口網站中的原則。 原則可包含很大的規則集，但通常是您撰寫的規則與相關的應用程式會使用原則的內容中的特定商務網域的原則。

###<a name="policy-testing"></a>原則測試
有效，才能使用生產環境中，您可以執行您的原則的測試執行。 [規則] 入口網站可讓您提供原則，輸入執行原則，並檢視輸出。 輸出包含記錄、 執行規則條件評估，和產生的輸出。

##<a name="sample-scenario---insurance-claims"></a>範例案例-保險理賠
現在就讓我們的範例案例並逐步我們撰寫相同的商務邏輯。

![替代文字][1]

在真正簡易的保險理賠案例中，Claimant 送出他保險理賠 （透過任何用戶端，例如網站、 手機應用程式等）。 此宣告要求取得傳送到公司宣告處理單位，根據 [處理程序的結果，宣告可以是 [已核准]，拒絕] 或 [傳送沿著進一步手動處理。
宣告處理單位案例中會包含系統的商務邏輯的項目。 取得深入瞭解此單位，我們可以看到以下訊息︰

![替代文字][2]

讓我們現在使用商務規則實作商務邏輯。


##<a name="creation-of-rules-api-app"></a>建立的規則 Api 應用程式


1. Azure 入口網站登入
2. 選取 [新增]-> [服務商場，然後搜尋*BizTalk 規則*
3. 從結果清單中選取 [BizTalk 規則]。 隨後便會開啟 BizTalk 規則刀
4. 選取 [*建立*] 按鈕![替代文字][3]
1. 在開啟的新刀，輸入下列資訊︰  
    1. 名稱 – 授與您的規則 API 應用程式名稱
    1. 應用程式服務計劃-選取或建立新的應用程式服務方案
    1. 價格層 – 選擇您要存放在此應用程式的價格層
    1. 資源群組-選取或建立應用程式中應所在位置的資源群組
    2. 訂閱-選取您想要使用的訂閱
    1. 位置 – 選擇您希望部署的應用程式的地理位置。
4.  選取 [*建立*]。 在幾分鐘內建立 BizTalk 規則 API 應用程式。

##<a name="vocabulary-creation"></a>建立詞彙
建立 BizTalk 規則 API 應用程式之後, 的下一個步驟就是建立詞彙。 預期是常見的角色，以可執行此練習開發人員。 以下是如何取得此完成︰


1. 啟動您 BizTalk 規則從入口網站 API 應用程式，前往 [瀏覽]-> [應用程式-API ><Your Rules API App>。 這會收到您規則 API 應用程式的儀表板類似如下︰

   ![替代文字][4]

2.選取 「 詞彙定義 」。 這會顯示詞彙撰寫畫面 3.選取 「 新增 」 若要開始新增新的詞彙定義。
有 2 詞彙定義目前支援 – 常值和 XML 類型。

##<a name="literal-definition"></a>常值定義
1.  按一下 「 新增 」 上後，新的 「 新增定義 」 刀開啟。 輸入下列的值
  1.    名稱 – 預期不含任何特殊字元的英數字元。 這也必須是唯一您現有的詞彙定義清單。
  2.    描述 – 選擇性的欄位。
  3.    定義類型 – 有 2 支援的類型。 在此範例中，選擇常值
  4.    資料類型-這可讓使用者選取的資料類型定義。 目前支援 4 的資料類型︰ 我。  字串 – 這些值必須以雙引號 （"範例字串 「） 輸入  
    ii。 布林值 – 可以是 true 或 false  
    iii。    電話 – 可以是任何小數位數的數字  
    iv。 DateTime – 這表示定義為類型日期類型。 必須使用此格式 – mm/dd/yyyy ss AM\PM 輸入資料  
  5. 輸入 – 這是定義的您用來輸入您的值。 在此處輸入的值必須符合所選的資料類型。 您可以輸入單一值的一組值，並以逗號或使用關鍵字*以*值的範圍。 例如，您可以在其中輸入唯一值 1;一組 1，2，3;或範圍 1 到 5。 請注意，範圍只支援的數字。
  6. 選取*[確定]*。

![替代文字][5]
##<a name="xml-definition"></a>XML 定義
如果詞彙類型選擇為 XML，必須指定下列輸入  
  。    結構描述-按一下這個隨即會開啟新刀允許使用者選擇從已上傳的結構，或讓上傳一個新的清單。
b。    此輸入取得 XPATH-只有在選擇結構描述中上一個步驟之後，才能解除鎖定。 按一下 [在此會顯示已選取，並可讓使用者選取詞彙定義需要建立的節點的結構描述。  
  c。    FACT – 這個輸入識別資料物件會送規則引擎進行處理。 這進階內容，並依預設設定為 [選取 XPATH 的上層。 FACT 會變成特別重要鏈結和集合案例。

![替代文字][6]

### <a name="add-bulk"></a>新增大量
上述步驟所擷取建立詞彙定義的體驗。 建立後，建立的詞彙定義會出現在 [清單] 表單。 沒有，才能從相同的結構描述，而不是重複上述步驟每一次產生多個定義的需求。 這是新增大量功能會很有用的位置。
選取 [大量新增] 將您移至新的刀。 若要選取多個定義所建立的結構描述是第一步。 選取這個隨即會開啟新的刀讓您可以從已上傳的結構描述的清單中選擇，或讓上傳到新的訂閱。
現在 XPATH 屬性取得已解除鎖定。 選取這會開啟結構描述檢視器可以選取多個節點的位置。
建立多個定義的名稱會預設為選取的節點的名稱。 這些永遠可以建立後進行修改。

![替代文字][7]

##<a name="policy-creation"></a>建立原則
開發人員已經建立所需的詞彙，預期會是商務分析師應一個建立商務原則，透過 Azure 入口網站。  
    1.在建立規則應用程式，有按一下使用者會進入原則建立頁面的原則 lens。  
    2.此頁面會顯示具有此特定規則應用程式原則的清單。 分析師可以新增新原則，只要輸入原則名稱，然後按 tab 鍵兩次。 多個原則可以存放在單一規則 API 應用程式。  
    3.選取建立的原則會使用者原則的詳細資料] 頁面將其中一個可以在這裡看見原則中的規則。  
    ![替代文字][8]
4。  選取 [新增]，若要新增新的規則。 這會將您帶往新的刀。

##<a name="rule-creation"></a>建立規則
規則就是條件和動作陳述式的集合。 如果條件結果為 true 時，會執行動作。 在 [建立規則刀中，為的 （適用於該原則） 的唯一的規則名稱及描述 （選擇性）。
[條件 （如果）] 方塊可以用於建立複雜的條件陳述式。 以下是支援的關鍵字︰  
1.  與 – 條件運算子  
2.  或者 – 條件運算子  
3.  沒有\_不\_存在  
4.  存在  
5.  false  
6.  是\_等於\_至  
7.  是\_大於\_比  
8.  是\_大於\_比\_等於\_至  
9.  是\_中  
10. 是\_小於\_比  
11. 是\_小於\_比\_等於\_至  
12. 是\_不\_中  
13. 是\_不\_等於\_至  
14. mod  
15. true  

Action(THEN) 方塊可以包含多個陳述式，其中一個每一行，以建立所要執行的動作。 以下是支援的關鍵字︰  
1.  等於  
2.  false  
3.  true  
4.  停止  
5.  mod  
6.  null  
7.  更新  

條件] 和 [巨集指令方塊提供 Intellisense 協助您快速作者規則。 這可以觸發按 ctrl + 空格或開始輸入。 自動會向下篩選並顯示符合輸入的字元的關鍵字。 Intellisense 視窗會顯示您所有的關鍵字和詞彙定義。
![替代文字][9]

##<a name="explicit-forward-chaining"></a>明確轉寄鏈結
BizTalk 規則支援明確轉寄鏈結因此，如果使用者想要重新評估回應特定動作的規則，他們可以觸發此使用特定關鍵字。 以下是支援的關鍵字︰  
   1.   更新<vocabulary definition>– 此關鍵字重新評估在其條件中使用指定的詞彙定義的所有規則。  
   2.   停止 – 此關鍵字停止所有規則執行

##<a name="enabledisable-rules"></a>Enable\Disable 規則
原則中的每個規則可以啟用或停用。 依預設會啟用所有的規則。 停用的規則不會執行原則執行期間。 Enable\Disable 規則可以進行，或從規則刀直接 – 刀，頂端的命令列中，您可以使用的命令或原則，從內容功能表 （用滑鼠右鍵按一下規則） enable\disable 的選項。

##<a name="rule-priority"></a>規則的優先順序
原則的所有規則的順序都執行。 取決於原則中發生的順序執行的優先順序。 只要將拖放規則可以變更此優先順序。

##<a name="test-policy"></a>測試原則
您可以使用 「 測試原則] 命令測試原則刀測試您的原則。 在此刀，您可以看到詞彙定義使用原則的需要使用者輸入的清單。 使用者可以手動新增這些輸入其測試案例的值。 或者，使用者也可以選擇要匯入測試 XMLs 輸入。 在所有的輸入後，可以執行測試和容易比較的輸出資料行中會顯示為每個詞彙定義的輸出。 若要檢視商務分析師好記的記錄，請按一下檢視執行記錄的 「 檢視記錄檔]。 若要儲存記錄檔，「 儲存輸出 」 選項有儲存所有測試獨立分析的相關的資料。

## <a name="using-rules-in-logic-apps"></a>使用邏輯應用程式中的規則
一旦已經撰寫並測試原則，就立即準備好進行消耗。 您可以從左側的入口網站首頁上選取邏輯應用程式來建立新的邏輯應用程式。 一旦您邏輯應用程式建立之後，啟動它，然後選取 [*觸發程序和動作*。 然後，您可以選取*從頭開始建立*的範本。 請依照將您 BizTalk 規則 API 應用程式新增至邏輯應用程式。 完成之後，會有一個選項來選擇目標的規則 API 應用程式 （動作）。 動作包含所要執行的原則的清單。 選擇特定的原則之後, 輸入所需的原則都需要獲得。 使用者可以使用的輸出規則 API 應用程式傳輸進行進一步的決策。

## <a name="using-rules-via-apis"></a>使用規則透過 Api
規則 API 應用程式可以也會使用叫用豐富的 Api。 此方式使用者不限於只使用邏輯應用程式，並且可以使用任何應用程式中的規則使其他來電。 確切的 REST Api 可以檢視上的規則儀表板中的 「 API 定義 」 鏡頭] 即可。

![替代文字][10]

以下是如何其中一個可能會使用這個 API C 中的範例#

            // Constructing the JSON message to use in API call to Rules API App

            // xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0=\"http://tempuri.org/XMLSchema.xsd\">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

            // The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
            // In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
            // This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

            // The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

請注意，上述規則 API 應用程式設定為 「 匿名公用 」 的安全性設定。 這可以從設定 API 應用程式使用-> [所有設定應用程式設定]-> 都 [存取層級。

![替代文字][11]

## <a name="editing-vocabulary-and-policy"></a>編輯詞彙和原則
使用商務規則的主要優點之一是，變更商務邏輯可以推廣生產快速許多。 詞彙和原則所做的變更會立即套用生產環境中。 瀏覽至個別詞彙定義或原則，然後進行的變更生效的就只需要使用者。

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG
