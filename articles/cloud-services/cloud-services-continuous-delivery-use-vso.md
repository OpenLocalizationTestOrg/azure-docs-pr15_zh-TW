<properties
    pageTitle="使用 Visual Studio 小組服務 Azure 中連續傳遞 |Microsoft Azure"
    description="瞭解如何設定 Visual Studio 小組服務小組專案會自動建立並部署至 Azure 應用程式服務或雲端服務的 Web 應用程式功能。"
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>連續傳遞至 Azure 使用 Visual Studio 小組服務

您可以設定 Visual Studio 小組服務小組專案會自動建立和 Azure web 應用程式部署或雲端服務。  （如需如何設定連續的建立並部署使用*內部部署*小組 Foundation 伺服器的系統資訊，請參閱[Azure 中的雲端服務的連續傳遞](cloud-services-dotnet-continuous-delivery.md)）。

本教學課程假設您有 Visual Studio 2013 和 Azure SDK 安裝。 如果您還沒有 Visual Studio 2013，請選擇 [**開始使用免費**的連結，位於[www.visualstudio.com](http://www.visualstudio.com)。 從 [[以下](http://go.microsoft.com/fwlink/?LinkId=239540)安裝 Azure SDK。

> [AZURE.NOTE]您必須完成本教學課程 Visual Studio 小組服務帳戶︰ 您可以[開啟 Visual Studio 小組服務帳戶的可用](http://go.microsoft.com/fwlink/p/?LinkId=512979)。

若要設定自動建立並部署至 Azure 使用 Visual Studio 小組 Services 雲端服務，請遵循下列步驟。

## <a name="1-create-a-team-project"></a>1︰ 建立專案小組

依照指示進行[以下](http://go.microsoft.com/fwlink/?LinkId=512980)建立您的小組專案，並將其連結至 Visual Studio。 此逐步解說假設您使用的小組 Foundation 版本控制項 (TFVC) 您來源控制項的解決方案。 如果您想要使用的版本控制的給，請參閱[此逐步解說給版本](http://go.microsoft.com/fwlink/p/?LinkId=397358)。

## <a name="2-check-in-a-project-to-source-control"></a>2︰ 存回專案，來源控制

1. 在 Visual Studio 中，開啟您想要部署的方案或建立新的項目。
您可以執行這個逐步解說中的步驟，來部署 web app 或雲端服務 （Azure 應用程式）。
如果您想要建立新的方案，建立新的 Azure 雲端服務專案或新 ASP.NET MVC 專案。 請確定專案目標.NET Framework 4 或 4.5，並如果您正在建立雲端服務專案，新增 ASP.NET MVC web 角色和工作者角色，，然後選擇 [web 角色的網際網路應用程式。 出現提示時，請選擇 [**網際網路應用程式**]。
如果您想要建立 web 應用程式，請選擇 ASP.NET Web 應用程式專案範本，然後選擇 [MVC。 請參閱[建立 ASP.NET web 應用程式中 Azure 應用程式服務](../app-service-web/web-sites-dotnet-get-started.md)。

    > [AZURE.NOTE] Visual Studio 小組服務只支援 SONT 部署 Visual Studio Web 應用程式的這一次。 網站專案的範圍之外。

1. 開啟操作功能表的方案，並選擇 [**新增至來源控制項的解決方案**。

    ![][5]

1. 接受或變更預設值，然後選擇**[確定**] 按鈕。 程序完成之後，出現在**方案總管**中的來源控制圖示。

    ![][6]

1. 開啟快顯功能表的方案，並選擇**回**。

    ![][7]

1. 在 [**小組總管]**的 [**擱置中變更**] 區域中，輸入的存回註解並選擇存**回**] 按鈕。

    ![][8]

    請注意，來包含或排除特定變更，當您核取的選項。 如果排除所需的變更，請選擇 [**所有包含**的連結]。

    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3︰ 將專案連接至 Azure

1. 既然您與小組服務小組專案來源中的部分程式碼中，您準備好您的小組專案連接至 Azure。  在[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)中，選取 [雲端服務或 web 應用程式，或選擇 [建立新**+**左下角，選擇**雲端服務**或**Web 應用程式**，然後**快速建立**的圖示。 選擇 [**設定與 Visual Studio 小組服務發佈**的連結。

    ![][10]

1. 在精靈中，在文字方塊中輸入您的 Visual Studio 小組服務帳戶的名稱，然後按一下 [**立即授權**] 連結。 可能會要求您登入。

    ![][11]

1. 在**連線要求**快顯對話方塊中，選擇 [**接受**] 按鈕，即可授權 Azure 與小組 Services 中設定您的小組專案。

    ![][12]

1. 當授權成功時，您會看到下拉式清單，其中包含 Visual Studio 小組服務小組專案中的清單。 選擇您在先前的步驟，建立小組專案的名稱，然後選擇 [精靈的核取記號] 按鈕。

    ![][13]

1. 連結專案後，您會看到一些檢查變更 Visual Studio 小組服務小組專案中的指示。  在您下一步存回，Visual Studio 小組服務會建立並部署至 Azure 的專案。  請嘗試現在，按一下 [**從 Visual Studio 另存**連結]，然後**啟動 Visual Studio**連結 （或在入口網站的畫面底部的 [對等**Visual Studio** ] 按鈕）。

    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4︰ 觸發重建並重新部署您的專案

1. 在 Visual Studio**小組檔案總管**] 中，選擇 [**來源控制總管]**中的 [連結]。

    ![][15]

1. 瀏覽至您的方案檔案，並將其開啟。

    ![][16]

1. 在**方案總管**中開啟檔案，然後進行變更。 例如，變更檔案`_Layout.cshtml`檢視下\\MVC 網頁角色中的共用資料夾。

    ![][17]

1. 編輯網站標誌，然後選擇 [ **Ctrl + S**儲存檔案。

    ![][18]

1. 在**小組檔案總管**] 中，選擇 [**擱置中變更**連結]。

    ![][19]

1. 輸入註解，然後選擇 [存**回**] 按鈕。

    ![][20]

1. 選擇 [**首頁**] 按鈕，若要返回**小組 Explorer**的 [首頁] 頁面。

    ![][21]

1. 選擇 [若要檢視進行中的 [**建置**] 連結。

    ![][22]

    **小組 Explorer**有顯示的已核取中的觸發建立。

    ![][23]

1. 若要檢視詳細的記錄建立隨著進行中的建立的名稱上按兩下。

    ![][24]

1. 進行中建立時，看看組建定義您連結至 Azure TFS 使用精靈時所建立。  開啟組建定義的快顯功能表，然後選擇 [**編輯建立的定義**。

    ![][25]

    在 [**觸發程序**] 索引標籤中，您會看到建立定義為建立於每個核取中設定預設。

    ![][26]

    在 [**程序**] 索引標籤中，您可以看到部署環境設定為雲端服務或 web 應用程式的名稱。 如果您正在使用 web 應用程式，您看到的內容會不同於此處所示。

    ![][27]

1. 如果您想要不同於預設值的值，請指定的屬性值。 Azure 發佈的內容是在 [**部署**] 區段。

    下表會顯示 [**部署**] 區段中可用的屬性︰

  	|屬性|預設值|
  	|---|---|
  	|允許不受信任的憑證|如果是 false，必須簽署 SSL 憑證根授權單位。|
  	|允許升級|可讓更新現有的部署，而不是建立一個新的部署。 保留的 IP 位址。|
  	|不要刪除|如果為 true，不會覆寫現有不相關的部署 （允許升級）。|
  	|部署設定的路徑|.Pubxml 檔案 web app，相對於 repo 的根資料夾路徑。 忽略雲端服務。|
  	|Sharepoint 部署環境|相同的服務名稱。|
  	|Azure 部署環境|Web 應用程式或雲端服務名稱。|

1. 如果您使用多個服務設定 （.cscfg 檔案），您可以指定所要的服務設定**建立，[進階]，[MSBuild 引數**設定。 例如，若要使用 ServiceConfiguration.Test.cscfg，設定 MSBuild 引數線條選項`/p:TargetProfile=Test`。

    ![][38]

    這次，以建立您應該順利完成。

    ![][28]

1. 如果您按兩下建立名稱時，Visual Studio 顯示**建立摘要**，包括任何的測試結果相關聯的單位測試專案。

    ![][29]

1. 在[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)，您可以在 [**部署**] 索引標籤上檢視相關聯的部署執行環境為選取狀態時。

    ![][30]

1.  瀏覽至您網站的 URL。 在 web 應用程式中，只要按一下命令列上的 [**瀏覽**] 按鈕。 雲端服務，會顯示在雲端服務的臨時環境的**儀表板**頁面的 [**快速瀏覽**] 區段中選擇 [URL]。 從雲端服務的連續整合部署預設發佈至臨時環境。 您可以變更此設定為 [**生產**的**替代雲端服務環境**屬性。 此網站的 URL 會位於雲端服務的儀表板頁面的螢幕擷取畫面會顯示。

    ![][31]

    新的瀏覽器索引標籤隨即會開啟，顯示您執行的網站。

    ![][32]

    如需雲端服務，如果您的專案中進行其他變更，您觸發程序更組建和累積多個部署。 最新標示為 [作用。

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5︰ 重新部署舊版組建

此步驟會套用至雲端服務，並為選用步驟。 在 Azure 傳統入口網站中選擇較早的部署，然後選擇 [**部署**] 按鈕，將其倒帶您網站，以舊版的存回。  請注意，這會觸發 TFS 中的新建立您的部署記錄中建立新的項目。

![][34]

## <a name="6-change-the-production-deployment"></a>6︰ 變更實際部署

此步驟只適用於雲端服務，web 應用程式。 當您準備好時，您可以將生產環境的臨時環境升級 Azure 傳統入口網站中，選擇 [**交換**] 按鈕。 生產，升級的最新的臨時環境，之前生產環境中，如果有的話，會變成臨時環境。 作用中的部署可能會有所不同生產及臨時環境，但部署歷程記錄的最近建置是相同環境。

![][35]

## <a name="7-run-unit-tests"></a>7︰ 執行單元測試

此步驟只適用於 web 應用程式，請不要雲端服務。 若要放置品質閘道在您的部署，您可以執行單位測試，如果失敗，您可以停止部署。

1.  在 Visual Studio 中，新增單位測試專案。

    ![][39]

1.  新增至您想要測試的專案的專案參考。

    ![][40]

1.  新增一些單位測試。 若要開始，請嘗試會永遠將傳遞的 dummy 測試。

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  編輯建立定義，選擇 [**處理程序**] 索引標籤，然後展開**測試**節點。

1.  設定**測試失敗建立**為 True。 這表示不會發生的部署，除非通過測試。

    ![][41]

1.  佇列中新的建立。

    ![][42]

    ![][43]

1. 建立會繼續，同時查看進度。

    ![][44]

    ![][45]

1. 建立完成時，請檢查測試結果。

    ![][46]

    ![][47]

1.  請嘗試建立的測試，將會失敗。 新增新的測試來複製第一個、 重新命名，及註解的程式碼表示 NotImplementedException 預期的例外狀況。

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. 存回的佇列中新建立的變更。

    ![][48]

1. 檢視測試結果，請參閱關於失敗的詳細資料。

    ![][49]

    ![][50]

## <a name="next-steps"></a>後續步驟
如需關於的測試 Visual Studio 小組服務中的單位，請參閱[執行在您建立的單位測試](http://go.microsoft.com/fwlink/p/?LinkId=510474)。 如果您使用的給，請參閱[共用您的程式碼給](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)和[連續部署至 Azure 應用程式服務。](../app-service-web/app-service-continuous-deployment.md)  如需有關 Visual Studio 小組服務的詳細資訊，請參閱[Visual Studio 小組服務](http://go.microsoft.com/fwlink/?LinkId=253861)。

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
