<properties
    pageTitle="快速入門 java Azure 搜尋 |Microsoft Azure |裝載的雲端搜尋服務"
    description="如何建立裝置上使用您的程式設計語言 Java Azure 裝載的雲端搜尋應用程式。"
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-java"></a>快速入門 java Azure 搜尋
> [AZURE.SELECTOR]
- [入口網站](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

瞭解如何建立自訂的 Java 搜尋應用程式使用其搜尋體驗 Azure 搜尋。 本教學課程中會使用[Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/dn798935.aspx)來建構物件和在練習中使用的作業。

若要執行這個範例中，您必須 Azure 搜尋服務，您可以註冊[Azure 入口網站](https://portal.azure.com)中。 如需逐步指示，請參閱[建立 Azure 搜尋服務在入口網站](search-create-service-portal.md)。

我們來建立並測試此範例使用下列軟體︰

- [蝕 IDE Java 卻開發人員](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar)。 請務必下載卻版本。 其中一個步驟所需只在這個版本中找到的功能。

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>資料的相關資訊

此範例應用程式使用[美國地理服務 (USG)](http://geonames.usgs.gov/domestic/download_data.htm)，篩選上狀態的羅德島來縮小資料集的資料。 我們將使用這項資料來建立傳回地標建築物醫院和學校，以及地理資料流、 湖和 summits 等功能，例如搜尋應用程式。

在此應用程式， **SearchServlet.java**程式建置並載入使用[重新](https://msdn.microsoft.com/library/azure/dn798918.aspx)架構，從公用 Azure SQL 資料庫擷取篩選的 USG 資料集的索引。 預先定義的認證與線上的資料來源的連線資訊會提供程式碼中。 資料存取龐大不不需要任何進一步的設定。

> [AZURE.NOTE] 我們在此保持在免費價格層的 10000 的文件限制] 下的資料集套用篩選。 如果您使用的標準層時，不適用此限制，以及修改使用較大的資料集將此程式碼。 容量的每一個價格的層級的詳細資訊，請參閱[限制及限制](search-limits-quotas-capacity.md)。

## <a name="about-the-program-files"></a>關於程式檔案

下列清單說明相關本範例中的檔案。

- Search.jsp︰ 提供的使用者介面
- SearchServlet.java︰ 提供 （類似 MVC 的控制站） 的方法
- SearchServiceClient.java︰ 控點 HTTP 要求
- SearchServiceHelper.java︰ 協助人員提供的類別靜態方法
- Document.java︰ 提供的資料模型
- config.properties︰ 設定搜尋服務 URL 和 api 金鑰
- Pom.xml: Maven 相依性

<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>尋找您的 Azure 搜尋服務的 api 金鑰與服務名稱

Azure 搜尋所有 REST API 通話都要求您提供的服務 URL 和 api 金鑰。 

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 跳轉列中，按一下 [**搜尋服務**] 清單中的所有 Azure 搜尋服務佈建後，您的訂閱。
3. 選取您想要使用的服務。
4. 在服務儀表板，您會看到磚的基本資訊，以及存取系統按鍵的按鍵圖示。

    ![][3]

5. 複製服務 URL] 與 [系統鍵。 您必須進行更新版本中，當您將其新增至**config.properties**檔案。

## <a name="download-the-sample-files"></a>下載範例檔案

1. 移至[AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) Github 上。

2. 按一下 [**下載 ZIP**.zip 檔案儲存到磁碟，與解壓縮其包含的所有檔案。 請考慮檔案解壓縮到 Java 工作區，使其更容易找到專案更新版本。

3. 範例檔案是唯讀狀態。 以滑鼠右鍵按一下資料夾內容，然後清除唯讀狀態屬性。

所有後續檔案修改和執行陳述式會對此資料夾中的檔案。  

## <a name="import-project"></a>匯入專案

1. 在蝕，選擇 [**檔案** > **匯入** > **一般** > **現有專案的工作區**。

    ![][4]

2. 在**選取的根目錄**，請瀏覽至含有範例檔案的資料夾。 選取包含.project 資料夾的資料夾。 專案應該會出現在 [**專案**] 清單中，為選取的項目。

    ![][12]

3. 按一下 [**完成**]。

4. 您可以使用 [**專案總管**來檢視和編輯檔案。 如果尚未開啟，按一下 [**視窗** > **顯示檢視** > **專案總管**或使用該捷徑，將其開啟。

## <a name="configure-the-service-url-and-api-key"></a>設定 api 金鑰與服務的 URL

1. 在**[專案總管**] 中，按兩下 [ **config.properties**編輯包含 [伺服器名稱] 和 [api 金鑰組態設定]。

2. 請參閱本文前面，其中在[Azure 入口網站](https://portal.azure.com)，取得您現在會輸入**config.properties**值找到 api 金鑰與服務的 URL 的步驟。

3. 在**config.properties**，「 Api 金鑰 」 取代為您的服務的 api 索引鍵。 接下來，服務名稱 （URL http://servicename.search.windows.net 的第一個元件） 會取代相同的檔案中的 「 服務名稱 」。

    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>設定專案、 建立與執行階段環境

1. 蝕，在 [專案總管] 中以滑鼠右鍵按一下專案 >**屬性** > **專案 Facet**。

2. 選取 [**動態網站模組**， **Java**和**JavaScript**]。

    ![][6]

3. 按一下 [**套用**]。

4. 選取 [**視窗**] > **喜好設定** > **伺服器** > **執行階段環境** > **新增。**。

5. 展開 Apache 並選取您先前安裝的 Apache Tomcat 伺服器版本。 在我們的系統，我們安裝版本 8。

    ![][7]

6. 在下一個頁面上，指定 Tomcat 安裝目錄。 在 Windows 電腦上，這會很可能會是 C:\Program Files\Apache 軟體 Foundation\Tomcat*版本*。

6. 按一下 [**完成**]。

7. 選取 [**視窗**] > **喜好設定** > **Java** > **安裝 JREs** > **新增**。

8. 在 [**新增 JRE**，選取 [**標準 VM**]。

10. 按一下 [**下一步**]。

11. 在 JRE 定義中，在 JRE 首頁，按一下 [**目錄**]。

12. 瀏覽至 [ **Program Files** > **Java**和選取 JDK 您先前安裝。 請務必選取 JDK 為 JRE。

13. 在安裝 JREs，選擇 [ **JDK**]。 您的設定值應該類似以下的螢幕擷取畫面。

    ![][9]

14. 您也可以選取**視窗** > **網頁瀏覽器** > **Internet Explorer**外部的瀏覽器視窗中開啟應用程式。 使用外部的瀏覽器可讓您更好的 Web 應用程式經驗。

    ![][8]

您現在已經完成設定工作。 接下來，您會建立並執行專案。

## <a name="build-the-project"></a>建立專案

1. 在 [專案總管] 中以滑鼠右鍵按一下專案名稱，然後選擇**執行為** > **Maven 建置...**設定專案。

    ![][10]

8. 在 [編輯設定] 中的目標中輸入 「 清除安裝 」，然後按一下**執行**。

狀態訊息會在主控台視窗。 您應該會看到建立的成功，指出不具有錯誤的內建的專案。

## <a name="run-the-app"></a>執行應用程式

在最後一個步驟中，您會在本機的伺服器執行階段環境中執行應用程式。

如果您還沒有指定伺服器執行階段環境蝕中，您必須優先執行此動作。

1. 在 [專案總管] 中，展開 [ **WebContent**]。

5. 以滑鼠右鍵按一下**Search.jsp** > **以執行** > **在伺服器上執行**。 選取 Apache Tomcat 伺服器，然後再按一下 [**執行**]。

> [AZURE.TIP] 如果您是使用非預設工作區儲存您的專案，您會需要修改**執行設定**為指向專案位置，以避免伺服器啟動錯誤。 在 [專案總管] 中以滑鼠右鍵按一下**Search.jsp** > **執行為** > **執行設定**。 選取 Apache Tomcat 伺服器。 按一下 [**引數**。 按一下 [**工作區**或**檔案系統**，若要設定包含專案的資料夾。

當您執行應用程式時，您應該會看到瀏覽器視窗中，輸入字詞提供的搜尋方塊。

請等候約一分鐘，再按一下 [**搜尋**]，讓服務的時間，以建立並載入索引。 如果您收到 HTTP 404 錯誤，只要稍微再等待再試一次。

## <a name="search-on-usgs-data"></a>搜尋 USG 資料

USG 資料集包含所要狀態的羅德島相關記錄。 如果您按一下 [**搜尋**] 空的搜尋方塊，您會收到前 50 個項目，這是預設值。

輸入搜尋字詞產生搜尋引擎內容移。 請輸入地區的名稱。 「 Roger Williams 」 是羅德島的第一個的管理員。 許多公園、 建築物及學校被命名他。

![][11]

您也可以嘗試的任何這些條件︰

- Pawtucket
- Pembroke
- goose + 海景

## <a name="next-steps"></a>後續步驟

這是根據 Java 與 USG 資料集的第一個 Azure 搜尋教學課程。 一段時間，我們會延伸此教學課程以示範額外的搜尋功能可能會想要使用的您自訂的解決方案。

如果您已經有一些背景 Azure 搜尋] 中，您可以使用這個範例以創造的進一步的實驗，可能是擴大[搜尋頁面](search-pagination-page-layout.md)，或實作[多面向導覽](search-faceted-navigation.md)。 您也可以藉由新增計數批次處理文件，好讓使用者可以瀏覽頁面結果改善在搜尋結果頁面。

Azure 搜尋的新手嗎？ 我們建議您嘗試其他教學課程，瞭解您可以建立。 請造訪我們[的說明文件頁面](https://azure.microsoft.com/documentation/services/search/)，尋找更多資源。 您也可以檢視連結，在我們的[影片和教學課程清單](search-video-demo-tutorial-list.md)存取更多的資訊。

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
