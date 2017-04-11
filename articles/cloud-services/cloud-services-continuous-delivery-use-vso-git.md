<properties
    pageTitle="連續傳遞給與 Azure 中的 Visual Studio 小組服務 |Microsoft Azure"
    description="瞭解如何設定 Visual Studio 小組服務小組專案使用給自動建立並部署至 Azure 應用程式服務或雲端服務的 Web 應用程式功能。"
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

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>使用 Visual Studio 小組服務和給 Azure 連續傳遞

您可以使用 Visual Studio 小組服務小組專案至裝載您的程式碼，就可以給存放庫和自動建立和 Azure web 應用程式部署或雲端服務，每當您推認可到儲存機制。

您需要 Visual Studio 2013 和 Azure SDK 安裝。 如果您還沒有 Visual Studio 2013，請選擇 [**開始使用免費**的連結，位於[www.visualstudio.com](http://www.visualstudio.com)。 從 [[以下](http://go.microsoft.com/fwlink/?LinkId=239540)安裝 Azure SDK。


> [AZURE.NOTE]您必須完成本教學課程 Visual Studio 小組服務帳戶︰ 您可以[開啟 Visual Studio 小組服務帳戶的可用](http://go.microsoft.com/fwlink/p/?LinkId=512979)。

若要設定自動建立並部署至 Azure 使用 Visual Studio 小組 Services 雲端服務，請遵循下列步驟。

## <a name="1-create-a-git-repository"></a>1︰ 建立給存放庫

1. 如果您還沒有 Visual Studio 小組服務帳戶，就能取得[以下](http://go.microsoft.com/fwlink/?LinkId=397665)。 當您建立小組專案時，請選擇給為來源控制系統。 請遵循指示連線至您的小組專案的 Visual Studio。

2. 在**小組檔案總管**] 中，選擇 [**複製此存放庫**的連結]。

    ![][3]

3. 指定的本機複本的位置，然後選擇 [**複製**] 按鈕。

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2︰ 建立專案，並確認其到儲存機制

1. 在**小組檔案總管**] 中，在 [**方案**] 區段中，選擇 [**新增**連結至本機存放庫中建立新專案]。

    ![][4]

2. 您可以執行這個逐步解說中的步驟，來部署 web app 或雲端服務 （Azure 應用程式）。 建立新的 Azure 雲端服務專案或新 ASP.NET MVC 專案。 請確定專案目標.NET Framework 4 或更新版本。 如果您正在建立雲端服務專案，新增 ASP.NET MVC web 角色和工作者角色。
如果您想要建立 web 應用程式，請選擇**ASP.NET Web 應用程式**專案範本，然後選擇 [ **MVC**。 如需詳細資訊，請參閱[建立 ASP.NET web 應用程式中 Azure 應用程式服務](../app-service-web/web-sites-dotnet-get-started.md)。

3. 開啟快顯功能表的方案，然後選擇 [**認可**。

    ![][7]

4. 如果這是您已使用給 Visual Studio 小組服務中的第一次，必須提供給在識別您自己的部分資訊。 在 [**小組總管]**的 [**擱置中變更**] 區域中，輸入您的使用者名稱和電子郵件地址。 輸入註解的認可]，然後選擇 [**認可**] 按鈕。

    ![][8]

5. 請注意，來包含或排除特定變更，當您核取的選項。 如果排除您要的變更，請選擇 [**包含所有**]。

6. 您現在認可變更您在本機複本存放庫。 接下來，將這些變更與伺服器同步處理透過選擇**同步處理**。

## <a name="3-connect-the-project-to-azure"></a>3︰ 將專案連接至 Azure

1. 有給存放庫 Visual Studio 小組服務中的部分來源程式碼之後，您準備好將您就可以給存放庫連線到 Azure。  在[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)中，選取 [雲端服務或 web 應用程式，或建立新的項目，選擇 [+] 圖示，在左下方，並選擇 [**雲端服務**或**Web 應用程式**，然後**快速建立**。

    ![][9]

3. 如需雲端服務，選擇 [**設定 Visual Studio 小組 services 發佈**連結。 Web 應用程式中，選擇 [**來源控制部署設定**的連結]。

    ![][10]

2. 在精靈中，在文字方塊中輸入您的 Visual Studio 小組服務帳戶的名稱，然後選擇 [**立即授權**] 連結。 可能會要求您登入。

    ![][11]

3. 在**連線要求**快顯對話方塊中，選擇 [**接受**]，即可授權 Azure Visual Studio 小組 Services 中設定您的小組專案]。

    ![][12]

4. 授權成功之後，您會看到包含 Visual Studio 小組服務小組專案的下拉式清單。  選取您在先前的步驟，建立小組專案的名稱，然後選擇精靈] 核取記號按鈕。

    ![][13]

    下次您推入您的存放庫的認可 Visual Studio 小組服務會建立並部署至 Azure 的專案。

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4︰ 觸發重建並重新部署您的專案

1. 在 Visual Studio 中，開啟檔案，然後進行變更。 例如，變更檔案`_Layout.cshtml`檢視下\\MVC 網頁角色中的共用資料夾。

    ![][17]

2. 編輯網站的頁尾文字，並儲存檔案。

    ![][18]

3. 在**方案總管]**中，開啟方案節點、 專案節點或您已變更，該檔案的快顯功能表，然後選擇**認可**。

4. 輸入註解，然後選擇 [**確認**]。

    ![][20]

5. 選擇 [**同步處理**] 連結。

    ![][38]

6. 選擇發送您認可到儲存機制 Visual Studio 小組服務中的 [**推入**] 連結。 （您也可以使用 [**同步處理**] 按鈕，將您的認可複製到儲存機制。 差異是**同步處理**也從存放庫擷取最新變更）。

    ![][39]

7. 選擇 [**首頁**] 按鈕，若要返回**小組 Explorer**的 [首頁] 頁面。

    ![][21]

8. 選擇 [若要檢視進行中的 [**建立**]。

    ![][22]

    **小組 Explorer**有顯示的已核取中的觸發建立。

    ![][23]

9. 若要建立隨著檢視詳細的記錄，請按兩下進行中建立的名稱。

10. 進行中建立時，看看組建定義您要連結至 Azure 使用精靈時所建立。  開啟組建定義的快顯功能表，然後選擇 [**編輯建立的定義**。

    ![][25]

11. 在 [**觸發程序**] 索引標籤中，您會看到建立定義為建立於每個核取中，設定預設。 （雲端服務的 Visual Studio 小組服務建置並部署主分支暫存環境自動。 您仍然必須執行手動步驟来部署至此即時的網站。 沒有臨時環境之 web 應用程式，它會直接至 live 網站部署主分支。

    ![][26]

1. 在 [**程序**] 索引標籤中，您可以看到部署環境設定為雲端服務或 web 應用程式的名稱。

    ![][27]

1. 如果您想要不同於預設值的值，請指定的屬性值。 Azure 發佈的內容是在 [**部署**] 區段中，您可能也需要設定 MSBuild 參數。 例如，在雲端服務的專案，若要指定 「 雲端 」，以外的服務設定 MSbuild 將參數設定`/p:TargetProfile=[YourProfile]` *[YourProfile]*符合的名稱，例如 ServiceConfiguration 服務設定檔的位置。*YourProfile*.cscfg。

    下表會顯示 [**部署**] 區段中可用的屬性︰

  	|屬性|預設值|
  	|---|---|
  	|允許不受信任的憑證|如果是 false，必須簽署 SSL 憑證根授權單位。|
  	|允許升級|可讓更新現有的部署，而不是建立一個新的部署。 保留的 IP 位址。|
  	|不要刪除|如果為 true，不會覆寫現有不相關的部署 （允許升級）。|
  	|部署設定的路徑|.Pubxml 檔案 web app，相對於 repo 的根資料夾路徑。 忽略雲端服務。|
  	|Sharepoint 部署環境|相同的服務名稱。|
  	|Azure 部署環境|Web 應用程式或雲端服務名稱。|

1. 這次，以建立您應該順利完成。

    ![][28]

1. 如果您按兩下建立名稱時，Visual Studio 顯示**建立摘要**，包括任何的測試結果相關聯的單位測試專案。

    ![][29]

1. 在[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)，您可以在 [**部署**] 索引標籤上檢視相關聯的部署執行環境為選取狀態時。

    ![][30]

1.  瀏覽至您網站的 URL。 Web 應用程式，只在入口網站中選擇 [**瀏覽**] 按鈕。 雲端服務，顯示臨時環境的**儀表板**頁面的 [**快速瀏覽**] 區段中選擇 [URL]。

    從雲端服務的連續整合部署預設發佈至臨時環境。 您可以變更此設定為 [**生產**的**替代雲端服務環境**屬性。 以下是您的網站 URL 位於雲端服務的儀表板頁面的位置。

    ![][31]

    新的瀏覽器索引標籤隨即會開啟，顯示您執行的網站。

    ![][32]

1.  如果您對專案進行其他變更，您觸發程序更多建立，並累積多個部署。 最新的項目會標示為 [作用。

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5︰ 重新部署舊版組建

此為選用步驟。 在 Azure 傳統入口網站中選擇較早的部署，選擇 [倒帶舊版的存回您網站的 [**重新部署**。 請注意，這會觸發 TFS 中的新建立您的部署記錄中建立新的項目。

![][34]

## <a name="6-change-the-production-deployment"></a>6︰ 變更實際部署

當您準備好時，您可以將生產環境的臨時環境升級選擇 Azure 傳統入口網站中的 [**交換**。 生產，升級的最新的臨時環境，之前生產環境中，如果有的話，會變成臨時環境。 作用中的部署可能會有所不同生產及臨時環境，但部署歷程記錄的最近建置是相同環境。

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7︰ 從工作分支部署。

當您使用給時，您通常會使用分支中進行變更，並整合主分支，當您開發達到完成的狀態。 在專案的開發階段，您需要建立並部署工作分支至 Azure。

1. 在**小組檔案總管**] 中，選擇 [**首頁**] 按鈕，然後選擇 [**分支**] 按鈕。

    ![][40]

2. 選擇**新的分支**連結]。

    ![][41]

3. 輸入分支，例如 「 正在，」 的名稱，然後選擇 [**建立子目錄**。 這樣會建立新的本機分支。

    ![][42]

4. 發佈分支。 在 [**解除發佈的分支**，選擇分支名稱，並選擇 [**發佈**。

    ![][44]

6. 根據預設，只有變更母片的分支觸發程序連續的建立。 設定工作分支的連續建立，請在**小組檔案總管**中，選擇 [**建立**] 頁面，並選擇 [**編輯建立的定義**。

7. 開啟 [**來源設定**] 索引標籤。 下**監視分支連續整合和建立**，選擇 [**按一下這裡以新增列**]。

    ![][47]

8. 指定您建立了，例如參照/家庭主事者/工作的分支。

    ![][48]

9. 程式碼中進行變更，開啟的快顯功能表變更的檔案，然後選擇**認可**。

    ![][43]

10. 選擇**保存其中的認可**連結，然後選擇**同步處理**] 按鈕或 [**推入**] 連結以將變更複製到工作分支 Visual Studio 小組服務中的複製]。

    ![][45]

11. 瀏覽至 [**建立**] 檢視並尋找只是您使用的是觸發建立工作分支。

## <a name="next-steps"></a>後續步驟

若要瞭解更多秘訣給使用 Visual Studio 小組服務，請參閱[開發及共用您的程式碼中使用 Visual Studio 給](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)和使用不受發佈至 Azure Visual Studio 小組服務給存放庫的相關資訊，請參閱[Azure 應用程式服務的連續部署](../app-service-web/app-service-continuous-deployment.md)。 如需有關 Visual Studio 小組服務的詳細資訊，請參閱[Visual Studio 小組服務](http://go.microsoft.com/fwlink/?LinkId=253861)。

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
