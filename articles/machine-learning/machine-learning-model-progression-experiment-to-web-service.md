<properties
    pageTitle="實驗電腦學習模型前進到 operationalized 的 Web 服務如何 |Microsoft Azure"
    description="如何從開發您 Azure 電腦學習模型與進度嘗試 operationalized 的 Web 服務的概觀。"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>如何電腦學習模型複寫實驗 operationalized 的 Web 服務

Azure 電腦學習 Studio 提供可讓您開發、 執行、 測試和重複***嘗試***代表預測分析模型互動式畫布。 有各式各樣的模組可用，可以︰

* 將您的實驗的輸入的資料
* 管理資料
* 訓練使用電腦學習演算法的模型
* 分數模型
* 評估結果
* 輸出的最後一個值

一旦您滿意實驗時，您可以將其部署為***傳統 Azure 電腦學習 Web 服務***或***新 Azure 電腦學習 Web 服務***，讓使用者可以將其傳送新的資料和接收後的結果。

在本文中，我們會為概略瞭解如何從開發您電腦學習模型與進度嘗試 operationalized 的 Web 服務的技巧。

>[AZURE.NOTE] 開發和部署電腦學習模型的其他方法，但本文著重於您如何使用電腦學習 Studio。 有關如何使用 R 建立傳統的預測 Web 服務，請參閱部落格文章[建立及部署預測 Web 應用程式使用 RStudio 和 Azure 毫升](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)的討論。

雖然 Azure 電腦學習 Studio 設計用來協助您開發和部署*預測分析模型*，就可以使用 Studio 開發實驗並不包含預測分析模型。 例如實驗可能只輸入資料、 管理，然後再輸出結果。 就像預測分析項實驗中，您可以部署為 Web 服務，這個非預測實驗，但這是一個簡單的程序因為實驗未訓練或計分電腦學習模型。 雖然您不在這種方式使用 Studio 一般，我們會將其加入討論區中，讓我們可以授與 Studio 的運作方式的完整說明。

## <a name="developing-and-deploying-a-predictive-web-service"></a>開發及部署預測 Web 服務

以下是常見的解決方案遵循開發，並將其使用電腦學習 Studio 中部署階段︰

![部署流程](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*圖 1-一般預測分析模型的階段*

### <a name="the-training-experiment"></a>訓練課程實驗

***訓練課程嘗試***是開發 Web 服務電腦學習 Studio 中的初始階段。 訓練課程實驗的目的是讓您開發、 測試，重複，然後最後訓練電腦，學習模型的位置。 您可以更訓練多個模型同時您搜尋的最佳解決方案，但是一旦完成時嘗試您會選取單一訓練模型，並排除實驗的其餘部分。 如需開發預測的分析的範例嘗試，請參閱[開發信用卡風險評定中 Azure 電腦學習的預測狀況分析解決方案](machine-learning-walkthrough-develop-predictive-solution.md)。

### <a name="the-predictive-experiment"></a>預測實驗

訓練課程實驗中有訓練的模型，按一下 [**設定 Web 服務**並選取電腦學習 Studio 進行轉換***預測實驗***訓練實驗的程序中的**預測 Web 服務**。 預測實驗的目的是使用訓練的模型分數使用的最後會變得 operationalized Azure Web 服務為目標的新資料。

您會執行此轉換，執行下列步驟︰

-   轉換用於到單一模組的訓練課程的模組的設定，並將其儲存為訓練模式

-   刪除到計分不相關的任何無關模組

-   新增輸入與輸出的最終的 Web 服務會使用的連接埠

可能會有其他您想要進行準備就緒預測實驗部署 Web 服務做的變更。 例如，如果您想要輸出結果的子集的 Web 服務時，您可以新增篩選之前的輸出連接埠模組。

在此轉換程序，不會捨棄訓練實驗。 程序完成後，您在 Studio 中有兩個索引標籤︰ 一個用於訓練實驗，一個用於預測實驗。 如此一來，您可以變更訓練實驗部署 Web 服務並重建預測實驗之前。 或，您可以儲存訓練實驗開始另一行實驗的複本。

>[AZURE.NOTE] 當您按一下**預測 Web 服務**時開始訓練實驗轉換預測的實驗，自動程序，這在大部分情況下也適。 如果您的訓練課程實驗複雜 （例如，您需要您結合在一起的訓練課程的多個路徑），您可能會想要手動進行此轉換。 如需詳細資訊，請參閱[轉換預測的實驗電腦學習訓練實驗](machine-learning-convert-training-experiment-to-scoring-experiment.md)。

### <a name="the-web-service"></a>Web 服務

一旦您滿意預測實驗已準備好，您可以將您的服務，為傳統的 Web 服務的部署或新的 Web 服務基礎上 Azure 資源管理員。 若要將其部署為*傳統電腦學習 Web 服務*operationalize 模型，按一下 [**部署 Web 服務**並選取**部署 Web 服務 [傳統]**。 若要部署為*新電腦學習 Web 服務*，按一下**部署 Web 服務**並選取**[新增] 部署 Web 服務**。 使用者可以立即傳送資料至您使用 Web 服務 REST API 的模型，並收到回結果。 如需詳細資訊，請參閱[如何使用的電腦，學習實驗從已部署 Azure 電腦學習 Web 服務](machine-learning-consume-web-services.md)。

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>非一般的情況下︰ 建立非預測 Web 服務

如果實驗不訓練預測分析模型，然後您不需要建立訓練實驗和計分的實驗-會只要實驗，而且您可以將其部署為 Web 服務。 電腦學習 Studio 偵測實驗是否包含預測模型，藉由分析您已使用的模組。

您已在實驗逐一查看和後感到滿意它︰

1.  按一下 [**設定 Web 服務**，然後選取 [**重新訓練 Web 服務**-輸入與輸出節點會自動加入

2.  按一下 [**執行**]

3. 按一下 [**部署 Web 服務**，然後選取 [根據您要部署環境的 [**部署 Web 服務 [傳統]**或**[新增] 部署 Web 服務**。

立即部署 Web 服務，您可以存取及管理其就像預測 Web 服務。

## <a name="updating-your-web-service"></a>更新您的 Web 服務

現在您已部署 Web 服務為實驗，該怎麼辦您需要將其更新？

您需要更新而定︰

**您想要變更輸入或輸出，或您想要修改的 Web 服務如何管理資料**

如果您不會變更模型，但只變更的 Web 服務處理資料的方式，您可以編輯預測實驗，然後按一下 [**部署 Web 服務**並再次選取**部署 Web 服務 [傳統]**或**[新增] 部署 Web 服務**。 Web 服務已停止部署更新預測實驗，與 Web 服務必須重新啟動。

以下是範例︰ 假設預測實驗傳回整列的輸入資料以預測的結果。 您可能會決定您想要的 Web 服務就會傳回結果。 因此，您可以新增**專案欄**模組預測實驗前的輸出連接埠，以排除資料行以外的結果。 當您按一下 [**部署 Web 服務**，並再次選取**部署 Web 服務 [傳統]**或**[新增] 部署 Web 服務**時，就會更新 Web 服務。

**您想要重新訓練以新資料模型**

如果您想要保留您的電腦學習模型，但是您想要重新其訓練以新資料，您會有兩個選擇︰

1.  **重新執行 Web 服務時的模型訓練**-如果您想要重新訓練時預測模型執行 Web 服務，您可以藉由幾個修改訓練實驗，使其***訓練實驗***中，然後將其部署為***訓練 web*服務**。 瞭解如何執行此動作，請參閱[重新訓練電腦學習模型以程式設計方式](machine-learning-retrain-models-programmatically.md)的相關指示。

2.  **回到原始的訓練課程實驗和開發模型使用不同的訓練資料**-預測實驗已連結至 Web 服務，但訓練實驗直接未連結以這種方式。 如果您修改原始的訓練課程實驗，並按一下 [**設定 Web 服務**，就會建立*新*預測，嘗試部署時，會建立*新*Web 服務。 它不只是更新原始的 Web 服務。

    如果您需要修改訓練實驗，將其開啟，然後按一下 [**另存新檔**來製作複本。 將保留原始的訓練課程實驗預測的實驗，並 Web 服務。 您現在可以建立新 Web 服務使用您的變更。 一旦您已部署新的 Web 服務，然後決定是否要停止上一個 Web 服務，或讓它的新節同時執行。

**您想要訓練不同的模型**

如果您想要變更原始預測實驗，例如選取不同的電腦學習演算法嘗試不同的訓練課程方法等等，則必須遵循上述重新訓練模型的第二個程序︰ 開啟訓練實驗，按一下 [**另存新檔**，藉以製作複本，，然後開始新的路徑的開發模型建立預測的實驗，及部署 web 服務。 這會建立一個新的 Web 服務不相關的原始項目-您可以決定哪一個，或兩者都要繼續執行。

## <a name="next-steps"></a>後續步驟

如需開發及實驗的程序的詳細資訊，請參閱下列文章︰

-   轉換實驗-[轉換預測的實驗電腦學習訓練實驗](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   部署 Web 服務-[部署 Azure 電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)

-   重新訓練模型-[重新訓練電腦學習模型以程式設計方式](machine-learning-retrain-models-programmatically.md)

如需整個程序的範例，請參閱︰

-   [電腦學習教學課程︰ 建立您的第一個實驗 Azure 電腦學習 Studio 中](machine-learning-create-experiment.md)

-   [逐步解說︰ 開發信用卡風險評定中 Azure 電腦學習的預測狀況分析解決方案](machine-learning-walkthrough-develop-predictive-solution.md)