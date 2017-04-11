<properties
    pageTitle="疑難排解 Azure 電腦學習傳統 Web 服務的 Retraining |Microsoft Azure"
    description="找出並修正常見的問題發現當您要重新模型訓練 Azure 電腦學習 Web 服務。"
    services="machine-learning"
    documentationCenter=""
    authors="VDonGlover"
   manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>疑難排解重新訓練 Azure 電腦學習傳統 Web 服務

## <a name="retraining-overview"></a>訓練概觀

部署預測實驗為計分 web 服務時的靜態模型。 可用的新資料時，或 API 的消費者用自己的資料時，必須重新培訓模型。 

傳統的 Web 服務的訓練程序完成逐步解說，請參閱[重新訓練電腦學習模型以程式設計方式](machine-learning-retrain-models-programmatically.md)。

## <a name="retraining-process"></a>訓練的程序

當您需要重新訓練的 Web 服務時，您必須新增一些額外的項目︰

* 從訓練實驗部署 Web 服務。 實驗必須具有附加的**訓練模型**模組輸出至**Web 服務輸出**模組。  

    ![將 web 服務輸出附加至訓練模型。][image1]

* 新的結束點，新增至您計分的 Web 服務。  您可以新增以程式設計方式使用 [以程式控制方式參照重新訓練電腦學習模型中的程式碼範例端點主題或透過 Azure 傳統入口網站。

訓練課程 Web 服務的 API 說明頁面的範例 C# 程式碼然後可用來重新訓練模型。 評估結果並感到滿意這些之後，您就會更新計分 web 服務使用新的端點您所新增的訓練的模型。

使用位置中的部分，您必須採取重新訓練模型的主要步驟如下︰

1.  通話訓練課程 Web 服務︰ 呼叫是以批次執行服務 (BES)，不要求回應服務 （給）。 您可以使用範例 C# 程式碼 API 說明頁面上，進行通話。 
2.  尋找*BaseLocation* *RelativeLocation*，與*SasBlobToken*的值︰ 這些值時，會傳回的列印輸出上，從您的電話轉接至的訓練課程 Web 服務。 
      ![顯示訓練範例和 BaseLocation RelativeLocation，與 SasBlobToken 值的輸出。][image6]
3.  更新新增的端點，從計分的 web 服務使用新的訓練模型︰ 使用 [以程式控制方式，重新訓練電腦學習模型中所提供的程式碼範例更新新您新增至新訓練模型計分模型訓練課程 Web 服務的結束點。

## <a name="common-obstacles"></a>常見的障礙

### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>請檢查您是否正確的修補 URL

您使用的修補 URL 必須是您新增至計分的 Web 服務的新計分端點相關聯的項目。 有數種方式，以取得更新的 URL:

**選項 1︰ 以程式設計方式**

若要取得正確的修補程式 URL:

1.  執行[AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs)範例程式碼。
2.  從 AddEndpoint 的輸出，找出*HelpLocation*值，複製 URL。

    ![HelpLocation addEndpoint 範例的輸出中。][image2]

3.  將 URL 貼入瀏覽至提供的 Web 服務說明連結的網頁瀏覽器。
4.  按一下 [**更新資源**連結，以開啟修補程式說明頁面。

**選項 2︰ 使用 Azure 傳統入口網站**

1.  [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2.  開啟電腦學習] 索引標籤。 
     ![電腦傾向] 索引標籤。][image4]
3.  按一下您的工作區名稱，然後**Web 服務**。
4.  按一下您正在使用的計分 Web 服務。 （如果您不是修改之 web 服務的預設名稱，它會在結束 [計分 Exp。]。）
5.  按一下 [**新增結束點**]。
6.  新增端點後，按一下 [結束點名稱。 然後按一下 [**更新資源**以開啟修補說明頁面。

>[AZURE.NOTE] 如果您已新增端點，而不是預測的 Web 服務訓練課程 Web 服務，當您按一下 [**更新資源**的連結時，您會收到下列錯誤︰ 很抱歉，但這項功能不受支援或不提供這項操作。 這個 Web 服務有沒有可更新的資源。 我們深感抱歉的不便並處理改善此工作流程。

![新的端點儀表板。][image3]

修補程式說明頁面包含您必須使用更新的 URL，並提供可用來呼叫該程式碼的範例。

![修補程式的 URL。][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>請檢查您要更新的正確計分端點

* 不會更新訓練課程 Web 服務︰ 計分的 Web 服務必須執行修補程式作業。
* 不會更新 Web 服務的預設端點︰ 修補程式作業必須執行的新計分 Web 服務端點您所新增的。

您可以驗證端點位於造訪 Azure 傳統入口網站以哪一種 Web 服務。 

>[AZURE.NOTE] 請確定您要新增端點預測的 Web 服務不訓練課程 Web 服務。 如果您正確部署訓練和預測的 Web 服務，您應該會看到兩個不同的 Web 服務所列。 「 [預測 exp。] 」 應該結尾預測的 Web 服務。

1.  [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2.  開啟電腦學習] 索引標籤。 
     ![電腦學習工作區 ui。][image4]
3.  選取您的工作區。
4.  按一下 [ **Web 服務**]。
5.  選取您的預測 Web 服務。
6.  確認已新增至 Web 服務的新的結束點。

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>檢查您的 web 服務位於以確保正確的區域中的工作區

1.  [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2.  從功能表選取電腦學習。
      ![電腦學習地區 ui。][image4]
3.  請確認您的工作區的位置。

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png