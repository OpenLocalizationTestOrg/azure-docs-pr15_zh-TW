<properties
   pageTitle="Azure 機器學習使用 SQL Data Warehouse |Microsoft Azure"
   description="教學課程中的運用 Azure 電腦學習 Azure SQL Data Warehouse 開發解決方案。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>使用 SQL Data Warehouse Azure 機器學習

Azure 電腦學習是您可以在 SQL Data Warehouse，建立預測的模型，針對您的資料，然後發佈為可供使用 web 服務的完整受管理的預測狀況分析服務。 您可以瞭解預測分析及電腦學習先閱讀[簡介電腦學習 Azure 上][]的基本概念。  然後，您可以瞭解如何建立、 訓練、 分數及測試電腦學習模型使用[嘗試教學課程中建立][]。

本文中，您將學習如何執行下列動作使用[Azure 電腦學習 Studio][]:

- 讀取您的資料庫來建立、 訓練及分數預測模型中的資料
- 撰寫您的資料庫的資料


## <a name="read-data-from-sql-data-warehouse"></a>從 SQL Data Warehouse 讀取資料

我們會讀取 AdventureWorksDW 資料庫中的 [產品] 資料表的資料。

### <a name="step-1"></a>步驟 1

開始新的實驗，按一下 [+ 新增底部的 [電腦學習 Studio] 視窗中，選取實驗，，，然後選取 [空白嘗試。 選取頂端的畫布預設實驗名稱，然後將其重新命名為有意義的例如自行車價格預測。

### <a name="step-2"></a>步驟 2

尋找閱讀程式中的模組資料集的調色盤，在左邊的實驗畫布上的模組。 實驗畫布上拖曳模組。
![][drag_reader]

### <a name="step-3"></a>步驟 3

選取 [讀取] 模組，然後填寫 [屬性] 窗格。

1. 選取作為資料來源的 Azure SQL 資料庫。
2. 資料庫伺服器的名稱︰ 輸入伺服器名稱。 若要尋找此，您可以使用[Azure 入口網站][]。

![][server_name]

3. 資料庫名稱︰ 在您指定的伺服器中輸入資料庫的名稱。
4. 伺服器的使用者帳戶名稱︰ 輸入具有資料庫的存取權限的帳戶的使用者名稱。
5. 伺服器的使用者帳戶密碼︰ 提供指定的使用者帳戶的密碼。
6. 接受任何伺服器的憑證︰ 使用這個選項 （較不安全），如果您想要略過讀取您的資料之前，請檢閱網站的憑證。
7. 資料庫查詢︰ 輸入描述您要讀取的資料的 SQL 陳述式。 在此案例中，我們會將資料讀取使用下列查詢的 [產品] 資料表。


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>步驟 4

1. 按一下 [實驗畫布下的 [執行] 來執行實驗。
2. 完成實驗之後，閱讀程式模組必須綠色的核取記號，以表示已順利完成。 請注意在右上角執行狀態已經完成。

![][run]

3. 若要查看匯入的資料，按一下頁面底部的汽車資料集的輸出連接埠並選取視覺效果。


## <a name="create-train-and-score-a-model"></a>建立、 訓練及分數模型

現在您可以使用此資料集︰

- 建立模型︰ 處理資料，並定義功能
- 訓練模型︰ 選擇並套用學習演算法
- 分數和測試模型︰ 預測新自行車價格


![][model]

若要進一步瞭解如何建立，訓練，分數並測試電腦，學習模型使用[建立嘗試教學課程][]。

## <a name="write-data-to-azure-sql-data-warehouse"></a>資料寫入 Azure SQL Data Warehouse

我們將撰寫結果集，以 ProductPriceForecast AdventureWorksDW 資料庫中的資料表。

### <a name="step-1"></a>步驟 1

尋找資料集的調色盤中的作者模組，在左邊的實驗畫布上的模組。 實驗畫布上拖曳模組。

![][drag_writer]

### <a name="step-2"></a>步驟 2

選取 [作者] 模組，然後填寫 [屬性] 窗格。

1. 選取資料目的地 Azure SQL 資料庫。
2. 資料庫伺服器的名稱︰ 輸入伺服器名稱。 若要尋找此，您可以使用[Azure 入口網站][]。
3. 資料庫名稱︰ 在您指定的伺服器中輸入資料庫的名稱。
4. 伺服器的使用者帳戶名稱︰ 輸入資料庫的寫入權限的帳戶的使用者名稱。
5. 伺服器的使用者帳戶密碼︰ 提供指定的使用者帳戶的密碼。
6. 接受任何的伺服器憑證 （不安全）︰ 選取此選項，如果您不想要檢視的憑證。
7. 若要儲存的資料行逗點分隔清單︰ 提供您想要輸出資料集或結果資料行的清單。
8. 資料表名稱︰ 指定 [資料] 資料表的名稱。
9. 逗點分隔的資料表資料行的清單︰ 指定要使用的新資料表中的資料行名稱。 資料行名稱可能不同來源資料集內的項目，但您必須清單的欄數一樣以下您所定義的輸出資料表。
10. 每個 SQL Azure 作業撰寫的資料列數目︰ 您可以設定會寫入到一個動作是 SQL 資料庫的資料列數目。

![][writer_properties]

### <a name="step-3"></a>步驟 3

1. 按一下 [實驗畫布下的 [執行] 來執行實驗。
2. 完成實驗之後，所有模組都必須綠色的核取記號，指出他們順利完成。

## <a name="next-steps"></a>後續步驟

如需開發秘訣，請參閱[SQL Data Warehouse 開發概觀][]。

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse 開發概觀]: ./sql-data-warehouse-overview-develop.md
[建立實驗教學課程]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[若要在電腦上 Azure 學習簡介]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure 機器學習 Studio]: https://studio.azureml.net/Home
[Azure 入口網站]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
