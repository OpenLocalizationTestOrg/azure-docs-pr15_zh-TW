<properties
    pageTitle="小組資料科學程序為何？  |Microsoft Azure"
    description="建置智慧型運用進階的分析的應用程式的系統化地方法有小組資料科學程序。"
    keywords="資料科學程序，資料科學小組"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev" />


# <a name="what-is-the-team-data-science-process-tdsp"></a>什麼是小組資料科學程序 (TDSP)？

小組資料科學程序 (TDSP) 提供系統化方式建置智慧型應用程式，讓資料科學家進行共同作業所需的開啟產品將這些應用程式的活動的完整的生命週期的有效的小組可以。

具體來說，TDSP 目前提供資料科學團隊使用︰

- **方法**︰ 列出了一系列的定義如何定義問題、 分析相關資料、 建立及評估預測模型，提供的指導開發週期，然後再部署這些模型企業應用程式中的步驟。
- **資源**︰ 工具與技術等資料科學 VM 以簡化設定資料科學活動的環境及上手新技術的實用指南。

以下是開發週期小組資料科學程序︰

![小組的圖表︰ 資料科學程序 ](./media/data-science-process-overview/data-science-process-for-teams-diagram.png)


此程序是**反覆運算**︰ 瞭解新的或現有的模型中的限制發展，並需要縮短先前已完成的順序步驟。 現有的組織開發和專案計劃程序會**輕鬆地調整**使用 TDSP 定義系列的步驟。

程序中的步驟是圖表連結[TDSP 學習路徑](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)，並如下所述。  


## <a name="planning-and-preparation-steps"></a>規劃與準備的步驟

## <a name="p1-business-and-technology-planning"></a>P1。 商業和技術規劃

定義您的業務目標和問題來啟動分析專案。 指定而言**業務需求**。 此步驟的中央的目標是識別分析必須以符合這些需求預測的主要商務變數 （銷售預測或的機率順序權利，例如）。 其他計劃是專案的然後通常基本的**資料來源**所需的位址目標從分析的觀點來瞭解。 不很，例如，若要尋找的現有系統需要來收集及記錄其他類型的資料來解決問題，並獲得專案目標。 如需，請參閱[規劃您的小組資料科學程序的環境](machine-learning-data-science-plan-your-environment.md)[中 Azure 電腦學習進階分析的案例](machine-learning-data-science-plan-sample-scenarios.md)。  


## <a name="p2-plan-and-prepare-infrastructure"></a>P2。 規劃及準備基礎結構

小組資料科學程序的分析環境包含數個元件︰

- **資料工作區**資料分析和建立模型的分段位置
- **處理基礎結構**的預先處理、 瀏覽，與模型資料
- **執行階段基礎結構**operationalize 分析模型，並執行智慧的用戶端使用模型的應用程式。  

設定需要分析基礎結構，通常會從核心作業系統不同環境的一部分。 但是，通常會運用從企業內的多個系統以及公司外部來源的資料。 分析基礎結構可完全雲端，內部部署安裝程式或兩者的混合式。 選項，請參閱[設定以使用小組資料科學程序中的資料科學環境](machine-learning-data-science-environment-setup.md)。


## <a name="analytics-steps"></a>分析的步驟︰  

## <a name="1-ingest-the-data-into-the-data-platform"></a>1.內嵌資料的資料平台

第一步是來自各種來源，或從或從企業、 外部的相關的資料帶入分析環境可以處理資料的位置。 在來源資料的**格式**，可能會有所不同目的地所需的格式。 因此 ingestion 工具完成也可能有一些資料轉換。 選項，請參閱[將分析的儲存空間環境的載入資料](machine-learning-data-science-ingest-data.md)

除了資料初始 ingestion，許多智慧應用程式必定期重新整理的資料，做為進行中學習程序的一部分。 這可以經由設定**資料管道的郵件**或工作流程。 此表單反覆運算部分包含重建與重新評估智慧部署解決方案的應用程式所使用的分析模型的程序的一部分。 例如，請參閱[移動從 SQL Azure Azure 資料工廠與內部部署 SQL server 資料](machine-learning-data-science-move-sql-azure-adf.md)。


## <a name="2-explore-and-visualize-the-data"></a>2.瀏覽，並以視覺化方式呈現資料

下一步是取得更深入瞭解資料調查其**摘要統計資料**，關聯，及使用技巧這種**視覺效果**。 這也是處理**資料的品質**及完整性，例如遺漏值與資料類型不相符，不一致的資料關聯的問題。 前置處理轉換會用來清理原始資料之前進一步分析和模型可以進行。 如需說明，請參閱[學習工作，準備好增強型電腦的資料](machine-learning-data-science-prepare-data.md)。


## <a name="3-generate-and-select-features"></a>3.產生，並選取的功能

資料科學家中使用網域專家共同作業，必須找出的擷取資料集的這些屬性和的最佳方式可用來預測識別規劃期間的主要商務變數的功能。 這些新功能可以從現有的資料，或可能需要額外收集的資料。 此程序稱為**功能工程**，而是其中一個中建立更有效率的預測狀況分析系統的主要步驟。 此步驟需要創意網域專業知識的組合與取得的資料探索步驟深入資訊。 如需指引，請參閱[工程團隊資料科學程序中的功能](machine-learning-data-science-create-features.md)。


## <a name="4-create-and-train-machine-learning-models"></a>4.建立及訓練電腦學習模型

資料科學家建立預測規劃的步驟，使用具有已清除的資料與 featurized 中定義的業務需求以區分的金鑰變數分析模型。 電腦學習系統支援多個**模型演算法**的適用於各種情況。 請參閱[如何選擇演算法的 Azure 電腦學習](machine-learning-algorithm-choice.md)。

資料科學家必須選擇最適合模型的預測工作並不是很結果來自多個模型需要結合起來，以獲得最佳結果。 輸入的資料的模型通常分成隨機三個部分︰

- 訓練資料集
- 驗證資料集
- 測試資料集

使用**訓練資料集**建立模型。 最佳組合，模型 （調整參數） 為選取執行模型與衡量**驗證資料集**的預測錯誤。 最後的**測試資料集**用來評估獨立訓練或驗證模型未使用的資料模型所選的效能。  程序，請參閱[如何評估 Azure 電腦學習模型效能](machine-learning-evaluate-model-performance.md)。


## <a name="5-deploy-and-consume-the-models-in-the-product"></a>5.部署與使用產品的模型

我們有後的模型，以及執行一組，則可**operationalized**的其他應用程式使用。 根據業務需求**即時**或**批次**為基礎進行預測。 若要 operationalized，模型必須與**開啟 API 介面**輕鬆耗用從各種不同的應用程式公開這類 online 網站、 試算表、 儀表板或企業版和後端的應用程式。 請參閱[部署 Azure 電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)。


## <a name="summary-and-next-steps"></a>摘要] 和 [下一步

[小組資料科學程序](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)化為一系列重複步驟來建立智慧應用程式使用進階的分析該**提供的指導方針**對任務所需。 每個步驟也會提供如何使用各種不同的 Microsoft 技術完成工作所述的詳細資料。

雖然 TDSP 事先特定類型的**文件**的成品不限定，則文件的資料探索、 模型和評估，結果，並儲存相關的程式碼，以便可以逐一查看分析，必要時的最佳作法。 使用包含類似的資料和預測工作其他應用程式時，這也可讓重複使用的分析工作。

另外也提供完整端對端示範**特定案例**的程序中的所有步驟的逐步解說。 列出與[小組資料科學程序的逐步解說](data-science-process-walkthroughs.md)主題的縮圖的描述。
