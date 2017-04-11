<properties
    pageTitle="R 語言，電腦學習的快速入門教學課程 |Microsoft Azure"
    description="使用此 R 程式設計教學課程，即可開始使用快速使用 R 語言 Azure 電腦學習 Studio 建立預測的解決方案。"
    keywords="快速入門、 r 語言、 r 程式設計語言、 r 程式設計教學課程"
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
    ms.date="07/12/2016"
    ms.author="garye"/>

# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning"></a>Azure 電腦學習的程式設計語言的快速入門教學課程

Stephen F Elston，Ph.D.

##  <a name="introduction"></a>簡介

此快速入門教學課程，可協助您快速開始延伸 Azure 電腦學習使用 R 程式設計語言。 請遵循此 R 程式設計教學課程中建立、 測試和執行 R Azure 電腦學習內的程式碼。 當您逐步教學課程，您將建立完整的預測解決方案中 Azure 電腦學習使用 R 語言。  

Microsoft Azure 電腦學習包含許多功能強大的電腦學習及資料管理模組。 為分析通用語言描述功能強大的 R 語言。 快樂，可以延伸分析與資料管理中 Azure 電腦學習使用。這種組合提供延展性和 Azure 電腦學習的部署簡易的彈性，以及深入分析的。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


###<a name="forecasting-and-the-dataset"></a>預測和資料集

預測是廣泛採用與非常實用的分析方法。 共同使用預測銷售季節性項目，決定最佳的庫存，來預測 macroeconomic 變數的範圍。 預測通常是使用時間數列模型。

時間數列資料是在其中值有時間索引的資料。 時間索引可以為一般，例如每月或每一分鐘，或不規則。 時間序列模型為基礎的時間系列資料。 程式設計語言 R 包含彈性的架構與更多時間數列資料分析。

此快速入門指南中我們會使用加州日常生產並價格資料。 此資料包括每月數個日常產品的以及資訊牛奶 fat，事先商品的價格。

在本文中 R 指令碼，以及所用的資料可以[在這裡下載][download]。 此資料原本被合成從在 http://future.aae.wisc.edu/tab/production.html 大專院校版威斯康辛從可用的資訊。

### <a name="organization"></a>組織

我們會介紹幾個步驟，以進一步了解如何建立、 測試和 Azure 電腦學習環境中執行分析與資料管理 R 程式碼。  

* 第一次，我們會探索使用 R 語言 Azure 電腦學習 Studio 環境中的基本概念。

* 然後，我們進行討論各種 I/O 資料、 R 程式碼和 Azure 電腦學習環境中的圖形。

* 建立資料清理和轉換程式碼，然後將建構預測解決方案的第一個部分。

* 我們準備的資料中，我們會執行數個資料集內的變數之間的關聯性的分析。

* 最後，我們會建立牛奶生產季節性時間序列預測模型。

##<a id="mlstudio"></a>在電腦學習 Studio R 語言與之互動

本節會帶領您完成的互動 R 程式設計語言電腦學習 Studio 環境中的一些基本概念。 R 語言提供功能強大的工具來建立自訂的分析及 Azure 電腦學習環境中的資料管理模組。

我會使用 RStudio 開發、 測試和偵錯 R 程式碼，小型的刻度。 將此程式碼是然後剪下] 和 [貼上[執行 R 指令碼]至[execute-r-script]電腦學習 Studio 準備好執行中的模組。  

###<a name="the-execute-r-script-module"></a>執行 R 指令碼模組

在電腦學習 Studio 中，[執行 R 指令碼]中執行 R 指令碼[execute-r-script]模組。 [執行 R 指令碼]範例[execute-r-script]模組電腦學習 Studio 中的會顯示在圖 1。

 ![R 程式設計語言︰ 選取電腦學習 Studio 中執行 R 指令碼模組][1]

*圖 1。顯示已選取 [執行 R 指令碼模組電腦學習 Studio 環境。*

參照圖 1，我們來看看部分的主要部分的[執行 R 指令碼]所使用的電腦學習 Studio 環境[execute-r-script]模組。

- 在中間窗格顯示實驗模組。

- 右窗格的上半包含來檢視和編輯 R 指令碼] 視窗。  

- 右窗格的下半部顯示部分的[執行 R 指令碼]的屬性[execute-r-script]。 您可以在適當的些許的窗格上的 [檢視錯誤與輸出記錄。

我們會當然，討論[執行 R 指令碼][execute-r-script]更詳細的這份文件其餘部分中。

使用複雜 R 函數工作時，建議您編輯、 測試和偵錯 RStudio 中。 進行任何的軟體開發、 延長您的程式碼從屬參照，並在小型簡單的測試情況下進行測試。 然後剪下並貼上您函數[執行 R 指令碼]的 R 指令碼] 視窗[execute-r-script]模組。 這種方法可讓您充分發揮 RStudio 整合的開發環境 (IDE) 和 Azure 電腦學習的能力。  

####<a name="execute-r-code"></a>執行 R 程式碼

R 中的任何程式碼[執行 R 指令碼][execute-r-script]當您按一下 [**執行**] 按鈕來執行實驗，就會執行模組。 當執行完成時，核取記號會出現在[執行 R 指令碼][execute-r-script]圖示。

####<a name="defensive-r-coding-for-azure-machine-learning"></a>防衛 R 編碼 Azure 電腦學習

如果您使用 Azure 電腦學習開發 R 程式碼，說 web 服務，您應該成長規劃如何程式碼會處理非預期的資料輸入和例外狀況。 若要維持清楚，我已不包含太多的檢查，或在大部分的程式碼範例所示的中處理的例外狀況。 不過，我們繼續進行我會提供您幾個函數的範例使用 R 的例外處理功能。  

如果您需要更完整的處理方式 R 例外處理，建議您閱讀[附錄 B，進一步讀取](#appendixb)中列出的活頁簿以 Wickham 適用的節。


####<a name="debug-and-test-r-in-machine-learning-studio"></a>偵錯及電腦學習 Studio 中測試 R

若要再次提醒您，建議您測試並偵錯小型 RStudio 中您 R 代碼。 不過，在一些情況下，您將需要追蹤 R 中[執行 R 指令碼]的程式碼問題[execute-r-script]本身。 此外，但最好檢查您的電腦學習 Studio 中的結果。

從您 R 程式碼，並在 Azure 電腦學習平台上執行的輸出位於主要 output.log。 Error.log 會顯示一些其他資訊。  

如果執行 R 程式碼時，電腦學習 Studio 發生錯誤，您的第一個採取的動作應該看 error.log。 此檔案可以包含實用的錯誤訊息，以協助您瞭解並修正錯誤。 若要檢視 error.log，按一下上的 [**檢視錯誤記錄**在**屬性] 窗格**上[執行 R 指令碼][execute-r-script]包含錯誤。

例如，執行下列 R 程式碼，定義的變數 y 時，在[執行 R 指令碼]中使用[execute-r-script]模組︰

    x <- 1.0
    z <- x + y

將此程式碼執行失敗，導致錯誤狀況。 在**屬性] 窗格**中按一下 [**檢視錯誤記錄檔**上會顯示圖 2] 中顯示。

  ![快顯的錯誤訊息][2]

*圖 2。快顯的錯誤訊息。*

看來我們需要查看 output.log 看到 R 錯誤訊息。 按一下 [[執行 R 指令碼][execute-r-script]然後在右側的**屬性] 窗格**中的**檢視 output.log**項目上按一下。 新的瀏覽器視窗隨即開啟，並看到下列。


    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found
    
    
    object 'y' not found
    ----------- End of error message from R -----------

此錯誤訊息會不包含任何意外，和清楚識別問題。

若要檢查 R 中的任何物件的值，您可以列印 output.log 檔案這些值。 檢查物件值的規則基本上是互動式 R 工作階段與相同。 例如，如果您在一行輸入變數的名稱，物件的值會列印 output.log 檔案。  

####<a name="packages-in-machine-learning-studio"></a>在電腦學習 Studio 套件

Azure 電腦學習隨附超過 350 預先安裝的 R 語言套件。 您可以使用下列程式碼中[執行 R 指令碼][execute-r-script]模組擷取預先安裝套件的清單。

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

如果您不在瞭解此程式碼的最後一行，繼續閱讀。 我們將在這份文件部分會全面討論 Azure 電腦學習環境中使用 R。

### <a name="introduction-to-rstudio"></a>RStudio 簡介

RStudio 是常用的 IDE 的。我會使用 RStudio 編輯、 測試和偵錯時，某些用於此快速入門指南的 R 程式碼。 R 程式碼測試並準備好後，您只要剪下，並從 [RStudio 編輯器] 中貼上機器學習 Studio[執行 R 指令碼][execute-r-script]模組。  

如果您沒有桌面電腦上安裝的 R 程式設計語言，建議您立即。 開啟來源 R 語言的免費下載，可在完整 R 封存網路 (CRAN) 在[http://www.r-project.org/](http://www.r-project.org/)。 有適用於 Windows、 Mac OS 及 Linux/UNIX 的下載。 選擇附近的左右對稱，然後依照下載]。 此外，CRAN 包含豐富的實用的分析與資料管理套件。

如果您是新 RStudio，您必須下載並安裝桌面版。 您可以找到 RStudio 下載 Windows、 Mac OS，及 Linux/UNIX 在 http://www.rstudio.com/products/RStudio/。 依照在桌上型電腦上安裝 RStudio 所提供的指示。  

使用在 https://support.rstudio.com/hc/sections/200107586-Using-RStudio RStudio 的教學課程介紹。

我使用 RStudio[附錄 A]中提供的其他資訊[appendixa]。  

##<a id="scriptmodule"></a>取得資料與執行 R 指令碼模組登出

在此區段探討的取得資料，將與不在[執行 R 指令碼][execute-r-script]模組。 我們會說明如何處理閱讀出[執行 R 指令碼]的各種資料型別[execute-r-script]模組。

本節的完整程式碼是您先前已下載的 zip 檔案中。

###<a name="load-and-check-data-in-machine-learning-studio"></a>載入及檢查電腦學習 Studio 中的資料

####<a id="loading"></a>載入資料集

我們會先將 Azure 電腦學習 Studio 載入**csdairydata.csv**檔案。

- 啟動 Azure 電腦學習 Studio 環境。

- 按一下__[+ 新增__在螢幕左下角，選取**資料集**。

- 選取**從本機檔案**，然後**瀏覽]**以選取該檔案。

- 請確認您已選取**頁首 (.csv) 一般的 CSV 檔案**做為資料集的類型。

- 按一下核取記號。

- 資料集已上傳後，您應該會看到新的資料集，按一下 [**資料集**] 索引標籤上的。  

####<a name="create-an-experiment"></a>建立實驗

現在，我們會有一些資料電腦學習 Studio 中，我們需要建立實驗執行分析。  

- 按一下__[+ 新增__左下方，並選取**實驗**，然後**空白實驗**。

- 您可以選取、 修改、**實驗...建立**標題頁面的頂端實驗的名稱。 例如，變更**CA 香蕉**分析。

- 在 [實驗] 頁面的左邊，展開 [**儲存資料集**，然後按一下 [**我的資料集**。 您應該會看到較舊版本上傳**cadairydata.csv** 。

- 拖放到實驗**csdairydata.csv 資料集**。

- 在左窗格上方的 [**搜尋嘗試項目**] 方塊中，輸入[執行 R 指令碼][execute-r-script]。 您會看到出現在 [搜尋] 清單中的模組。

- 拖放[執行 R 指令碼][execute-r-script]拖曳到您的平板的模組。  

- [執行 R 指令碼]最左邊的輸入 (**Dataset1**) 連線**csdairydata.csv 資料集**的輸出[execute-r-script]。

- **按一下 [儲存] 別忘了 ！**  

此時實驗看起來應該像圖 3。

![CA 香蕉分析嘗試與資料集執行 R 指令碼的模組][3]

*圖 3。CA 香蕉分析嘗試使用資料集執行 R 指令碼的模組。*

####<a name="check-on-the-data"></a>查看資料

讓我們來看看我們的實驗有載入的資料。 在實驗中，按一下 [ **cadairydata.csv 資料集**的輸出上，選取 [**以視覺化方式呈現**。 您應該會看到類似圖 4。  

![Cadairydata.csv 資料集的摘要][4]

*圖 4。Cadairydata.csv 資料集的摘要。*

在此檢視中，我們會看到許多有用的資訊。 我們可以看到該資料集的前幾個資料列。 如果我們選取資料行，統計資料] 區段會顯示資料行的詳細資訊。 例如，功能類型列顯示我們 Azure 電腦學習 Studio 指派給資料行資料類型。 快速看起來像這樣是很好的有效檢查我們開始處理任何嚴重之前。

### <a name="first-r-script"></a>第一個 R 指令碼

現在就讓我們建立簡單第一個 R 指令碼試驗 Azure 電腦學習 Studio 中。 已建立並測試 RStudio 中的下列指令碼。  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    str(cadairydata)
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

現在我需要傳輸 Azure 電腦學習 Studio 這個指令碼。 我無法只剪下及貼上。 不過，在此情況下，我會轉接我的 R 指令碼透過 zip 檔案。

### <a name="data-input-to-the-execute-r-script-module"></a>若要執行 R 指令碼模組的資料輸入

讓我們來看看[執行 R 指令碼]的輸入[execute-r-script]模組。 我們會在此範例中讀取加州日常資料[執行 R 指令碼]至[execute-r-script]模組。  

有三種可能[執行 R 指令碼]輸入[execute-r-script]模組。 您可以使用下列任何一項或所有這些輸入，根據您的應用程式。 這也是使用可在讓沒有輸入 R 指令碼非常合理。  

讓我們來看看這些輸入，從左移到右邊的各個。 您可以看到的名稱，輸入的每個藉由將游標放在輸入及閱讀的工具提示。  

#### <a name="script-bundle"></a>指令碼套件

指令碼套件輸入可讓您在[執行 R 指令碼]傳遞 zip 檔案的內容[execute-r-script]模組。 您可以使用下列命令的其中一個閱讀將您的 R 程式碼的 zip 檔案的內容。

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [AZURE.NOTE] Azure 電腦學習會視為 zip 檔案中 src / 目錄，所以您要加上字首目錄同名的檔案名稱。 例如，如果 zip 包含的檔案`yourfile.R`和`yourData.rdata`zip 的根目錄，在您想要說明這些為`src/yourfile.R`和`src/yourData.rdata`時使用`source`和`load`。

我們已將新書中[載入資料集](#loading)的載入資料集。 當您建立並測試顯示上一節的 R 指令碼時，執行下列動作︰

1. 儲存到 R 指令碼。R 檔案。 我要撥號給我的指令碼檔 」 simpleplot。R 」。 以下是內容。

        ## Only one of the following two lines should be used
        ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
        ## If in RStudio, use the second line with read.csv()
        cadairydata <- maml.mapInputPort(1)
        # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
        str(cadairydata)
        pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
        ## The following line should be executed only when running in
        ## Azure Machine Learning Studio
        maml.mapOutputPort('cadairydata')

2.  建立 zip 檔案，並將您的指令碼複製到此 zip 檔案。 在 Windows 中，您可以以滑鼠右鍵按一下檔案，然後選取 [__傳送至__]，然後__壓縮的資料夾__。 這會建立新的 zip 檔案包含 「 simpleplot。R 」 檔案。

3.  將**資料集**在電腦學習 Studio 中，指定類型為**zip**檔案。 您現在應該會看到 zip 檔案中資料集。

4.  從拖放 zip 檔案拖曳到**毫升 Studio 畫布****資料集**。

5.  **Zip 資料**圖示的輸出連線至[執行 R 指令碼]的**指令碼套件**輸入[execute-r-script]模組。

6.  輸入`source()`您 zip 檔案的名稱將程式碼視窗[執行 R 指令碼]函數[execute-r-script]模組。 在這個案例中鍵入`source("src/simpleplot.R")`。  

7.  請確定您按一下 [**儲存**]。

完成這些步驟後，[執行 R 指令碼][execute-r-script]模組會執行 R 指令碼 zip 檔案中執行實驗時。 此時實驗看起來應該像圖 5。

![嘗試使用壓縮的 R 指令碼][6]

*圖 5。嘗試使用壓縮的 R 指令碼。*

#### <a name="dataset1"></a>Dataset1

您可以使用 Dataset1 輸入，將矩形資料表傳遞到 R 程式碼。 在 [簡單的指令碼`maml.mapInputPort(1)`函數從連接埠 1 讀取資料。 然後，此資料指派給您的程式碼中的 dataframe 變數名稱。 在簡單的指令碼的程式碼的第一行，請執行作業。

    cadairydata <- maml.mapInputPort(1)

按一下 [**執行**] 按鈕，以執行實驗。 執行完成時，按一下[執行 R 指令碼][execute-r-script]模組，然後按一下 [屬性] 窗格中的 [**檢視輸出記錄檔]** 。 在瀏覽器顯示 output.log 檔案的內容，會出現新頁面。 向下捲動時，您應該會看到如下所示的項目。

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

在頁面久是的詳細資訊，看起來會如下所示的資料行。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

結果是大多如預期地佔用 228 觀察與 dataframe 9 資料行。 我們可以看到的欄名、 R 資料類型和每一欄的範例。

> [AZURE.NOTE] 此相同的列印的輸出有方便地從 R 裝置的輸出[執行 R 指令碼][execute-r-script]模組。 將討論[執行 R 指令碼]的輸出[execute-r-script]模組中下一節。  

####<a name="dataset2"></a>Dataset2

Dataset2 輸入的行為是 Dataset1 相同。 使用此輸入您可以將矩形第二個資料表的傳入 R 程式碼。 函數`maml.mapInputPort(2)`，與引數 2，用來傳遞此資料。  

###<a name="execute-r-script-outputs"></a>執行 R 指令碼輸出

####<a name="output-a-dataframe"></a>輸出 dataframe

您也可以使用為結果 Dataset1 連接埠矩形表格輸出的 R dataframe 內容`maml.mapOutputPort()`函數。 簡單的 R 指令碼在這是由下列行執行。

    maml.mapOutputPort('cadairydata')

執行之後實驗，結果 Dataset1 輸出連接埠上按一下，然後按一下**視覺效果**上。 您應該會看到類似圖 6。

![加州日常資料的輸出的視覺效果][7]

*圖 6。加州日常資料的輸出的視覺效果。*

此輸出看起來相同，輸入，我們預期一樣。  

### <a name="r-device-output"></a>R 裝置輸出

[執行 R 指令碼]的裝置輸出[execute-r-script]模組包含郵件及圖形輸出。 兩個標準輸出和標準錯誤訊息會傳送到 R 裝置輸出連接埠。  

若要檢視 R 裝置輸出，按一下 [連接埠，然後在 [**視覺效果**]。 我們會看到的標準輸出及圖 7 R 指令碼的標準誤差。

![標準輸出] 和 [R 裝置連接埠的標準誤差][8]

*圖 7。標準輸出和從 R 裝置連接埠的標準誤差。*

我們向下捲動，請參閱在 [圖 8 我們 R 指令碼的圖形輸出。  

![從 R 裝置連接埠輸出的圖形][9]

*圖 8。從 R 裝置連接埠輸出的圖形。*  

##<a id="filtering"></a>資料篩選及轉換

此區段中，我們會執行一些基本篩選的資料和加州日常資料轉換作業。 本節的結尾，就必須資料適用於建立的分析的模型格式。  

更明確地說，此區段中我們將會執行幾個常見資料清理及轉換工作︰ 輸入轉換，dataframes，新增新的計算資料行的篩選和轉換的值。 此背景應能助您處理多種實際的問題進行疑難排解。

本節的完整 R 程式碼位於您先前已下載的 zip 檔案中。

### <a name="type-transformations"></a>類型轉換

現在，我們可以讀取加州日常資料到[執行 R 指令碼]R 程式碼[execute-r-script]模組，我們需要以確保資料行中的資料具有預定的類型和格式。  

R 是動態輸入的語言]，這表示的資料類型會強制的方式視需要。 原子中的資料類型 R 包含數值、 邏輯和字元。 因子變異數類型用來簡潔儲存分類資料。 您可以在[附錄 B，進一步閱讀](#appendixb)參照尋找更多有關資料類型。

將外部來源的 R 讀取表格式資料時，一律是建議您先檢查資料行中的結果類型。 您可以輸入字元的欄，但在許多情況下這會顯示為因數或反向操作。 在您認為應該是資料行的其他情況下數值會以字元資料，例如 「 1.23 的 」，而非 1.23 為浮點數字。  

所幸，您可以輕鬆成另一種類型，只要對應可能。 例如，您無法將 'Nevada' 轉換成數字的值，但您可以將其轉換成因子變異數 （分類變數）。 另一個範例，您可以將數字 1 轉換成字元 '1' 或因子變異數。  

任何這些轉換的語法是簡單︰ `as.datatype()`。 這些類型轉換函數包含下列項目。

* `as.numeric()`

* `as.character()`

* `as.logical()`

* `as.factor()`

查看我們前一節中的 [輸入的資料行的資料類型︰ 所有資料行皆為數值，除了標記為 「 月份 」，這是輸入字元的欄位類型。 現在就讓我們轉換為因子變異數，並測試結果。  

我已經刪除的建立 scatterplot 矩陣和新增線條的 'Month] 資料行轉換為線條。 在 [我的實驗我將只剪下並 R 程式碼貼入[執行 R 指令碼]的程式碼視窗[execute-r-script]模組。 您也可以更新 zip 檔案上, 傳至 Azure 電腦學習 Studio，但這需要幾個步驟。  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(cadairydata$Month)
    str(cadairydata) # Check the result
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

讓我們來執行這個程式碼，然後查看 [R 指令碼的輸出記錄。 從記錄檔的相關資料會顯示在圖 9。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*圖 9。因子變異數變數 dataframe 的摘要。*

月份的類型現在應該 '**因子變異數，以 14 層級**]。 這是問題，因為年度中有只 12 個月。 您也可以檢查結果集連接埠的**視覺效果**類型為 「**Categorical**」。

是的 'Month] 資料行具有不系統化地撰寫。 在某些情況下一個月稱為年 4 月，其他人在它縮寫為四月。我們可以修整 3 個字元的字串，以解決這個問題。 程式碼的行現在看起來如下所示︰

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

重新執行實驗，並檢視輸出記錄。 預期的結果會顯示在 [圖 10。  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*圖 10。使用正確的因素層級數目 dataframe 的摘要。*

因子變異數變數現在有所需的 12 程度。

###<a name="basic-data-frame-filtering"></a>基本資料框架篩選

R dataframes 支援功能強大的篩選功能。 使用邏輯篩選列或欄資料集可以內嵌。 在許多情況下，將會需要複雜的準則。 在[附錄 B，進一步閱讀](#appendixb)參照包含篩選 dataframes 的更多範例。  

有一位元的篩選我們應該如何在資料集。 如果您看看 cadairydata dataframe 中的欄，您會看到兩個不需要的欄。 第一欄只保留資料列數字，不是很有用。 第二個資料行，Year.Month，包含重複的資訊。 我們可以輕鬆地使用下列 R 程式碼中排除這些資料行。

> [AZURE.NOTE] 現在在此區段中，我只會帶您我在[執行 R 指令碼]加入額外的程式碼[execute-r-script]模組。 將每個新行**之前**`str()`函數。 我可以使用此函數來驗證我 Azure 電腦學習 Studio 中的結果。

將下列行新增至我的 R 程式碼中[執行 R 指令碼][execute-r-script]模組。

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

執行這個程式碼實驗中，並檢查結果的輸出記錄。 圖 11，會顯示下列結果。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*圖 11。含有兩個欄 dataframe 的摘要中移除。*

好 ！ 我們取得預期的結果。

###<a name="add-a-new-column"></a>新增欄

若要建立時間數列模型會方便包含時間序列啟動之後的月份的資料行。 我們會建立新的資料行 'Month.Count'。

若要協助您組織的程式碼，我們將會建立我們的第一份簡單函數`num.month()`。 此外，我們就會套用此函數來建立新的資料行中 dataframe。 新的程式碼如下所示。

    ## Create a new column with the month count
    ## Function to find the number of months from the first
    ## month of the time series
    num.month <- function(Year, Month) {
      ## Find the starting year
      min.year  <- min(Year)

      ## Compute the number of months from the start of the time series
      12 * (Year - min.year) + Month - 1
    }

    ## Compute the new column for the dataframe
    cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

立即執行更新的實驗，並使用輸出記錄來檢視結果。 這些結果會顯示在圖 12。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*圖 12。與其他資料行 dataframe 的摘要。*

看來一切運作。 我們在我們 dataframe 有新的資料行與預期的值。

###<a name="value-transformations"></a>值轉換

此區段中我們會執行一些簡單的轉換部分我們 dataframe 的資料行中的值。 R 語言支援幾乎任意值轉換。 在[附錄 B，進一步讀取](#appendixb)參照包含更多範例。

如果您看看我們 dataframe 的摘要中的值應該會在此看到奇數頁的項目。 更多冰淇淋比牛奶產生加州嗎？ 否，當然，為此沒有任何意義，sad 為這項事實可能有些人冰淇淋能。 單位會不同。 價格為單位我們磅牛奶為單位的 1m 美國磅冰淇淋中 1000 的單位我們加侖，而小屋凸圓會 1000 美國磅為單位。 假設冰淇淋衡量關於 6.5 磅每加侖，我們可以輕鬆地執行乘法轉換這些值，使其在等於 1000 磅單位。

我們預測的模型中，我們會使用乘法模型趨勢和季節性調整此資料。 記錄轉換可讓我們使用線性的模型，以簡化此程序。 我們可以套用相同的函數，加成套用的位置中的記錄轉換。

在下列程式碼中，定義新的函數， `log.transform()`，並將其套用到包含數值的列。 R`Map()`函數用於套用`log.transform()`dataframe 的所選資料行的函數。 `Map()`類似`apply()`但可讓函數的引數的一個以上的清單。 請注意清單有提供的第二個引數`log.transform()`函數。 `na.omit()`函數用於清理有點確保我們沒有 dataframe 中遺失或未定義的值。

    log.transform <- function(invec, multiplier = 1) {
      ## Function for the transformation, which is the log
      ## of the input value times a multiplier

      warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                           "ERROR: Arguments to function log.transform must be greate than zero",
                           "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                           "ERROR: Invalid time seies value encountered in function log.transform"
                           )

      ## Check the input arguments
      if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
      if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
      if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

      ## Wrap the multiplication in tryCatch
      ## If there is an exception, print the warningmessage to
      ## standard error and return NA
      tryCatch(log(multiplier * invec),
               error = function(e){warning(warningmessages[4]); NA})
    }


    ## Apply the transformation function to the 4 columns
    ## of the dataframe with production data
    multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
    cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

    ## Get rid of any rows with NA values
    cadairydata <- na.omit(cadairydata)  

有中的很元發生`log.transform()`函數。 大部分的此程式碼會檢查之引數的潛在問題的例外狀況，仍會計算期間發生的處理。 只需幾行此程式碼的實際進行計算。

防衛程式設計的目標是函數的要防止失敗的單一，以避免無法繼續處理。 長時間執行分析突然失敗可能很令使用者。 若要避免這種情況下，預設傳回的值必須選擇，將會限制下游處理損壞跡象。 項目已經錯誤的通知使用者，也會產生一則訊息。

如果您不是使用防衛程式設計中 R，此所有的程式碼看起來似乎有點可觀。 我會引導您執行的主要步驟︰

1. 定義的四個訊息向量。 這些郵件用來傳達一些可能的錯誤和可能導因於此程式碼的例外狀況的相關資訊。

2. 傳回每個案例的 NA 的值。 有許多其他可能會有較少的側邊效果的可能性。 我無法傳回的零，向量或原始的輸入的向量，例如。

3. 檢查執行函數之引數。 一種情況，如果偵測錯誤，則會傳回預設值，並郵件產生的`warning()`函數。 我使用的`warning()`而非`stop()`為後者會終止執行時，完全什麼我試著避免。 請注意，我有寫入此程式碼中的程序的樣式，在此情況下似乎複雜且遮住功能性方法。

4. 記錄計算包裝在`tryCatch()`以便例外狀況不會導致突然停止處理。 不含`tryCatch()`引發的停駐點訊號，僅具有 R 函數結果的大部分錯誤。

執行此 R 程式碼實驗中，而且有 output.log 檔案中的列印輸出看。 現在，如下圖所示圖 13] 中，您會看到在記錄中的四個資料行轉換後的值。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*圖 13。已轉換的值中 dataframe 摘要。*

我們會看到已轉換的值。 現在牛奶生產大幅超過所有其他日常產品生產，恢復我們現在尋找記錄的縮放比例。

現在清除我們的資料，我們已可供某些模型。 查看摘要結果集輸出的視覺效果的[執行 R 指令碼]我們[execute-r-script]模組，您會看到的 'Month] 資料行是 'Categorical' 12 的唯一值，一次，就像我們想。

##<a id="timeseries"></a>時間數列物件及相互關聯分析

此區段中，我們會探索一些基本 R 時間數列物件，並分析變數部分之間的關聯性。 我們的目標是輸出 dataframe，包含數個 lags 成對的相關資訊。

本節的完整 R 程式碼是您先前已下載的 zip 檔案中。

###<a name="time-series-objects-in-r"></a>R 時間數列物件

以已經提及、 時間數列是一系列的時間編製索引的資料值。 R 時間數列物件用來建立及管理時間索引。 有幾項優點使用時間數列物件。 時間數列物件免費您管理時間序列索引值會封裝物件中的許多的詳細資料。 此外，時間數列物件讓您使用多個時間數列方法來繪製，列印模型等。

POSIXct 時間系列課程常用，而是相當簡單。 此時間序列類別的期間，1970 年 1 月 1 日開始的時間量值。 在此範例中，我們將使用 POSIXct 時間數列物件。 其他常用的 R 時間數列物件類別包含動物園 xts，可延伸時間序列。
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>時間數列物件範例

現在就讓我們開始使用我們的範例。 拖放**新**[執行 R 指令碼][execute-r-script]到實驗的模組。 將現有的[執行 R 指令碼]的結果 Dataset1 輸出連接埠[execute-r-script]模組至 Dataset1 輸入新的[執行 R 指令碼]的連接埠[execute-r-script]模組。

我在第一個範例中，介紹範例所示，在某些點我會顯示只累加其他 R 程式碼行的每一個步驟。  

####    <a name="reading-the-dataframe"></a>閱讀 dataframe

為第一個步驟中，現在就讓我們閱讀 dataframe 中，請確認您得到預期的結果。 下列程式碼應該執行的工作。

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

現在，請執行實驗。 新的執行 R 指令碼圖形的登入看起來應該像圖 14。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*圖 14。在 [執行 R 指令碼模組中 dataframe 的摘要。*

此資料為預期的類型和格式。 請注意，'Month] 資料行類型因子變異數為具有預期的階層數。

####<a name="creating-a-time-series-object"></a>建立時間數列物件

我們需要將時間數列物件加入我們 dataframe。 以下列，新增一個欄位的類別 POSIXct 取代目前的程式碼。

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

現在，核取登入。 它看起來應該像圖 15。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*圖 15。使用時間數列物件 dataframe 的摘要。*

我們可以看到從類別 POSIXct 實際上是新的資料行的摘要。

###<a name="exploring-and-transforming-the-data"></a>探索及轉換資料

現在就讓我們探索一些在此資料集中的變數。 Scatterplot 矩陣是產生快速檢視的好方法。 我正在取代`str()`函數中的下列行與先前的 R 程式碼。

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

執行這個程式碼，請參閱會發生什麼情況。 R 裝置連接埠所產生的繪圖看起來應該像圖 16。

![選取變數的 Scatterplot 矩陣圖][17]

*圖 16。選取變數 Scatterplot 矩陣。*

有一些奇怪結構關聯之間這些變數。 可能發生的資料的趨勢和事實上，我們不標準化變數。

###<a name="correlation-analysis"></a>相互關聯分析

若要執行相互關聯分析，我們需要取消趨勢和 standardize 變數。 我們無法使用 R`scale()`函數、 置中對齊和調整變數。 此函數也可能會執行速度更快。 不過，我要顯示您的防衛 programing 範例中。

`ts.detrend()`下方所顯示的函數執行這兩種作業。 下列程式碼的兩行就能取消趨勢資料，然後標準化的值。

    ts.detrend <- function(ts, Time, min.length = 3){
      ## Function to de-trend and standardize a time series

      ## Define some messages if they are NULL  
      messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                    'ERROR: ts.detrend requires argument ts to be of type numeric',
                    paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                    'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                    'ERROR: Detrend regression has failed in ts.detrend',
                    'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
    )
      # Create a vector of zeros to return as a default in some cases
      zerovec  <- rep(length(ts), 0.0)

      # The input arguments are not of the same length, return ts and quit
      if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

      # If the ts is not numeric, just return a zero vector and quit
      if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

      # If the ts is too short, just return it and quit
      if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

      ## Check that the Time variable is of class POSIXct
      if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

      ## De-trend the time series by using a linear model
      ts.frame  <- data.frame(ts = ts, Time = Time)
      tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
               error = function(e){warning(messages[5]); zerovec})

      tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
                 ts <- ts/stdev},
                error = function(e){warning(messages[6]); zerovec})

      ts
    }  
    ## Apply the detrend.ts function to the variables of interest
    df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

    ## Plot the results to look at the relationships
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

有中的很元發生`ts.detrend()`函數。 大部分的此程式碼會檢查之引數的潛在問題的例外狀況，仍會計算期間發生的處理。 只需幾行此程式碼的實際進行計算。

我們已討論如何防衛程式設計[值轉換](#valuetransformations)的範例。 兩個計算區塊包裝在`tryCatch()`。 某些錯誤合理回到原始的輸入的向量，並在其他情況下，傳回的零向量。  

請注意，就能取消感所用的線性迴歸時間數列迴歸分析。 預測變數是時間數列物件。  

一次`ts.detrend()`定義我們將其套用至我們 dataframe 感興趣的變數。 我們必須強制所建立的結果清單`lapply()`要使用的資料 dataframe `as.data.frame()`。 由於防衛層面`ts.detrend()`，其中一個變數的程序失敗不會防止其他人的正確處理。  

最後一列的程式碼會建立成對的 scatterplot。 執行之後 R 程式碼，scatterplot 的結果會顯示在圖 17。

![成對 scatterplot 的時間就能取消趨勢和標準化序列][18]

*圖 17。成對的時間就能取消趨勢和標準化序列 scatterplot。*

您可以比較這些圖 16] 中顯示下列結果。 移除趨勢及標準化的變數，我們可以看到這些變數之間的關聯性中更少結構。

若要計算為 R ccf 物件，相互關聯的程式碼如下所示。

    ## A function to compute pairwise correlations from a
    ## list of time series value vectors
    pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
      ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
    }

    ## A list of the pairwise indices
    corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

    ## Compute the list of ccf objects
    cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

    cadairycorrelations

執行這個程式碼會產生圖 18 中顯示的記錄檔。

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*圖 18。從成對相互關聯分析的物件 ccf 的清單。*

有每個延隔時間的相關值。 沒有夠大，無法重要這些相關值。 因此，我們可以推斷我們可以獨立模型每個變數。

###<a name="output-a-dataframe"></a>輸出 dataframe

我們已為 R ccf 物件清單計算成對的關聯性。 結果集輸出連接埠真的需要 dataframe，這樣的問題。 此外，ccf 物件本身清單，而我們想只有值第一個項目] 清單中，在各種 lags 的相互關聯。

下列程式碼會起 ccf 物件本身就是清單的清單中的延隔時間值。

    df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

    c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
    r.names  <- c("Corr Cot Cheese - Ice Cream",
                  "Corr Cot Cheese - Milk Prod",
                  "Corr Cot Cheese - Fat Price",
                  "Corr Ice Cream - Mik Prod",
                  "Corr Ice Cream - Fat Price",
                  "Corr Milk Prod - Fat Price")

    ## Build a dataframe with the row names column and the
    ## correlation data frame and assign the column names
    outframe <- cbind(r.names, df.correlations)
    colnames(outframe) <- c.names
    outframe


    ## WARNING!
    ## The following line works only in Azure Machine Learning
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

程式碼的第一行較難，而某些說明可協助您瞭解。 我們使用從 inside out 有下列動作︰

1.  「**[[**」 運算子的引數 '**1**' ccf 的物件] 清單中的第一個項目從選取的關聯性在 lags 向量。

2.  `do.call()`函數適用於`rbind()`清單的項目上的函數傳回`lapply()`。

3.  `data.frame()`函數會強制變成所產生的結果`do.call()`至 dataframe。

請注意，列名稱 dataframe 一欄。 在[執行 R 指令碼]的輸出時執行，因此保留資料列名稱[execute-r-script]。

執行程式碼會產生圖 19 中所顯示的輸出時我**視覺效果**的輸出結果集連接埠。 列名稱會在第一欄中，如預期般。

![從相互關聯分析輸出的結果][20]

*圖 19。輸出相互關聯分析的結果。*

##<a id="seasonalforecasting"></a>時間的數列範例︰ 季節性預測

我們的資料現在適用於分析表單中，而我們判定有變數之間沒有有效的關聯性。 現在就讓我們上移動，並建立時間序列預測模型。 我們會使用此模型預測加州牛奶生產 12 個月的 2013年。

我們預測模型會有兩個元件、 趨勢元件和季節性元件。 完成的預測有下列兩個元件。 此類型的模式就是乘法的模型。 替代方案是累加的模型。 我們已套用記錄轉換讓此分析容易處理感興趣的變數。

本節的完整 R 程式碼是您先前已下載的 zip 檔案中。

### <a name="creating-the-dataframe-for-analysis"></a>建立分析 dataframe

新增**新**[執行 R 指令碼]來[execute-r-script]實驗模組。 將現有的[執行 R 指令碼]的**結果集**輸出[execute-r-script] **Dataset1**輸入新的模組的模組。 結果看起來應該像圖 20。

![新增新的執行 R 指令碼模組實驗][21]

*圖 20。新增新的執行 R 指令碼模組實驗。*

與相互關聯分析完成，我們需要將具有 POSIXct 時間數列物件的欄。 下列程式碼會執行此。

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

執行這個程式碼，並查看的記錄檔。 結果看起來應該像圖 21。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*圖 21。Dataframe 的摘要。*

此結果，我們已準備好開始我們分析項目。

###<a name="create-a-training-dataset"></a>建立訓練資料集

建構 dataframe 我們需要建立訓練資料集。 此資料會包含所有 2013年一年中的觀察值以外的最後一個 12，這是測試資料集。 下列程式碼子集 dataframe 並建立繪圖的日常生產與價格的變數。 我建立繪圖的四個生產然後價格的變數。 匿名函式用來定義的圖表，某些加強，然後逐一查看其他兩個引數清單`Map()`。 如果您的想法的循環播放想過要這裡，您是否正確。 不過，R 是功能語言我有顯示功能的方法。

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

執行程式碼會產生的數列繪製 R 裝置輸出圖 22 中顯示的時間的數列。 請注意，時間座標軸單位的日期數列繪製方法時間部份的優點。

![加州日常生產和價格資料的時間數列繪圖中的第一個](./media/machine-learning-r-quickstart/unnamed-chunk-161.png)

![第二個時間數列繪圖加州日常生產和價格資料](./media/machine-learning-r-quickstart/unnamed-chunk-162.png)

![第三個時間數列繪圖加州日常生產和價格資料](./media/machine-learning-r-quickstart/unnamed-chunk-163.png)

![第四個的時間數列繪圖加州日常生產和價格資料](./media/machine-learning-r-quickstart/unnamed-chunk-164.png)

*圖 22。時間數列繪製加州日常生產和價格資料。*

### <a name="a-trend-model"></a>趨勢模型

無法建立時間數列物件並有有資料看，現在就讓我們開始建構加州牛奶生產資料的趨勢模型。 使用時間數列迴歸，我們可以執行此動作。 不過，它是我們會需要更多斜率，並以正確地建立模型訓練資料中的觀察的趨勢 intercept 繪圖中的 [清除。

指定資料的小小數位數，我會建立模型中 RStudio 趨勢，然後剪下並貼入產生模型 Azure 電腦學習。 RStudio 提供此類型的互動式分析的互動式環境。

為在第一次嘗試時，我會嘗試使用力量進位到 3 的多項式迴歸分析。 有的過度放置這些類型的模型真實風險。 因此，最好避免高順序字詞。 `I()`函數禁止的內容解譯 （解譯內容 「 現狀 」），讓您撰寫迴歸方程式如實解譯的函數。

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

這會產生下列。

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

從 P 值 (Pr (> | t |)) 在此輸出中，我們可以看到平方的字詞可能不是有效。 會使用`update()`拖放平方的字詞來修改此模型的函數。

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

這會產生下列。

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

看起來更好。 條款都有效。 不過，2e 16 值為預設值，並不應該也會採取。  

為有效測試，讓我們來進行加州日常生產資料的時間數列繪圖與趨勢曲線顯示。 我已新增下列程式碼中 Azure 電腦學習[執行 R 指令碼][execute-r-script]模型 (不 RStudio)，建立模型，並讓繪圖。 結果會顯示在圖 23。

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![加州牛奶生產資料趨勢模型顯示](./media/machine-learning-r-quickstart/unnamed-chunk-18.png)

*圖 23。加州牛奶生產趨勢模型顯示資料。*

看來很不錯的趨勢模型符合的資料。 此外，那里似乎不會辨識項過度調整，例如奇數 wiggles 模型曲線。  

###<a name="seasonal-model"></a>季節性模型

在手趨勢模型，我們需要推包含季節性效果。 我們會為空變數線性的模型中使用年的月份，擷取依月份效果。 請注意，當您為模型介紹因子變異數變數，截距必須計算。 如果您不要這麼做，過度指定公式和 R 會放的所需的因素之一，但保留截距字詞。

因為滿意趨勢模型我們可以使用`update()`函數，將字詞新增至現有的模型。 -1 更新公式中的卸除截距字詞。 繼續 RStudio 中的時間︰

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

這會產生下列。

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,   Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

我們會看到的模型不再有截距字詞，而且有 12 嚴重月因素。 這只是想要查看。

現在讓我們建立另一個時間數列繪圖加州日常生產資料的季節性模型看看能夠運作。 我已新增下列程式碼中 Azure 電腦學習[執行 R 指令碼][execute-r-script]可建立模型和繪圖。

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

在 Azure 電腦學習執行這個程式碼會顯示在圖 24 繪圖。

![加州牛奶生產包括季節性效果的模型](./media/machine-learning-r-quickstart/unnamed-chunk-20.png)

*圖 24。加州牛奶生產包括季節性效果的模型。*

調整成圖 24 中顯示的資料是而鼓勵。 趨勢和季節性效果 （也就是每月變化） 看起來合理。

在我們的模型的其他檢查，讓我們來看看餘。 下列程式碼會計算兩個模型中的預測的值、 計算餘季節性的模型，然後繪製這些餘訓練資料。

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

剩餘的繪圖會顯示在圖 25。

![殘差的季節性訓練資料模型](./media/machine-learning-r-quickstart/unnamed-chunk-21.png)

*圖 25。殘季節性訓練資料模型的差。*

這些餘尋找合理。 有無特定的結構，除了 2008年 2009年經歷，我們的模型並未考量其中的效果特別好。

顯示在圖 25 繪圖]，適用於在餘偵測任何與時間相關的模式。 計算和繪製餘我使用的明確的方式放置餘時間順序在繪圖上。 如果相反地，我已繪製`milk.lm$residuals`，繪圖就沒有時間順序。

您也可以使用`plot.lm()`來產生一系列的診斷繪圖。

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

將此程式碼會產生一系列的診斷圖 26 所示的繪圖。

![診斷繪圖季節性模型中的第一個](./media/machine-learning-r-quickstart/unnamed-chunk-221.png)

![第二個診斷繪圖季節性模型](./media/machine-learning-r-quickstart/unnamed-chunk-222.png)

![第三個季節性模型診斷繪圖](./media/machine-learning-r-quickstart/unnamed-chunk-223.png)

![第四個的季節性模型診斷繪圖](./media/machine-learning-r-quickstart/unnamed-chunk-224.png)

*圖 26。診斷繪製季節性模型。*

有幾個高度有影響力的點識別這些圖，但不會導致相當大的問題。 此外，我們可以看到從標準問問繪製餘會以正常方式散發線性模型的重要假設關閉。

###<a name="forecasting-and-model-evaluation"></a>預測和模型評估

還有一件事方式若要完成我們的範例。 我們需要計算預測和實際資料測量錯誤。 12 個月的 2013年會我們預測。 我們可以計算實際資料的不是我們訓練資料集的一部分此預測錯誤量的值。 此外，我們可以比較 18 年的訓練資料測試資料的 12 個月的效能。  

指標的數字用來衡量時間數列模型的效能。 在此例中，我們將使用均方根 (RMS) 錯誤。 下列函數會計算兩個數列之間 RMS 錯誤。  

    RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
      ## Function to compute the RMS error or difference between two
      ## series or vectors

      messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                    "ERROR: Input vector to function RMS.error is too short",
                    "ERROR: Input vectors to function RMS.error must be of same length",
                    "WARNING: Funtion rms.error has received invald input time series.")

      ## Check the arguments
      if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
        warning(messages[1])
        return(NA)}

      if(length(series1) < min.length) {
        warning(messages[2])
        return(NA)}

      if((length(series1) != length(series2))) {
        warning(messages[3])
        return(NA)}

      ## If is.log is TRUE exponentiate the values, else just copy
      if(is.log) {
        tryCatch( {
          temp1 <- exp(series1)
          temp2 <- exp(series2) },
          error = function(e){warning(messages[4]); NA}
        )
      } else {
        temp1 <- series1
        temp2 <- series2
      }

     ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute the RMS error in a dataframe
      tryCatch( {
        sqrt(sum((temp1 - temp2)^2) / length(temp1))},
        error = function(e){warning(messages[4]); NA})
    }

使用`log.transform()`函數我們所述的 「 Value 轉換 」 區段中，有很多檢查及例外狀況復原中的錯誤碼此函數。 採用的原則都相同。 在兩個位置的工作完成`tryCatch()`。 首先，時間序列都是 exponentiated，因為我們已工作記錄的值。 第二，計算實際 RMS 錯誤。  

配備測量 RMS 錯誤的函數，讓我們來建立和輸出 dataframe，包含 RMS 錯誤。 我們會包含趨勢模型單獨的條款與季節性因素完成的模型。 下列程式碼會使用我們建構的兩個線性模型的工作。

    ## Compute the RMS error in a dataframe
    ## Include the row names in the first column so they will
    ## appear in the output of the Execute R Script
    RMS.df  <-  data.frame(
    rowNames = c("Trend Model", "Seasonal Model"),
      Traing = c(
      RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
      RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
      Forecast = c(
        RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
        RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
    )
    RMS.df

    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('RMS.df')

執行這個程式碼會產生結果集輸出連接埠圖 27 所顯示的輸出。

![模型 RMS 錯誤的比較][26]

*圖 27。模型 RMS 錯誤的比較。*

從這些結果，我們可以看到的模型中加入季節性因素會減少 RMS 錯誤大幅。 太當然 RMS 錯誤訓練資料是位元小於的預測。

##<a id="appendixa"></a>RStudio 附錄 a︰ 指南

良好記錄 RStudio，所以本附錄要提供一些重要的章節，協助您入門的 RStudio 文件的連結。

1.  建立專案

    您可以將組織使用及管理 R 程式碼到專案 RStudio。 使用專案的文件位於 https://support.rstudio.com/hc/articles/200526207-Using-Projects。

    建議您，請遵循這些指示，並建立 R 程式碼範例專案文件中。  

2.  編輯和執行 R 程式碼

    RStudio 提供整合式的環境，編輯及執行 R 程式碼。 文件位於 https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code。

3.  偵錯

    RStudio 包含強大的偵錯功能。 文件，這些功能位於 https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio。

    在 https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting 說明中斷點疑難排解功能。

##<a id="appendixb"></a>深入閱讀本文件隨附

此 R 程式設計教學課程涵蓋了您要使用 Azure 電腦學習 Studio R 語言的基本概念。 如果您不熟悉 R，兩個簡介上都提供 CRAN:

- 以 Emmanuel Paradis 初學者 R 是從開始 http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf 的好地方。  

- 工時 n，R 簡介 Venables set。 al。 更多深度，在 http://cran.r-project.org/doc/manuals/R-intro.html 進入。

可協助您快速入門的 R 上有多個活頁簿。 以下是的一些找到很有用︰

- R 程式設計的美工圖案︰ 導覽的統計軟體設計來諾曼 Matloff 是簡介程式設計中。  

- R 食譜 Paul Teetor 所提供的問題和解決方案的方式，使用。  

- Robert Kabacoff 採取動作中的 R 是另一個實用的簡介活頁簿。 小幫手] 快速 R 網站是在 http://www.statmethods.net/ 實用的資源。

- R 煉獄主文張靜止，是相當幽默活頁簿，可在程式設計中 r 時遇到困難且困難主題的數字，處理活頁簿的免費 http://www.burns-stat.com/documents/books/the-r-inferno/。

- 如果您想將進階主題 R 深度剖析，看看進階 R Hadley Wickham 的活頁簿。 此活頁簿的線上版本的免費 http://adv-r.had.co.nz/。

時間序列分析 CRAN 任務檢視中找 R 時間數列套件的具有權威︰ http://cran.r-project.org/web/views/TimeSeries.html。 關於特定時間數列物件封包，您應該參考套件的文件。

書籍與 Paul Cowpertwait 並為 Andrew Metcalfe R 簡介時間序列提供使用 R 時間數列分析的簡介。 許多其他理論文字提供 R 範例。

某些很棒的網際網路資源︰

- DataCamp: DataCamp 指導 R 的視訊教學單元與編碼練習瀏覽器中。 有最新的 R 技巧和套件的互動式教學課程。 在 https://www.datacamp.com/courses/introduction-to-r 取得免費的互動式 R 教學課程  

- 快速 R 教學課程以 Kelly 黑色從 Clarkson 大專院校版 http://www.cyclismo.org/tutorial/R/

- 列在 http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html 60 + R 資源

<!--Image references-->
[1]: ./media/machine-learning-r-quickstart/fig1.png
[2]: ./media/machine-learning-r-quickstart/fig2.png
[3]: ./media/machine-learning-r-quickstart/fig3.png
[4]: ./media/machine-learning-r-quickstart/fig4.png
[5]: ./media/machine-learning-r-quickstart/fig5.png
[6]: ./media/machine-learning-r-quickstart/fig6.png
[7]: ./media/machine-learning-r-quickstart/fig7.png
[8]: ./media/machine-learning-r-quickstart/fig8.png
[9]: ./media/machine-learning-r-quickstart/fig9.png
[10]: ./media/machine-learning-r-quickstart/fig10.png
[11]: ./media/machine-learning-r-quickstart/fig11.png
[12]: ./media/machine-learning-r-quickstart/fig12.png
[13]: ./media/machine-learning-r-quickstart/fig13.png
[14]: ./media/machine-learning-r-quickstart/fig14.png
[15]: ./media/machine-learning-r-quickstart/fig15.png
[16]: ./media/machine-learning-r-quickstart/fig16.png
[17]: ./media/machine-learning-r-quickstart/fig17.png
[18]: ./media/machine-learning-r-quickstart/fig18.png
[19]: ./media/machine-learning-r-quickstart/fig19.png
[20]: ./media/machine-learning-r-quickstart/fig20.png
[21]: ./media/machine-learning-r-quickstart/fig21.png
[22]: ./media/machine-learning-r-quickstart/fig22.png

[26]: ./media/machine-learning-r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
