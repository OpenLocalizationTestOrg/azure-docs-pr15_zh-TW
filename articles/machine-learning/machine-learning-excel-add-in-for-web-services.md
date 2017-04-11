<properties
    pageTitle="Excel 增益集電腦學習 Web 服務 |Microsoft Azure"
    description="如何直接在 Excel 中使用 Azure 電腦學習 Web 服務，無須撰寫程式碼。"
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />

# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Excel 增益集 Azure 電腦學習 Web 服務

Excel 方便您將來電直接而無需撰寫程式碼的 Web 服務。

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>若要使用現有的 Web 服務活頁簿中的步驟

1. 開啟[範例 Excel 檔案](http://aka.ms/amlexcel-sample-2)，其中包含 Excel 增益集及乘客 Titanic 上的相關資料。
2. 按一下以選擇 Web 服務-「 浩大保養預測工具 （Excel 增益集範例） [成績] 「 在此範例中。

    ![選取 [Web 服務][01]

3. 這會將您帶往 [ **Predict** ] 區段。  此活頁簿已經包含範例資料，但的空白活頁簿，您可以在 Excel 中選取儲存格，按一下 [**使用範例資料**。
4. 選取具有標題的資料，然後按一下 [輸入的資料範圍] 圖示。  請確定已核取 「 我的資料有標題] 方塊。
5. 在**輸出**] 底下輸入您要的輸出，例如 「 H1 」 以下的儲存格的數字。
6. 按一下 [**預測**。

    ![預測] 區段][02]

## <a name="steps-to-add-a-new-web-service"></a>若要新增新的 Web 服務的步驟

部署 Web 服務，或使用現有的 Web 服務。 如需部署 Web 服務的詳細資訊，請參閱[逐步解說步驟 5︰ 部署 Azure 電腦學習 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)。

取得您的 Web 服務的 API 金鑰。 您執行這取決於您是否發佈傳統電腦學習 Web 服務的新電腦學習 Web 服務。

**使用傳統的 Web 服務** 

1. 在電腦學習 Studio 中，按一下左窗格中的 [ **WEB 服務**] 區段，然後選取 Web 服務。

    ![Studio 選取 Web 服務][04]

2. 將複製的 Web 服務的 API 金鑰。

    ![Studio API 金鑰][05]

3. 在 Web 服務的 [**儀表板**] 索引標籤中，按一下 [**要求/回應**] 連結。
4. 尋找 [**要求 URI** ] 區段。  複製並儲存 URL。

>[AZURE.NOTE] 您現在可以登入[Azure 機器學習 Web 服務](https://services.azureml.net)入口網站以取得傳統電腦學習 Web 服務的 API 金鑰。

**使用新的 Web 服務**

1. 在[Azure 機器學習 Web 服務](https://services.azureml.net)入口網站中，按一下 [ **Web 服務**]，然後選取您的 Web 服務。 
2. 按一下 [**取用**]。
3. 尋找 [**基本消耗資訊**] 區段。 複製並儲存**主索引鍵**和**要求回應**的 URL。


## <a name="steps-to-add-a-new-web-service"></a>若要新增新的 Web 服務的步驟

1. 部署 Web 服務，或使用現有的 Web 服務。 如需部署 Web 服務的詳細資訊，請參閱[逐步解說步驟 5︰ 部署 Azure 電腦學習 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)。
2. 按一下 [**取用**]。
3. 尋找 [**基本消耗資訊**] 區段。 複製並儲存**主索引鍵**和**要求回應**的 URL。
2. 在 Excel 中，請移至 [ **Web 服務**] 區段 （如果您是在**Predict** ] 區段中，按一下以移至 Web 服務的清單中的返回箭號）。

    ![移至 Web 服務的選取範圍][03]
    
3. 按一下 [**新增 Web 服務**。
4. 將 URL 貼入 [Excel 增益集] 文字方塊中標示為**URL**。
5. 標示為 [ **API 金鑰**在文字方塊中，貼上的 API/主索引鍵。
6. 按一下 [**新增**]。

    ![傳統的 Web 服務的 URL] 與 [API 金鑰。][06]

10. 若要使用的 Web 服務，依照上述，」 步驟以使用現有 Web 服務]。

## <a name="sharing-your-workbook"></a>共用活頁簿

如果您儲存活頁簿時，會一併儲存您已新增的 Web 服務的 API/主索引鍵。 這表示您只應與您所信任的個人共用活頁簿。

要求動作的 [註解] 區段中，或我們[論壇](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)上任何問題。

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png
