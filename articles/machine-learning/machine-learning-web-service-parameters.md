<properties 
    pageTitle="使用 Azure 電腦學習 Web 服務參數 |Microsoft Azure" 
    description="如何使用 Azure 電腦學習 Web 服務參數存取 web 服務時，請修改模型的行為。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="raymondl;garye"/>

#<a name="use-azure-machine-learning-web-service-parameters"></a>使用 Azure 電腦學習 Web 服務參數

Azure 電腦學習 web 服務建立發佈實驗包含可設定參數使用的模組。 在某些情況下，您可能會想要執行的 web 服務時，變更的模組行為。 *Web 服務參數*可讓您執行這項工作。 

常見的範例設定[匯入資料][reader]模組，讓使用者發佈的 web 服務的存取 web 服務時，指定不同的資料來源。 設定[匯出資料]或者[writer]模組，讓您可以指定不同的目的地。 其他一些範例包括變更數字的位元版的[功能雜湊][feature-hashing]模組或數所需功能為[篩選依據功能選擇][filter-based-feature-selection]模組。 

您可以設定 Web 服務參數，並產生關聯一或多個模組參數的實驗中,，您可以指定是否有必要或選用。 Web 服務的使用者可以再提供值這些參數撥打電話的 web 服務時。 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##<a name="how-to-set-and-use-web-service-parameters"></a>如何設定和使用 Web 服務參數

您可以定義 Web 服務參數按一下模組參數] 旁的圖示，然後選取 「 設定為 [web 服務參數 」。 這會建立新 Web 服務參數，並將它連接至該模組參數。 然後，存取 web 服務時，使用者可以將 Web 服務參數指定的值，並會套用至模組參數。

一旦您定義 Web 服務的參數，就使用實驗中的任何其他模組參數。 如果您定義 Web 服務參數的參數，其中一個模組相關聯時，您可以使用 [該相同的 Web 服務參數的任何其他模組，只要參數預期相同類型的值也可以。 例如，如果 Web 服務參數是一個數字值，然後它僅能模組參數的預期的數值。 當使用者設定 Web 服務參數的值時，其套用到所有相關聯的模組參數。

您可以決定是否要提供 Web 服務參數的預設值。 如果您這麼做，參數是選擇性的 web 服務的使用者。 如果您不提供的預設值，輸入值，存取 web 服務時所需的使用者。

Web 服務的 API 文件，包括如何指定 Web 服務參數以程式控制方式存取 web 服務時的 web 服務使用者資訊。

>[AZURE.NOTE] 傳統的 web 服務的 API 文件是透過**API 說明頁面**中的 [連結的 web 服務**儀表板**電腦學習 Studio 中提供。 新的 web 服務的 API 文件，透過[Azure 機器學習 Web 服務](https://services.azureml.net/Quickstart)入口網站頁面**消耗**和**Swagger API**提供 web 服務。


##<a name="example"></a>範例

例如，假設我們有[匯出資料]的實驗[writer]會傳送資訊給 Azure blob 儲存體的模組。 我們會定義一個名為 「 Blob 路徑 」 的 Web 服務參數可讓 web 服務使用者存取服務時，變更路徑至 blob 儲存體。

1.  在電腦學習 Studio 中，按一下 [[匯出資料][writer]模組加以選取。 在右側的實驗畫布上的 [屬性] 窗格中顯示其內容。

2.  指定的儲存空間類型︰

    - **請指定資料目的地**，請在 [選取 「 Azure Blob 儲存體]。
    - 在 [**請指定驗證類型**，請選取 「 帳戶 」。
    - 輸入 Azure blob 儲存體帳戶資訊。 
    <p />

3.  按一下右邊的**路徑 blob 開頭為容器參數**的圖示。 它看起來像這樣︰

    ![Web 服務參數圖示][icon]

    選取 「 設定為 [web 服務參數 」。

    在 [ **Web 服務參數**名稱 」 路徑 blob 開頭為容器 」 的 [屬性] 窗格底部新增項目。 這是 Web 服務的參數，現在這個[匯出資料]與相關聯[writer]模組參數。

4.  若要重新命名 Web 服務參數，請按一下名稱，輸入 「 Blob 路徑 」，按**Enter**鍵。 
 
5.  Web 服務參數提供的預設值，按一下右邊的 [名稱] 圖示、 選取 「 提供預設值]、 輸入值 (例如，「 container1/output1.csv 」)，然後按**Enter**鍵。

    ![Web 服務的參數][parameter]

6.  按一下 [**執行**]。 

7.  按一下 [**部署 Web 服務**，然後選取**部署 Web 服務 [傳統]**或**[新增] 部署 Web 服務**部署 web 服務。

Web 服務的使用者現在可以[匯出資料]的指定新目的地[writer]存取 web 服務時的模組。

##<a name="more-information"></a>詳細資訊

如需更詳細的範例，請參閱[Web 服務參數](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx)中的項目[電腦學習部落格](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx)。

如需有關如何存取電腦學習 web 服務的詳細資訊，請參閱[如何使用已發佈的電腦學習 web 服務](machine-learning-consume-web-services.md)。



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 
