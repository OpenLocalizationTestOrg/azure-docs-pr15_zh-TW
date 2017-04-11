<properties 
    pageTitle="電腦學習建議 API 中的共同作業 |Microsoft Azure" 
    description="Azure 毫升建議範例應用程式" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 


# <a name="recommendations-api-sample-application-walkthrough"></a>建議 API 範例應用程式的逐步解說

>[AZURE.NOTE]您應該開始使用，而非此版本的建議 API 認知服務。 建議認知服務會取代這項服務，並會那里開發所有的新功能。 有新的功能，例如批次處理支援、 獲得更佳的 API 檔案總管，取得更簡潔的 API 表面、 更加一致註冊/計費體驗等。
> 深入瞭解[移轉至新的認知服務](http://aka.ms/recomigrate)

##<a name="purpose"></a>用途

這份文件會顯示 Azure 電腦學習建議 API，透過[範例應用程式](https://code.msdn.microsoft.com/Recommendations-144df403)的使用方式。

這個應用程式不是包含完整的功能，也不會使用所有 Api。 它會示範一些常見作業時您想要與電腦學習 recommendation service 執行。 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="introduction-to-machine-learning-recommendation-service"></a>電腦學習建議服務簡介

當您建立根據下列資料的建議模型時，會啟用透過機器學習 recommendation service 的建議︰

* 您想要建議，也稱為目錄項目的存放庫
* 代表每個使用者或 （這可取得一段時間，透過資料擷取，不做為範例應用程式的一部分） 工作階段的項目使用的資料

內建的建議模型之後，可以使用預測項目，使用者可能有興趣，根據項目 （或單一項目） 的一組使用者選取。

若要啟用前一個案例中，執行下列電腦學習 recommendation service 中︰

* 建立模型︰ 這是保留資料 （類別目錄及使用方式） 和預測模型的邏輯容器。 唯一識別碼，建立時，會配置透過識別每個模型容器。 此 ID 稱為模型識別碼，大部分的 Api 使用它。 
* 上的傳到目錄]: 建立模型容器時，您可以建立關聯，目錄。

**附註**︰ 建立模型及上傳至目錄通常會執行一次模型生命週期。

* 上傳的使用方式︰ 使用情況資料新增至模型容器。
* 建立建議模型︰ 有足夠的資料之後，您可以建立建議模型。 這項作業會使用上方的電腦學習演算法來建立建議的模型。 每個組建是與相關聯的唯一識別碼。 您需要保留這個 ID 的記錄，因為它是必要的一些 Api 的功能。
* 監控建置程序︰ 建議模型建立非同步作業，並可能需要幾分鐘的時間從數小時，根據資料 （類別目錄及使用方式） 和建立參數的量。 因此，您需要監控建立。 其相關聯的建立順利完成時，才建立的建議模型。
* （選用）選擇 [作用中的建議模型建置︰ 此步驟中，才需要，如果您有一個以上的建議模型內建於您的模型容器。 為建議，不必指出作用中的建議模型任何要求自動重新導向至 [預設作用中建立系統內。 

**附註**︰ 作用中的建議模型是實際執行和內建的生產工作量。 這是非作用中建議模型，會保留在 （有時稱為臨時） 測試類似的環境與不同。

* 取得的建議︰ 建議模型之後，您可以觸發單一項目或您選取的項目清單的建議。 

您通常會叫取得建議的某一段時間。 該期間的時間，您可以使用資料重新導向至電腦學習建議系統，加入此資料至指定的模型容器。 如果您有足夠的使用情況資料時，您可以建立新的建議模型，其中包含其他的狀況。 

##<a name="prerequisites"></a>必要條件

* Visual Studio 2013
* 網際網路存取 
* 訂閱 API (https://datamarket.azure.com/dataset/amla/recommendations) 的建議。

##<a name="azure-machine-learning-sample-app-solution"></a>Azure 電腦學習範例應用程式解決方案

此方案包含原始程式碼、 範例使用狀況、 目錄檔案，以及下載所需的編譯的套件的指示詞。

##<a name="the-apis-used"></a>使用 Api

應用程式使用電腦學習建議功能，透過使用 Api 的子集合。 下列 Api 示範應用程式中︰

* 建立模型︰ 建立邏輯容器，以保留資料以及建議的模型。 模型由名稱，並包含相同的名稱，則無法建立一個以上的模型。
* 上傳的檔案目錄︰ 使用上傳目錄資料。
* 使用檔案上傳︰ 使用上傳的使用情況資料。
* 觸發建立︰ 使用建立建議模型。
* 監控建置執行︰ 使用監視建議模型建立的狀態。
* 選擇建議的內建的模型︰ 使用指出預設用於特定模型容器的建議模型。 您有一個以上的建議模型，而您想要啟動的非作用中建立為作用中的建議模式時，才需要此步驟。
* 取得建議︰ 使用來擷取建議的項目，根據指定的單一項目或一組項目。 

Api 的完整描述，請參閱 Microsoft Azure Marketplace 文件。 

**附註**︰ 模型可以有多個建置一段時間 （不同時）。 每個組建建立具有相同或更新的目錄與其他使用情況資料。

## <a name="common-pitfalls"></a>常見的錯誤

* 您需要提供您的使用者名稱與您的 Microsoft Azure Marketplace 主要帳戶金鑰，以執行範例應用程式。
* 連續執行的範例應用程式將會失敗。 建立、 上傳、 建立監視器，以及從預先定義的模型; 取得建議，包括應用程式的流程因此，它將會失敗連續執行您不要變更引動之間的模型名稱。
* 建議可能會傳回不含資料。 範例應用程式使用很小目錄及使用方式的檔案。 因此，從目錄的某些項目會有任何建議的項目。

## <a name="disclaimer"></a>免責聲明
若要執行生產環境中不是範例應用程式。 在目錄中提供的資料很小，而它不會提供有意義的建議模型。 示範提供資料了。 
 
