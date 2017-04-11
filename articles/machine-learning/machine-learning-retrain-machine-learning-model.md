<properties
    pageTitle="重新訓練電腦，學習模型 |Microsoft Azure"
    description="瞭解如何重新訓練模型及更新 Web 服務中 Azure 電腦學習使用新訓練的模型。"
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
    ms.date="10/10/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-machine-learning-model"></a>重新訓練電腦，學習模型

Operationalization 的電腦學習模型中 Azure 電腦學習的程序的一部分，是訓練模型，並將其儲存中。 您再使用它來建立 predicative Web 服務。 在網站、 儀表板，以及行動應用程式中時，可以再使用 Web 服務。 

使用電腦學習建立的模型通常是不是靜態。 可用的新資料時，或當 API 的消費者有自己的資料模型必須重新培訓。 

重新訓練可能的常見問題。 使用以程式設計方式重新訓練 API 功能，您可以以程式設計方式重新訓練使用重新訓練 Api 模型] 並更新 Web 服務訓練新的模型。 

這份文件說明的訓練的程序，並說明如何使用重新訓練 Api。

## <a name="why-retrain-defining-the-problem"></a>為什麼重新訓練︰ 定義問題  

電腦學習訓練程序的一部分，模型被訓練使用一組資料。 使用電腦學習建立的模型通常是不是靜態。 可用的新資料時，或當 API 的消費者有自己的資料模型必須重新培訓。

在下列情況下，以程式設計方式的 API 方便您允許您或您的 api 消費者建立的用戶端，可以為基礎一次性或一般，重新訓練使用自己的資料模型。 他們可以評估結果重新訓練，然後更新 Web 服務 API 使用新訓練的模型。

>[AZURE.NOTE] 如果您有現有的訓練課程實驗和新的 Web 服務時，您可能會想要查看重塑現有預測 Web 服務，而不是遵循下一節中所提及的逐步解說。

## <a name="end-to-end-workflow"></a>端對端工作流程 

程序牽涉下列元件︰ 訓練實驗及預測的實驗發佈為 Web 服務。 若要啟用重新訓練的訓練的模型，訓練實驗必須發佈為 Web 服務的訓練模型的輸出。 這可讓 API 存取模型重新訓練。 

下列步驟適用於 [新增] 和 [傳統的 Web 服務︰

建立的初始的預測 Web 服務︰

* 建立訓練實驗
* 建立預測 web 實驗
* 部署預測 web 服務

重新訓練的 Web 服務︰

* 若要允許重新訓練更新訓練實驗
* 部署訓練 web 服務
* 使用批次執行服務代碼重新訓練模型

上述步驟的逐步解說，請參閱[重新訓練電腦學習模型以程式設計方式](machine-learning-retrain-models-programmatically.md)。

如果您部署傳統的 Web 服務︰

* 預測 Web 服務上建立新的端點
* 取得更新的 URL 及程式碼
* 使用更新的 URL 指向 retrained 模型中的新的端點 

上述步驟的逐步解說，請參閱[重新訓練傳統的 Web 服務](machine-learning-retrain-a-classic-web-service.md)。

如果您遇到困難重新訓練傳統的 Web 服務時，請參閱[疑難排解重新訓練 Azure 電腦學習傳統 Web 服務](machine-learning-troubleshooting-retraining-models.md)。

如果您部署新的 Web 服務︰

* Azure 資源管理員帳戶登入
* 取得 Web 服務定義
* 將 Web 服務定義匯出為 JSON
* 更新的參照`ilearner`blob JSON 中
* JSON 匯入 Web 服務定義
* 使用新的 Web 服務定義更新 Web 服務

上述步驟的逐步解說，請參閱[重新訓練新的 Web 服務使用的電腦學習管理 PowerShell 指令程式](machine-learning-retrain-new-web-service-using-powershell.md)。

設定重新訓練傳統的 Web 服務的程序牽涉下列步驟︰

![訓練的程序概觀][1]

設定新的 Web 服務重新訓練的程序牽涉下列步驟︰

![訓練的程序概觀][7]

## <a name="other-resources"></a>其他資源

- [Azure 資料工廠重新訓練與更新 Azure 電腦學習模型](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
- [建立多個電腦學習模型和 web 服務端點從一個實驗使用 PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
- [AML 重新訓練模型使用 Api](https://www.youtube.com/watch?v=wwjglA8xllg)影片示範如何重新訓練電腦學習模型建立 Azure 電腦學習使用重新訓練 Api 與 PowerShell。

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

