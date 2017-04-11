<properties
    pageTitle="與電腦學習信用風險的預測解決方案 |Microsoft Azure"
    description="顯示如何建立的預測狀況分析解決方案的信用風險評定 Azure 電腦學習 Studio 中詳細逐步解說。"
    keywords="信用卡風險、 預測分析解決方案、 風險評定"
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
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>逐步解說︰ 開發信用卡風險評定中 Azure 電腦學習的預測狀況分析解決方案

假設您需要預測個人的信用風險根據他們在信用應用程式中提供的資訊。  

信用卡風險評估複雜的問題，當然，但現在就讓我們簡化有點問題的參數。 然後，我們可以將其作為範例如何使用 Microsoft Azure 電腦學習和電腦學習 Studio 及電腦學習 web 服務來建立預測分析解決方案。  

在此詳細逐步解說，我們會追蹤開發電腦學習 Studio 中的預測狀況分析模型，然後將其部署 Azure 電腦學習 web 服務為程的序。 我們將會開始可公開使用信用卡風險資料、 開發及訓練該資料為基礎的預測模型，然後為 web 服務可讓其他人的信用風險評定的部署模型。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] 若要下載及列印的圖表，可讓電腦學習 Studio 的功能的概觀，請參閱[Azure 電腦學習 Studio 功能的概觀圖表](machine-learning-studio-overview-diagram.md)。

若要建立信用卡風險評定解決方案，我們會遵循下列步驟︰  

1.  [建立電腦學習工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [上傳現有的資料](machine-learning-walkthrough-2-upload-data.md)
3.  [建立新的實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [訓練，以及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [部署 web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Access web 服務](machine-learning-walkthrough-6-access-web-service.md)

此逐步解說為基礎的簡化版[二進位分類︰ 信用卡風險預測](http://go.microsoft.com/fwlink/?LinkID=525270) [Cortana 智慧圖庫](http://gallery.cortanaintelligence.com/)中的範例實驗。
