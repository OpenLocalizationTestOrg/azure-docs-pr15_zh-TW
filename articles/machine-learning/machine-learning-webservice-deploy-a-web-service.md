<properties
   pageTitle="部署新的 Web 服務"
   description="工作流程的部署手臂依據 web 服務"
   services="machine-learning"
   documentationCenter=""
   authors="vDonGlover"
   manager="raymondl"
   editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>

# <a name="deploy-a-new-web-service"></a>部署新的 web 服務

Microsoft Azure 電腦學習現在提供 web 服務為基礎的[Azure 資源管理員](../azure-resource-manager/resource-group-overview.md)允許新的計費方案選項及部署到多個區域的 web 服務。

部署 web 服務使用 Microsoft Azure 電腦學習 Web 服務的一般工作流程是︰

* 建立預測的實驗
* 將其部署
* 設定它的名稱
* 帳單寄送的計劃
* 測試
* 使用它。

下圖說明工作流程。

![Web 服務部署工作流程][1]
 
## <a name="deploy-web-service-from-studio"></a>部署 Studio 從 web 服務 

若要部署實驗做為新的 web 服務。 登入電腦學習 Studio，並建立新的預測 web 服務。 

**注意**︰ 如果您已經部署為傳統的 web 服務的實驗無法將其部署做為新的 web 服務。
 
按一下 [實驗畫布底部的 [**執行**，然後按一下 [**部署 Web 服務**及**部署 Web 服務 [新增]**。 隨即會開啟 [部署] 頁面的電腦學習 Web 服務管理員。

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>電腦學習 Web 服務管理員部署實驗頁面
在 [部署實驗] 頁面中，輸入 web 服務的名稱。
選取價格的計劃。 如果您有現有的價格計劃加以選取，否則您必須建立新的價格方案的服務。 

1.  **價格計劃**中的下拉式清單，選取現有的計劃或選取 [**選取新的計劃**] 選項。
2.  在 [**名稱**] 中，輸入會找出您的帳單上的方案名稱。
3.  選取其中**每月方案層級**的詳細資訊。 請注意您預設的地區和您的 web 服務方案的計劃層預設已部署至該區域。

按一下 [**部署**] 和 [您的 web 服務隨即會開啟用 [快速入門] 頁面。

## <a name="quickstart-page"></a>快速入門頁面
Web 服務快速入門頁面可讓您存取及建立新 web 服務之後，您將會執行的常見工作。 從這裡您可以輕鬆存取的**測試**頁 」 及 「**消耗**頁面。

## <a name="testing-your-web-service"></a>測試您的 web 服務

從 [快速入門] 頁面中，按一下 [常見的工作] 下方的 [測試 web 服務]。   

若要測試的 web 服務為要求回應服務 （給）︰

* 按一下 [在功能表列上的 [**測試**]。
* 按一下 [**要求回應**]。
* 輸入的資料行的實驗輸入適當的值。
* 按一下 [測試**要求回應**]。

您的結果會顯示在右側的頁面上。

若要測試批次執行服務 (BES) web 服務，您會使用 CSV 檔案︰

* 按一下 [在功能表列上的 [**測試**]。
* 按一下 [**批次**]。
* 在您輸入時，按一下 [瀏覽並瀏覽至您的範例資料檔案。
* 按一下 [**測試**]。

測試您的狀態，顯示在**測試批次工作**。

## <a name="consuming-your-web-service"></a>使用 Web 服務

部署為 web 服務，Azure 電腦學習實驗會提供可由許多裝置與平台 REST API。 這是因為簡單 REST API 接受及回應使用 JSON 格式化郵件。 Azure 電腦學習入口網站提供可用於呼叫 R、 C# 和 Python web 服務的程式碼。
 
在 [Consuming 頁面上，您可以找到︰

* API 金鑰與 URI 的使用中應用程式的 web 服務。
* Excel 和 web 應用程式範本以開始啟動消耗程序。
* C#、 python 和 R 協助您入門的範例。

如需有關如何使用 web 服務的詳細資訊，請參閱[如何使用的電腦，學習實驗從已部署 Azure 電腦學習 web 服務](machine-learning-consume-web-services.md)。

## <a name="next-steps"></a>後續步驟

如需有關如何使用 web 服務的詳細資訊，請參閱︰

[如何使用已部署從電腦，學習實驗 Azure 電腦學習 web 服務](machine-learning-consume-web-services.md)

[Azure 機器學習 Web 服務︰ 部署及消耗](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->
