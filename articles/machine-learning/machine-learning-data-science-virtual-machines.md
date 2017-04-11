<properties
    pageTitle="Azure 中的資料科學虛擬機器 |Microsoft Azure"
    description="設定設定資料科學虛擬機器"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="data-science-virtual-machines-in-azure"></a>Azure 中的資料科學虛擬機器

提供指示以下的說明如何為您設定 Azure VM 及 SQL 服務 Azure VM 為 IPython 筆記本伺服器。 設定 Windows 虛擬機器是支援工具，例如 IPython 筆記本、 Azure 儲存檔案總管和 AzCopy，以及其他公用程式適合資料科學專案。 Azure 儲存檔案總管] 及 [AzCopy，例如，提供方便的方式，將資料上傳至 Azure 儲存從本機電腦，或從儲存空間將其下載至您的本機電腦。 

主題說明如何設定[小組資料科學程序 (TDSP)](data-science-process-overview.md)所使用的各種資料科學環境此功能表連結。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

可以佈建數種類型的 Azure 虛擬機器，並將其設為使用雲端為基礎的資料科學環境的一部分。 決定哪些類別的虛擬機器使用取決於要建立電腦學習與目標位置，在雲端的資料模型的類型和資料量。 

* 這個決策時要考慮的問題，請參閱[規劃您 Azure 電腦學習資料科學環境](machine-learning-data-science-plan-your-environment.md)。 
* 執行進階的分析時可能遇到的案例的一些目錄，請參閱[進階分析程序及技術中 Azure 電腦學習的案例](machine-learning-data-science-plan-sample-scenarios.md)

提供兩組指示︰

* [設定進階分析 IPython 筆記本伺服器 Azure 虛擬機器](machine-learning-data-science-setup-virtual-machine.md)示範如何佈 Azure 虛擬機器 IPython 筆記本與執行資料科學中可以使用的非 SQL Azure 儲存空間的表單來儲存資料的情況下使用其他工具。

* [設定進階分析 IPython 筆記本伺服器 Azure SQL Server 虛擬機器](machine-learning-data-science-setup-sql-server-virtual-machine.md)示範如何佈 Azure SQL Server 虛擬機器 IPython 筆記本與執行資料科學的 SQL 資料庫可以用來儲存資料的情況下使用其他工具。

能夠並設定之後，這些虛擬機器為 IPython 筆記本伺服器探索及處理的資料，以及搭配 Azure 電腦學習及小組資料科學程序 (TDSP) 所需的其他工作已可供使用。 資料科學程序中的下一個步驟對應中[TDSP 學習路徑](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)，也可能包括步驟，將資料移到 SQL Server 或 HDInsight、 [處理程序和範例並準備資料學習 Azure 電腦學習。


> [AZURE.NOTE] Azure 虛擬機器價格大於為**支付僅適用於您的使用**。 若要確保，您不計費不使用您的虛擬機器時，其位於 [從[Azure 傳統入口網站](http://manage.windowsazure.com/)的 [**停止 (Deallocated)** ] 狀態。 逐步指示或如何解除您虛擬機器，請參閱[關機和解除配置在不使用時的虛擬機器](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 
