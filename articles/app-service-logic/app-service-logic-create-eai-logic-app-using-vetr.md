<properties
   pageTitle="建立 Azure 應用程式服務中的邏輯應用程式中使用 VETR EAI 邏輯應用程式 |Microsoft Azure"
   description="驗證、 編碼及轉換 BizTalk XML 服務的功能"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="create-eai-logic-app-using-vetr"></a>建立使用 VETR EAI 邏輯應用程式

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

大部分的企業應用程式整合 (EAI) 案例橋樑資料來源與目的地之間。 這種情況，通常有一組通用的需求︰

- 請確定其他系統的資料格式正確。
- 內送的資料做出決策執行 [查閱]。
- 將資料格式轉換到另一個。 例如，將資料從 CRM 系統的資料格式轉換成 ERP 系統的資料格式。
- 將資料傳送到您要的應用程式或系統。

本文將示範常見的整合模式: 「 單向訊息中繼 」 或 VETR （驗證，Enrich，轉換，路線）。 VETR 圖樣會調解來源實體和目的地實體之間的資料。 通常來源與目的地為資料來源。

請考慮接受訂單的網站。 使用者可張貼文章訂單] 以使用 HTTP 系統。 在幕後系統驗證連入資料的正確性，正規化，並將其保存在服務匯流排佇列中，以進一步處理。 系統會關閉佇列中，必須將其以特定格式的訂單。 因此，端對端流程是︰

**HTTP** →**驗證**→**轉換**→**服務匯流排**

![基本 VETR 流程][1]

下列的 BizTalk API 應用程式的說明建立此模式︰

* **HTTP 觸發程序**觸發程序訊息事件的來源
* **驗證**-驗證連入資料的正確性
* **轉換**為內送格式化下游系統所需的轉換資料
* **服務匯流排連接器**目的地實體資料的傳送位置


## <a name="constructing-the-basic-vetr-pattern"></a>建立基本的 VETR 圖樣
### <a name="the-basics"></a>基本概念

在 Azure 入口網站中，選取 [ **+ 新增**、 選取**Web + 行動**]，然後選取**邏輯應用程式**。 選擇的名稱、 位置、 訂閱、 資源] 群組中及運作的位置。 資源群組做為容器的應用程式。所有資源的應用程式移至相同的資源群組。

接下來，讓我們來新增引動程序和動作。


## <a name="add-http-trigger"></a>新增 HTTP 觸發程序
1. 在**邏輯應用程式範本**中，選取 [**從從頭開始建立**。
1. 若要建立新的接聽程式庫選取**HTTP 接聽程式**。 呼叫**HTTP1**。
2. 設定**自動傳送回應？**設定為 false。 設定來設定_HTTP 方法__文章_和設定_的 URL_以_/OneWayPipeline_的觸發程序動作︰  
    ![HTTP 觸發程序][2]
3. 選取 [完成觸發程序的綠色勾選記號]。

## <a name="add-validate-action"></a>新增驗證巨集指令

現在讓我們來輸入每次觸發程序，則會啟動時執行的動作，也就是在 HTTP 端點上接到來電時。

1. 從圖庫新增**BizTalk XML 驗證程式**，並將其命名為 [ _(Validate1)_建立一個執行個體。
2. 設定 XSD 結構描述驗證的內送的 XML 訊息。 選取 [_驗證_] 動作，然後選取_triggers('httplistener').outputs。內容_為_inputXml_參數值。

現在，驗證動作是在 HTTP 接聽後的第一個動作︰ 

![BizTalk XML 驗證程式][3]

同樣地，讓我們來新增其餘的動作。 

## <a name="add-transform-action"></a>新增轉換動作
讓我們來設定要標準化的內送的資料轉換。

1. 從圖庫新增**BizTalk 轉換服務**。
2. 若要設定要轉換的內送的 XML 訊息的轉換，選取 [**轉換**] 動作為此 API 稱為時執行的動作。 選取 [ ```triggers(‘httplistener’).outputs.Content``` _inputXml_為的值。 *對應*是選用的參數，由於內送的資料相符的所有設定轉換，而且只有符合結構描述會套用。
3. 最後，驗證成功，只有執行轉換。 若要設定這種情況，請選取齒輪圖示右上方，並選取 [_新增符合特定條件_。 若要設定條件```equals(actions('xmlvalidator').status,'Succeeded')```:  

![BizTalk 轉換][4]


## <a name="add-service-bus-connector"></a>新增服務匯流排連接器
接下來，新增目的地，服務匯流排佇列中，若要撰寫資料。

1. 從圖庫新增**服務匯流排連接器**。 設定**名稱** _Servicebus1_，設定您的服務匯流排執行個體之連線字串的**連接字串**、 設定**實體名稱**至_佇列中_，並且略過**訂閱名稱**。
2. 選取 [**傳送訊息**] 動作， _actions('transformservice').outputs 來設定 [**內容**] 欄位的動作。OutputXml_。
3. 設定 [**內容類型**] 欄位為*應用程式/xml*:  

![服務匯流排][5]


## <a name="send-http-response"></a>傳送 HTTP 回應
一旦完成管線處理後，返回傳送 HTTP 回應的成功與失敗，執行下列步驟︰

1. 從圖庫新增**HTTP 接聽程式**，然後選取 [**傳送 HTTP 回應**] 動作。
2. 設定要傳送*訊息*的**回應識別碼**。
2. 將**回應內容**為*已完成的管線處理*。
3. **回應狀態碼**為*200*表示 HTTP 200 [確定]。
4. 選取右上方的下拉式功能表，然後選取 [**新增符合特定條件**]。  設定為下列運算式的條件︰  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. 重複這些步驟來傳送 HTTP 回覆以及失敗。 變更**條件**下列運算式︰  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. 選取**[確定**]，然後**建立**。



## <a name="completion"></a>完成
每當有人傳送郵件給 HTTP 端點時，它會觸發應用程式，並執行您剛剛建立的動作。 若要管理這類邏輯應用程式您可以建立、 選取 Azure 入口網站中的 [**瀏覽**並選取**邏輯應用程式**。 選取您的應用程式，若要查看更多的資訊。

某些很有幫助的主題︰

[管理及監視 API 應用程式與連接器](app-service-logic-monitor-your-connectors.md)  <br/>
[監控您的邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG
