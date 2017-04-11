<properties
    pageTitle="PowerShell 模組的電腦學習 |Microsoft Azure"
    description="在公用預覽模式中使用 PowerShell 模組的 Azure 電腦學習。 您可以使用 PowerShell 來建立及管理工作區、 實驗、 web 服務及其他功能。"
    keywords="嘗試線性迴歸、 電腦學習演算法、 電腦學習教學課程預測模型技巧，資料科學實驗"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>適用於 Microsoft Azure 電腦學習的 PowerShell 模組

Azure 電腦學習的 PowerShell 模組是功能強大的工具，可讓您使用 Windows PowerShell 來管理工作區、 實驗、 資料集、 web serivces 及其他功能。

您可以檢視文件，並下載[https://aka.ms/amlps](https://aka.ms/amlps)的完整來源程式碼，以及模組。 

## <a name="what-is-the-machine-learning-powershell-module"></a>什麼是電腦學習 PowerShell 模組？

電腦學習 PowerShell 模組。網路基礎 DLL 模組，可讓您完全管理從 Windows PowerShell 的 Azure 電腦學習工作區、 實驗、 資料集、 web 服務與 web 服務端點。 模組，以及您可以下載包含清楚分隔[C# API 圖層](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)的完整原始程式碼。 這表示您可以從您自己的.NET 專案參考此 DLL 及管理 Azure 電腦學習.NET 程式碼。 此外，DLL 直接從您最愛的用戶端可以使用的基礎 REST Api 而定。

## <a name="what-can-i-do-with-the-powershell-module"></a>我可以使用 PowerShell 模組做什麼？

以下是一些您可以使用這個 PowerShell 模組中執行的工作。 查看[完整的文件](https://aka.ms/amlps)，這些和許多其他功能。

- 佈建新的工作區使用管理憑證 ([新增 AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- 匯出與匯入 JSON 檔案代表實驗圖形 （[匯出 AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph)和[匯入 AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph)）
- 執行實驗 ([開始 AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- 建立 web 服務登出預測的實驗 ([新增 AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- 建立新的端點上發佈的 web 服務 ([新增 AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- 呼叫給及/或 BES web 服務端點 （[叫用 AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint)和[叫用 AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)）

以下是使用 PowerShell 來執行現有實驗的快速範例︰

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

更深入的使用案例，請參閱本文，使用 PowerShell 模組自動化非常經常要求工作︰[建立許多電腦學習模型和 web 服務端點從一個實驗使用 PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)。

## <a name="how-do-i-get-started"></a>如何開始？

若要開始使用電腦學習 PowerShell，下載[發行套件](https://github.com/hning86/azuremlps/releases)從 GitHub 並遵循[指示進行安裝](https://github.com/hning86/azuremlps/blob/master/README.md)。 您需要解除封鎖下載/解壓縮 DLL，然後將其匯 PowerShell 環境。 大部分的指令程式會要求您提供的工作區識別碼、 工作區的授權權杖和 Azure 區域中的工作區。 提供這些最簡單的方法是透過預設 config.json 檔案，其涵蓋的安裝指示中的詳細資料。 當然，您可以同時也複製給樹狀並修改/編譯本機使用 Visual Studio 的程式碼。

## <a name="next-steps"></a>後續步驟

改良的和展開預覽期間會繼續 PowerShell 模組。 留意[Cortana 智慧及電腦學習部落格](https://blogs.technet.microsoft.com/machinelearning/)的更多新聞及資訊。
