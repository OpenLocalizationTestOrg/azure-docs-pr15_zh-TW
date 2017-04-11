<properties
    pageTitle="使用邏輯應用程式中的檔案連接器 |Microsoft Azure 應用程式服務"
    description="如何建立及設定檔案連接器或 API 應用程式，並將其用於 Azure 應用程式服務中的邏輯應用程式"
    authors="rajeshramabathiran"
    manager="erikre"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="rajram"/>

# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>開始使用檔案連接器並將其新增至您的邏輯應用程式
>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽架構版本。

連線到檔案系統上傳、 下載和更多] 以您主機上的檔案。 觸發邏輯應用程式，可以根據各種不同的資料來源與優惠連接器，以取得並處理資料。 您可以新增檔案連接器商務工作流程與程序資料做為此工作流程中的邏輯應用程式的一部分。 

檔案連接器連線時使用混合式連線管理員的混合式主機檔案系統。

## <a name="creating-a-file-connector-for-your-logic-app"></a>建立檔案連接器邏輯應用程式 ##
若要使用的檔案連接器，您需要先建立的檔案連接器 API 應用程式的執行個體。 這可以如下所示︰

1.  開啟的 + 新增使用 Azure Marketplace Azure 入口網站的左側的選項。
2.  搜尋 「 檔案連接器]。
3.  從搜尋結果中選取**檔案連接器 （預覽版本）** 。
4.  選取 [**建立**] 按鈕
5.  設定檔案連接器，如下所示︰  
![][1]

    - **名稱**授與您檔案的連接器名稱
    - **封裝設定**
        - **根資料夾**-主機電腦上指定的根資料夾路徑。 例如。 D:\FileConnectorTest
        - **服務匯流排連線字串**-提供服務匯流排連線字串。 請務必服務匯流排命名空間類型標準的並不允許的服務匯流排轉送的使用基本。  服務匯流排轉送用來連線到混合式連線管理員。
    - 選取**應用程式服務計劃**-，或建立應用程式服務計劃
    - **價格層**-選擇連接器的價格層
    - **資源群組**-選取或建立連接器的所在位置的資源群組
    - **訂閱**-選擇您想要在中建立此連接器的訂閱
    - **位置**-選擇您希望部署連接器的地理位置

4. 在上的按一下 [建立]。 建立新的檔案連接器

## <a name="configure-hybrid-connection-manager"></a>混合式連線管理員設定 ##
API 應用程式執行個體建立之後，瀏覽至其儀表板]。  這可以按一下 [瀏覽] > API 應用程式 > 選取您的檔案連接器 API 應用程式。  從這裡開始混合式連線管理員必須設定。
如需設定和疑難排解混合式連線管理員的相關資訊請參閱[使用混合式連線管理員]。

## <a name="using-the-file-connector-in-your-logic-app"></a>使用邏輯應用程式中的檔案連接器 ##
您的 API 應用程式建立後，您現在可以邏輯應用程式使用作為動作檔案連接器。 若要這麼做，您需要︰

1.  建立新的邏輯應用程式，然後選擇有檔案連接器相同資源群組]。 若要[建立新的邏輯應用程式]，請遵循指示。

2.  開啟 [觸發程序和動作 」 建立邏輯應用程式來開啟邏輯應用程式設計工具，並設定您的流程。

3.  檔案連接器會顯示在右側的庫的 「 API 此資源] 群組中的應用程式 」 一節。

4.  您可以放到編輯器] 中的檔案連接器 API 應用程式，即可在 「 檔案連接器]。 檔案連接器公開一個觸發程序與 4 的動作︰  
![][5]

6.  每個其中一種顯示特定內容。 下圖會列出觸發程序的內容，並取得檔案的動作︰  
![][6]

7. 一旦這些設定，請的觸發程序和動作可用於您的流程。 同樣地，也可以設定採取其他動作。

> [AZURE.NOTE] 成功讀取資料夾之後，檔案的觸發程序會刪除的檔案。

## <a name="file-connector-rest-apis"></a>檔案連接器 REST Api ##
若要使用的連接器以外的邏輯應用程式，可以運用公開連接器的 REST Api。 您可以使用瀏覽此 API 定義 Api 應用程式]-> [檢視]-> [檔案連接器。 現在按一下檢視所有公開此連接器的 Api 的 [摘要] 區段下的 API 定義透鏡︰  
![][7]

在[檔案連接器 API 定義]找的 Api 的詳細資料。

## <a name="do-more-with-your-connector"></a>執行更多您的連接器
現在，建立連接器時，您可以將其新增商務工作流程使用邏輯的應用程式。 請參閱[邏輯應用程式有哪些？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要開始使用 Azure 邏輯應用程式註冊 Azure 帳戶之前，請移至[試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic)，可以讓您立即建立短暫入門邏輯應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

在[連接器與 API 應用程式參照](http://go.microsoft.com/fwlink/p/?LinkId=529766)檢視 Swagger REST API 參照。

您也可以檢閱效能統計資料，並控制安全性連接器。 請參閱[管理與監視內建的 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[建立新的邏輯應用程式]: app-service-logic-create-a-logic-app.md
[檔案連接器 API 定義]: https://msdn.microsoft.com/library/dn936296.aspx
[使用混合式連線管理員]: app-service-logic-hybrid-connection-manager.md
