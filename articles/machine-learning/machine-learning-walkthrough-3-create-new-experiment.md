<properties
    pageTitle="步驟 3︰ 建立新的電腦學習實驗 |Microsoft Azure"
    description="開發預測解決方案逐步引導的步驟 3︰ 建立新的訓練課程實驗 Azure 電腦學習 Studio 中。"
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


# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>逐步解說步驟 3︰ 建立新的 Azure 電腦學習實驗

這是第三個步驟的逐步解說，[開發中 Azure 電腦學習的預測狀況分析解決方案](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [建立電腦學習工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [上傳現有的資料](machine-learning-walkthrough-2-upload-data.md)
3.  **建立新的實驗**
4.  [訓練，以及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [部署 web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Access web 服務](machine-learning-walkthrough-6-access-web-service.md)

----------

此逐步解說中的下一步是使用我們上傳的資料集的電腦學習 Studio 中建立實驗。  

1.  在 Studio 中，按一下 [ **+ 新增**底部的視窗]。
2.  選取**實驗**，，然後選取 [「 空白實驗 」。 選取頂端的畫布預設實驗名稱，然後將其重新命名為有意義

    > [AZURE.TIP] 最好填入實驗中，在 [**屬性**] 窗格中**摘要**和**描述**。 這些屬性可讓您有機會文件實驗，以便稍後再看起來的人能夠理解您的目標和方法。

3.  在左邊的實驗的模組調色盤畫布中，展開**儲存的資料集**。
4.  尋找您在**我的資料集**所建立的資料集，並將其拖曳至畫布。 您也可以在色板上方的 [**搜尋**] 方塊中輸入名稱，以尋找資料集。  

##<a name="prepare-the-data"></a>準備的資料
按一下資料集 （底部的小圓圈） 的輸出連接埠並選取**視覺效果**，您可以檢視前 100 個資料列以及整個資料集的一些統計資訊。  

資料檔案並未隨附的欄標題，因為 Studio 提供一般標題*（欄 1、 第 2 欄等）*。 建議的標題不必要建立模型，，但是他們更容易使用實驗中的資料。 此外，當我們最後發佈此模型中的 web 服務時，標題可協助找出要服務的使用者的資料行。  

我們可以新增使用[編輯中繼資料]的欄標題[edit-metadata]模組。
使用[編輯中繼資料][edit-metadata]若要變更資料集相關聯的中繼資料的模組。 在此情況下，也可以提供多個好記的名稱，為欄名。 

若要使用[編輯中繼資料][edit-metadata]，您第一次指定要修改 （在此例中所有。） 的資料行接下來，您可以指定要在這些資料行 （在此例中變更欄標題。） 上執行的動作

1.  在模組面板中，輸入 「 中繼資料 」 的**搜尋**方塊中。 您會看到[編輯中繼資料][edit-metadata]模組清單中顯示。
2.  按一下並拖曳[編輯中繼資料][edit-metadata]到畫布模組拖放到下方我們先前新增的資料集。
3.  若要[編輯的中繼資料]連線的資料集[edit-metadata]︰ 按一下 [輸出連接埠的資料集 （底部的資料集的小圓形。）接下來，請拖曳到 [[編輯中繼資料]的輸入連接埠[edit-metadata]（頂端的模組小圓圈），然後放開滑鼠按鈕。 即使您移動 [畫布上，資料集和模組保持連線。

    實驗現在看起來應該像這樣︰  

    ![新增編輯中繼資料][2]
    
    紅色驚嘆號表示我們尚未尚未設定此模組的屬性。 我們會執行下, 一步]。
    
    > [AZURE.TIP] 您可以新增註解至模組，按兩下模組]，輸入文字。 這可以協助您一眼看到模組執行實驗中。 在此情況下，按兩下 [[編輯中繼資料][edit-metadata]模組]，然後輸入註解] 新增欄名]。 按一下 [關閉 [文字] 方塊畫布上的其他位置。 若要顯示註解，請按一下模組上的向下箭號。

4.  選取 [[編輯中繼資料][edit-metadata]，然後在右邊畫布上的 [**屬性**] 窗格中，按一下 [**啟動欄選取器**。
5.  在 [**選取資料行**] 對話方塊中，選取 [**可用的資料行**中的 [所有資料列，然後按一下 > 移至**選取的資料行**。
對話方塊看起來應該像這樣︰![所有選取的資料行的資料行選取器][4]
7.  按一下**[確定]**核取記號。
8.  回 [**屬性**] 窗格中，尋找**新的資料行名稱**參數。 在此欄位中，輸入的清單中的資料集，以分隔逗號，然後在 [欄順序的 21 資料行的名稱。 您可以從資料集上的文件 UCI 網站，取得資料行名稱，或以方便使用，您可以複製並貼上下列的清單︰  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    [屬性] 窗格，看起來像這樣︰

    ![編輯中繼資料屬性][1]

> [AZURE.TIP] 如果您要驗證的欄標題，請執行實驗 （按一下 [**執行**實驗畫布之下）。 當執行完成 (綠色核取記號會出現在 [[編輯中繼資料][edit-metadata])，按一下 [[編輯中繼資料]的輸出連接埠[edit-metadata]模組，並選取**視覺效果**。 您可以檢視任何模組的輸出以相同的方式檢視資料透過實驗的進度。

##<a name="create-training-and-test-datasets"></a>建立訓練及測試資料集

實驗的下一個步驟是產生訓練及測試我們的模型，我們將使用的不同資料集。

若要這麼做，我們使用[分割的資料][split]模組。  

1.  尋找[分割的資料][split]模組，請將其拖曳至畫布上，並將其連線至最後一個[編輯中繼資料][edit-metadata]模組。
2.  根據預設，分割比 0.5，而**Randomized 分割**參數設。 這表示資料的隨機半是透過[分割的資料]的一個連接埠輸出[split]模組和透過其他的下半部。 您可以調整這些項目，以及**隨機種子**參數，若要變更的分隔訓練及測試資料。 例如，我們會將其作為保留-是。
    
    > [AZURE.TIP] **第一個輸出資料集內的資料列的**屬性會決定資料中有多少是透過左的輸出的連接埠的輸出。 舉個例說，如果您設定為 0.7 的比率，然後 70%的資料是透過左側的連接埠輸出] 和 [30%，到正確的連接埠。  
    
3. 按兩下[分割的資料][split]模組輸入註解，「 訓練/測試資料分割 50%]。 

我們可以使用[分割的資料]的輸出[split]模組然而我們的話，但現在就讓我們選擇使用左側的輸出作為訓練資料] 和 [右輸出為測試資料。  

如上所述 UCI 網站上，將錯誤分類為低高信用卡風險的成本大於五個時間將錯誤分類為高低信用卡風險的成本。 若要此帳戶，我們會產生新的資料集，以反映此成本函數。 在新的資料集，每個高風險範例複製五個時間，而不會複寫每個低風險範例。   

我們可以執行此複寫使用 R 程式碼︰  

1.  尋找並拖曳[執行 R 指令碼][execute-r-script]到實驗模組畫布並連線[分割的資料]的左側的輸出連接埠[split]模組[執行 R 指令碼]的第一個輸入連接埠 (「 Dataset1 」) 以[execute-r-script]模組。
2. 按兩下[執行 R 指令碼][execute-r-script]模組，並輸入註解，「 成本調整設定]。
2.  在 [**屬性**] 窗格中，刪除**R 指令碼**參數中的預設文字，然後輸入這個指令碼︰

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


我們需要執行每個輸出此相同複寫作業的[分割的資料][split]模組，讓訓練課程和測試資料有相同的成本調整。

1.  以滑鼠右鍵按一下 [[執行 R 指令碼][execute-r-script]模組並選取 [**複製**。
2.  以滑鼠右鍵按一下實驗畫布上，選取 [**貼**。
3.  連線此[執行 R 指令碼]的第一個輸入連接埠[execute-r-script]右邊的模組輸出[分割的資料]的連接埠[split]模組。 
4.  在畫布底部，按一下 [**執行**]。 

> [AZURE.TIP] 執行 R 指令碼模組複本包含相同的指令碼的原始的模組。 當您複製並貼上在畫布上的 [模組時，複製會保留所有原始的屬性。  

現在，我們實驗看起來應該像這樣︰

![新增分割模組和 R 指令碼][3]

如需有關如何在您的實驗中使用 R 指令碼的詳細資訊，請參閱[延長您實驗 R](machine-learning-extend-your-experiment-with-r.md)。

**下一步︰[訓練和評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
