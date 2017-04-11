<properties
    pageTitle="載入使用 Visual Studio 小組 Services 測試您的應用程式 |Microsoft Azure"
    description="了解如何進行測試 Azure 服務布料的轉印圖樣應用程式使用 Visual Studio 小組服務。"
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>載入使用 Visual Studio 小組 Services 測試您的應用程式

本文說明如何使用 Microsoft Visual Studio 載入測試功能來強調測試應用程式。 使用 Azure 服務布料的轉印圖樣狀態服務後端和狀態服務 web 前端。 使用這裡的範例應用程式是飛機位置模擬器。 您提供飛機識別碼、 出發時間和目的地。 應用程式的後端處理要求，並在地圖上顯示的前端飛機符合準則。

下圖說明會測試您的服務布料的轉印圖樣應用程式。

![範例飛機位置應用程式的圖表][0]

## <a name="prerequisites"></a>必要條件
之前開始，您必須執行下列動作︰

- 取得 Visual Studio 小組服務帳戶。 您可以取得免費在[Visual Studio 小組服務](https://www.visualstudio.com)。
- 取得並安裝 Visual Studio 2013 或 Visual Studio 2015。 本文使用 Visual Studio 2015 企業版中，但 Visual Studio 2013 與其他版本應該同樣的工作。
- 部署您執行的環境的應用程式。 如需有關此資訊，請參閱[如何部署至遠端叢集使用 Visual Studio 應用程式](service-fabric-publish-app-remote-cluster.md)。
- 瞭解您的應用程式使用模式。 這項資訊用於模擬負載模式。
- 瞭解您負載測試的目標。 這可協助您解譯及分析負載測試結果。

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>建立並執行網頁的效能及負載測試的專案

### <a name="create-a-web-performance-and-load-test-project"></a>建立網頁的效能及負載測試專案

1. 開啟 Visual Studio 2015。 選擇 [**檔案** > **新增** > **Project**在功能表列上，開啟**新的專案**] 對話方塊。

2. 展開 [ **Visual C#**節點並選擇 [**測試** > **Web 效能與負載測試專案**。 提供專案名稱，然後選擇 [**確定**] 按鈕。

    ![[新專案] 對話方塊的螢幕擷取畫面][1]

    您應該會看到新的 Web 效能與負載測試專案總管] 中。

    ![顯示新的專案的方案總管的螢幕擷取畫面][2]

### <a name="record-a-web-performance-test"></a>錄製 web 效能測試

1. 開啟.webtest 專案。

2. 選擇 [**新增錄製**] 圖示可在瀏覽器中啟動錄製工作階段。

    ![在瀏覽器中的 [新增錄製] 圖示的螢幕擷取畫面][3]

    ![在瀏覽器中的 [記錄] 按鈕的螢幕擷取畫面][4]

3. 瀏覽至服務布料的轉印圖樣應用程式。 [錄製] 面板應該會顯示的 web 要求。

    ![在 [錄製] 面板中的 web 要求的螢幕擷取畫面][5]

4. 執行您預期使用者執行的動作的順序。 這些動作會作為產生的負載圖樣的線條。

5. 當您完成時，請選擇 [**停止**] 按鈕，若要停止錄製]。

    ![[停止] 按鈕的螢幕擷取畫面][6]

    在 Visual Studio.webtest 專案應該所擷取的一系列的要求。 動態參數會自動被取代。 此時，您可以刪除任何不屬於您測試的案例的額外、 重複的相依性要求。

6. 儲存專案，然後選擇 [**執行測試**] 命令，在本機上執行 web 效能測試，以確保一切運作無誤。

    ![執行測試] 命令的螢幕擷取畫面][7]

### <a name="parameterize-the-web-performance-test"></a>參數化 web 效能測試

您可以將其轉換成編碼的 web 效能測試，然後編輯 [程式碼參數化 web 效能測試。 或者，您可以結合 web 效能測試至資料清單，好讓測試逐一資料。 如需有關如何將網站效能測試轉換為測試的詳細資訊，請參閱[產生及執行編碼的 web 效能測試](https://msdn.microsoft.com/library/ms182552.aspx)。 如需如何將資料繫結至 web 效能測試的資訊，請參閱[新增資料來源，以 web 效能測試](https://msdn.microsoft.com/library/ms243142.aspx)。

此範例中，我們將會轉換 web 效能測試為測試，讓您可以取代產生的 GUID 飛機識別碼，並新增更多的要求班機傳送到不同的位置。

### <a name="create-a-load-test-project"></a>建立載入測試專案

載入測試專案被由 web 效能測試和單位測試，以及其他特定的負載測試設定所述的一或多個案例。 下列步驟會顯示如何建立載入測試專案︰

1. 網頁的效能及負載測試專案的快顯功能表，選擇 [**新增** > **負載測試**。 **載入測試**在精靈中，選擇 [**下一步**] 按鈕設定測試設定。

2. 在 [**載入模式**] 區段中，選擇您要常數負載或步驟載入，第一句是一些使用者，並增加一段時間的使用者。

    如果您已做好完善的評估使用者負載量的並想要查看目前系統的執行方式，請選擇 [**固定載入**]。 如果您的目標是瞭解系統一致的方式執行各種載入底下，選擇 [**步驟載入**]。

3. 在 [**測試混合**] 區段中，選擇 [**新增**] 按鈕，然後選取您想要包含在載入測試，測試。 若要指定針對每個測試執行的測試總計的百分比，您可以使用 [**通訊**] 欄。

4. 在 [**執行設定**] 區段中，指定載入測試工期。

    >[AZURE.NOTE] 只有當您執行本機使用 Visual Studio 負載測試時，才使用**測試高次數**] 選項。

5. 在 [**執行設定**的 [**位置**] 區段中，指定的位置載入測試要求產生的位置。 精靈可能會提示您登入您的小組服務帳戶。 登入，然後選擇 [地理位置。 完成後，選擇 [**完成**] 按鈕。

6. 建立負載測試後，開啟.loadtest 專案，並選擇 [執行設定，例如**執行設定**目前 > **執行 Settings1 [作用中]**。 隨即在 [**屬性**] 視窗中開啟執行的設定。

7. **執行設定**[內容] 視窗的 [**結果**] 區段中，在**預存時間詳細資料儲存區**設定應該有**無**做為預設值。 此值變更為 [**所有個別的詳細資料**，以取得詳細資訊，在載入測試結果。 如需有關如何連線至 Visual Studio 小組服務及執行負載測試，請參閱[載入測試](https://www.visualstudio.com/load-testing.aspx)。

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>使用 Visual Studio 小組 Services 執行負載測試

選擇 [**執行載入測試**的命令，以開始執行測試。

![執行負載測試] 命令的螢幕擷取畫面][8]

## <a name="view-and-analyze-the-load-test-results"></a>檢視及分析負載測試結果

為載入測試進行時，效能資訊開始一直計算為止。 您應該會看到類似下圖。

![效能圖形負載測試結果的螢幕擷取畫面][9]

1. 選擇頁面頂端附近的 [**下載報告**] 連結。 下載報表後，選擇 [**檢視報表**] 按鈕。

    在 [**圖表**] 索引標籤上，您可以看到各種效能計數器的圖表。 在 [**摘要**] 索引標籤上的整體的測試結果會出現。 [**表格**] 索引標籤會顯示傳遞和失敗載入測試總數。

2. 選擇 [**測試**的數字連結 > **失敗**及**錯誤** > **計算**欄，請參閱錯誤詳細資料。

    [**詳細資料**] 索引標籤會顯示失敗要求的虛擬使用者和測試案例資訊。 此資料可如果負載測試包含多個案例。

如需詳細資訊，檢視負載測試結果，請參閱[分析負載測試結果圖形的檢視中的 [載入測試分析](https://www.visualstudio.com/load-testing.aspx)。

## <a name="automate-your-load-test"></a>自動化負載測試

Visual Studio 小組服務載入測試提供 Api 以協助您管理負載測試和分析小組服務帳戶的結果。 如需詳細資訊，請參閱[雲端載入測試 Rest Api](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) 。

## <a name="next-steps"></a>後續步驟
- [監視及診斷在本機電腦開發設定服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
