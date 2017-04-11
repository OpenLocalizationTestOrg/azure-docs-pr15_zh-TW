<properties
   pageTitle="使用邏輯應用程式中 HTTP 接聽和連接器 |Microsoft Azure 應用程式服務 "
   description="如何建立及設定 HTTP 接聽和 HTTP 動作連接器] 或 [API 應用程式，並將其用於 Azure 應用程式服務中的邏輯應用程式"
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
   ms.date="08/31/2016"
   ms.author="prkumar"/>


# <a name="get-started-with-the-http-listener-and-http-action-and-add-it-to-your-logic-app"></a>快速入門的 HTTP 接聽和 HTTP 動作，並將其新增至您的邏輯應用程式

> [AZURE.NOTE]因為其功能現在包含預設為**手動觸發程序**建立新的邏輯應用程式時，我們會結束支援此連接器。  我們建議您升級所有使用此連接器您邏輯應用程式。  
> 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽架構版本。

直接連接到 HTTP 資源接聽 HTTP 要求及設定 HTTP web 要求。 有某些情況下，您可能需要使用直接 HTTP 連線，包括︰

1.  若要開發邏輯應用程式支援的 web 或行動使用者互動式前端。
2.  若要取得程序沒有方塊連接器辦公室的 web 服務的資料。
3.  若要執行的動作，已公開為 web 服務，但不是提供 API 應用程式。

下列情況下，有兩個選項︰

1. **HTTP 接聽**︰ 此連接器做為觸發程序，並接聽 HTTP 要求設定的端點上。 設定的端點上收到通話時，它會觸發程序的流程新的執行個體，並傳送邀請的流程處理中收到的資料。 它也可以設定流程已啟動，或可讓您建構根據資料流程執行的回應，並傳送回覆給呼叫者時自動回覆傳入的邀請。
2. **動作 HTTP**︰ 這可讓您在網際網路上設定任何可用的端點的 web 要求取得回回應，，可供使用的其他動作來流程中。

觸發邏輯應用程式，可以根據各種不同的資料來源與優惠連接器可取得和流程屬於處理資料。 您可以新增 HTTP 連接器商務工作流程與程序資料做為此工作流程中的邏輯應用程式的一部分。 

## <a name="creating-an-http-listener-for-your-logic-app"></a>建立 HTTP 接聽項邏輯應用程式
連接器可以建立邏輯應用程式中，或直接從 Azure Marketplace 建立。 若要建立從服務商場的連接器︰  

1. 在 Azure startboard 中，選取 [ **Marketplace**]。
2. 搜尋 「 HTTP 」、 選取 HTTP 接聽並選取 [**建立**]。
3.  設定 HTTP 接聽如下所示︰  
![][1]

4.  設定封裝設定，您會看到下列選項上接聽是否應該自動回覆，或要求您傳送明確的回應。 將此設為**False**傳送您的回應︰  
![][2]

5.  按一下**[確定**] 以建立。
6.  API 應用程式執行個體建立後，開啟 [設定安全性設定]。 HTTP 接聽程式目前支援基本驗證。 您可以設定此 HTTP 接聽程式開啟時，請使用 [安全性] 選項︰  
![][3]
  
    **已知問題**  *安全性設定會顯示 「 無 」 作為預設值，但是它尚未定義。您必須變更設定為 [基本] 以及回到無儲存以確保 HTTP 接聽已正確設定之前，先。*

7. 最後，設定的安全性設定的 API 應用程式公開 （匿名） 若要允許外部用戶端存取結束點。 此設定會在 「 所有設定 > 應用程式設定 「 HTTP 接聽 API 應用程式︰![][10]

完成之後，您現在可以建立使用 HTTP 接聽邏輯應用程式。

## <a name="using-the-http-listener-in-your-logic-app"></a>邏輯應用程式中使用 HTTP 接聽程式
您的 API 應用程式建立後，您現在可以邏輯應用程式使用的觸發程序 HTTP 接聽程式。 若要這麼做，您需要︰

4.  建立新的邏輯應用程式。
5.  開啟 「 引動程序和動作]，開啟 [邏輯應用程式設計工具並設定您的流程。 HTTP 接聽程式會列在圖庫]。 請選取它。
6.  您現在可以設定 HTTP 方法，需要觸發流程接聽的 URL:  
![][4]  
![][5]

7.  若要取得完整的 URI，按一下要檢視其設定的設定，並為 「 主機 」 API 應用程式中複製 URL HTTP 接聽程式︰  
![][6]
8.  您現在可以使用資料收到 HTTP 要求中流程中的其他動作，如下所示︰  
![][7]  
![][8]
9.  最後，若要傳送回應，新增另一個 HTTP 接聽程式，然後選取 [傳送 HTTP 回應動作。 要求將 ID 設定至 RequestID 取自 HTTP 接聽程式，並填入回應內容和您想要回到傳回 HTTP 狀態︰  
![][9]

## <a name="using-the-http-action"></a>使用 HTTP 動作
HTTP 動作原本即支援邏輯應用程式，而且您不需要 API 應用程式，先建立要能夠使用。 您可以隨時插入 HTTP 動作，邏輯應用程式中，選擇 [URI、 標題和本文的號碼。
HTTP 動作會支援的用戶端安全性的多個的選項。 請參閱[用戶端側安全性選項](../scheduler/scheduler-outbound-authentication.md)。

HTTP 動作的輸出是標題和本文，可供進一步傳輸流程類似如何耗用的其他動作和連接器輸出中。

## <a name="do-more-with-your-connector"></a>執行更多您的連接器
現在，建立連接器時，您可以將其新增商務工作流程使用邏輯的應用程式。 請參閱[邏輯應用程式有哪些？](app-service-logic-what-are-logic-apps.md)。

在[連接器與 API 應用程式參照](http://go.microsoft.com/fwlink/p/?LinkId=529766)檢視 Swagger REST API 參照。

您也可以檢閱效能統計資料，並控制安全性連接器。 請參閱[管理與監視內建的 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

> [AZURE.NOTE] 如果您想要開始使用 Azure 邏輯應用程式註冊 Azure 帳戶之前，請移至[試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic)。 應用程式服務中，可以立即建立短暫入門邏輯應用程式。 必要; 沒有信用卡沒有承諾。

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png
