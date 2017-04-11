<properties
    pageTitle="步驟 6︰ 存取電腦學習 web 服務 |Microsoft Azure"
    description="開發預測解決方案逐步引導的步驟 6︰ 存取作用中的 Azure 電腦學習 web 服務。"
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


# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>逐步解說步驟 6︰ 存取 Azure 電腦學習 web 服務

這是最後一個步驟的逐步解說，[開發中 Azure 電腦學習的預測狀況分析解決方案](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [建立電腦學習工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [上傳現有的資料](machine-learning-walkthrough-2-upload-data.md)
3.  [建立新的實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [訓練，以及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [部署 web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.  **Access web 服務**

----------

在先前的步驟，在此逐步解說我們部署 web 服務使用我們信用風險預測模型。 現在使用者必須能夠傳送給該資料和得到的結果。 

Web 服務是 Azure web 服務的可以接收，並傳回在兩種方法之一中使用 REST Api 的資料︰  

-   **要求/回應**使用者的信用卡資料的一或多個列至服務使用 HTTP 通訊協定與服務回應一或多個結果集。
-   **批次執行**使用者 Azure blob 儲存信用卡資料的一或多個資料列，，然後傳送服務中的 [blob 位置。 服務得到所有的資料列中輸入 blob，將結果儲存在其他 blob，並傳回該容器的 URL。  

存取 web 服務的最快，最簡單的方法是透過[Azure Web 應用程式服務商場](https://azure.microsoft.com/marketplace/web-applications/all/)提供的 Web 應用程式範本。
這些 web 應用程式範本可以建立知道您的 web 服務輸入的資料，以及它會傳回自訂 web 應用程式。 您只需要提供存取 web 服務及資料，而範本執行其餘部分。

如需有關如何使用 web 應用程式範本的詳細資訊，請參閱[消耗 web 應用程式範本 Azure 電腦學習 web 服務](machine-learning-consume-web-service-with-web-app-template.md)。

您也可以開發自訂的應用程式，來存取 web 服務使用 R、 C# 和 Python 程式設計語言中，為您提供的起始程式碼。
如何[使用已發佈的電腦，學習實驗從 Azure 電腦學習 web 服務](machine-learning-consume-web-services.md)，您可以尋找完整的詳細資料。
