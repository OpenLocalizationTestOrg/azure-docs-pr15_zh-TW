<properties
    pageTitle="Azure 電腦學習常見問題集 |Microsoft Azure"
    description="Azure 電腦學習簡介︰ 常見問題集涵蓋計費、 能力和雲端服務的精簡的預測模型的限制。"
    keywords="電腦學習簡介預測模型、 什麼電腦學習"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="garye"/>

# <a name="azure-machine-learning-frequently-asked-questions-faq-billing-capabilities-limitations-and-support"></a>Azure 電腦學習常見問題集 (FAQ): 計費、 功能、 限制，以及支援

此常見問題集解答 Azure 電腦學習開發預測模型和另尋高就透過 web 服務的解決方案的雲端服務的相關問題。 此常見問題集涵蓋使用的服務，包括帳單模型、 功能、 限制，以及支援的相關問題。

## <a name="general-questions"></a>一般問題

**什麼是 Azure 電腦學習？**

Azure 電腦學習是完全受管理的服務，可用來建立、 測試，操作，以及管理雲端中的預測分析解決方案。 使用僅瀏覽器中，您可以登入上, 傳的資料，並立即開始電腦學習實驗。 拖放預測模型、 模組大型調色盤和簡單且快速啟動範本會讓一般電腦學習工作的文件庫。  如需詳細資訊，請參閱[Azure 電腦學習服務概觀](https://azure.microsoft.com/services/machine-learning/)。 電腦學習簡介，其涵蓋重要的術語和概念，請參閱[Azure 電腦學習的簡介](machine-learning-what-is-machine-learning.md)。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**什麼是電腦學習 Studio？**

電腦學習 Studio 是您可以透過網頁瀏覽器存取工作環境。 電腦學習 Studio 裝載的視覺撰寫介面，可讓您建立的端對端、 實驗的表單中的資料科學工作流程模組的調色盤。

如需電腦學習 Studio 的詳細資訊，請參閱[什麼是電腦學習 Studio？](machine-learning-what-is-ml-studio.md)

**什麼是電腦學習 API 服務？**

電腦學習 API 服務可讓您部署預測模型，例如的內建於電腦學習 Studio 中，以調整、 容錯，web 服務。 電腦學習 API 服務建立 web 服務是 REST Api 提供介面外部應用程式與您的預測狀況分析模型之間的通訊。

如需詳細資訊，請參閱[連線到電腦學習 web 服務](machine-learning-connect-to-azure-machine-learning-web-service.md)。

**其中列出我傳統的 web 服務？其中列出我的新 Azure 資源管理員依據 web 服務？**

傳統的 Web 服務和新的 Azure 資源管理員依據的 Web 服務會列在 [ [Microsoft Azure 機器學習 Web 服務](https://services.azureml.net/)入口網站。 

在 [Web 服務] 索引標籤上，傳統的 Web 服務也會列在[電腦學習 Studio](http://studio.azureml.net) 。

## <a name="microsoft-azure-machine-learning-web-service-questions"></a>Microsoft Azure 電腦學習 Web 服務的問題

**什麼是 Azure 機器學習 Web 服務？**

電腦學習 Web 服務會提供應用程式及電腦學習工作流程計分模型之間的介面。 使用 Azure 電腦學習 web 服務，外部應用程式可以與電腦學習工作流程計分模型中進行即時通訊。 電腦學習 web 服務呼叫預測結果傳回外部應用程式。 若要讓來電電腦學習 web 服務，您可以傳送部署 web 服務時所建立的 API 金鑰。 電腦學習 web 服務為基礎其餘部分中，web 程式設計專案的常用架構選擇。

Azure 電腦學習有兩種類型的服務︰

* 要求回應服務 （給）-低延遲、 彈性的服務，提供從電腦學習 Studio 以無模型建立並部署介面。
* 批次執行服務 (BES)-非同步服務的資料記錄的批次的分數。

有數種方式可使用 REST API 及存取 web 服務。 例如，您可以在 C#、 R 或使用部署 web 服務時，為您產生的程式碼範例 Python 撰寫應用程式。

才會提供程式碼範例︰ 使用 Azure 機器學習 Web 服務入口網站中的 Web 服務的頁面。
[API 說明] 頁面中電腦學習 Studio 的 Web 服務儀表板。

或者，您可以使用範例 Microsoft Excel 活頁簿，為您建立 （也可以在 web 服務儀表板 Studio 中）。

**什麼是主要會更新為新的 Azure 毫升 Web 服務？**

如需有關新 Azure 機器學習 Web 服務的詳細資訊，請參閱[相關的文件](machine-learning-whats-new.md)。

## <a name="machine-learning-studio-questions"></a>電腦學習 Studio 問題

### <a name="importing-and-exporting-data-for-machine-learning"></a>匯入及匯出資料的電腦學習

**電腦學習支援哪些資料來源？**

資料載入到電腦學習 Studio 實驗中有三種︰ 上傳的本機的檔案，以資料集，使用模組從雲端資料服務，匯入資料或匯入資料集從另一個儲存嘗試。 若要瞭解更多關於支援的檔案格式，請參閱[將電腦學習 Studio 訓練資料匯入](machine-learning-data-science-import-data.md)。


#### <a id="ModuleLimit"></a>如何大型資料集可以為我模組？

模組電腦學習 Studio 中的支援常見的使用情況資料集的 10 GB 的密集數字的資料。 如果模組需要多個輸入，10 GB 是所有輸入大小總計。 您也可以範例大型資料集透過登錄區或 Azure SQL 資料庫的查詢，或依計數學習預先處理 ingestion 之前。  

下列類型的資料可以為較大的資料集展開功能正規化期間並小於 10 GB 限制︰

- 疏鬆
- 分類
- 字串
- 二進位資料

下列模組限於小於 10 GB 的資料集︰

- Recommender 模組
- SMOTE 的模組
- 指令碼的模組︰ R，Python SQL
- 可輸入的資料的大小，例如加入或功能雜湊大於輸出資料大小的模組。
- 跨驗證與微調模型 Hyperparameters、 序數迴歸分析，一個與全部 Multiclass，當反覆運算的數字是很大。

對於大於幾個 GB 的資料集，您應該 Azure 儲存空間或 Azure SQL 資料庫或使用 HDInsight，而非直接上傳從本機檔案上傳資料。


####<a id="UploadLimit"></a>資料上傳的限制是什麼？
對於大於幾個 GB 的資料集上, 傳至 Azure 儲存空間或 Azure SQL 資料庫或使用 HDInsight，而不是直接從本機檔案上傳的資料。

**我可以從 Amazon S3 讀取資料？**

如果您有少量的資料，而且想要將它公開透過 http URL，然後您可以使用[匯入資料][import-data]模組。 任何大量的資料傳輸至 Azure 儲存體第一次，然後使用 [[匯入資料]的[import-data]併入實驗的模組。
<!--
<SEE CLOUD DS PROCESS>
-->

**有內建的圖像輸入的功能嗎？**

您可以瞭解[匯入圖像]中的圖像輸入功能[image-reader]參照。

### <a name="modules"></a>模組

**演算法、 資料來源、 資料格式] 或我正在尋找的資料轉換作業無法 Azure 電腦學習 Studio 中。我還有什麼？**

您可以造訪[使用者意見反應](http://go.microsoft.com/fwlink/?LinkId=404231)，若要查看我們正在追蹤的功能要求。 如果您正在尋找的功能已要求，請新增至邀請的投票。 如果您正在尋找的功能不存在，建立新的邀請。 您可以在此論壇太檢視您要求的狀態。 我們密切追蹤此清單，並經常更新的功能可用性狀態。 此外，使用內建支援 R 和 Python 如有需要您可以建立自訂的轉換。


**可以沿用現有的程式碼電腦學習 studio 嗎？**

是的可以顯示機器學習 Studio 現有 R 或 Python 程式碼、 在 Azure 電腦學習學習相同實驗中執行，並將解決方案部署為透過 Azure 電腦學習 web 服務。 如需詳細資訊，請參閱[延長您實驗 R](machine-learning-extend-your-experiment-with-r.md)及[執行 Python 電腦學習 Azure 電腦學習 Studio 中的指令碼](machine-learning-execute-python-scripts.md)。

**是否可以使用類似[PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)定義模型？**

否的不支援，不過自訂 R 和 Python 程式碼可以用來定義模組。

**多少模組可以我平行執行我實驗中？**  

平行實驗中，您可以執行多達四個模組。


### <a name="data-processing"></a>處理資料

**有能夠以視覺化方式呈現資料 （除了 R 視覺效果） 內實驗互動的方式嗎？**

按一下 [輸出的模組，您可以以視覺化方式呈現資料，並取得統計資料。

**當預覽結果] 或 [在瀏覽器中的資料的列和欄數只限，為什麼？**

資料傳輸至瀏覽器，並可能很大，資料大小會受限於防止變慢電腦學習 Studio。 若要以視覺化方式呈現的所有資料/結果，最好是下載的資料，並使用 Excel] 或 [其他工具。

### <a name="algorithms"></a>演算法

**在電腦學習 Studio 中支援哪些演算法？**

電腦學習 Studio 提供的圖案狀態演算法，例如可縮放提高決策樹，Bayesian 建議系統深類神經網路，並在 Microsoft Research 開發的決策 Jungles。 調整開啟來源電腦學習套件，例如 Vowpal Wabbit 也都包含在內。 電腦學習 Studio 支援電腦學習演算法 multiclass 和二進位分類、 迴歸分析，及叢集。 請參閱[電腦學習模組]的完整清單[machine-learning-modules]。

**您自動建議的權限電腦學習演算法用於 [我的資料？**

否，不過有各種方式可以在電腦學習 Studio 比較結果的每個演算法來判斷適合您的問題。

**您有任何移到另一個用於提供演算法挑選一個演算法的方針嗎？**
請參閱[如何選擇一種演算法](machine-learning-algorithm-choice.md)。

**提供的演算法撰寫 R 或 Python 嗎？**

否，這些演算法大多語言撰寫的編譯提供更高的效能。

**有任何所提供的演算法的詳細資料？**

文件提供的一些演算法，相關資訊，微調提供的參數會描述，最佳化您使用的演算法。  

**有任何支援的線上學習嗎？**

否，目前僅程式設計重新訓練支援。

**我可以視覺化使用內建的模組類神經網路模型的圖層嗎？**

[否]。

**可以建立自己的模組 C# 或其他語言？**

目前新的自訂模組，只可以建立在。

### <a name="r-module"></a>R 模組

**哪些 R 套件可在電腦學習 Studio？**

電腦學習 Studio 支援 400 + CRAN R 套件，，以下是包含封裝[目前的清單](http://az754797.vo.msecnd.net/docs/RPackages.xlsx)。 此外，請參閱[延長您實驗 R](machine-learning-extend-your-experiment-with-r.md) ，瞭解如何自行擷取這份清單。 如果您想要的套件不在此清單中，提供在[使用者意見反應](http://go.microsoft.com/fwlink/?LinkId=404231)套件的名稱。

**是否可以建立自訂的 R 模組？**

如需詳細資訊，是的請參閱[Azure 電腦學習撰寫自訂 R 模組](machine-learning-custom-r-modules.md)。

**上有複寫環境嗎？**

否，有 studio 沒有複寫環境。

### <a name="python-module"></a>Python 模組

**是否可以建立自訂的 Python 模組？**

目前未，但是您可以使用一或多個[執行 Python 指令碼][python]模組以相同的結果。

**Python 上有複寫環境嗎？**

您可以在電腦學習 Studio 中使用 Jupyter 筆記本。 如需詳細資訊，請參閱 [介紹 Azure 電腦學習 Studio 中 Jupyter 筆記本] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)。

## <a name="web-service"></a>Web 服務

###<a name="retraining-models-programmatically"></a>以程式設計方式重新訓練模型

**我要如何重新訓練 Azure 電腦學習模型以程式設計方式嗎？**

使用訓練 Api。 如需詳細資訊，請參閱[重新訓練電腦學習模型以程式設計方式](machine-learning-retrain-models-programmatically.md)。 範例程式碼中也會有[Microsoft Azure 電腦學習重新訓練的示範](https://azuremlretrain.codeplex.com/)。

### <a name="create"></a>建立

**在本機或在沒有網際網路連線應用程式可以部署模型？**

[否]。


**有預期的所有 web 服務的比較基準延遲嗎？**

請參閱[Azure 訂閱限制](../azure-subscription-service-limits.md)

### <a name="use"></a>使用

**何時要以批次執行服務要求] 回應服務與執行我預測模型？**

要求回應服務 （給） 是用來提供介面，來建立並部署的試驗環境無模型低延遲、 高階 web 服務。 批次執行服務 (BES) 是非同步計分一批資料記錄中的服務。 輸入的 BES 很類似給中使用的資料輸入。 主要差異是 BES 會從各種不同的來源，例如 Blob 服務和 Azure SQL 資料庫，Azure HDInsight （登錄區查詢） 中的表格服務及 HTTP 來源讀取區塊的記錄。 如需詳細資訊，請參閱[如何使用電腦學習 web 服務](machine-learning-consume-web-services.md)。

**我要如何更新模型已部署的 web 服務？**

更新預測模型已部署的服務是只要修改，然後重新執行您用來製作並儲存訓練的模型實驗。 一旦您有可用的訓練模型的新版本時，電腦學習 Studio 會詢問您是否要更新您的 web 服務。 如需如何更新已部署的 web 服務的詳細資訊，請參閱[部署電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

您也可以使用重新訓練 Api。
如需詳細資訊，請參閱[重新訓練電腦學習模型以程式設計方式](machine-learning-retrain-models-programmatically.md)。 範例程式碼中也會有[Microsoft Azure 電腦學習重新訓練的示範](https://azuremlretrain.codeplex.com/)。

**我要如何監視生產環境中部署我 web 服務？**

一旦預測模型已部署，您可以監視它從 Azure 傳統入口網站 （僅限傳統 web 服務） 或 Azure 機器學習 Web 服務入口網站。 每個已部署的服務會有自己儀表板，您可以查看監控該服務的資訊。 如需有關如何管理您的部署的 web 服務的詳細資訊，請參閱[管理 Web 服務使用 Azure 機器學習 Web 服務入口網站](machine-learning-manage-new-webservice.md)和[管理 Azure 電腦學習工作區](machine-learning-manage-workspace.md)。

**有我可以在這裡看見我給/BES 的輸出位置嗎？**

給，web 服務回應是通常您會看到結果位置。 您也可以撰寫至 Azure blob 儲存體。 針對 BES，輸出寫入 blob 預設。 您也可以將輸出寫入到資料庫或使用[匯出資料]的表格[export-data]模組。

**可以建立 web 服務僅從電腦學習 Studio 中建立的模型？**

否，您也可以直接從 Jupyter 筆記本和 RStudio 建立 web 服務。

**哪裡可以找到錯誤碼的相關資訊？**

如需錯誤碼和描述的清單，請參閱[電腦學習模組錯誤代碼](https://msdn.microsoft.com/library/azure/dn905910.aspx)。

## <a name="scalability"></a>延展性

**什麼是 web 服務的延展性？**

目前的預設端點是提供給 20 並行給要求，每個端點。 您可以調整此 200 同時的要求，每個端點和[縮放比例 Web 服務](machine-learning-scaling-webservice.md)所述，您可以擴充至每個 web 服務的 10000 端點每個 web 服務。 如 BES，每個端點允許一次處理 40 個要求及佇列超過 40 個要求其他的要求。 為佇列中排水，這些佇列的要求自動執行。


**散佈在節點 R 工作嗎？**

[否]。  


**我可以使用的資料量訓練課程？**

模組電腦學習 Studio 中的支援常見的使用情況資料集的 10 GB 的密集數字的資料。 模組所需輸入一個以上的時間，所有輸入的總大小一起有 10GB 容量。 您也可以範例大型資料集透過登錄區或 Azure SQL 資料庫的查詢，或使用[學習計數與]測試處理[counts]ingestion 之前的模組。  

下列類型的資料可以為較大的資料集展開功能正規化期間並小於 10 GB 限制︰

- 疏鬆
- 分類
- 字串
- 二進位資料

下列模組限於小於 10 GB 的資料集︰

- Recommender 模組
- SMOTE 的模組
- 指令碼的模組︰ R，Python SQL
- 可輸入的資料的大小，例如加入或功能雜湊大於輸出資料大小的模組。
- 跨驗證微調模型 Hyperparameters、 序數迴歸分析，與一個與全部 Multiclass，很大次數時。

對於大於幾個 GB 的資料集，您應該 Azure 儲存空間或 Azure SQL 資料庫，或使用 HDInsight，而非直接上傳從本機檔案上傳資料。


**是否有任何向量大小限制？**

列和欄限於每個最大值 Int.NET 限制︰ 2147483647。

**可以調整大小的虛擬機器用來執行 web 服務嗎？**

[否]。  

## <a name="security-and-availability"></a>安全性與可用性

**誰能存取至 http 端點 web 服務的預設？如何限制端點存取？**

部署 web 服務後，該服務的預設端點是自動建立。 可以使用其 API 金鑰呼叫的預設端點。 從 Azure 傳統入口網站，或使用 Web 服務管理 Api 以程式設計方式，可以使用自己的索引鍵新增額外的結束點。 若要撥打的 web 服務需要便捷鍵。 如需詳細資訊，請參閱[連線到電腦學習 web 服務](machine-learning-connect-to-azure-machine-learning-web-service.md)。


**如果找不到我的 Azure 儲存體帳戶，會發生什麼情況？**

電腦學習 Studio 依賴使用者提供 Azure 儲存體帳戶執行工作流程時，儲存中繼資料。 若要同時電腦學習 Studio 建立工作區則此儲存帳戶。 工作區建立之後即，如果儲存帳戶會刪除並找不再、 工作區會停止運作，該工作區中的所有實驗會都失敗。

如果您不小心刪除儲存的帳戶，請重新建立相同的名稱為已刪除的儲存空間帳戶相同的區域中的儲存空間帳戶。 之後，重新同步處理便捷鍵。


**同步處理我的儲存空間帳戶便捷鍵時，會發生什麼情況？**

電腦學習 Studio 依賴儲存中繼資料執行工作流程時的使用者提供 Azure 儲存體帳戶。 此儲存帳戶則若要同時電腦學習 Studio 建立工作區且便捷鍵相關聯的工作區。 如果變更便捷鍵，在工作區建立之後，工作區無法再存取儲存帳戶。 它會停止運作，該工作區中的所有實驗會都失敗。

如果您已變更儲存帳戶便捷鍵，重新同步處理工作區使用 Azure 傳統入口網站中的便捷鍵。  


## <a name="azure-marketplace"></a>Azure Marketplace

請參閱[發佈並使用電腦學習市場中的應用程式的常見問題集](machine-learning-marketplace-faq.md)。

## <a name="support-and-training"></a>支援與訓練

**哪裡可以取得的訓練課程 Azure 電腦學習？**

[Azure 電腦學習文件中心](https://azure.microsoft.com/services/machine-learning/)主控影片教學課程及使用方法輔助線。 這些逐步指示提供服務的簡介，並逐一瀏覽資料科學生命週期的匯入資料、 清除資料、 建立預測模型和 Azure 電腦學習使用生產環境中部署。

我們要加入新的材料當前電腦學習中心。 您可以提交電腦學習中心[使用者意見反應](https://windowsazure.uservoice.com/forums/257792-machine-learning)在其他學習材料的要求。

您也可以在[Microsoft 虛擬學院](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)尋找訓練課程。

**我要如何取得 Azure 電腦學習支援？**

若要取得 Azure 電腦學習的技術支援，移至[Azure 支援](/support/options/)並選取**電腦學習**。

Azure 電腦學習也是您可以在此詢問 Azure 電腦學習相關問題的 msdn 社群論壇。 在論壇監視的 Azure 電腦學習小組。 請造訪[Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)。

## <a name="billing-questions"></a>帳單問題

**電腦學習計費如何運作？**

有兩個元件 Azure 電腦學習服務。 電腦學習 Studio 及電腦學習 Web 服務。

當您正在評估電腦學習 Studio 時，您可以使用免費的帳單層。  免費層也可讓您部署容量有限的傳統的 web 服務。

一旦您決定 Azure 電腦學習符合您的需求，您可以註冊標準層。 若要登入，您必須使用 Microsoft Azure 訂閱。

在標準層向您收費每月的每個您在電腦學習 Studio 中定義的工作區。 當您在 studio 中執行實驗時，是向您收費計算資源的當您執行的實驗。 當您部署傳統的 Web 服務，交易，並計算小時向收費支付另存新檔您前往 (PAYG) 為基礎。

新的電腦學習 Web 服務介紹計費方案所允許的更多預測的成本。 分層價格折扣的率會需要大量的容量的客戶。

當您建立一個方案時，您認可隨附的 API 計算小時和 API 交易包含數量的固定成本。 如果您需要更多包含的數量，您可以新增其他執行個體，您的方案。 如果您需要更多包含的數量，您可以選擇較高的層計劃提供更多包含 quantities 和更佳的折扣的率。

在現有的執行個體中包含的數量可後，其他的使用方式是 overage 工資率支付薪資相關聯的帳單的計劃層。

附註︰ 包含的 quantities 會重新配置每 30 天及未使用過的包含的 quantities 執行不能拖到下一個週期。

如需其他計費與價格資訊，請參閱[電腦學習價格](https://azure.microsoft.com/pricing/details/machine-learning/)。

**電腦學習是否有免費試用版？**

 Azure 電腦學習有的免費訂閱] 選項 （請參閱[電腦學習定價](https://azure.microsoft.com/pricing/details/machine-learning/)詳細資料），且機器學習 Studio 8 小時快速評估試用版提供 （登入[電腦學習 Studio](https://studio.azureml.net/?selectAccess=true&o=2)此試用版）。

 此外，當您註冊 Azure 的免費試用版時，您可以嘗試月份的任何 Azure 服務。 若要進一步瞭解 Azure 免費試用版，請造訪[Azure 免費試用版常見問題集](/pricing/free-trial-faq/)。

**什麼是交易？**

交易代表 API 呼叫 Azure 電腦學習回應。 要求回應服務 （給） 和批次執行服務 (BES) 通話的交易會彙總，而且會依據您的帳單計劃。

**我可以使用包含的交易 quantities 計劃中給和 BES 交易？**

是的您的交易從給及 BES 會彙總，而且會依據您的帳單計劃。

**什麼是 API 計算小時？**

API 計算小時是帳單單位時間 API 通話需要使用毫升計算資源執行。 您的來電會彙總帳單。

**如何會一般生產 API 通話的時間？**

生產 API 通話時間變化相當大，通常範圍從下數百個毫秒到幾秒鐘，但可能需要根據複雜的資料處理及電腦學習模型的分鐘。 估計生產 API 通話時間的最佳方式就是基準測試電腦學習服務上的模型。

**什麼是在 Studio 計算小時？**

在 Studio 計算小時是您實驗使用彙總時間的帳單單位計算 studio 中的資源。

**在 [新的 Web 服務] 是開發/測試層所謂的？**

Azure 毫升新的 Web 服務會提供您可以使用佈建帳單計劃的多個層級。 開發/測試層會提供可讓您測試實驗為新的 web 服務，不會產生的成本限制包含的 quantities 層。 您有機會 」 預定輪胎 」，瞭解其運作方式。

**有個別的儲存空間的費用嗎？**

在電腦學習免費層不需要或允許個別的儲存空間。 在標準電腦學習層需要有一個 Azure 儲存體帳戶的使用者。 Azure 儲存為[個別計費](https://azure.microsoft.com/pricing/details/storage/)。

**電腦學習支援高可用性如何運作？**

生產 API 通話時間變化相當大，通常範圍從下數百個毫秒到幾秒鐘，但可能需要根據複雜的資料處理及電腦學習模型的分鐘。 估計生產 API 通話時間的最佳方式就是基準測試電腦學習服務上的模型。

**計算資源的特定種類生產 API 來電會在都能夠執行？**

電腦學習服務是 multitenant 服務，並使用的後端上的實際計算資源視情況而定最佳化效能及預測。

### <a name="management-of-new-web-services"></a>新的 Web 服務的管理

**如果刪除我的方案，會發生什麼情況？**

方案會從您的訂閱中移除，會向您收費按比例的使用方式。

注意︰ 您無法刪除的 web 服務正在使用的計劃。 若要刪除的方案，您必須將新的方案指派給 web 服務，或刪除 web 服務。

**什麼是計劃執行個體？**

計劃執行個體是包含您可以新增至您的帳單計劃的數量的單位。 當您選取的帳單層帳單計劃時，其會出現一個執行個體。 如果您需要更多包含的數量，您可以新增您的方案的所選的帳單層的執行個體。

**計劃執行個體數目可以新增？**

在訂閱中，您可以讓開發/測試層的執行個體之一。

您可以層 S1、 S2 及 S3 新增為必要項目。

附註︰ 根據您預期的使用，可能更成本有效率升級到較高的包含的 quantities 層，而不是新增至目前的層的執行個體。

**變更計劃層時，會發生什麼情況 (升級 / 降級)？**

刪除舊方案，而目前的使用方式計費按比例為基礎。 升級/降級層的完整包含數量與新的方案會建立期間的其餘部分。

附註︰ 包含的 quantities 配置期和未使用過的數量不變換。

**當我增加計劃中的執行個體，會發生什麼情況？**

Quantities 包含按比例為基礎，可能需要 24 小時，才能生效。

**刪除方案的執行個體時，會發生什麼情況？**

執行個體會移除您的訂閱，會向您收費按比例的使用方式。


### <a name="signing-up-for-new-web-services-plans"></a>註冊新的 Web 服務方案

**我如何註冊的方案？**

您有兩種方式建立帳單計劃。

當您第一次部署新的 web 服務時，您可以選擇現有的計劃，或建立新的方案。

以這種方式建立的計劃是您預設的地區，而且您的 web 服務將會部署到該區域。

如果您想要將服務部署到您預設的地區以外的區域，您可能會想在部署您的服務之前，定義您的帳單計劃

在此情況下，您可以登入 Azure 機器學習 Web 服務入口網站，並瀏覽至 [方案] 頁面。 之後，您可以新增和刪除方案，以及修改現有的計劃。

**若要開始應該選擇您的方案？**

我們建議您開始您會使用標準 S1 層，並監控您的服務的使用方式。 如果您使用的您包含的 quantities 快速找到後，您就可以新增執行個體，或移動到較高的層和取得更佳的折扣的率。 您可以調整您的帳單計劃，視需要在您的帳單週期。

**哪些地區有新的方案？**

新的帳單方案可用於三個生產區域中，我們支援新的 web 服務︰

* 美國中部南美洲
* 西歐
* 東南亞

**我有多個區域中的 web 服務。需要每個區域的方案嗎？**

[是]。 計劃價格不盡相同的區域。 當您部署 web 服務至其他區域時，您需要指派給該區域特定的計劃。

### <a name="new-web-services---overages"></a>新的 Web 服務-Overages

**如何檢查我的 web 服務使用是否 overage？**

您可以檢視 Azure 機器學習 Web 服務入口網站中的 [方案] 頁面上的所有方案的使用方式。 登入入口網站，然後按一下 [方案] 功能表選項。

交易資料表計算資料行，您可以看到包含的數量的計劃] 和 [使用百分比。

**使用 [在開發/測試層包含數量設定時，會發生什麼情況？**

有指派開發/測試階層的服務會停止在下一個週期為止，或將其移至其中一個付費的層級。

**對於傳統的 Web 服務和 Overages 的新 Web 服務，如何價格計算要求回應 （給） 與批次 (BES) 工作負載的？**

給工作負載您負責您進行每個 API 交易通話，以及這些要求相關聯的計算時間。 給生產 API 交易成本的計算方式的 API 總數打電話給您請乘以單價 1000 交易 （個別交易依比例） 的項目。 您給 API 生產 API 計算的小時的成本所計算的每個 API 呼叫執行時，所需的時間量乘以乘以單價生產 API 計算小時的 API 交易總數。

例如的標準 S1 overage 1000000 秒後才 0.72 每個要執行的 API 交易會導致 (1000000 *$0.50/1 K API 交易) 中生產 API 交易成本為 $500 和 (1000000* 0.72 sec * $2/hr) $400 生產 API 計算時，針對總計 $900。

BES 工作負載的您會以相同的方式，不過，API 交易成本表示您提交批次工作的數字，計算成本代表這些批次工作相關聯的計算時間。 您 BES 生產 API 交易的成本所計算的工作送出的總數量乘以單價 1000 交易 （依比例個別交易）。 您 BES API 生產 API 計算的小時的成本的計算方式為所需在您工作中的每個資料列執行乘以乘以總數乘以單價生產 API 計算小時工作的工作中的資料列的總數的時間量。 交易量表時使用電腦學習計算機，表示您計劃要送出工作的數目，每次交易欄位的時間表示合併執行每項工作中的所有資料列所需的時間。

例如使用標準 S1 overage，如果您送出每天每個 consist 500 的列的 100 工作需要 0.72 秒每個，然後就會將每月 overage 成本 (每日 100 工作 = 3,100 的工作 mo *$0.50/1 K API 交易) 生產 API 交易成本為 $1.55 和 (500 個資料列*0.72 sec *3,100 工作*$2/hr) $620 生產 API 計算時針對總計 $621.55。

### <a name="azure-ml-classic-web-services"></a>Azure 毫升傳統 Web 服務

**是支付另存新檔您前往仍可使用？**
是的傳統的 Web 服務可仍在 Azure 電腦學習。  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Azure 電腦學習免費和標準層

**Azure 電腦學習免費層中包含哪些產品？**

Azure 電腦學習免費層被要提供 Azure 電腦學習 Studio 深入簡介。 您只需要是 Microsoft 帳戶登入。 免費層包括免費存取 Azure 電腦學習 Studio 工作區每個[Microsoft 帳戶](https://www.microsoft.com/account/default.aspx)。 其包含能夠使用 10 GB 的儲存空間及 operationalize 為暫存的 Api 的模型功能。 免費層負載未涵蓋的 SLA 和用於開發和僅限個人使用。 免費層負載 canâ€™ t access 資料連線到內部部署的 SQL server。

**Azure 電腦學習標準層和計劃中包含哪些產品？**

Azure 電腦學習標準層是 Azure 電腦學習 Studio 的付費的生產版本。 Azure 毫升服務 Studio 每月費用計費在每個工作區每個月基礎和按比例部分月。 Azure 毫升 Studio 實驗小時被付費每計算使用中的實驗小時。 計費按比例部分小時]。  

Azure 毫升 API 服務被付費是傳統的 web 服務或新的 web 服務而定。

下列費用會彙總每個訂閱的工作區。

* 電腦學習工作區訂閱-電腦學習工作區訂閱是提供存取毫升 Studio 工作區，才能執行實驗 studio 和利用生產 Api 的每月費用。
* Studio 營業時間-嘗試所有計算累算方式毫升 Studio 中執行的實驗，然後執行的生產 api 執行環境中的費用此量表彙總。
* Access 資料連線到內部部署中的 SQL server 訓練您模型和計分。
* 傳統的 Web 服務︰
    * 生產 API 營業時間-計算此量表包含計算費用累算方式執行生產環境中的 Web 服務。
    * 生產 API 交易 （1000s)-此量表包含每個您生產的 web 服務呼叫累算的費用。

除了前面的費用，若是使用新的 Web 服務費用彙總到選取方案︰

* 此表計劃的標準 S1/S2 S3 API （單位）-代表選取新的 Web 服務的執行個體的類型
* 標準 S1/S2 S3 Overage API 營業時間-計算此量表包含累算方式新的 Web 服務後使用現有的執行個體中包含的數量，生產環境中執行計算費用。 其他的使用狀況薪資 overate S1/S2 S3 計劃層相關聯的工資率。
* 標準 S1/S2 S3 Overage API 交易 （1,000s)-此量表包含現有的執行個體中包含的數量用完之後，每個新的 Web 服務您生產呼叫累算的費用。 其他的使用狀況薪資 overate S1/S2 S3 計劃層相關聯的工資率。
* 包含的數量 API 計算營業時間-使用新的 Web 服務，此量表代表包含的數量的 API 計算的時數
* 包含的數量 API 交易 （1,000s)-以新的 Web 服務，此量表代表包含的 API 交易的數量


**我如何註冊 Azure 毫升免費層？**

您只需要是 Microsoft 帳戶。 移至 [ [Azure 電腦學習 [常用](https://azure.microsoft.com/services/machine-learning/)]，然後按一下 [**立即開始**。 登入您的 Microsoft 帳戶和中可用的工作區為您建立層。 您可以開始探索，並立即建立電腦學習實驗。

**我如何註冊 Azure 毫升標準層？**

您必須必須先 Azure 訂閱的存取權才能建立標準毫升工作區。 您可以註冊 30 天免費試用 Azure 訂閱及更新版本升級成付費的 Azure 訂閱，或購買付費 Azure 訂閱徹底。 然後，您可以建立從 Microsoft Azure 傳統入口網站的電腦學習工作區以訂閱的存取後。 檢視的[逐步指示](https://azure.microsoft.com/trial/get-started-machine-learning-b/)。

或者，您可以邀請標準毫升工作區擁有者存取擁有者的工作區。

**指定若要使用的免費層自己 Azure blob 儲存體帳戶？**

否，標準層相當於電腦學習服務層帶來之前所使用的版本。

**可以部署學習為 Api 的模型中的免費層我的電腦嗎？**

是的您可以 operationalize 電腦學習模型與臨時 API 服務做為免費層的一部分。 才能讓實際執行暫存的 API 服務生產結束點取得 operationalized 服務，您必須使用標準層。

**什麼是 Azure 免費試用版和 Azure 電腦學習免費層之間的差異？**

[Microsoft Azure 免費試用版](https://azure.microsoft.com/free/)提供可套用至任何一個月的 Azure 服務的信用額度總計。 Azure 電腦學習免費層提供專為非生產負載 Azure 電腦學習服務的持續存取。

**如何從 Free 移動實驗標準層？**

若要從 Free 複製您實驗標準層︰

1.  登入 Azure 電腦學習 Studio，並確認您可以查看可用的工作區和標準的工作區中上方導覽列中的工作區選取器。
2.  如果您是在標準的工作區中，請切換至免費的工作區。
3.  在 [實驗清單] 檢視中，選取您想要複製，然後按一下 [複製] 命令] 按鈕實驗。
4.  從快顯對話方塊中選取標準的工作區，然後按一下 [複製] 按鈕。
    在標準的工作區實驗與複製所有相關聯的資料集、 訓練的模型等。
6.  您必須重新執行實驗，並重新發佈您在標準的工作區中的 web 服務。

### <a name="studio-workspace"></a>Studio 工作區

**會看到不同的工作區的不同帳單嗎？**

工作區費用發生分開每個適用計量器單一的帳單上。

**計算資源的特定種類我實驗會在都能夠執行？**

電腦學習服務是 multitenant 服務，並使用的後端上的實際計算資源視情況而定最佳化效能及預測。

### <a name="guest-access"></a>來賓存取

**什麼是 Azure 電腦學習 Studio 來賓存取？**

在受限制的試用體驗，可讓您可建立並執行實驗中 Azure 電腦的學習有 Studio 免費和驗證來賓存取。 來賓工作階段是非持續 （無法儲存） 和限制為 8 小時。 其他限制包含缺乏 R 和 Python 支援，缺少執行 Api 及受限制的資料集大小和儲存空間容量。 比較之下，選擇 [使用 Microsoft 帳戶登入的使用者擁有完整存取權的電腦學習 Studio 免費層所述上述其中包含 [常設工作區和完整功能。 選擇您可用的電腦學習經驗上的 [**快速入門** [https://studio.azureml.net](https://studio.azureml.net)，選取 [猜測 Access 或 Microsoft 帳戶登入。

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
