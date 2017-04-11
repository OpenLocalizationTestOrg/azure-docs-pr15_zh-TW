<properties
   pageTitle="若要建立的解決方案範本 Marketplace 的指南 |Microsoft Azure"
   description="如何建立、 認證及部署多重 VM 圖像解決方案範本的詳細的指示購買 Azure marketplace。"
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
      ms.date="07/27/2016"
      ms.author="hascipio; v-divte" />

# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>若要建立的解決方案範本 Azure Marketplace 的指南
完成步驟 1，[帳戶建立和註冊]後[link-acct-creation]，我們會引導您[建立的解決方案範本的必要條件技術](marketplace-publishing-solution-template-creation-prerequisites.md)相容於 Azure 的解決方案範本建立。 現在，我們會引導您執行的步驟建立的解決方案範本的[發佈入口網站]上的多個 Vm[ link-pubportal] Azure Marketplace 的。

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>發佈入口網站中建立您的方案範本提供的服務
移至[https://publish.windowsazure.com](http://publish.windowsazure.com)。 [發佈入口網站](https://publish.windowsazure.com/)以第一次登入時，使用貴公司的銷售者設定檔已註冊的同一個帳戶。 之後，您可以新增任何您公司的員工當作發佈入口網站中的共同管理員。

### <a name="1-select-solution-templates"></a>1.選取 「 解決方案範本 」

  ![繪圖][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2.建立新的方案範本

  ![繪圖][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3.開始拓撲
解決辦法範本是所有其拓撲"parent"。 您可以定義多個拓撲一個優惠/方案範本中。 當提議放入臨時，其推入所有其拓撲的。 請遵循下列步驟以定義您的提議︰     

- 建立拓撲: 「 拓撲識別碼 」 通常是拓撲方案範本的名稱。 拓撲識別碼用在 URL 中，如下所示︰

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Azure 入口網站︰ https://portal.azure.com/#gallery/ {PublisherNamespace}。{OfferIdentifier}{TopologyIdentifier}

- 新增新的版本。

### <a name="4-get-your-topology-versions-certified"></a>4.取得認證您拓撲版本
上傳 zip 檔案中含有佈建特定版拓撲的所有必要的檔案。 此 zip 檔案必須包含下列內容︰

- 在其根目錄*mainTemplate.json*和*createUiDefinition.json*檔案。
- 任何連結的範本和所有必要的指令碼。

  > [AZURE.TIP] 而您開發人員建立方案範本拓撲，使其認證，企業、 行銷及法律內容就可以使用您的公司行銷，及/或法律部門。

## <a name="next-steps"></a>後續步驟
既然您已建立您的方案的範本，並上傳 zip 檔案，請遵循前推入臨時提供的優惠方案的[服務商場行銷內容指南](marketplace-publishing-push-to-staging.md)中的指示進行。 若要查看完整的服務商場發佈文章，請造訪[快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)。

您也可能會感興趣的下列相關文章︰

- VM 圖像︰[關於虛擬機器中的圖像 Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
- VM 副檔名︰ [VM 代理程式和 VM 副檔名概觀](https://msdn.microsoft.com/library/azure/dn832621.aspx)和[Azure VM Extensions 和功能](https://msdn.microsoft.com/library/azure/dn606311.aspx)
- Azure 資源管理員︰[撰寫 Azure ARM 範本](../resource-group-authoring-templates.md)及[簡單 ARM 範本的範例](https://github.com/rjmax/ArmExamples)
- 儲存帳戶節流︰[如何儲存帳戶節流的監視器](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx)，[進階版的儲存空間](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
