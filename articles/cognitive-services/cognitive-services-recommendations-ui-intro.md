<properties
    pageTitle="建置模型與 Recommnendations UI |Microsoft Azure"
    description="Azure 電腦學習建議-建置與建議 UI 模型"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="luisca"/>

# <a name="building-a-model-with-the-recommendations-ui"></a>建立具有建議使用者介面的模型

這份文件是逐步指示。 我們的目標是逐步引導您完成所需訓練使用[建議使用者介面](https://recommendations-portal.azurewebsites.net/)的模型的步驟。
通過練習可讓您瞭解前請先將其以程式設計方式建立模型的程序。 它也有助於您熟悉的 UI，開始使用服務時，這是方便。

此練習需要大約 30 分鐘。

<a name="Step1"></a>
## <a name="step-1---sign-in-to-the-recommendations-ui"></a>步驟 1-登入的建議使用者介面 ##

1. 如果您有不這麼做，您需要[註冊](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)新[建議 API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api)訂閱。 您可以註冊**Azure**上的服務，即可在[http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)並登入使用 Azure 帳戶。 *任務 1*的[快速入門指南 》](cognitive-services-recommendations-quick-start.md)中所提供的註冊程序的詳細的指示 

1. 一旦取得**金鑰**建議 API 訂閱，請移至[建議使用者介面](https://recommendations-portal.azurewebsites.net/)。 

1. 在 [**帳戶金鑰**] 欄位中，輸入您的金鑰登入入口網站，然後按一下 [**登入**] 按鈕。

    ![使用者介面的建議︰ 登入] 對話方塊。][reco_signin]


<a name="Step2"></a>
## <a name="step-2---lets-gather-some-training-data"></a>步驟 2-現在就讓我們收集一些訓練資料 ##

引擎您可以建立建置之前，必須兩部分資訊︰ 目錄檔案及一組使用檔案。 

目錄檔案包含您為客戶提供項目的相關資訊。 使用檔案包含如何使用這些項目，或從您的業務交易的相關資訊。

通常您想要查詢儲存資料庫的這項資訊。 現在，我們有一些範例資料提供給您，以便您可以瞭解如何使用建議 API。

您可以從[http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData)下載資料。 複製並解壓縮**Books.Zip**檔案到您的本機電腦上的資料夾。 例如， **c:\data**。

[若要訓練模型收集資料](cognitive-services-recommendations-collecting-data.md)，請參閱找結構描述檔案目錄及使用方式的詳細的資訊。
 
此練習中，我們將使用一個小檔案，讓您不必非常訓練太久，請等候。 如果您想要嘗試更實際的檔案，我們也有放**MsStoreData.zip**含有範例交易從 Microsoft 市集在[同一個位置](http://aka.ms/RecoSampleData)。

<a name="Step3"></a>
## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>步驟 3-建立專案並上傳目錄及使用方式的資料 ##

在登入[建議使用者介面](https://recommendations-portal.azurewebsites.net/)，您會看到 [專案] 頁面。 如果您先前已建立任何專案，您應該會在此看到。
專案 （也稱為*模型*中的 API 參照） 是容器，資料目錄及使用方式。 您可以建立多個*建置*專案內。 我們會引導您執行中的下一個步驟的程序。

1. 若要建立新專案，請在 （像是會使用 「 MyFirstModel 」） 的 [文字] 方塊中輸入名稱，按一下 [**新增專案**。
 
    ![使用者介面的建議: [專案] 頁面。][reco_projects]

1. 專案會建立後，按一下 [**新增類別目錄檔案**區段上的 [**瀏覽至檔案**] 按鈕。 上傳您在步驟 2 中適合目錄。 如果您儲存在*c:\data*，必須先瀏覽至該資料夾。

    ![使用者介面的建議︰ 將資料加入至專案。][reco_firstmodel]

1. 目錄上傳後，請按一下 [**新增使用檔**區段的 [**瀏覽至檔案**] 按鈕。 新增 usage_large.txt 檔案。

> **如果我有使用情況資料的大型檔案？**
>
> 只使用檔小於 200 MB 可以上傳。 然而，系統可以保留 2 GB 價值交易資料，如有需要，您可以上傳多個檔案。
> 您可能不需要的多產生良好的模型的資料，不只是資料的重要的資訊，但資料的品質的大小。 通常會看到 [使用情況資料其中大多數的交易都只在少數幾個項目，且沒有 「 稍微訊號 」 的其他項目。

<a name="Step4"></a>
## <a name="step-4---lets-do-some-training"></a>步驟 4-讓我們來執行某些訓練 ！ ##

既然您已上傳目錄和使用資料，我們已準備好訓練引擎，讓它可以瞭解從我們的資料的模式。

1.  按一下 [**建立新**的按鈕。

1.  選取 [**建議**] 作為建立類型。 請注意，我們支援排名建立而 FBT （經常購買一起） 建立以及類型。

    ![使用者介面的建議︰ 建立] 對話方塊。][reco_build_dialog.png]


    FBT 建立可讓您找出產品通常購買/中使用相同的交易的模式。
    排名建立用來識別感興趣的功能。 
    我們不會移非常深入 FBT 或排名會建立在此研討會中，但如果您想要您應該取出[建立類型與模型品質文件的頁面](cognitive-services-recommendations-buildtypes.md)。

1. 按一下 [**建立**] 按鈕。 如果您使用的活頁簿資料，建立程序會引導約 5 分鐘的時間。 花更長的時間在大型資料集。

<a name="Step5"></a>
## <a name="step-5---lets-find-out-what-the-machine-learned"></a>步驟 5-讓我們來找出電腦所學的內容 ！ ##

一旦您建立完成後，您會發現在 [建置] 清單中的新建立。 此建立可以查詢的項目與使用者的建議。

1. 一旦您建立完成後，按一下 [**成績**]。 這個選項可讓您播放含有此模型，並查看建議使用什麼項目。

    ![使用者介面的建議︰ 成績] 按鈕][reco_score_button]

1. 選取的項目，以查看建議的項目會傳回哪些項目。 請注意，是否沒有足夠的交易預測的建議特定項目，系統將不會傳回該項目的任何建議。  如果基於某種原因，您會有會傳回不建議的項目，請嘗試計分其他項目。

<a name="Step6"></a>
## <a name="step-6---next-steps"></a>步驟 6-接下來的步驟 ##
恭喜您 ！ 您有訓練模型，而且再向模型中的建議。  建議使用者介面是實用的工具，可讓您查看專案的狀態，並在建置。 

現在您擁有一個模型，您可能要瞭解如何以程式設計方式執行上述的所有步驟。 若要執行，瞭解如何以程式設計方式呼叫 API，邀請您檢查[建議 API 參考](http://go.microsoft.com/fwlink/?LinkId=759348)，及下載[建議範例應用程式](http://go.microsoft.com/fwlink/?LinkID=759344)。


[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png
