<properties
    pageTitle="建立 Azure 批次帳戶 |Microsoft Azure"
    description="瞭解如何執行在雲端的大型平行負載 Azure 入口網站中建立 Azure 批次帳戶"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>建立 Azure 批次帳戶使用 Azure 入口網站

> [AZURE.SELECTOR]
- [Azure 入口網站](batch-account-create-portal.md)
- [批次管理.NET](batch-management-dotnet.md)

瞭解如何在[Azure 入口網站]中建立 Azure 批次帳戶[azure_portal]，以及尋找重要的便捷鍵與帳戶的 Url，例如帳戶內容的位置。 我們也討論批次價格，並連結 Azure 儲存體帳戶批次帳戶，以便您可以使用[應用程式套件](batch-application-packages.md)，並[保存工作成果](batch-task-output.md)。

## <a name="create-a-batch-account"></a>建立批次帳戶

1. [Azure 入口網站]登入[azure_portal]。

2. 按一下 [**新** > **計算** > **批次的服務**。

    ![批次中服務商場][marketplace_portal]

3. **批次的新帳戶**刀會顯示。 請參閱項目**到*e*下方的每個刀元素的描述。

    ![建立批次帳戶][account_portal]

    。 **帳戶名稱**︰ 批次帳戶的唯一名稱。 此名稱必須是唯一 Azure 帳戶會建立 （請參閱*位置*下方） 的區域內。 它可能只包含小寫字母字元，數字，而且必須 3 24 個字元的長度。

    b。 **訂閱**︰ 在其中建立批次帳戶的訂閱。 如果您只有一個訂閱，預設會選取它。

    c。 **資源群組**︰ 現有的資源群組新批次帳戶，或您也可以建立一個新。

    d。 **位置**︰ Azure 區域來建立批次帳戶。 僅支援您的訂閱和資源群組區域會顯示為選項。

    e。 **儲存帳戶**（選用）︰ 建立您的新批次帳戶的關聯 （連結） 的**一般用途**儲存帳戶。 如需詳細資訊，請參閱下方的[連結 Azure 儲存體帳戶](#linked-azure-storage-account)。

4. 按一下 [**建立**]，以建立帳戶]。

  入口網站會指出它是**部署**帳戶，而且完成時，**成功部署**通知會出現在*通知*。

## <a name="view-batch-account-properties"></a>檢視批次帳戶內容

只要建立帳戶之後，您可以開啟**批次帳戶刀**存取其設定和內容。 您可以使用左側的功能表的批次帳戶刀存取所有帳戶設定] 和 [屬性。

![批次帳戶刀 Azure 入口網站中][account_blade]

* **批次帳戶 URL**︰ 您以[批次開發的 Api](batch-technical-overview.md#batch-development-apis)建立的應用程式需要管理資源，並在帳戶執行工作的帳戶 URL。 批次帳戶 URL 會包含下列格式︰

    `https://<account_name>.<region>.batch.azure.com`

![批次帳戶 URL，在入口網站][account_url]

* **便捷鍵**︰ 您的應用程式也需要便捷鍵時使用批次帳戶中的資源。 若要檢視或重新產生批次帳戶的便捷鍵，請輸入`keys`中上批次帳戶刀的左側的功能表**搜尋**方塊，然後選取 [**索引鍵**。

    ![批次帳戶鍵 Azure 入口網站][account_keys]

## <a name="pricing"></a>價格

批次帳戶會提供只在 「 免費層，「 這表示您不會針對批次帳戶本身。 您會使用您批次的解決方案，基礎 Azure 計算資源，以及您負載執行時，其他服務所使用的資源。 例如，您運算節點會在您的資料庫中及資料您儲存在中 Azure 儲存體為輸入或輸出為任務。 同樣地，如果您使用的[應用程式套件](batch-application-packages.md)」 功能批次時，您會用來儲存您的應用程式套件的 Azure 儲存體資源。 請參閱[批次價格][batch_pricing]如需詳細資訊。

## <a name="linked-azure-storage-account"></a>連結的 Azure 儲存體帳戶

如先前所述，您可以 （選擇性） 連結**一般用途**儲存帳戶，您批次的帳戶。 批次的[應用程式套件](batch-application-packages.md)」 功能會使用 blob 儲存體中連結的一般用途儲存帳戶[批次檔案慣例.NET](batch-task-output.md)文件庫一樣。 這些選用的功能將協助您部署批次工作執行時，應用程式和保存他們所產生的資料。

批次目前支援*只***一般用途**儲存的帳戶類型[的相關 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的步驟 5，[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)，所述。 當您以批次帳戶連結 Azure 儲存體帳戶時，確認連結*只*是**一般用途**儲存帳戶。

![建立 「 通用 」 儲存帳戶][storage_account]

我們建議您建立供自己使用的儲存空間帳戶以批次帳戶。

>[AZURE.WARNING] 請注意時重新產生的連結的儲存空間帳戶便捷鍵。 重新產生單一儲存帳戶金鑰]，然後按一下 [連結的儲存空間帳戶刀上的 [**同步處理索引鍵**。 允許按鍵會傳播到運算節點，在您的資料庫，然後重新產生並同步處理其他按鍵，如有必要，請等候 5 分鐘。 如果您同時產生兩個機碼，您運算節點將無法同步處理任一鍵，然後對方將失去存取儲存帳戶。

  ![重新產生儲存帳戶金鑰][4]

## <a name="batch-service-quotas-and-limits"></a>批次服務配額和限制

請注意，有 Azure 訂閱與其他 Azure 服務，某些[配額和限制](batch-quota-limit.md)適用於批次帳戶。 目前的配額批次帳戶會出現在帳戶**內容**入口網站中。

![批次 Azure 入口網站中的帳戶配額][quotas]

當您設計並設定您的批次負載縮放比例，請記住這些配額。 例如，如果您的資料庫不達到目標的數目您指定的運算節點，您可能已達到核心配額限制批次帳戶。

另請注意，您不限於單一批次帳戶 Azure 訂閱。 您可以執行多個批次負載單一批次帳戶，或發佈您批次帳戶在同一份訂閱，但在不同的 Azure 區域之間的工作量。

這些配額許多您可以只使用免費的產品支援要求 Azure 入口網站中送出增加。 如需要求配額增加詳細資訊，請參閱[配額和 Azure 批次服務的限制](batch-quota-limit.md)。

## <a name="other-batch-account-management-options"></a>其他批次帳戶管理選項

除了使用 Azure 入口網站，您也可以建立及管理批次帳戶使用下列步驟︰

* [批次 PowerShell cmdlet](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](../xplat-cli-install.md)
* [批次管理.NET](batch-management-dotnet.md)

## <a name="next-steps"></a>後續步驟

* 請參閱[Azure 批次功能的概觀](batch-api-basics.md)，若要進一步瞭解批次服務概念和功能。 本文討論主要批次資源集區、 運算節點、 工作和工作，例如，並提供服務的功能，可讓大型運算工作量執行的概觀。

* 瞭解開發使用[批次.NET 用戶端文件庫](batch-dotnet-get-started.md)啟用批次的應用程式的基本概念。 [簡介本文](batch-dotnet-get-started.md)會引導您完成的工作應用程式使用批次服務執行工作負載上多個運算節點，並包含工作量檔案臨時與擷取使用 Azure 儲存體。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "重新產生儲存帳戶金鑰"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
