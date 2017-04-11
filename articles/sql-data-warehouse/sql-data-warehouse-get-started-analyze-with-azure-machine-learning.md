<properties
   pageTitle="Azure 電腦學習使用分析資料 |Microsoft Azure"
   description="用於 Azure 電腦學習建立預測的電腦學習基礎 Azure SQL Data Warehouse 中儲存的資料模型。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/14/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="analyze-data-with-azure-machine-learning"></a>Azure 電腦學習使用分析資料

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure 機器學習](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

本教學課程中使用 Azure 電腦學習建立預測的電腦學習基礎 Azure SQL Data Warehouse 中儲存的資料模型。 具體來說，此建立目標的行銷活動的 Adventure Works，safetystockllevel 店預測客戶是否可能或不購買 safetystockllevel。

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## <a name="prerequisites"></a>必要條件
若要在逐步執行本教學課程中，您需要︰

- SQL Data Warehouse 預先載入 AdventureWorksDW 範例資料。 佈建這種情況，請參閱[建立 SQL Data Warehouse][]並選擇載入範例資料。 如果您已有資料倉庫，但不是會有範例資料，您可以[手動載入範例資料][]。

## <a name="1-get-data"></a>1.取得資料
資料位於 AdventureWorksDW 資料庫中的 [dbo.vTargetMail] 檢視中。 讀取此資料︰

1. 登入[Azure 電腦學習 studio][] ，然後按一下 [在我的實驗。
2. 按一下 [ **+ 新增]** ，然後選取 [**空白實驗**。
3. 輸入您的實驗的名稱︰ 行銷目標。
4. 將 [模組] 窗格的**閱讀程式**模組到畫布。
5. 在 [屬性] 窗格中，指定 SQL Data Warehouse 資料庫的詳細資料。
6. 指定資料庫**查詢**讀取感興趣的資料。

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

按一下 [實驗畫布底下的 [**執行**執行實驗。
![執行實驗][1]


實驗成功執行之後，請按一下 [輸出連接埠底部的 [讀取] 模組，然後選取 [若要查看匯入的資料**視覺效果**。
![檢視匯入的資料][3]


## <a name="2-clean-the-data"></a>2.清除資料
若要讓資料更簡潔，放不相關的模型的某些資料欄。 若要執行這項操作︰

1. 將**Project 資料行**模組拖曳到畫布中。
2. 若要指定您想要捨棄的資料行的 [屬性] 窗格中按一下 [**啟動欄選取器**]。
![專案資料行][4]

3. 排除兩個資料行︰ CustomerAlternateKey 和 GeographyKey。
![移除不需要的欄][5]


## <a name="3-build-the-model"></a>3.建立模型
我們會分割的資料 80-20: 80%訓練電腦，學習模型和測試模型 20%。 我們會使用於 」 兩個類別 」 演算法的二進位分類問題。

1. 將畫布上拖曳**分割**模組。
2. 輸入第一個輸出資料集，在 [屬性] 窗格中的資料列的分數 0.8。
![資料分割為訓練和測試設定][6]
3. 將**兩個類別提高決策樹**模組拖曳到畫布中。
4. 將畫布上拖曳**訓練模型**模組，並指定輸入值。 然後按一下 [屬性] 窗格中的**啟動欄選取器**。
      - 第一次輸入︰ 毫升演算法。
      - 第二個輸入︰ 訓練演算法上的資料。
![連線訓練模型模組][7]
5. 預測資料行選取**BikeBuyer**欄。
![選取要預測資料行][8]


## <a name="4-score-the-model"></a>4.分數模型
現在，我們將測試測試資料模型的執行方式。 我們會比較演算法我們所選擇的不同演算法若要查看更有效地執行。

1. 將**分數模型**模組拖曳到畫布中。
    第一次輸入︰ 訓練模型第二個輸入︰ 測試資料![分數模型][9]
2. 將**兩個類別機率分類點電腦**拖曳到實驗畫布中。 我們會比較兩個類別提高決策樹與此演算法的執行方式。
3. 複製並貼上訓練模型和分數模型模組畫布中。
4. 將**評估模型**模組拖曳到 [比較兩種演算法畫布。
5. **執行**實驗。
![執行實驗][10]
6. 按一下底部的評估模型模組的輸出連接埠，然後按一下 [視覺效果。
![以視覺化方式呈現評估結果][11]

提供度量資訊是巨鵬曲線、 精確度回收圖表和增益曲線。 看看下列指標，我們可以看到的第一個模型，比第二個更好執行。 若要查看的什麼預測，第一個模型按一下分數模型輸出連接埠，然後按一下 [視覺效果。
![以視覺化方式呈現的成績結果][12]

您會看到兩個新增至您的測試資料集的詳細資料行。

- 計分機率︰ 客戶是 safetystockllevel 採購者的可能性。
- 計分的標籤︰ 分類或未完成模型 safetystockllevel 買方 (1) (0)。 標記此機率閥值會設定為 50%，可以調整。

比較欄 BikeBuyer （實際的） 含計分標籤 （預測），您可以看到模型執行的程度。 接下來的步驟，為您可以使用此模型的新客戶的預測和發佈為 web 服務此模型或 SQL Data Warehouse 回寫的結果。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解建立預測電腦學習模型，請參閱[簡介電腦學習 Azure 上][]。

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure 電腦學習 studio]:https://studio.azureml.net/
[若要在電腦上 Azure 學習簡介]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[手動載入範例資料]: sql-data-warehouse-load-sample-databases.md
[建立 SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
