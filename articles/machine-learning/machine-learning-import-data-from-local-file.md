<properties
    pageTitle="資料匯入的本機檔案從電腦學習 Studio |Microsoft Azure"
    description="如何將資料匯入訓練 Azure 電腦學習 Studio 從本機檔案。"
    keywords="匯入資料的資料格式、 資料類型、 資料來源、 訓練資料"
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
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>訓練資料匯入 Azure 電腦學習 Studio 從本機檔案

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


若要在電腦學習 Studio 中使用您自己的資料，您可以從，建立您的工作區中的資料集的模組本機硬碟上傳提前資料檔案。 


## <a name="import-data-from-a-local-file"></a>從本機檔案匯入資料

您可以執行下列動作匯入資料從本機磁碟機︰

1. 按一下 [ **+ 新增**電腦學習 Studio 視窗的底部。
2. 選取**資料集**和**從本機檔案**。
3. 在 [**上傳新的資料集**] 對話方塊中，瀏覽至您要上傳的檔案
4. 輸入名稱、 找出資料類型]，您也可以輸入描述。 描述建議-，可讓您記錄的資料，您會想要在未來使用的資料時，請謹記有關任何特性。
5. **這是新版本的現有資料集**的核取方塊可讓您以新的資料更新現有的資料集。 只要按一下此核取方塊，然後輸入 [現有的資料集的名稱。

在上傳]，您會看到一則訊息，要上傳檔案。 上傳時間取決於您的資料大小和服務的連線速度。
如果您知道檔案會花很長的時間，您可以在等待時執行電腦學習 Studio 內的其他項目。 不過，關閉瀏覽器會造成資料上傳失敗。

上傳您的資料後，儲存在資料集模組，讓您的工作區中的任何實驗。
當您編輯實驗時，您可以尋找您已建立模組調色盤中的 [**儲存資料集**] 清單底下的 [**我的資料集**] 清單中的資料集。 您可以藉由拖放到實驗畫布資料集，當您想要進一步分析及電腦學習使用資料集。



