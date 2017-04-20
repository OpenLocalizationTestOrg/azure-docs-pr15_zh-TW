<properties
    pageTitle="步驟 5： 部署機器學習 Web 服務 |Microsoft Azure"
    description="步驟 5 的發展預測性解決方案逐步解說： 部署以 Web 服務的電腦學習 studio 預測實驗。"
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
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>逐步解說步驟 5： 部署 Azure 機器學習 Web 服務

第五個步驟的逐步解說、 [Develop Azure 機器學習預測性分析解決方案](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [建立機器學習工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [將現有的資料上傳](machine-learning-walkthrough-2-upload-data.md)
3.  [建立新的實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **部署 Web 服務**
6.  [存取 Web 服務](machine-learning-walkthrough-6-access-web-service.md)

----------

若要授與其他人能夠使用我們在這個逐步解說已開發的預測模型，我們將它部署為 Azure 上的 Web 服務。

到此刻為止我們已經被實驗訓練我們的模型。 但已部署的服務不會再將要執行訓練-則會預測產生由計分板模型為基礎的使用者的輸入。 讓我們要做一些準備從***訓練***實驗轉換這項實驗***預測***試驗。 

這是兩步驟程序：  

1. 轉換成*預測項實驗中*的 [*訓練實驗*我們已建立
2. 預測性實驗部署成 Web 服務

但首先，我們需要一點修剪這項實驗中向下。 我們目前有兩種不同方法在項實驗中，但是我們只想一個模型當我們部署這做為 Web 服務。  

假設我們已決定已擴大樹狀目錄模型所要使用的較佳模型。 讓執行最先會移除[兩個類別支援向量機器][two-class-support-vector-machine]單元和用於訓練它的模組。 若要複製實驗先依序按一下 [**另存新檔**實驗畫布的底部。

我們需要刪除下列模組：  

- [兩個類別支援向量機器][two-class-support-vector-machine]
- [訓練模型][train-model]和[分數模型][score-model]已連接至其的模組
- [正規化資料][ normalize-data] （這些兩者）
- [評估模型][evaluate-model]

選取的模組和按 Delete 鍵，或以滑鼠右鍵按一下模組，並選取 [**刪除**]。

現在我們已經準備好要部署此模型使用[兩個類別提高決策樹][two-class-boosted-decision-tree]。

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>轉換成預測性實驗的訓練實驗

將轉換成預測性實驗包含三個步驟：

1. 儲存的模型我們已經過訓練，然後取代我們訓練單元
2. Trim 來移除模組只需要訓練實驗
3. 定義所在的 Web 服務將用來接受輸入和則會產生輸出

幸運地是，可以完成所有三個步驟依序按一下 [**設定設定 Web 服務**底部的實驗畫布 （選取 [**預測 Web 服務**] 選項）。

當您按一下 [**設定設定 Web 服務**時、 數個情形：

- 訓練的模型做為單一的**受過訓練的模型**模組儲存到模組調色盤 （您可以找到其下**受過訓練的模型**） 實驗畫布的左邊。
- 會移除所用的訓練的模組。 特別：
  - [兩個類別已擴大的決策樹][two-class-boosted-decision-tree]
  - [訓練模型][train-model]
  - [分割資料][split]
  - 第二個[執行 R Script] [execute-r-script]用於測試資料的模組
- 已儲存的訓練的模型新增回實驗。
- 新增**web 服務輸入**及**Web 服務輸出**模組。

> [AZURE.NOTE] 符合原先的假設已經儲存已新增在實驗畫布的頂端的索引標籤下的兩個部分： 原始的訓練實驗**訓練實驗**、] 索引標籤下，新建預測符合原先的假設下**Predictive 試驗**。

我們需要才會使用這項實驗中特定一部額外的步驟。
我們新增兩個[執行 R 指令碼][execute-r-script]模組來提供資料的加權函數的訓練和測試。 我們不需要若要這樣做，最後一個模型。

機器學習 Studio 中移除一部[執行 R 指令碼][execute-r-script]模組時移除[分割][split]模組。 現在我們現在可以移除另及連線[中繼資料編輯器][metadata-editor]直接以[分數模型][score-model]。    

我們實驗現在應該看起來如下：  

![計分經過訓練的模型][4]  

> [AZURE.NOTE] 您可能會想要知道為什麼我們預測實驗中左 UCI 德文信用卡資料的資料集。 若要使用的使用者資料，不原始的資料集，因此為何模型中保留原始的資料集將要服務？
>
>它是 true 是表示此服務不需要原始信用卡資料。 但仍必須在結構描述的資料，其中包含有多少個資料行資訊及哪些欄的數字。 此結構描述資訊是必要轉譯使用者的資料。 我們將保留連接使計分模組具有資料集結構描述服務正在執行時這些元件。 未使用的資料、 結構描述。  

執行一次實驗 (按一下 [**執行**)。若要驗證模型是否運作正常，請按一下 [[分數模型]的輸出[score-model]模組並選取 [**檢視結果**。 您會看見的原始資料會顯示，以及信用風險值 （"計分標籤"） 和計分機率值 （"計分機率"。） 

## <a name="deploy-the-web-service"></a>部署 Web 服務

您可以部署為傳統的 Web 服務或新的 Web 服務以在 Azure 資源管理員實驗。

### <a name="deploy-as-a-classic-web-service"></a>部署為傳統的 Web 服務 ###

若要部署衍生自我們實驗的傳統 Web 服務、 畫布下方按一下 [**部署 Web 服務**並選取 [**部署 Web 服務 [傳統]**。 機器學習 Studio 部署項實驗中的做為 Web 服務並前往該 Web 服務的儀表板。 從這裡，您可以返回實驗 （**檢視快照**或**檢視最新**） 並執行 Web 服務 （請參閱**測試 Web 服務**下） 的簡易測試。 也有以下建立可以存取 Web 服務 （會詳細說明這個逐步解說下一步） 的應用程式的資訊。

![Web 服務儀表板][6]

您可以藉由按一下 [**設定**] 索引標籤的 [設定服務。 您可以在這裡修改的服務名稱 （其已符合原先的假設名稱預設） 並為其指定描述。 您也可以提供更多的好記標籤輸入與輸出欄。  

![設定 Web 服務][5]  

### <a name="deploy-as-a-new-web-service"></a>部署為新的 Web 服務

若要部署新的 Web 服務衍生自我們項實驗中，按一下 [**部署 Web 服務**下方的畫布和**部署 Web 服務 [新增]**。 機器學習 Studio 可讓您轉移到 Azure 機器學習 Web 服務部署實驗] 頁面上。

輸入 Web 服務的名稱，然後選取定價計劃。 如果您有現有的定價計劃可以選取，否則您必須建立服務的新價格計劃。 

1.  在 [**價格計劃**] 下拉式清單中，選取現有的計劃或選取 [**選取新的計劃**] 選項。
2.  在**計劃名稱**] 中輸入識別您 bill 在計劃的名稱。
3.  選取一個或**每月的規劃層**。 至預設區域和 Web 服務的計劃的計劃層預設會部署到該區域。

按一下 [**部署**] 和 [Web 服務會開啟的 [**快速入門**] 頁面。

您可以按一下 [**設定**] 功能表選項來設定服務。 您可以在這裡修改的服務名稱 （其已符合原先的假設名稱預設） 並為其指定描述。 

若要測試 Web 服務選取，請按一下 [**測試**] 功能表選項 （請參閱**測試 Web 服務**的下方）。 如需建立可以存取 Web 服務應用程式，按一下 [**消耗**功能表選項 （會詳細說明這個逐步解說下一步）。

> [AZURE.TIP] 您可以在已部署之後來更新 Web 服務。 例如，如果您想要變更您模型、 編輯訓練實驗、 調整] 模式參數，然後按一下 [**部署 Web 服務**。 然後選取 [**部署 Web 服務 [傳統]**或**[新增] 部署 Web 服務**。 當您重新部署符合原先的假設時，它會取代 Web 服務，現在可以使用更新的模型。  

## <a name="test-the-web-service"></a>測試的 Web 服務

### <a name="test-a-classic-web-service"></a>測試傳統 Web 服務

您可以測試服務機器學習 Studio 或 Azure 機器學習 Web 服務入口網站中。 測試 Azure 機器學習 Web 服務入口網站中具有容許您若要啟用的優點 

**測試的機器學習 Studio**

在 [**儀表板**] 頁面上按一下 [**測試**] 按鈕下**預設端點**。 ] 對話方塊會快顯並要求您輸入的資料服務。 這些是相同的資料行所出現的原始的德文信用風險資料集。  

輸入的資料集，然後按一下 [**確定]**。 

**測試 Azure 機器學習 Web Services 入口網站**

在 [**儀表板**] 頁面上按一下 [下**預設端點**的**測試**的 [預覽] 連結。 Azure 機器學習 Web 服務入口網站的 Web 服務端點中的 [測試] 頁面上會開啟並要求您輸入資料服務。 這些是相同的資料行所出現的原始的德文信用風險資料集。

按一下 [**測試要求回應**。 

Web 服務中的資料輸入透過**Web 服務輸入**模組的 [[中繼資料編輯器]透過[metadata-editor]模組和[分數模型][score-model]它的計分所在的模組。 結果會接著輸出從**Web 服務的輸出**透過 Web 服務。

**測試新的 Web 服務**

Azure 機器學習 Web services 入口網站中，按一下 [**測試**上方的頁面]。 **測試**] 頁面隨即開啟並可以輸入服務的資料。 輸入的欄位的顯示會對應至原始的德文信用風險資料集所出現的欄。 

輸入的資料集，然後按一下 [**測試要求回應**。

測試的結果會顯示在頁面的輸出欄的右邊。 

在測試時在 Azure 機器學習 Web 服務入口網站中，您可以啟用可用來測試要求回應服務的範例資料。 如果您機器學習 Studio 中建立的 Web 服務範例資料是取自資料您用來訓練您模型。

> [AZURE.TIP] 我們已設定，預測性實驗的方式[分數模型]結果整個[score-model]模組所傳回。 這包括所有輸入的資料加上信用風險值和計分機率。 若您想要傳回某些項目不同-例如僅信用風險值-無法插入的[專案資料行]則[project-columns][分數模型]之間的模組[score-model]和**Web 服務輸出**至消除您不想要傳回之 Web 服務的資料行。 

## <a name="manage-the-web-service"></a>管理 Web 服務

**管理傳統 Web 服務**

一旦您已部署傳統 Web 服務，您可以從[Azure 傳統入口網站](https://manage.windowsazure.com)來進行管理。

1. 登入[Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在 [Microsoft Azure 服務] 面板中，按一下 [**機器學習**。
3. 按一下 [工作區]。
4. 按一下 [ **Web 服務**] 索引標籤。
5. 按一下 [我們建立的 Web 服務。
6. 按一下 ["default"端點。

這裡可做到像是監視執行 Web 服務的方式和透過變更多少並行呼叫服務進行效能更多內容可以處理。
您甚至可以在 Azure Marketplace 發佈 Web 服務。

如需詳細資訊，請參閱：

- [建立端點](machine-learning-create-endpoint.md)
- [擴充 Web 服務](machine-learning-scaling-webservice.md)
- [Azure Marketplace 來發佈 Azure 機器學習 Web 服務](machine-learning-publish-web-service-to-azure-marketplace.md)

**管理 Azure 機器學習 Web Services 入口網站 Web 服務**

一旦您已部署您的 Web 服務、 傳統或新增]，您可以從[Azure 機器學習 Web services 入口網站](https://servics.azureml.net)來進行管理。

若要監視效能的 Web 服務：

1. 登入[Azure 機器學習 Web services 入口網站](https://servics.azureml.net)。
2. 按一下 [ **Web 服務**。
3. 按一下 [Web 服務。
4. 按一下 [**儀表板**]。

----------

**下一步]：[存取 Web 服務](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/