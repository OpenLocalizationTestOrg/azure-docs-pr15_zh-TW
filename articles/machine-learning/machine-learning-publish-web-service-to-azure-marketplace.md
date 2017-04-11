<properties 
    pageTitle="發佈電腦學習 web 服務 Azure Marketplace |Microsoft Azure" 
    description="如何將 Azure 電腦學習 Web 服務發佈至 Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="BharathS" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="bharaths"/>

# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>將 Azure 機器學習 Web 服務發佈至 Azure Marketplace 

Azure Marketplace 提供發佈 Azure 電腦學習 web 服務時所支付或釋消耗量外部的客戶服務的能力。 本文提供的指導方針可協助您著手連結的程序概觀。 藉由使用此程序，您可以提供 web 服務應用程式中使用其他開發人員。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>發佈程序概觀 

發佈至 Azure Marketplace 的 Azure 電腦學習 web 服務的步驟如下︰

1. 建立及發佈電腦學習要求回應服務 （給）
2. 部署服務生產，並取得 API 金鑰與 OData 的結束點資訊。
3. 使用已發佈的 web 服務的 URL 發佈至[Azure Marketplace （資料市集）](https://publish.windowsazure.com/workspace/) 
4. 一旦送出，檢閱您的提議是，必須先經過核准，您的客戶之前可以購買該。 發佈程序可能需要幾個工作日。 

## <a name="walk-through"></a>逐步解說
###<a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>步驟 1︰ 建立及發佈電腦學習要求回應服務 （給）###
 如果您有沒有這樣做，請查看此[逐步解說](machine-learning-walkthrough-5-publish-web-service.md)。

###<a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>步驟 2︰ 將服務部署生產，並取得 API 金鑰與 OData 結束點資訊###
1. 從[Azure 傳統入口網站](http://manage.windowsazure.com)中，選取**電腦學習**選項從左側的導覽列中，然後選取您的工作區。 

2. 按一下 [ **WEB 服務**] 索引標籤，然後選取您想要發佈至服務商場的 web 服務。

    ![Azure Marketplace][workspace]

3. 選取您想要有取用服務商場的端點。 如果您還沒有建立任何其他的結束點，您可以選取**預設**的結束點。

4. 當您按一下端點上時，您可以查看**API 金鑰**。 您將需要這段稍後在步驟 3 中的資訊，讓它的複本。

    ![Azure Marketplace][apikey]

5. 按一下 [**要求/回應**的方法，我們不支援發佈批次執行服務商場這一點。 如此您將會 API 說明] 頁面的 [要求/回應] 方法。

6. 複製**OData 端點位址**，您必須在步驟 3 中稍後這項資訊。

    ![Azure Marketplace][odata]




生產部署服務。



###<a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>步驟 3︰ 使用已發佈的 web 服務的 URL 發佈至 Azure Marketplace (DataMarket)###

1.  瀏覽至[Azure Marketplace （資料市集）](http://datamarket.azure.com/home) 
2.  按一下頁面頂端的 [**發佈**] 連結。 這將您移至[Microsoft Azure 發佈入口網站](https://publish.windowsazure.com)
3.  按一下 [註冊為 publisher**的發行者**] 區段。
4.  在建立新優惠給時，選取 [**資料服務**]，然後按一下 [**建立新的資料服務**。 
 
    ![Azure Marketplace][image1]

    <br />


5.  在 [**方案]**下提供您的產品，包括價格的計劃中的資訊。 如果您將會提供的免費或付費服務，決定。 若要取得支付提供付款資訊，例如您銀行與稅額的資訊。

6.  下**行銷**提供您提供的服務，例如標題和描述您的提議的相關資訊。

7.  在 [**價格**您可以為特定國家/地區中，您計劃的價格，或讓系統 」 autoprice 「 您的提議。

8. 在 [**資料服務**] 索引標籤上按一下 [ **Web 服務**做為**資料來源**。

    ![Azure Marketplace][image2]

9.  在上述的步驟 2 中所述，請從 Azure 傳統入口網站，取得 web 服務 URL] 與 [API 金鑰。

10. 在 [Marketplace 資料服務設定] 對話方塊中，貼上將**服務 URL** ] 文字方塊中的 OData 端點位址。

11. 進行**驗證**，選擇 [為**驗證配置**的 [**頁首**]。

    - 輸入 「 授權 」 做為**標頭名稱**。
    - 針對**標頭值**中，輸入 「 承載者 」 （不含引號） 列的 [**間距**]，然後按一下再貼上的 API 金鑰。
    - 選取**此服務是 OData** ] 核取方塊。
    - 按一下 [**測試連接**]，測試連線]。

12. 在 [**類別**] 底下，確定**電腦學習**已選取。

13. 當您完成輸入您的提議，所有的中繼資料**發佈**]，然後**推入臨時**上按一下。 此時，您就會收到任何剩餘的問題，您必須先修正。

14. 您已完成所有未完成的問題之後，按一下**要求核准的推入生產**。 發佈程序可能需要幾個工作日。 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 
