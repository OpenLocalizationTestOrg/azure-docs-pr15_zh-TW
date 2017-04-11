<properties
    pageTitle="連接到電腦學習 Web 服務 |Microsoft Azure"
    description="使用 C# 或 Python，連線至 Azure 電腦學習 Web 服務使用授權金鑰。"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016" 
    ms.author="garye" />


# <a name="connect-to-an-azure-machine-learning-web-service"></a>連線至 Azure 機器學習 Web 服務

Azure 電腦學習開發人員體驗是 Web 服務的 API 即時或批次模式中進行預測從輸入資料。 您可以使用 Azure 電腦學習 Studio 建立預測及部署 Azure 電腦學習 Web 服務。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

若要瞭解如何建立及部署電腦學習 Web 服務使用電腦學習 Studio:

- 如何在電腦學習 Studio 建立實驗的教學課程，請參閱[建立您的第一個實驗](machine-learning-create-experiment.md)。
- 如需如何部署 Web 服務的詳細資訊，請參閱[部署電腦學習 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。
- 如需電腦學習一般而言，請瀏覽[電腦學習文件中心](https://azure.microsoft.com/documentation/services/machine-learning/)。

## <a name="azure-machine-learning-web-service"></a>Azure 電腦學習 Web 服務 ##

Azure 電腦學習 Web 服務時，外部應用程式會與電腦學習工作流程計分模型中進行即時通訊。 電腦學習 Web 服務呼叫預測結果傳回外部應用程式。 若要讓來電電腦學習 Web 服務，您可以傳送部署預測時所建立的 API 金鑰。 電腦學習 Web 服務為基礎其餘部分中，web 程式設計專案的常用架構選擇。

Azure 電腦學習有兩種類型的服務︰

- 要求回應服務 （給） – 低延遲、 彈性的服務，提供從電腦學習 Studio 以無模型建立並部署介面。
- 批次執行服務 (BES) – 非同步服務的資料記錄的批次的分數。

如需電腦學習 Web 服務的詳細資訊，請參閱[部署電腦學習 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

## <a name="get-an-azure-machine-learning-authorization-key"></a>取得 Azure 電腦學習授權金鑰 ##

當您部署實驗時，會產生 API 金鑰，Web 服務。 您可以從數個位置來擷取按鍵。

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>從 Microsoft Azure 機器學習 Web 服務入口網站

[Microsoft Azure 機器學習 Web 服務](https://services.azureml.net)入口網站登入。

若要擷取的新電腦學習 Web 服務的 API 金鑰︰

1. 在 [Azure 機器學習 Web 服務] 入口網站中，按一下 [ **Web 服務**頂端的功能表。
2. 按一下您要擷取金鑰的 Web 服務。
3. 在頂端的功能表中，按一下 [**消耗**。
4. 複製並儲存**主索引鍵**。


若要擷取傳統電腦學習 Web 服務的 API 金鑰︰

1. 在 [Azure 機器學習 Web 服務] 入口網站中，按一下 [**傳統的 Web 服務**頂端的功能表。
2. 按一下您要使用的 Web 服務。
3. 按一下您要擷取金鑰的端點。
3. 在頂端的功能表中，按一下 [**消耗**。
4. 複製並儲存**主索引鍵**。

## <a name="classic-web-service"></a>傳統的 Web 服務 ##

 您也可以從電腦學習 Studio 或 Azure 入口網站擷取傳統的 Web 服務的索引鍵。

### <a name="machine-learning-studio"></a>電腦學習 Studio ###

1. 在電腦學習 Studio 中，按一下左側的 [ **WEB 服務**。
2. 按一下 [Web 服務]。 在 [**儀表板**] 索引標籤上，是**API 金鑰**。

### <a name="azure-portal"></a>Azure 入口網站 ###

1. 按一下左側的 [**電腦學習**。
2. 按一下您的 Web 服務所在的工作區。
3. 按一下 [ **WEB 服務**]。
4. 按一下 [Web 服務]。
5. 按一下 [結束點]。 在右下角，向下是 「 API 金鑰 」。

## <a id="connect"></a>連接到電腦學習 Web 服務

您可以連線到電腦學習 Web 服務使用任何支援 HTTP 邀請及回應的程式設計語言。 從電腦學習服務說明網頁，您可以在 C#、 Python 和 R 檢視範例。

**電腦學習 API 說明**當您部署 Web 服務時，會建立電腦學習 API 說明。 請參閱[Azure 機器學習逐步解說-部署 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)。
電腦學習 API 說明包含預測 Web 服務的詳細資料。

2. 按一下您要使用的 Web 服務。
3. 按一下您要檢視 API 的 [說明] 頁面的端點。
3. 在頂端的功能表中，按一下 [**消耗**。
3. 按一下 [要求回應] 或 [批次執行結束點之下的 [ **API 說明頁面**]。

**若要檢視電腦學習 API 新的 Web 服務的說明**

在 [Azure 的電腦，學習 Services 入口網站︰

1. 在頂端的功能表上，按一下 [ **WEB 服務**。
2. 按一下您要擷取金鑰的 Web 服務。

按一下 [取得 Uri 要求 Reposonse 批次執行服務和程式碼範例 C#、 R，和 Python**消耗**]。

按一下 [ **Swagger API** ，以取得所提供的 Uri 從呼叫的 api Swagger 根據文件]。

### <a name="c-sample"></a>C# 範例 ###

若要連線到電腦學習 Web 服務，使用**HttpClient**傳遞 ScoreData。 ScoreData 包含 FeatureVector，代表 ScoreData n 維度向量的數字的功能。 您使用的 API 金鑰電腦學習服務進行驗證。

若要連線到電腦學習 Web 服務，必須安裝**Microsoft.AspNet.WebApi.Client** NuGet 套件。

**在 Visual Studio 中安裝 Microsoft.AspNet.WebApi.Client NuGet**

1. 發佈下載中的資料集 UCI︰ 限制級 2 類別資料集 Web 服務。
2. 按一下 [**工具]** > **NuGet 封裝管理員** > **封裝管理員主控台**。
2. 選擇 [**安裝套件 Microsoft.AspNet.WebApi.Client**]。

**若要執行的程式碼範例**

1. 發佈 」 範例 1︰ 從 UCI 下載資料集︰ 家長授予 2 類別資料集 」 實驗、 電腦學習範例集合的一部分。
2. 指派與索引鍵的 apiKey 來自 Web 服務。 請參閱**取得 Azure 電腦學習授權金鑰**的上方。
3. 指派要求 uri serviceUri。


### <a name="python-sample"></a>Python 範例 ###

若要連線到電腦學習 Web 服務，使用傳遞 ScoreData **urllib2**文件庫。 ScoreData 包含 FeatureVector，代表 ScoreData n 維度向量的數字的功能。 您使用的 API 金鑰電腦學習服務進行驗證。


**若要執行的程式碼範例**

1. 部署 」 範例 1︰ 從 UCI 下載資料集︰ 家長授予 2 類別資料集 」 實驗、 電腦學習範例集合的一部分。
2. 指派與索引鍵的 apiKey 來自 Web 服務。 請參閱**取得 Azure 電腦學習授權金鑰**開頭處本文的一節。
3. 指派要求 uri serviceUri。
