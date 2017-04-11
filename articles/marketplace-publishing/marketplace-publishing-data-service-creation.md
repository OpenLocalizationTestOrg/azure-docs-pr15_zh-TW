<properties
   pageTitle="若要建立 Marketplace 資料服務的指南 |Microsoft Azure"
   description="如何建立、 認證及部署資料服務的詳細的指示購買 Azure marketplace。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>發佈指南 Azure Marketplace 資料服務

>[AZURE.IMPORTANT] **現在我們已不再登入任何新的資料服務的發行者。新的 dataservices 非取得已核准的清單。** 如果您有您想要發佈上 AppSource SaaS 商務應用程式，您就可以找到更多資訊[以下](https://appsource.microsoft.com/partners)。 如果您有 IaaS 應用程式，或您想要發佈 Azure 服務商場的開發人員服務可以找到更多資訊[以下](https://azure.microsoft.com/marketplace/programs/certified/)。

完成步驟 1，[帳戶建立和註冊](marketplace-publishing-accounts-creation-registration.md)之後, 我們引導您[技術一般](marketplace-publishing-pre-requisites.md)設定和資料服務優惠的[技術需求](marketplace-publishing-data-service-creation-prerequisites.md)Azure marketplace。 現在，我們會逐步引導您完成建立資料服務優惠[發佈入口網站]上的步驟[link-pubportal]Azure Marketplace 的。

## <a name="1---login-to-the-publishing-portal"></a>1.登入 [發佈入口網站。

移至[https://publish.windowsazure.com](https://publish.windowsazure.com. )

**發佈入口網站的第一次登入，使用相同的帳戶，與貴公司的銷售者設定檔已登錄在開發人員中心。**  （稍後您可以新增任何您公司的員工發佈入口網站中的共同管理員）。

如果這是將發佈入口網站的第一個登入，請按一下 [**發佈資料服務**圖磚上]。

## <a name="2---choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2.在左側導覽功能表中選擇**資料服務**。

  ![繪圖](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3---create-a-new-data-service"></a>3.建立新的資料服務

填入您新的資料服務提供標題，然後按一下 [在 [+] 右側。

  ![繪圖](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4---review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4.檢閱導覽] 功能表中新建立的資料服務] 下的子功能表。

**逐步解說**] 索引標籤上按一下並檢閱發佈正確 Azure marketplace 資料服務所需的所有必要的步驟。

> [AZURE.TIP] 您永遠可以按一下 「 逐步解說 」 頁面中的連結，或使用資料服務優惠的左側的子功能表上的索引標籤。

## <a name="5---create-a-new-plan"></a>5.建立新的方案。

### <a name="offers-plans-transactions"></a>提供，計劃、 交易。

每個優惠可以有多個方案，但必須至少有一 （1） 計劃。 當使用者訂閱您的提議他們訂閱的優惠方案。 每個計劃定義如何使用者將能夠使用您的服務。

目前 Azure Marketplace 資料服務支援只按月訂閱交易基礎的模型，亦即使用者付的價格，他們的特定方案根據每月費用都能使用計劃所定義的交易每個月數。

定義您的資料服務會傳回的記錄數目通常每次交易根據服務所傳送的查詢。 預設值為 100。 數字的交易的形式傳回至每個查詢會數字的記錄除以 100 kb 四捨五入到最接近的整數。

負責 Azure 服務商場圖層監控 （公尺） 的每個查詢所使用的交易的數字。

> [AZURE.IMPORTANT] 達到交易當月期間的使用者會遭到封鎖，無法繼續使用服務，直到其每月的訂閱週期結束為止。

> 計劃 」 或 「 計劃的其中一個可以 （但不是必須） 包含不受限制的交易的數字。

### <a name="create-a-plan"></a>建立計劃。
1. 按一下**[+]**旁 [新增新方案]。

2. 選擇其中一個選項︰ 此計劃**無限制**或**受限制**的使用方式。  如果限制然後提供的交易計劃可讓使用月份中的數字。

    ![繪圖](media/marketplace-publishing-data-service-creation/step-5.1.png)  

    發佈入口網站也會建議 「 計劃識別碼 」，它會用來與使用者的使用者介面中的方案名稱，也用來識別計劃服務商場服務。 如果您想要您可以變更 「 計劃識別碼 」。

    > [AZURE.NOTE] 「 規劃 」 必須是唯一範圍內的每個優惠。 發佈入口網站規劃識別項中使用的許多其他識別項會鎖定之後第一個發佈至生產，並將無法變更此識別碼。

3. 按一下以接受您的選擇。

4. 然後您會被要求一些其他問題的回答有關您新建立的方案。

    ![繪圖](media/marketplace-publishing-data-service-creation/step-5.2.png)


|問題|比較基數倍數|
|----|----|
|**此計劃是免費且可供全球？**|您可以建立完全免費的-免費計劃。 如果是這項優惠 – 的唯一計劃即表示，您要發佈 」 免費提供 「 市場中。 如果是僅適用於其中一個 （少） 計劃，提供您其中一個選項以提供若要進一步瞭解您的服務，以較小的數字的每個月的交易的使用者。  如果回答 [是]，然後會被要求沒有進一步的問題。|

> [AZURE.NOTE] 使用者可以將升級付費的方案。

|問題|比較基數倍數|
|----|----|
|**提供免費試用版？**|您可以在選擇 「 無試用版]，或授與選項以使用您計劃以 「 1 個月 」。 使用此選項以提供使用者瞭解的 1 個月免費提供優點可能要發行者。|

> [AZURE.IMPORTANT] 使用者只能購買免費試用版，如果建立付費方式例如信用卡，企業版合約。

> 一個免費試用版的月份之後, Azure Marketplace 就會開始充電客戶的訂閱，日期的價格除非客戶發起的租用戶訂閱取消。 任何特殊的通知將會不提供給使用者。

|問題|比較基數倍數|
|----|----|
|**本方案需要購買促銷代碼？**| 擁有特定客戶發行者的其中一個選項以提供特殊的程式碼，稱為 「 Promocode 「 限制存取他們的服務方案。 使用者會有此 Promocode 無法訂閱方案。 如果您選擇 [否] 時，您同意優惠哪裡使用 （請參閱[服務商場行銷內容指南](marketplace-publishing-push-to-staging.md)的更多詳細資料） 的地區的每個人都能訂閱此計劃。 沒有進一步的問題會被要求。|
|**也隱藏從任何人沒有有效的促銷代碼本方案嗎？**|如果在上一個問題的答案是 「 是 」 Publisher 具有完全移除此計劃服務商場的使用者介面中出現的選項。 這表示，客戶不會看到此優惠的詳細資料頁面中的計劃。 將會收到 promocode 購買，使用者無法使用此 promocode 訂閱。|

## <a name="6---create-your-marketplace-marketing-content"></a>6。 建立您的服務商場行銷內容
如何提供所需的資訊**行銷、 價格、 支援與類別**] 索引標籤中請造訪[服務商場行銷內容指南](marketplace-publishing-push-to-staging.md)是常見所有成品發佈 Azure Marketplace 中。  

## <a name="7---connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7。 連接您的提議到您的服務 （依據 SQL Azure 或 Web 服務為基礎）。

按一下 [**資料服務**] 子功能表上。

在頁面的上半會要求您提供的優惠**命名空間**。  

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.png)

問題下方就會定義發行者前往 Azure Marketplace 提供的公開新建立的優惠方案的方式。 （如需詳細資訊，請參閱[資料 Services 技術必要指南](marketplace-publishing-data-service-creation-prerequisites.md)）。

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.2.png)

**發佈資料庫基礎服務**

按一下 [**資料庫**]。 將會出現下列頁面︰

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.3.png)

若要建立 CSDL 對應根據 SQL Azure 資料庫的資料集︰

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.4.png)

然後每個資料表

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.6.png)

如果 Web 服務

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] 閱讀[對應到 OData CSDL 到現有的 web 服務](marketplace-publishing-data-service-creation-odata-mapping.md)的詳細的指示和建立 CSDL Web 服務的範例。

## <a name="next-steps"></a>後續步驟
現在，您已建立您所提供的資料服務，請確定您完成[服務商場行銷內容指南](marketplace-publishing-push-to-staging.md)中的指示進行，才能進行[測試您的資料在服務臨時](marketplace-publishing-data-service-test-in-staging.md)向前移動。

## <a name="see-also"></a>另請參閱
- [快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
- 如果您有興趣瞭解的整體 OData 對應程序和用途，請閱讀本文[資料服務 OData 對應](marketplace-publishing-data-service-creation-odata-mapping.md)檢閱定義、 結構和指示。
- 如果您有興趣學習特定節點和其的參數，了解閱讀本文[資料服務 OData 對應節點](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)定義和說明，範例，並使用大小寫的內容。
- 如果您想要檢閱範例，請閱讀本文以查看範例程式碼，並瞭解程式碼語法和內容的[資料服務 OData 對應的範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)。


[link-pubportal]:https://publish.windowsazure.com
