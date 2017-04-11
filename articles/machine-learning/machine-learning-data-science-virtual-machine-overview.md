<properties
    pageTitle="什麼是資料科學虛擬機器？ |Microsoft Azure"
    description="瞭解的重要的案例、 功能，以及如何開始使用資料科學虛擬機器環境並準備好進行分析工具組。"
    keywords="資料科學工具、 資料科學虛擬機器、 資料科學，linux 資料科學工具"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="bradsev" />


# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Linux 和視窗的簡介至雲端資料科學虛擬機器

資料科學虛擬機器是專為執行資料科學內建的 Microsoft Azure 雲端上的自訂的 VM 圖像。 有許多熱門的資料科學及其他工具已預先安裝並將預先設為協助您快速建立智慧的應用程式的進階分析。 使用 Windows Server 2012 或 OpenLogic 7.2 CentOS 型 Linux 版本上。 

本主題將告訴您可以使用資料科學 VM 做什麼、 概述一些使用 VM 的重要的案例、 會條列索引鍵的功能，在 Windows 和 Linux 版本中，並說明如何開始使用。


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>我要如何使用資料科學虛擬機器？

目標的資料科學虛擬機器是提供資料專業人員的所有技能層級和角色人事空閒資料科學環境。 此 VM 儲存，如果您有自己導相容的環境，您想要花很長的時間。 不過，立即新建立的 VM 執行個體中開始資料科學專案。 

資料科學 VM 是設計，以及設定成使用主要的狀況。 您可以調整您的環境向上或向下您的專案需求的改變。 您就可以使用您慣用的語言，以程式資料科學工作。 您可以安裝其他工具，並為您的需求自訂系統。
 
## <a name="key-scenarios"></a>索引鍵的案例
本節會建議資料科學 VM 可以部署的一些重要案例。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>在雲端的預先設定的分析桌面

資料科學 VM 提供資料科學小組想要使用受管理的雲端桌面取代本機桌上型電腦的比較基準設定。 此比較基準可確保小組中的所有資料科學家都有一致的安裝程式用來確認實驗，並宣傳共同作業。 也可以縮減的系統管理員負擔並儲存在評估、 安裝及維護執行進階的分析所需的各種軟體套件所需的時間降低成本。  

### <a name="data-science-training-and-education"></a>資料科學訓練和教育版

企業訓練人員和教育界人士的類別通常會提供的虛擬機器圖像，以確保學生擁有一致的設定和範例使用預測教導資料科學。 資料科學 VM 建立視環境減輕支援與不相容的挑戰一致設定。 需要針對經常，尤其是短的訓練課程，這些環境的優點大幅。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>視需要彈性容量大型的專案

資料科學 hackathons/池或大型資料模型及探索需要橫向硬體容量，通常是簡短的持續時間。 資料科學 VM 可協助複寫快速視不按比例縮放出允許需要執行強大計算資源的實驗的伺服器上的資料科學環境。

### <a name="short-term-experimentation-and-evaluation"></a>這是短期的實驗和評估

資料科學 VM 可以是用來評估，或者進一步瞭解 Microsoft R 伺服器，SQL Server，例如工具 Visual Studio 工具，Jupyter，深入瞭解 / 毫升工具組] 和 [熱門社群中的最小的新工具設定投入。 資料科學 VM 可以快速地將設定，因為它可以套用至其他這是短期的使用狀況，例如複寫已發佈的實驗，執行示範在線上工作階段或會議教學課程中的下列逐步解說。


## <a name="whats-included-in-the-data-science-vm"></a>資料科學 VM 中包含哪些產品？

資料科學虛擬機器有許多熱門的資料科學工具已經安裝並設定。 也包含工具，讓您更輕鬆地使用各種不同的 Azure 資料和分析產品。 您可以瀏覽及大型資料集使用 Microsoft R Server 或 SQL Server 2016 建立預測模型。 從開啟的來源社群及 Microsoft 的其他工具主機，也會包含在內，以及範例的程式碼和筆記本。 下表會條列，並比較包含的 Windows 和 Linux 版本的資料科學虛擬機器中的主要元件。


|**Windows 版** | **Linux 版本** |
|----------------|---------------|
|Microsoft R 伺服器開發人員版本 | Microsoft R 伺服器開發人員版本 |
|Anaconda Python 2.7 3.5 | Anaconda Python 2.7 3.5 |
|Jupyter 筆記本伺服器 （R，Python） | JupyterHub︰ 多使用者 Jupyter 筆記本 (R，Python Julia) |
|SQL Server 2016 開發人員版︰ 調整資料庫中分析 R 服務 | Postgres、 松鼠 SQL （資料庫工具）、 SQL Server 驅動程式，以及 （bcp、 sqlcmd） 的命令列 |
|Visual Studio 社群 Edition 2015 (IDE) </br> -Azure HDInsight (Hadoop)，資料湖 SQL Server 資料工具 </br> -Visual Studio Node.js、 Python 和 R 工具 |Ide 和編輯器 </br> -（蝕色) 與 Azure 工具組外掛程式 </br> -（含 ESS auctex) Emacs gedit |
|Power BI desktop | -- |
|電腦學習工具 </br> -Azure 機器學習整合 </br> -CNTK (深學習/AI) </br> -Xgboost （熱門毫升工具中的資料科學池） </br> -Vowpal Wabbit （快速 online 是型學習者） </br> -祕 （視覺化快速開始資料以及分析工具） </br> -Mxnet (深學習/AI) | 電腦學習工具 </br> -Azure 機器學習使用整合 </br> -CNTK (深學習/AI) </br> -Xgboost （熱門毫升工具中的資料科學池） </br> -Vowpal Wabbit （快速線上是型學習者） </br> -祕 （視覺化快速開始資料以及分析工具）  |
| 若要存取 Azure 及服務的 Cortana 智慧套件 Sdk | 若要存取 Azure 及服務的 Cortana 智慧套件 Sdk |
| 移動資料和 Azure 和大型資料資源管理工具︰ Azure 儲存檔案總管、 CLI、 PowerShell、 AdlCopy （Azure 資料湖）、 AzCopy、 dtui （適用於 DocumentDB)，Microsoft 資料管理閘道 | 移動資料和 Azure 和大型資料資源管理工具︰ Azure 儲存檔案總管、 CLI |
| 給，Visual Studio 小組服務外掛程式 | 給 |
| 最常用 Linux/Unix 命令列公用程式透過 GitBash/命令提示字元視窗連接埠 | -- |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>如何開始使用 Windows 資料科學 VM

- 在 Windows 上建立 VM 執行的個體，請瀏覽[此](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)頁面並選取 [綠色**建立虛擬機器**] 按鈕。
- 從遠端桌面使用您建立 VM 時，您所指定的認證登入 VM。
- 若要找出並啟動可用的工具，按一下**[開始**] 功能表。


## <a name="get-started-with-the-linux-data-science-vm"></a>快速入門 Linux 資料科學 VM

- 在執行個體的 VM Linux （OpenLogic CentOS 型） 瀏覽[此](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)頁面，然後選取 [**建立虛擬機器**] 按鈕。
- 從 SSH 用戶端，例如 Putty 或 SSH] 命令，使用您指定當您建立 VM 的認證登入 VM。
- 在命令介面提示中，輸入 dsvm 其他資訊。
- 以圖形的桌面下載您的用戶端平台的 X2Go 用戶端[以下](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)並遵循 Linux 資料科學 VM 文件[佈建 Linux 資料科學虛擬機器](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client)中的指示進行。


## <a name="next-steps"></a>後續步驟

### <a name="for-the-windows-data-science-vm"></a>針對 Windows 資料科學 VM

- 如需有關如何執行的 Windows 版本提供的特定工具的詳細資訊，請參閱[佈建 Microsoft 資料科學虛擬機器](machine-learning-data-science-provision-vm.md)和
-  如需有關如何執行各種工作所需的資料上 Windows VM 科學專案的詳細資訊，請參閱[您可以在 [資料科學虛擬機器執行的 10 個項目](machine-learning-data-science-vm-do-ten-things.md)。

### <a name="for-the-linux-data-science-vm"></a>針對 Linux 資料科學 VM

- 如需有關如何執行 Linux 版本上的特定的工具的詳細資訊，請參閱[佈建 Linux 資料科學虛擬機器](machine-learning-data-science-linux-dsvm-intro.md)。
- 說明您如何執行多個資料科學工作與 Linux VM 的逐步解說，請參閱[資料科學上 Linux 資料科學虛擬機器](machine-learning-data-science-linux-dsvm-walkthrough.md)。
