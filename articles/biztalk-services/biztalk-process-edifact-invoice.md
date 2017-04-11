<properties
   pageTitle="教學課程: [處理程序使用 Azure BizTalk 服務 EDIFACT 發票 |Microsoft Azure BizTalk 服務"
   description="如何建立及設定方塊連接器或 API 應用程式，並將其用於 Azure 應用程式服務中的邏輯應用程式"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="deonhe"/>

# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>教學課程︰ 使用 Azure BizTalk 服務發票程序 EDIFACT
您可以使用 [BizTalk Services 入口網站設定及部署 X12 和 EDIFACT 合約。 在本教學課程中，我們看看如何建立 EDIFACT 合約交換交易夥伴間的發票。 本教學課程中會寫入周圍包含兩個交易夥伴北風和 Contoso 交換 EDIFACT 訊息的端對端商務解決方案。  

## <a name="sample-based-on-this-tutorial"></a>根據本教學課程中的範例
本教學課程中會寫入周圍的範例，**傳送 EDIFACT 發票使用 BizTalk 服務**，這是可以從[MSDN 程式碼庫](http://go.microsoft.com/fwlink/?LinkId=401005)下載。 您可以使用範例，瀏覽此教學課程以瞭解如何建立範例。 或者，您可以使用此教學課程中建立您自己的方案地上向上。 本教學課程被衝第二種方法，讓您瞭解如何建立此方案。 此外，最大的教學課程一致的樣本，並使用相同的名稱，例如結構描述 （轉換） 的成品為使用中的範例。  

>[AZURE.NOTE] 此方案包含從 EAI 橋在傳送郵件到 EDI 橋，因為它會重複使用[BizTalk 服務橋鏈結範例](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104)的範例。  

## <a name="what-does-the-solution-do"></a>解決方案做什麼？

在本方案中，[北風接收 Contoso EDIFACT 發票。 這些發票並不是標準的 EDI 格式。 因此，發票北風之前，必須轉換 EDIFACT 發票 （也稱為 INVOIC） 文件。 在收到時，必須 EDIFACT 發票，[處理程序北風，並將其返回 Contoso （也稱為 CONTRL） [控制] 訊息中。

![][1]  

若要達到此商務案例，Contoso 使用與 Microsoft Azure BizTalk 服務所提供的功能。

*   Contoso 使用 EAI 橋 EDIFACT INVOIC 將原始發票轉換。

*   EAI 橋傳送郵件給部署屬於 BizTalk Services 入口網站中設定協議 EDI 傳送橋。

*   EDI 傳送橋處理 EDIFACT INVOIC，並將其傳送到 [北風。

*   接收發票後, 北風傳回 CONTRL 訊息到 EDI 會收到橋部署合約的一部分。  

> [AZURE.NOTE] 您也可以此方案也會示範如何使用批次處理傳送批次，而非分別傳送每個發票的發票。  

若要完成此案例，我們可以使用服務匯流排佇列發票 Contoso 從 Northwind 以從傳送或接收認可 Northwind。 可以使用用戶端應用程式，是以下載和中所提供的封裝範例包含建立這些佇列此教學課程的一部分。  

## <a name="prerequisites"></a>必要條件

*   您必須服務匯流排命名空間。 如需建立命名空間的指示，請參閱[如何︰ 建立或修改服務匯流排服務命名空間](https://msdn.microsoft.com/library/azure/hh674478.aspx)。 讓我們假設您已經有佈建後，服務匯流排命名空間呼叫**edifactbts**。

*   您必須 BizTalk 服務訂閱。 如需相關指示，請參閱[建立 BizTalk 服務使用 Azure 傳統的入口網站](http://go.microsoft.com/fwlink/?LinkID=302280)。 在此教學課程中，讓我們是假設您稱為**contosowabs**BizTalk 服務訂閱。

*   註冊 BizTalk 服務訂閱 BizTalk Services 入口網站上。 如需相關指示，請參閱[註冊 BizTalk 服務上的部署 BizTalk Services 入口網站](https://msdn.microsoft.com/library/hh689837.aspx)

*   您必須安裝的 Visual Studio。

*   您必須安裝 BizTalk 服務 SDK。 您可以從[http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)下載 SDK  

## <a name="step-1-create-the-service-bus-queues"></a>步驟 1︰ 建立服務匯流排佇列  
此解決方案使用服務匯流排佇列交換交易夥伴間的訊息。 Contoso 和北風傳送訊息給佇列從 EAI 及/或 EDI 橋位置使用它們。 本方案中，您需要三個服務匯流排佇列︰

*   **northwindreceive** – 北風接收發票 Contoso 透過此佇列。

*   **contosoreceive** – Contoso 收到認可從 Northwind 透過此佇列。

*   所有**暫停**– 暫停郵件路由到此佇列中。 如果電腦無法處理時，會暫停郵件。

您可以建立這些服務匯流排佇列使用範例套件中包含的用戶端應用程式。  

1.  從您已下載的範例的位置，開啟**教學課程傳送發票使用 BizTalk 服務 EDI Bridges.sln**。

2.  按**F5**來建立和啟動的**教學課程用戶端**應用程式。

3.  在畫面中，輸入 [服務匯流排 ACS 命名空間、 發行者名稱和發行者鍵。

    ![][2]  
4.  訊息方塊會提示三個佇列將會建立在您的服務匯流排命名空間。 按一下**[確定]**。

5.  [離開執行教學課程用戶端。 開啟，按一下 [**服務匯流排** > **_服務匯流排命名空間_** > **佇列**，並確認會建立三個佇列。  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>步驟 2︰ 建立並部署交易合作夥伴合約
建立 Contoso 之間北風貿易合作夥伴合約。 交易的合作夥伴合約定義貿易合約之間的兩個業務合作夥伴，例如哪些郵件結構描述，若要使用，哪些訊息的通訊協定，以使用等等。交易的合作夥伴合約包含兩個 EDI 橋、 一個交易 （稱為**EDI 傳送橋**） 的合作夥伴，才能傳送郵件，一個交易夥伴 （稱為**EDI 接收橋**） 接收訊息。

在此方案的內容，EDI 傳送橋對應到合約傳送端和用來從 Contoso 傳送 EDIFACT 發票，以 [北風。 同樣的 EDI 接收橋對應至合約接收側，並從 Northwind 用於接收通知。  

### <a name="create-the-trading-partners"></a>建立交易夥伴

開始使用，建立 Contoso 和北風貿易協力廠商。  

1.  在 BizTalk Services 入口網站，在 [**合作夥伴**] 索引標籤上按一下 [**新增**]。

2.  在新的合作夥伴頁面上，輸入**Contoso**為位合作夥伴的名稱，然後再按一下 [**儲存**。

3.  重複步驟，以建立第二個合作夥伴**北風**。  

### <a name="create-the-agreement"></a>建立合約
交易合作夥伴合約會建立之間的商業夥伴的企業設定檔。 此解決方案使用我們所建立的合作夥伴會自動建立的預設合作夥伴設定檔。  

1.  在 [BizTalk Services 入口網站中，按一下 [**合約** > **新增**。

2.  在新的合約**一般設定**] 頁面上指定的值，如下所示，圖像，然後按 [**繼續**。

    ![][3]  

    按一下 [**繼續]**之後，就可以使用 [兩個索引標籤，**會收到設定**與**傳送設定**。

3.  建立 Contoso 和北風之間傳送合約。 本合約管理 Contoso 至北風傳送 EDIFACT 發票的方式。

    1.  按一下 [**傳送設定**。

    2.  保留**連入 URL**、**轉換**，以及**批次處理**] 索引標籤上的預設值。

    3.  在**通訊協定**] 索引標籤的 [**結構**] 區段中上, 傳**EFACT_D93A_INVOIC.xsd**結構描述。 此結構描述中皆可取得封裝範例。

        ![][4]  
    4.  在 [**傳輸**] 索引標籤中，指定服務匯流排佇列的詳細資料。 傳送端協議，我們將使用**northwindreceive**佇列 EDIFACT 發票傳送至 [北風]，然後**暫停**佇列中，將路由處理期間失敗，而且會暫停的任何訊息。 您建立這些佇列中的**步驟 1︰ 建立服務匯流排佇列**（在本主題中）。

        ![][5]  

        在下**傳輸設定 > 傳輸類型**和**訊息暫停設定 > 傳輸類型**Azure 服務匯流排，然後選取提供的值，如下圖所示。

4.  建立接收合約 Contoso 和北風之間。 本合約管理 Contoso 從 Northwind 所收到的通知。

    1.  按一下 [**接收設定**]。

    2.  保留**傳輸**及**轉換**] 索引標籤上的預設值。

    3.  在**通訊協定**] 索引標籤的 [**結構**] 區段中上, 傳**EFACT_4.1_CONTRL.xsd**結構描述。 此結構描述中皆可取得封裝範例。

    4.  在 [**傳送**] 索引標籤上建立篩選，以確保只有 northwind 通知傳閱前往 Contoso。 **路由設定**] 下按一下 [**新增**]，以建立路由篩選]。

        ![][6]  
        1.  提供的**規則名稱**]、 [**傳送規則**及 [**路由目的地**的值，如下圖所示。

        2.  按一下 [**儲存**]。

    5.  在 [**傳送**] 索引標籤上，指定已擱置的通知 （在處理期間失敗的通知） 傳送到的位置。 Azure 服務匯流排設定傳輸類型、 路由**佇列中**的驗證類型的目的地輸入要**共用的 Access 簽章**(SA)，提供服務匯流排命名空間 SA 連線字串，然後輸入佇列中名稱為 [**暫停**。

5.  最後，按一下 [**部署**至部署授權合約。 請注意，端點位置傳送及接收部署合約。

    *   在**傳送設定**] 索引標籤的 [**輸入 URL**，請注意，結束點。 若要以 [北風使用 EDI 傳送橋 Contoso 傳送的郵件，您必須將訊息傳送給此端點。

    *   在**接收設定**] 索引標籤的 [**傳輸**，請注意，結束點。 若要傳送的郵件從 Northwind Contoso 使用 EDI 接收橋，您必須將訊息傳送給此端點。  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>步驟 3︰ 建立及部署 Biztalk 專案

在上一個步驟中，您可以部署 EDI 傳送和接收合約 EDIFACT 發票和通知的處理。 這些合約可以標準 EDIFACT 訊息結構描述的程序郵件。 不過，此方案案例中，每 Contoso 會傳送發票給 Northwind 內部的專屬結構描述中。 因此，訊息會傳送至 EDI 傳送橋之前，必須轉換從內部的結構描述至標準 EDIFACT 發票結構描述。 BizTalk 服務 EAI project 的做法的。

Biztalk 專案， **InvoiceProcessingBridge**，轉換郵件也是您所下載範例的一部分。 專案會包含下列的成品︰

*   **INHOUSEINVOICE。XSD** – 會傳送至北風的內部發票的結構描述。

*   **EFACT_D93A_INVOIC。XSD** -的標準 EDIFACT 發票的結構描述。

*   **EFACT_4.1_CONTRL。XSD** – 北風會傳送給 Contoso EDIFACT 通知的結構描述。

*   **INHOUSEINVOICE_to_D93AINVOIC。TRFM** – 將內部發票結構描述對應至標準 EDIFACT 發票結構描述轉換。  

### <a name="create-the-biztalk-services-project"></a>建立 Biztalk 專案
1.  在 Visual Studio 解決方案，請展開 InvoiceProcessingBridge 專案，然後再開啟**MessageFlowItinerary.bcs**檔案。

2.  按一下畫布上的任一處並**BizTalk 服務 URL**中設定 [屬性] 方塊，指定您 BizTalk 服務的訂閱名稱。 例如， `https://contosowabs.biztalk.windows.net`。

    ![][7]  
3.  從工具箱拖曳 [ **Xml 單向橋**畫布上。 設**ProcessInvoiceBridge**橋的**實體名稱**和**相關的地址**屬性。 按兩下以開啟橋設定介面**ProcessInvoiceBridge** 。

4.  **郵件類型**] 方塊中，按一下加號 (**+**) 按鈕，以指定的內送郵件的結構描述。 因為內送郵件，以 EAI 橋都是內部發票，請將此設**INHOUSEINVOICE**。

    ![][8]  
5.  按一下 [ **Xml 轉換**] 圖形，然後在 [屬性] 方塊的 [**地圖**] 屬性中，按一下省略符號 （**...**）] 按鈕。 在**地圖選項**] 對話方塊中，選取**INHOUSEINVOICE_to_D93AINVOIC**轉換檔案，然後再按一下**[確定]**。

    ![][9]  
6.  回到**MessageFlowItinerary.bcs**，並從工具箱]，將**雙向外部服務端點**拖曳到右邊的**ProcessInvoiceBridge**。 您可以將其**實體 Name**屬性為**EDIBridge**。

7.  在方案總管中，展開 [ **MessageFlowItinerary.bcs**並按兩下**EDIBridge.config**檔案。 以下列取代**EDIBridge.config**的內容。

    > [AZURE.NOTE] 為什麼需要編輯.config 檔案？ 我們橋設計畫布上所新增的外部服務端點代表我們先前部署 EDI 橋。 EDI 橋雙向橋，使用傳送，而且接收側。 不過，我們新增橋設計工具 EAI 橋是單向橋。 因此，若要處理的兩個橋不同的郵件交換模式，我們使用自訂橋行為.config 檔案中包括它的設定。 此外，自訂行為也會處理 EDI 傳送橋端點驗證。在[BizTalk 服務橋鏈結範例-EAI EDI 至](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104)另一個範例使用這個自訂的行為。 此方案會重複使用的範例。  
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  更新 EDIBridge.config 檔案，包括設定的詳細資訊

    *   在下_<behaviors>_，提供 ACS 命名空間和 BizTalk 服務訂閱相關聯的金鑰。

    *   在下_<client>_，提供的端點 EDI 傳送合約部署的位置。

    儲存變更並關閉設定檔。

9.  從工具箱中，按一下 [**連接器**]，加入**ProcessInvoiceBridge**和**EDIBridge**元件。 選取連接器，並在 [屬性] 方塊中，設定**篩選條件**至**所有相符項目**。 如此一來，可確保所有郵件處理 EAI 橋都傳閱 EDI 橋接。

    ![][10]  
10.  將變更儲存至方案。  

### <a name="deploy-the-project"></a>部署專案

1.  在電腦上建立 Biztalk 專案的位置，下載並安裝 BizTalk 服務訂閱的 SSL 憑證。 從，BizTalk 在服務底下，按一下 [**儀表板**，，然後按一下 [**下載 SSL 憑證**。 按兩下憑證，然後依照提示完成安裝。 請確定您已安裝在**受信任的根憑證授權單位**憑證存放區的憑證。

2.  在 Visual Studio 方案總管**InvoiceProcessingBridge**專案，以滑鼠右鍵按一下，然後按一下**部署**。

3.  提供圖像，所示的值，然後按一下 [**部署**。 您可以取得 ACS 認證 Biztalk BizTalk Services 儀表板中按一下 [**連線資訊**。

    ![][11]  

    從 [輸出] 窗格中，複製 [EAI 橋已部署的位置，例如端點`https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`。 您稍後需要此端點 URL。  

## <a name="step-4-test-the-solution"></a>步驟 4︰ 測試方案


本主題中，我們看看如何使用**教學課程用戶端**應用程式的範例中所測試方案。  

1.  在 Visual Studio 中，請按 F5 開始**教學課程用戶端**。

2.  畫面必須具備我們所建立的服務匯流排佇列預先填入的步驟中的值。 按一下 [**下一步**]。

3.  在下一個視窗中，提供 BizTalk 服務訂閱 ACS 認證和結束位置 EAI 和 EDI （接收） 橋部署。

    在上一個步驟中，您必須複製 EAI 橋結束點。 EDI 收到橋端點 BizTalk Services 入口網站中，移至合約 > 接收設定 > 傳輸 > 結束點。

    ![][12]  
4.  在下一個視窗中，在 Contoso，按一下 [**傳送內部發票**] 按鈕。 在檔案中開啟] 對話方塊中，開啟 INHOUSEINVOICE.txt 檔案。 檢查檔案的內容，然後按一下 [傳送發票的**[確定]** 。

    ![][13]  
5.  在幾秒鐘 northwind 收到發票。 按一下**檢視郵件**連結，請參閱北風收到的發票。 請注意北風收到的發票標準 EDIFACT 結構描述中時 Contoso 所傳送的是內部的結構描述。

    ![][14]  
6.  選取 [發票，然後按一下 [**傳送通知**。 在出現的對話方塊中，請注意，交換識別碼相同收到的發票和傳送通知。 按一下 [在**傳送通知**] 對話方塊中的 [確定]。

    ![][15]  
7.  幾秒數，Contoso 成功會收到通知。

    ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>步驟 5 （選用）︰ 傳送 EDIFACT 發票批次 
BizTalk 服務 EDI 橋也支援外寄郵件的批次。 這項功能很適合用於接收比較想要收到的郵件 （符合特定準則），而不是個別郵件以批次的合作夥伴。

最重要的長寬使用批次時是該批次，也稱為發行準則的實際版本。 發行準則可以為依據如何接收合作夥伴想將接收訊息。 如果已啟用批次，EDI 橋不會傳送外寄郵件接收夥伴鍵，直到已完成發行準則。 例如，批次的準則根據郵件大小分派以批次時才 'n' 批次處理郵件。 批次準則也可以是以時間為基礎，如此一批次固定每天傳送。 在本方案中，我們可以嘗試的郵件大小根據的準則。

1.  在 BizTalk Services 入口網站中，按一下您先前建立的合約。 按一下 [傳送設定 > 批次處理 > 新增批次。

2.  批次的名稱，輸入**InvoiceBatch**、 提供描述，然後按 [**下一步**。

3.  指定批次的準則，定義必須批次的郵件。 在本方案中，我們批次的所有郵件。 因此，選取 [使用進階定義] 選項，並輸入**1 = 1**。 這是一律為 true，條件，因此會批次的所有郵件。 按一下 [**下一步**]。

    ![][17]  
4.  指定批次發行準則。 從下拉式方塊中，選取**MessageCountBased**，並**計算**，指定**3**。 這表示，以批次的三個訊息會傳送到北風。 按一下 [**下一步**]。

    ![][18]  
5.  檢閱摘要，然後按一下 [**儲存**。 按一下 [重新部署合約**部署**]。

6.  回到**教學課程用戶端****傳送內部發票**，，然後按一下遵循提示，傳送發票。 您會發現沒有發票會收到 northwind，因為不符合批次的大小。 重複此步驟兩次，以便您有三個發票 Northwind 的郵件。 此滿足批次發行的準則的 3 的訊息，您現在應該會看到 northwind 發票。


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

