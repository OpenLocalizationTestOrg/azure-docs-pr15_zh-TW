<properties 
    pageTitle="設定和使用電腦學習建議 API |Microsoft Azure" 
    description="Microsoft 建議 API 使用 Azure 電腦學習常見問題集" 
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

#<a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>設定與使用電腦學習建議 API 常見問題集


**建議是什麼？**

>[AZURE.NOTE]您應該開始使用，而非此版本的建議 API 認知服務。 建議認知服務會取代這項服務，並會那里開發所有的新功能。 有新的功能，例如批次處理支援、 獲得更佳的 API 檔案總管，取得更簡潔的 API 表面、 更加一致註冊/計費體驗等。
> 深入瞭解[移轉至新的認知服務](http://aka.ms/recomigrate)

針對組織，以交叉銷售建議和向上銷售產品和服務，其客戶所依賴的企業中 Azure 電腦學習建議會提供自助建議引擎。 是，使用矩陣 factorization 為其核心演算法的共同作業的篩選實作。 應用程式開發人員可以使用 REST Api 來存取的建議。 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**我可以建議用來做什麼？**

建議會採用做為輸入項目或一組項目，並傳回的相關建議清單。 例如︰ 線上零售商的客戶按一下產品。 線上零售商該產品做為建議輸入、，取得的產品清單與決定哪一項這些產品就會顯示給客戶。 您可能會想要使用的建議，最佳化您的線上商店或甚至會影響您的內部銷售部門或通話的中心。

**是否有任何使用限制？**

建議具有以下的使用方式限制︰
* 最大的數字的每個訂閱的模型︰ 10
* 目錄可以保留的項目數目上限︰ 100000
* 使用點會保留的數目上限為 ~ x 5000000。 從最舊會被刪除如果要上傳新檔案，或報告。
* 可以傳送電子郵件 （例如，匯入目錄資料，匯入使用情況資料） 中的資料大小上限為 200 MB
* 秒交易的 (TPS) 的建議模型建立的不是作用中的數字是 ~ 2 TPS。 作用中的建議模型建立可以按住 20 個 TPS。

##<a name="purchase-and-billing"></a>購買與帳單 


**多少建議成本啟動期間？**

建議是訂閱式服務。 付費為基礎的每個月的交易音量。 您可以核取 [提供頁面] (https://datamarket.azure.com/dataset/amla/recommendations) 在 Microsoft Azure Marketplace 的價格資訊。

**有任何有建議相關聯的成本追蹤及儲存為我的使用者活動嗎？**

不在的時間。

**建議是否有免費試用版？**

有哪些限制為每個月的 10000 交易的免費記錄。

**何時我計費建議？**

付費的訂閱是每月的費用是任何訂閱。 當您購買的付費的訂閱時，您立即會以第一個月的使用。 您會在 [訂閱] 頁面 （加號） 稅金優惠相關聯的量。 這個月的費用是由原始購買的行事曆日期相同的每個月，直到您取消訂閱。 

**如何升級到較高的層服務？**

您可以購買或更新您的訂閱 [優惠頁碼] of 從 Microsoft Azure Marketplace (https://datamarket.azure.com/dataset/amla/recommendations) 頁面。

當您在升級訂閱︰

* 會繼續使用您舊有訂閱的交易不會新增至您的新訂閱。 
* 您是每年支付完整價格新的訂閱，即使您有未使用過的交易舊訂閱。

若要升級訂閱的程序︰

* 以 [優惠頁碼] of Nevigate (https://datamarket.azure.com/dataset/amla/recommendations)。
* 如果您尚未登，登入服務商場。
* 在右窗格中，會列出所有可用的方案。 按一下您想要升級至計劃的 [選項] 按鈕。
* 如果您想要升級，請按一下**[確定]**。 如果您不想要升級，請按一下 [**取消**]。

**重要**因為沒有帳單與使用的含意升級之前，請仔細閱讀] 對話方塊。

**何時我的訂閱建議會結束？**

當您將其取消時，會結束您的訂閱。 如果您想要取消您的訂閱，請參閱下列指示。

**如何取消訂閱建議？**

若要取消您的訂閱，請使用下列步驟。 如果您目前的訂閱是付費的訂閱，表示您的訂閱作用中持續直到目前計費期間的結尾。 如果您需要為有效立即取消，請與我們連絡[Microsoft 支援](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)在。

**附註**如果您或未使用過的交易的計費期間結束之前取消計費週期中，不提供任何退款。

* 瀏覽至 [優惠頁面] (https://datamarket.azure.com/dataset/amla/recommendations)。
* 如果您尚未登，登入服務商場。
* 按一下 [**取消**] 右邊的 [資料集名稱] 和 [狀態]。 您可以使用此訂閱到目前計費期間的結尾，或您的交易限制到達 （以會發生第一次）。

如果您想要因此您可以購買新訂閱，請立即取消訂閱，請在[Microsoft 支援服務](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)檔案票證。

##<a name="getting-started-with-recommendations"></a>快速入門的建議

**我是建議？** 

在電腦學習建議適用於組織和交叉銷售的建議和向上銷售產品或服務給他們的客戶所依賴的公司。 如果您有客戶對外公開網站、 銷售、 內銷售人員或通話中心，而且如果您提供的多個數十個產品或服務的目錄，您的下一行能藉由使用建議。 

建議的實驗被專為相當簡單。 目前的 API 版本需要程式設計的基本技巧。 如果您需要協助，請連絡開發您網站的廠商。 如果您有內部的 IT 部門或內部開發人員，他們可以取得您的建議。 

**設定 [建議的必要條件為何？**

您需要的使用者選項，您的目錄與相關時，才能建議。 如果您不 t 有這類記錄及您需要的客戶具網站，建議會收集您的使用者活動。 

建議也需要您的產品或服務的目錄。 如果您不 t 目錄，建議使用真正客戶使用情況資料與濃縮成目錄。 隱含的目錄不會包含回報不屬於使用者交易的項目。

**我該如何在第一次設定建議？**

在 [訂閱] (https://datamarket.azure.com/dataset/amla/recommendations) 建議，您應該使用 API 文件[Azure 電腦學習建議快速入門指南](machine-learning-recommendation-api-quick-start-guide.md)中設定服務。

**哪裡可以找到 API 文件？** 

API 文件是[Azure 電腦學習建議快速入門指南](machine-learning-recommendation-api-quick-start-guide.md)。

**若要上傳到建議的目錄及使用方式的資料是否必須哪些選項？**

您有兩個選項上傳您目錄及使用方式的資料︰ 您可以 CRM 系統或其他記錄檔中的資料匯出，並將它上傳至建議，或者您可以新增標籤至您的網站，將追蹤使用者活動。 如果您使用的第二個方法時，資料會儲存在 Azure 中。

##<a name="maintenance-and-support"></a>維護與支援

**如何大型我的資料集可？**

每個資料集可以包含多達 100000 目錄項目，並設定為 2048 MB 的使用情況資料。
此外，訂閱可以包含最多 10 個資料集 （模型）。

**哪裡可以取得建議的技術支援？**

[Microsoft Azure 支援](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)網站上有技術支援。

**哪裡可以找到使用規定？**

[Microsoft Azure 電腦學習建議 API 服務條款](https://datamarket.azure.com/dataset/amla/recommendations#terms)。



 
