<properties
    pageTitle="步驟 2︰ 將資料上傳到電腦學習實驗 |Microsoft Azure"
    description="開發預測解決方案逐步引導的步驟 2︰ 上傳到 Azure 電腦學習 Studio 儲存公用資料。"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>逐步解說步驟 2︰ 將現有的資料上傳到 Azure 電腦學習實驗

這是第二個步驟的逐步解說，[開發預測分析解決方案中 Azure 電腦學習](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [建立電腦學習工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  **上傳現有的資料**
3.  [建立新的實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [訓練，以及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [部署 web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Access web 服務](machine-learning-walkthrough-6-access-web-service.md)

----------

若要開發信用風險的預測模型，我們需要我們可以用來訓練，然後再測試模型的資料。 在此逐步解說，我們將使用 「 UCI Statlog （德文信用卡資料） 資料集 」 從 UCI 電腦學習存放庫。 您可以在這裡找到︰  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

我們將使用名為**german.data**的檔案。 此檔案下載到本機硬碟。  

此資料集的信用 1000年過去求職包含 20 變數的資料列。 這些 20 變數代表資料集的功能向量提供每個信用卡應徵人員評分表識別特性。 其他資料行的每一列代表應徵人員評分表的計算結果的信用風險，與 700 求職識別為 [低信用卡風險及 300 高風險。

UCI 網站說明的功能向量，其中包含的財務資訊、 信用歷程記錄、 年資狀態及個人資訊的屬性。 二進位評等的每個應徵人員評分表，具有已指定表示它們較小] 或 [高信用卡風險。  

我們將使用這項資料來訓練的預測狀況分析模型。 當我們完成時，我們的模型應該仍可接受新的個人資訊和預測它們是否低或高信用卡風險。  

以下是一個感興趣的變化。 資料集的描述說明的低信用卡風險實際高信用卡風險時，它們是 5 乘以其他成本至財務機構將錯誤分類為高低信用卡風險時，將人員錯誤分類。 考慮這我們實驗中的其中一個簡單的方法是透過複製 （5 乘以） 的項目代表他人高信用卡風險。 然後，如果模型利用高信用卡風險為低，它會執行該 misclassification 5 的時間，一次的每個重複。 這會增加這項錯誤訓練結果中的成本。  

##<a name="convert-the-dataset-format"></a>轉換資料集格式
原始資料集會使用空白分隔的格式。 電腦學習 Studio 搭配更有效地逗點分隔值 (CSV) 檔案，所以我們會以逗號分隔取代空格轉換資料集。  

有許多方法可以轉換資料。 其中一個方法是使用下列 Windows PowerShell 命令︰   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

另一個方法是使用 Unix sed 命令︰  

    sed 's/ /,/g' german.data > german.csv  

在任一情況，我們會建立一個名為**german.csv**我們實驗中，我們將使用的檔案中的資料的逗點分隔版本。

##<a name="upload-the-dataset-to-machine-learning-studio"></a>上傳至電腦學習 Studio 的資料集

一旦將資料轉換為 CSV 格式，我們需要上傳到電腦學習 Studio。 如需有關如何開始使用電腦學習 Studio 的詳細資訊，請參閱[Microsoft Azure 電腦學習 Studio 首頁](https://studio.azureml.net/)。

1.  開啟電腦學習 Studio ([https://studio.azureml.net](https://studio.azureml.net))。 當要求登入，請使用您指定為工作區的擁有者的帳戶。
1.  按一下視窗頂端的 [ **Studio** ] 索引標籤。
1.  按一下 [ **+ 新增**底部的視窗]。
1.  選取**資料集**。
1.  選取 [**從本機檔案**。
1.  在 [**上傳新的資料集**] 對話方塊中，按一下 [**瀏覽]** ，找出您建立的**german.csv**檔案。
1.  輸入資料集的名稱。 在此逐步解說，我們會呼叫該 「 UCI 德文信用卡資料 」。
1.  資料類型，選取 [**沒有標題的一般 CSV 檔案 (。 nh.csv)**。
1.  如果您想要請新增描述。
1.  按一下**[確定]**。  

![上傳資料集][1]  


這會將資料上載到實驗中，我們可以使用的資料集模組。

> [AZURE.TIP] 若要管理您已上傳到 Studio 的資料集，請按一下 Studio 視窗左側的 [**資料集**] 索引標籤。

如需有關如何將實驗匯入資料的各種類型的詳細資訊，請參閱[Azure 電腦學習 studio 將訓練資料匯入](machine-learning-data-science-import-data.md)。

**下一步︰[建立新的實驗](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
