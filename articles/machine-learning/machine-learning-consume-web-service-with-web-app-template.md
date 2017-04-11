<properties
    pageTitle="使用電腦學習 web 服務 web 應用程式範本 |Microsoft Azure"
    description="使用在 Azure Marketplace 的 web 應用程式範本，來使用中 Azure 電腦學習預測 web 服務。"
    keywords="web 服務，operationalization，REST API 電腦學習"
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
    ms.date="10/10/2016"
    ms.author="garye;raymondl"/>

# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>使用 web 應用程式範本 Azure 電腦學習 web 服務

>[AZURE.NOTE] 本主題將說明適用於傳統的 web 服務的技巧。 

一次您開發預測模型及部署其為使用電腦學習 Studio，Azure web 服務或工具，例如 R 或 Python，您可以使用存取使用 REST API operationalized 的模型。

有數種方式來取用 REST API 及存取 web 服務。 例如，您可以在 C#、 R 或使用部署 （在電腦學習 Studio 的 web 服務儀表板的 API 說明頁面上提供） 的 web 服務時，為您產生的程式碼範例 Python 撰寫應用程式。 或者，您可以使用範例 Microsoft Excel 活頁簿，為您建立 （也可以在 web 服務儀表板 Studio 中）。

但存取您的 web 服務的最快，最簡單的方法是透過[Azure Web 應用程式服務商場](https://azure.microsoft.com/marketplace/web-applications/all/)提供的 Web 應用程式範本。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Azure 電腦學習 Web 應用程式範本

Web 應用程式中的可用範本 Azure Marketplace 可以建立自訂 web 應用程式的知道您的 web 服務輸入的資料，以及預期的結果。 您只需要提供 web 應用程式存取您的 web 服務和資料，而範本執行其餘部分。

兩個範本可供使用的︰

- [Azure 毫升要求回應服務 Web 應用程式範本](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Azure 毫升批次執行服務 Web 應用程式範本](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

每個範本建立範例 ASP.NET 應用程式，使用 API URI 和鍵 web 服務，，並將其部署為 Azure 網站。 要求回應服務 （給） 範本建立可讓您傳送至 web 服務以取得單一結果的單一資料列的 web 應用程式。 批次執行服務 (BES) 範本建立可讓您傳送多個資料列需要取得多個結果的 web 應用程式。

沒有編碼，才能使用這些範本。 您只需提供的 API URI 及索引鍵和範本所建立您的應用程式。

## <a name="how-to-use-the-request-response-service-rrs-template"></a>如何使用要求回應服務 （給） 範本

一旦您已部署 web 服務，您可以遵循下列步驟以使用給 web 應用程式範本，如下圖所示。

![若要使用給網站範本的程序][image1]

1. 在電腦學習 Studio 中，開啟 [ **Web 服務**] 索引標籤，然後開啟您要存取的 web 服務。 複製列在 [ **API 金鑰**下的鍵，並將其儲存。

    ![API 金鑰][image3]

2. 開啟**要求/回應**API 說明頁面。 按一下頂端的 [說明] 頁面下**要求**，請複製**要求 URI**值並將其儲存。 此值看起來像這樣︰

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![要求 URI][image4]

3. 請移至[Azure 入口網站](https://portal.azure.com)，**登入**，再按一下 [**新增**]、 [搜尋] 和 [選取**Azure 毫升要求回應服務 Web 應用程式**，然後按一下 [**建立**]。 

    - 授與您的 web 應用程式的唯一名稱。 Web 應用程式的 URL 會此名稱後面加上`.azurewebsites.net.`，例如`http://carprediction.azurewebsites.net.`

    - 選取 [Azure 訂閱及執行您的 web 服務的服務。

    - 按一下 [**建立**]。

    ![建立 web 應用程式][image5]

4. 當 Azure 完成部署 web 應用程式時，請按一下 Azure 中的 [web 應用程式設定] 頁面上的**URL** ，或在網頁瀏覽器中輸入的 URL。 例如，`http://carprediction.azurewebsites.net.`

5. Web 應用程式首次執行時，就會要求您**API 文章 URL**以及**API 金鑰**。
輸入您先前儲存的值︰
    - 從**API 文章 URL**的 API 說明頁面的 [**要求 URI**
    - 從 web 服務儀表板**API 金鑰** **API 金鑰**。

    按一下 [**送出**]。

    ![輸入電子郵件地址] 和 API 金鑰文章 URI][image6]

6. Web 應用程式會顯示其與目前的 web 服務設定**Web 應用程式設定**] 頁面。 這裡，您可以使用 web 應用程式的設定進行變更。

    > [AZURE.NOTE] 變更的設定只會將其變更此 web 應用程式。 它不會變更您的 web 服務的預設設定。 例如，如果您變更**描述**以下它不會變更電腦學習 Studio 的 web 服務儀表板上顯示的說明。

    當您完成時，按一下 [**儲存變更**，，然後按一下 [**移至首頁**。

7. 從 [首頁] 頁面，您可以輸入值傳送給您的 web 服務，按一下 [**提交**]，並會傳回的結果。

如果您想要回到 [**設定**] 頁面，請移至`setting.aspx`web 應用程式的頁面。 例如︰`http://carprediction.azurewebsites.net/setting.aspx.`系統會提示您再次輸入 API 金鑰-您需要的存取頁面及更新的設定。

您可以停止、 重新啟動，或刪除像其他任何 web 應用程式 Azure 入口網站中的 web 應用程式。 只要執行您可以瀏覽至常用的網址，然後輸入新的值。

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>如何使用批次執行服務 (BES) 範本

除了，web 應用程式所建立可讓您送出多個資料列及接收多個結果，您可以使用 BES web 應用程式範本給範本，為相同的方式。

批次執行 web 服務的結果會儲存在 Azure 存放容器。輸入的值可以來自 Azure 儲存空間或本機檔案。
因此，您需要 Azure 存放容器，以保留 web 應用程式時，所傳回的結果，必須先準備好您輸入的資料。

![若要使用 BES 網站範本的程序][image2]

1. 請依照建立 BES web 應用程式給範本，先以外的相同程序︰
    - 從**批次執行**API [說明] 頁面中取得 web 服務**要求 URI** 。
    - 移至[Azure 毫升批次執行服務 Web 應用程式範本](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)開啟 Azure marketplace BES 範本，然後按一下 [**建立 Web 應用程式**。

2. 若要指定您要儲存的結果，請輸入目的地容器資訊在 web 應用程式首頁上。 也指定 web 應用程式可讓輸入的值的本機檔案或 Azure 存放容器。
按一下 [**送出**]。

    ![儲存資訊][image7]

Web 應用程式會顯示與工作狀態的頁面。
完成工作後會提供您 Azure blob 儲存體中的結果的位置。 您也可以選擇下載至本機檔案的結果。

## <a name="for-more-information"></a>如需詳細資訊

若要深入瞭解...

- 與電腦學習 Studio 建立電腦學習實驗，請參閱[建立第一個實驗 Azure 電腦學習 Studio 中](machine-learning-create-experiment.md)

- 如何部署您的電腦學習嘗試為 web 服務，請參閱 [[部署 Azure 電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)

- 若要存取您的 web 服務的其他方法，請參閱[如何使用 Azure 電腦學習 web 服務](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png
