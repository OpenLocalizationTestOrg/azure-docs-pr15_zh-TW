<properties
    pageTitle="Azure 批次服務基本概念 |Microsoft Azure"
    description="瞭解如何使用大型平行及 HPC 負載 Azure 批次服務"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>

# <a name="basics-of-azure-batch"></a>Azure 批次的基本概念

Azure 批次可讓您在雲端中有效率地執行大型平行和高效能運算 (HPC) 應用程式。 排程受管理的虛擬機器集合上執行計算密集作業的平台服務並可以自動小數位數計算資源，以符合您的工作的需求。

批次服務時，您可以定義 Azure 計算資源平行，並在執行您的應用程式。 您可以視需要或排程執行的工作，而且您不需要手動建立、 設定及管理 HPC 叢集、 個別的虛擬機器、 虛擬網路或複雜的工作和工作排程基礎結構。

## <a name="use-cases-for-batch"></a>批次的使用案例

批次是受管理的 Azure 服務，用於*處理批次*或*批次運算*-執行大量類似的工作，以取得一些所要的結果。 批次運算是最常用由組織的定期處理程序、 轉換，及分析大量資料。

批次處理也實際上平行 （又稱為 「 平行 」） 的應用程式的工作量。 實際上平行負載輕鬆分成多個工作，同時在多部電腦上執行的工作。

![平行工作][1]<br/>

會使用這項技巧經常處理的工作負載的部分範例如下︰

* 財務風險模型
* 趨勢和 hydrology 資料分析
* 圖像轉譯、 分析和處理
* 媒體編碼方式和轉碼
* 起源順序分析
* 工程壓力分析
* 軟體測試

批次也可以執行平行計算結尾，縮減步驟，並執行更複雜的 HPC 負載，例如[郵件傳遞介面 (MPI)](batch-mpi.md)應用程式。

比較批次與 Azure 中的其他 HPC 方案] 選項，請參閱[批次並 HPC 解決方案](batch-hpc-solutions.md)。

## <a name="developing-with-batch"></a>開發批次

處理批次的平行工作負載通常是以程式設計方式使用其中一個[批次 Api](#batch-development-apis)。 批次的 Api，與您建立及管理資料庫的運算節點 （虛擬機器） 與工作排程及執行這些節點的工作。 在用戶端應用程式或服務，您撰寫使用批次 Api 通訊與批次服務。

可以有效率地處理大型負載為您的組織或客戶提供服務的前端，使其可執行工作和工作，視情況下，或在排程，項目、 數百，或甚至數以千計的節點。 您也可以使用批次，做為較大的工作流程，以使用工具，例如[Azure 資料工廠](../data-factory/data-factory-data-processing-using-batch.md)受管理的一部分。

> [AZURE.TIP] 當您準備好深入批次 api，它所提供的功能更深入瞭解時，請查看[適用於開發人員批次功能概觀](batch-api-basics.md)。

### <a name="azure-accounts-youll-need"></a>您需要的 azure 帳戶

當您開發批次解決方案時，您會使用下列帳戶 Microsoft Azure 中。

- **Azure 帳戶與訂閱**-如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者受益][msdn_benefits]，或註冊[免費的 Azure 帳戶][free_account]。 當您建立帳戶時，就會為您建立的預設訂閱。

- **批次帳戶**-當您的應用程式互動的批次的服務，帳戶名稱，而帳戶]，然後便捷鍵的 URL 會作為認證。 集區，例如您所有的批次資源計算節點，工作，而工作以批次帳戶相關聯。 您可以在 Azure 入口網站中的 [[建立批次帳戶](batch-account-create-portal.md)。

- **儲存帳戶**批次包含在[Azure 儲存體]中使用檔案的內建支援[azure_storage]。 幾乎批次案例使用 Azure 儲存體-暫存之任務執行之程式 」 和 「 資料的這些程序，以及它們所產生的輸出資料的儲存空間。 若要建立的儲存空間帳戶，請參閱[關於 Azure 儲存體帳戶](./../storage/storage-create-storage-account.md)。

### <a name="batch-development-apis"></a>批次開發 Api

應用程式和服務可以發行直接 REST API 通話、 使用一或多個動作的用戶端文件庫或兩者都来管理的計算資源和使用批次服務在執行平行的工作量。

| API    | API 參考 | 下載 | 程式碼範例 |
| ----------------- | ------------- | -------- | ------------ |
| **批次其餘** | [MSDN][batch_rest] | N/A | [MSDN][batch_rest] |
| **批次.NET**    | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **批次 Python**  | [readthedocs.io][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **批次 Node.js** | [github.io][api_nodejs] | [npm][api_nodejs_npm] | - |
| **批次 Java**（預覽版本） | [github.io][api_java] | [Maven][api_java_jar] | [GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>批次資源管理

除了用戶端 Api，您也可以使用下列您批次帳戶內管理資源。

- [批次 PowerShell cmdlet][batch_ps]: Azure 批次 cmdlet [Azure PowerShell](../powershell-install-configure.md)模組中的可讓您管理使用 PowerShell 批次資源。

- [Azure CLI](../xplat-cli-install.md): Azure 命令列介面 (Azure CLI) 是提供的命令介面命令互動許多 Azure 服務，包括批次跨平台工具組。

- [批次管理.NET](batch-management-dotnet.md)用戶端文件庫︰ 也可透過[NuGet][api_net_mgmt_nuget]，您可以使用批次管理.NET 用戶端文件庫以程式設計方式管理批次帳戶、 配額和應用程式套件。 參照的[MSDN]上的管理文件庫是[api_net_mgmt]。

### <a name="batch-tools"></a>批次工具

而不需要使用批次建置解決方案，以下是一些建置和偵錯批次應用程式和服務時所使用的工具。

 - [Azure 入口網站][portal]︰ 您可以建立、 監控及刪除批次資料庫、 工作和 Azure 入口網站批次刀中的工作。 您可以檢視這些的狀態資訊和鍵，同時執行您的工作，甚至其他資源下載檔案從您的資料庫中的計算節點 (下載失敗的任務的`stderr.txt`時進行疑難排解，例如)。 您也可以下載遠端桌面 (RDP) 檔案，您可以使用運算節點登入。

 - [Azure 批次檔案總管][batch_explorer]︰ 批次檔案總管會提供類似批次資源管理功能 Azure 入口網站中，但在獨立的 Windows Presentation Foundation (WPF) 用戶端應用程式。 一個批次.NET 範例應用程式提供[GitHub][github_samples]，您可以建立的 Visual Studio 2015 或上方，並使用它來瀏覽和管理批次帳戶中的資源，而您開發偵錯批次方案。 檢視工作、 資料庫，工作詳細資料，運算節點下載的檔案，並連線到節點遠端批次檔案總管中使用遠端桌面 (RDP) 檔案，您可以下載。

 - [Microsoft Azure 儲存檔案總管][storage_explorer]︰ 時不全然 Azure 批次工具，儲存檔案總管是另一個寶貴的工具，讓您是開發和偵錯時，您批次解決方案時。

## <a name="scenario-scale-out-a-parallel-workload"></a>案例︰ 延展平行的工作量

常見的解決方案，以互動的批次 Api 使用批次服務涉及擴展實際上平行工作，例如的 3D 場景-運算節點的集區上的圖像轉譯。 此資料庫的運算節點可以是您的 「 轉譯陣列 」 提供的數十、 數百或千的核心轉譯工作，例如。

下圖顯示一般批次工作流程，與用戶端應用程式或使用批次執行平行工作負載的代管的服務。

![批次解決方案工作流程][2]

在此常見的案例中，您的應用程式或服務來處理 Azure 批次中的計算工作量藉由執行下列步驟︰

1. 上傳**輸入的檔案**和**應用程式**會處理這些檔案至您的 Azure 儲存體帳戶。 輸入的檔案可以是任何資料，您的應用程式將程序，例如財務模型資料或要轉碼視訊檔案。 應用程式檔案可能會用來處理資料，例如 3D 呈現應用程式或媒體 transcoder 任何應用程式。

2. 建立批次**資料庫**計算中的節點您批次的帳戶，這些節點的虛擬機器執行您的工作。 您指定 Azure 儲存體的情況下節點加入集區 （您在步驟 1 中上傳的應用程式） 安裝的應用程式的內容，例如[節點大小](./../cloud-services/cloud-services-sizes-specs.md)、 其作業系統，以及的位置。 您也可以動態設定的資料庫，若要[自動調整](batch-automatic-scaling.md)，調整計算中的節點數目的資料庫，您的任務產生的工作量來回應。

3. 建立批次**工作**来執行的工作負載的運算節點集區。 當您建立工作時，您建立關聯，以批次集區。

4. 將**任務**新增至工作。 當您將任務新增至工作時，批次服務會自動排程集區中的計算節點上執行的工作。 每項工作會使用您上傳到 [處理程序輸入的檔案的應用程式。

    - 匣 4a。 任務執行之前，它可以下載的程序會被指派到運算節點的資料 （輸入檔案）。 如果應用程式不已安裝的節點 （請參閱步驟 #2），就可以下載以下改為。 完成下載後，在上執行其指定的節點。

5. 執行的工作，您可以查詢批次來監控工作和子任務的進度。 用戶端應用程式或服務批次服務透過與進行通訊 HTTPS，因為您可能會監視數以千計的數以千計的運算節點上執行的工作，請務必[有效率地查詢批次服務](batch-efficient-list-queries.md)。

6. 為完成的工作，他們可以將其結果資料上傳至 Azure 儲存體。 您也可以直接從運算節點擷取檔案。

7. 當您監控偵測到您的工作中的任務已完成時，用戶端應用程式或服務可以下載進一步處理或評估的輸出資料。

請記住這是只是其中一個方法使用批次，而且這個案例說明只是其中幾提供的功能。 例如，您可以在每個運算節點，來執行[平行的多個工作](batch-parallel-node-tasks.md)，而且您可以使用[準備及完成的工作](batch-job-prep-release.md)節點準備您的工作，然後清除之後。

## <a name="next-steps"></a>後續步驟

您已經有概觀批次服務，則深入瞭解如何使用它來處理您運算密集平行工作負載的時間。

- 請閱讀[適用於開發人員批次功能概觀](batch-api-basics.md)、 準備使用批次的任何人的基本資訊。 本文包含批次服務資源集區、 節點、 工作和工作，例如相關詳細的資訊，以及建立批次應用程式時，您可以使用許多 API 功能。

- [開始使用.net Azure 批次文件庫](batch-dotnet-get-started.md)，以瞭解如何使用 C# 和批次.NET 文件庫，以執行簡單的工作量使用一般的批次工作流程。 本文應該是您第一份時學習如何使用批次服務的停駐點的其中一個。 此外，還有[Python 版本](batch-python-tutorial.md)的教學課程。

- 下載[GitHub 上的程式碼範例][github_samples]若要查看如何 C# 和 Python 可以介面批次排程與程序範例工作量。

- 請查看[批次學習路徑][ learning_path] ，了解可用的資源您為您瞭解如何使用批次。

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
