<properties 
    pageTitle="佈建 Microsoft 資料科學虛擬機器 |Microsoft Azure" 
    description="設定並建立資料科學虛擬機器上執行分析及電腦學習 Azure。" 
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
    ms.date="09/07/2016" 
    ms.author="bradsev" />


# <a name="provision-the-microsoft-data-science-virtual-machine"></a>佈建 Microsoft 資料科學虛擬機器

Microsoft 資料科學虛擬機器已預先安裝並設定數個常用的工具，常用的資料分析及電腦學習 Azure 虛擬機器 (VM) 圖像。 所包含的工具︰

- Microsoft R 伺服器開發人員版本
- Anaconda Python 通訊群組
- Jupyter 筆記本 （含 R，Python 核心)
- Visual Studio 社群版本
- Power BI desktop
- SQL Server 2016 開發人員版
- 電腦學習工具
    - [計算網路工具組 (CNTK)](https://github.com/Microsoft/CNTK): 學習軟體工具組，從 Microsoft Research 深度。
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit)︰ 快速電腦學習支援技巧，例如線上、 雜湊，allreduce、 大幅降低、 learning2search、 使用中時，系統與互動式學習。
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/)︰ 提供快速而正確提高樹狀實作的工具。
    - [祕](http://rattle.togaware.com/)（R 分析工具來瞭解輕鬆）︰ 可讓您快速入門資料分析及電腦學習中 R 簡單，GUI 為基礎的資料探索與模型與自動產生的 R 程式碼的工具。
    - [mxnet](https://github.com/dmlc/mxnet)︰ 專為效率和彈性的深學習架構
- R 和 Python 的文件庫中使用 Azure 電腦學習和其他 Azure 服務
- 給包括給艦隊與來源的程式碼存放庫包括 GitHub，Visual Studio 小組服務搭配使用
- Windows 的幾個常見 Linux 命令列公用程式 （包括 awk sed、 perl、 grep、 尋找、 wget、 捲曲等） 的連接埠透過命令提示字元。 


執行資料科學涉及重複上一系列的工作︰ 尋找、 載入，和前置處理資料，建立並測試模型，和部署消耗智慧應用程式中的模型。 資料科學家使用各種不同的工具來完成這些工作。 可能相當費時尋找適當的軟體、 版本及下載並安裝。 Microsoft 資料科學虛擬機器可以簡化負擔 Azure 上預先安裝並設定所有幾個的常用工具提供可以佈建準備使用圖像。 

Microsoft 資料科學虛擬機器 jump-starts 分析專案。 讓您處理包括 R、 Python、 SQL，及 C# 使用不同語言的任務。 Visual Studio 提供 IDE 開發及測試您很容易使用的程式碼。 Azure SDK VM 中包含可讓您建立 Microsoft 的雲端平台上使用不同的服務應用程式。 

不有任何軟體費用，此資料科學 VM 圖像。 您只支付 Azure 使用費用哪些依存於您佈建虛擬機器的大小。 在 [[資料科學虛擬機器](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)頁面上的 [價格詳細資料] 區段中找計算費用更多詳細資料。 


## <a name="prerequisites"></a>必要條件

您可以建立 Microsoft 資料科學虛擬機器之前，您必須具備下列項目︰

- **Azure 訂閱**︰ 若要取得一個，請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

*   **Azure 儲存體帳戶**︰ 若要建立一個，請參閱[建立 Azure 儲存體帳戶](storage-create-storage-account.md#create-a-storage-account)。 或者，可以建立存放區帳戶建立 VM，如果您不想要使用現有的帳戶的程序的一部分。


## <a name="create-your-microsoft-data-science-virtual-machine"></a>建立您的 Microsoft 資料科學虛擬機器

若要建立執行個體的 Microsoft 資料科學虛擬機器步驟如下︰

1.  瀏覽至虛擬機器[Azure 入口網站](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)上的清單。
2.   選取要納入精靈底部的 [**建立**] 按鈕。![設定-資料-科學-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.   用來建立 Microsoft 資料科學虛擬機器精靈需要**輸入**每個列舉右側此圖的**五個步驟**。 以下是設定每一個步驟所需的輸入︰
    
    1.   **基本概念**
        1.   **名稱**︰ 建立資料科學伺服器的名稱。
        2.   **使用者名稱**︰ 管理員帳戶登入 id。
        3.   **密碼**︰ 管理員帳戶的密碼。
        4.   **訂閱**︰ 如果您有多個訂閱，請選取其中一個所建立和帳單的電腦。
        5.   **資源群組**︰ 您可以建立新的或使用現有的群組。
        6.   **位置**︰ 選取最適合資料中心。 通常是資料中心大部分的資料或最接近您實際的位置，最快的網路存取。
         
    2.   **大小**︰ 選取其中一個符合您的功能需求和成本限制式伺服器類型]。 您可以取得更多選擇 VM 大小的選取 [檢視全部]。
    
    3.   **設定**︰
        1.   **磁碟類型**︰ 選擇 [進階版如果您喜歡 solid-state 的磁碟機 (SSD)，還選擇 [標準]。
        2.   **儲存帳戶**︰ 您可以在 [您的訂閱中建立新的 Azure 儲存體帳戶，或使用所選的同一個*位置*的現有在精靈的 [**基本**步驟。
        3.   **其他參數**︰ 通常是您剛才使用預設值。 您可以將游標停留在特定欄位的說明資訊的連結以避免您想要考慮使用非預設值。

    4.   **摘要**︰ 確認您輸入的所有資訊正確都無誤。
    5.   **購買**︰ 按一下 [啟動佈建的 [**購買**。 連結提供交易的條款。 VM 沒有任何額外的費用，除了您選擇在 [**大小**] 步驟中的伺服器大小的計算。 


>[AZURE.NOTE] 佈建應該需要花 10-20 分鐘。 佈建的狀態會顯示在 Azure 入口網站。

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>如何存取 Microsoft 資料科學虛擬機器

VM 建立後，您可以遠端桌面將它使用您在上的**基本概念**一節中設定的管理員帳戶認證。 

一旦您 VM 會建立並佈建後，您準備好開始使用安裝並設定其上的工具。 有一些開始功能表磚和桌面圖示的工具]。 

## <a name="how-to-create-a-strong-password-on-the-jupyter-notebook-server"></a>如何在 Jupyter 筆記本伺服器上建立強式密碼 

若要建立您自己的強式密碼 Jupyter 筆記本伺服器電腦上安裝，請執行下列命令在命令提示字元資料科學虛擬機器上。

    c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

選擇 [出現提示時強式密碼。

您會看到密碼雜湊 「 sha1:xxxxxx 」 的列印輸出格式。 複製此密碼雜湊，取代現有的筆記本設定檔案位於雜湊︰ 使用參數名稱***c.NotebookApp.password*** **C:\ProgramData\jupyter\jupyter_notebook_config.py** 。

取代 (xxxxxx) 部分引號中的現有雜湊值。 引號和***sha1:***兩者都要保留的參數值的前置字元。

最後，您需要停止然後再重新 Jupyter 伺服器，稱為**Start_IPython_Notebook**windows 排程工作 VM 上執行。 如果重新啟動此工作後不接受您的新密碼，則請嘗試刪除所有執行 python 處理序從 [工作管理員，並重新啟動排程的任務。 或者，請嘗試重新啟動虛擬機器。

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>安裝在 Microsoft 資料科學虛擬機器中的工具

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R 伺服器開發人員版本
如果您想要使用您的狀況分析 R，VM 已安裝 Microsoft R 伺服器開發人員版本。 Microsoft R 伺服器是根據 R 支援與可調整安全廣泛部署企業級分析平台。 支援各種不同的大型資料的統計資料、 建立預測模型及電腦學習功能，R 伺服器支援完整的分析 – 探索、 分析、 視覺效果和模型。 使用延伸開啟來源 R，Microsoft R 是伺服器的完整相容 R 指令碼、 函數與 CRAN 套件，以分析企業資料。 它也可以處理開啟來源 R 記憶體內限制藉由新增平行和區塊處理資料。 這可讓您執行遠大於什麼放入主記憶體的資料分析。  Visual Studio 社群版包含 VM 上包含 R 工具的 Visual Studio 副檔名，可提供完整的 IDE 使用。您也可以下載及使用其他 Ide 以及例如[RStudio](http://www.rstudio.com)。 

### <a name="python"></a>Python
使用 Python 開發，已安裝 Anaconda Python 分配 2.7 和 3.5。 此分配包含關於 300 個最常用的數學運算、 工程和資料分析封包以及基底 Python。 您可以使用 Python 工具的 Visual Studio (PTVS) 已安裝在 Visual Studio 2015 社群 edition 或其中一個 Ide 結合在一起使用 Anaconda 閒置或 Spyder 等。 您可以在其中一個，在 [搜尋] 列搜尋來啟動 (**Win** + **S**鍵)。

>[AZURE.NOTE] 若要指向 Anaconda Python 2.7 和 3.5 Visual studio Python 工具，您需要建立自訂的環境，每個版本。 若要設定這些環境路徑 Visual Studio 2015 社群版本中，瀏覽至 [**工具** -> **Python 工具** -> **Python 環境**，然後按一下 [ **+ 自訂**。 

Anaconda Python 2.7 安裝在 C:\Anaconda 和 Anaconda Python 3.5 安裝在 c:\Anaconda\envs\py35。 如需詳細步驟，請參閱[PTVS 文件](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)。 

### <a name="jupyter-notebook"></a>Jupyter 筆記本
Anaconda 分配也會隨附 Jupyter 筆記本中，若要共用的程式碼及分析的環境。 Jupyter 筆記本伺服器已預先設定 Python 2、 Python 3 與 R 核心。 桌面圖示名為 「 啟動的瀏覽器存取筆記本伺服器 Jupyter 筆記本。 如果您是透過遠端桌面 VM，您也可以瀏覽[https://localhost:9999 /](https://localhost:9999/)存取 Jupyter 筆記本伺服器時的 vm 登入。
 
>[AZURE.NOTE] 繼續如果您收到任何憑證警告。 

我們有封裝範例筆記本 Python 和中。Jupyter 筆記本會顯示如何使用 Microsoft R 伺服器、 SQL Server 2016 R Services （資料庫中分析）、 Python，以及其他 Azure 技術，當您登入 Jupyter。 使用您在先前的步驟建立密碼的 Jupyter 筆記本項目會驗證後，可以在筆記本的 [首頁] 頁面上看到範例的連結。 

### <a name="visual-studio-2015-community-edition"></a>Visual Studio 2015 社群版
VM 上所安裝的 visual Studio 社群版。 它是 microsoft，您可以使用評估之用，以及小型團隊熱門 IDE 免費版本。 您可以查看授權條款[以下](https://www.visualstudio.com/support/legal/mt171547)。  按兩下 [桌面] 圖示或**[開始**] 功能表開啟 Visual Studio。 您也可以搜尋程式**Win** + **S** ，輸入 「 Visual Studio 」。 一次那里您可以在中建立專案的語言，如 C# Python、 R，node.js。 外掛程式，也會安裝，會使其更方便搭配 Azure 資料目錄、 Azure HDInsight （Hadoop、 火花） 等 Azure 資料湖 Azure 服務。 

>[AZURE.NOTE] 您可能會收到訊息，指出您的試用期已過期。 輸入您的 Microsoft 帳戶認證或建立新可用來存取 Visual Studio 社群版帳戶。 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 開發人員版本
若要執行的資料庫分析 R 服務的 SQL Server 2016 的開發人員版本提供 VM 上。 R 服務可提供開發及部署智慧應用程式的平台。 您可以使用豐富且強大 R 語言和社群許多封裝建立模型，並產生的 SQL Server 資料的預測。 您可以將自己的資料分析，因為 R 服務 （資料庫） 與 SQL Server 整合 R 語言。 如此成本及移動資料的相關的安全性風險。

>[AZURE.NOTE] SQL Server 2016 開發人員 edition 只用於開發和測試之用。 您需要執行生產環境中的授權。 

您可以啟動**SQL Server Management Studio 中**，以存取 SQL server。 填入您 VM 名稱作為伺服器名稱。 使用 Windows 驗證時以 Windows 管理員身分登入。 當您在 SQL Server Management Studio 中您可以建立其他使用者、 建立資料庫、 匯入資料，並執行 SQL 查詢。 

若要啟用資料庫中分析使用 Microsoft R，請執行下列命令以一個時間在 SQL Server management studio 中伺服器的系統管理員的身分登入之後的動作。 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure 
VM 上安裝數個 Azure 工具︰

- 沒有存取 Azure SDK 的文件的桌面捷徑。 
- **AzCopy**︰ 用來移動資料與您的 Microsoft Azure 儲存體帳戶登出。 若要查看使用方式，請輸入**Azcopy**命令提示字元中，查看使用方式。 
- **Microsoft Azure 儲存檔案總管**︰ 用來瀏覽儲存 Azure 儲存體帳戶和傳送資料至和從 Azure 儲存體中的物件。 您可以在搜尋中輸入**儲存檔案總管**，或在 Windows [開始] 功能表，以存取這項工具上找到。 
- **Adlcopy**︰ 用來將資料移至 Azure 資料湖。 若要查看使用方式，請在命令提示字元輸入**adlcopy** 。 
- **dtui**︰ 用來將資料移動與 Azure DocumentDB，NoSQL 資料庫在雲端上。 在命令提示字元中，輸入**dtui** 。 
- **Microsoft 資料管理閘道**︰ 可讓內部部署資料來源與雲端之間移動資料。 Azure 資料工廠等工具中使用它。 
- **Microsoft Azure Powershell**︰ 用來管理中的 Powershell 指令碼語言也會安裝在您 VM Azure 資源的工具。 

###<a name="power-bi"></a>Power BI

若要協助您建立儀表板及很棒的視覺效果， **Power BI Desktop**已安裝。 使用此工具來擷取資料來自不同來源，撰寫您的儀表板和報表，並將其發佈至雲端。 如需的資訊，請參閱[Power BI](http://powerbi.microsoft.com)網站。 您可以在 [開始] 功能表上，找到 Power BI desktop。 

>[AZURE.NOTE] 您需要存取 Power BI 的 Office 365 帳戶。 

## <a name="additional-microsoft-development-tools"></a>更多 Microsoft 開發工具
[**Microsoft Web 平台安裝程式**](https://www.microsoft.com/web/downloads/platform.aspx)可用來探索及其他 Microsoft 開發工具下載。 也是 Microsoft 資料科學虛擬機器桌面上所提供的工具的快速鍵。  

## <a name="important-directories-on-the-vm"></a>VM 上的重要目錄

| 項目                          | 目錄 |
| ------------------------------| ---------------- |
|Jupyter 筆記本伺服器設定 | C:\ProgramData\jupyter |
|Jupyter 筆記本範例主目錄| c:\dsvm\notebooks|
|其他範例 | c:\dsvm\samples|
| Anaconda (預設︰ Python 2.7) | c:\Anaconda |
| Anaconda Python 3.5 環境 | c:\Anaconda\envs\py35|
|R 伺服器獨立執行個體目錄 （預設 R 執行個體） | C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| R 伺服器資料庫中執行個體目錄 | C:\Program Files\Microsoft SQL Server\MSSQL13。MSSQLSERVER\R_SERVICES |
| 其他工具 | c:\dsvm\tools|

>[AZURE.NOTE] 執行個體的 Microsoft 資料科學虛擬機器之前 1.5.0 （之前 2016 年 9 月 3 年)，建立用的位置略有不同的目錄結構，比上述表格中指定。 

## <a name="next-steps"></a>後續步驟
以下是您的學習及探索繼續下一個步驟。 

* 探索各種資料科學工具資料科學 VM 上，按一下 [開始] 功能表，然後取出功能表中列出的工具。
* 導覽至**C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts**範例使用支援的資料分析，在企業版的 R RevoScaleR 文件庫。  
* 閱讀文件︰[您可以在 [資料科學虛擬機器執行的 10 個項目](http://aka.ms/dsvmtenthings)
* 瞭解如何建立端對端分析的解決方案，系統化地使用[小組資料科學程序](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)。
* 請造訪[Cortana 智慧庫](http://gallery.cortanaintelligence.com)電腦學習及資料分析範例，使用 Cortana 智慧套件。 在**[開始**] 功能表上，然後在虛擬機器此組件庫的桌面，我們也有提供圖示。

