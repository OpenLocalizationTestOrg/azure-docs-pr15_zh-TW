<properties
    pageTitle="批次服務配額和限制 |Microsoft Azure"
    description="深入了解預設 Azure 批次配額、 限制及限制式，以及如何要求配額會增加"
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
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>配額和 Azure 批次服務的限制

以與其他 Azure 服務，有限制特定批次服務相關聯的資源。 許多這些限制是 Azure 訂閱或帳戶層級套用預設的配額。 本文討論這些預設值]，並方式，您可以要求配額會增加。

如果您打算生產負載批次，您可能需要增加一或多個預設上方配額。 如果您想要提高配額時，您可以開啟線上[客戶支援要求](#increase-a-quota)免費。

>[AZURE.NOTE] 配額是信用額度，不容量保證。 如果您有大型的容量需求，請連絡 Azure 支援。

## <a name="subscription-quotas"></a>訂閱配額
**資源**|**預設的限制**|**最大限制**
---|---|---
批次每個地區每個訂閱的帳戶 | 1 | 50

## <a name="batch-account-quotas"></a>批次帳戶配額
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>其他限制
**資源**|**最大限制**
---|---
每個運算節點的[同時工作](batch-parallel-node-tasks.md) | 節點核心 4 x 數目
每個批次帳戶的[應用程式](batch-application-packages.md)        | 20
每個應用程式的應用程式套件  | 40
應用程式套件大小 （每個）       | 1tb 195 GB<sup>1</sup>

<sup>1</sup>的最大的區塊 blob 大小的 azure 儲存空間限制

## <a name="view-batch-quotas"></a>檢視批次配額

[Azure 入口網站]中檢視您的批次帳戶配額[portal]。

1. 選取**批次帳戶在入口網站，**然後選取您感興趣的批次帳戶。

2. 選取 [批次帳戶的功能表刀上的 [**摘要資訊**

3. 屬性刀會顯示套用到批次帳戶上目前的**配額**

    ![批次帳戶配額][account_quotas]

## <a name="increase-a-quota"></a>增加配額

請遵循下列步驟可要求配額增加使用[Azure 入口網站][portal]。

1. 選取 [**說明 + 支援**磚在入口網站儀表板或問號 （**？**） 右上角的入口網站。

2. 選取 [**新增支援要求** > **基本概念**。

3. 在 [**基本概念**刀︰

    。 **問題類型** > **配額**

    b。 選取您的訂閱。

    c。 **配額類型** > **批次**

    d。 **支援方案** > **配額支援-包含**

    按一下 [**下一步**]。

4. 在 [**問題**刀︰

    。 選取 [根據您的[企業影響]**嚴重性**[support_sev]。

    b。 在 [**詳細資料**，指定您想要變更每個配額、 批次帳戶名稱和新的限制。

    按一下 [**下一步**]。

5. 在 [**連絡人資訊**刀︰

    。 選取**慣用連絡方式**。

    b。 請確認，然後輸入所需的連絡人詳細資料。

    按一下 [**建立**提交支援要求。

一旦您上傳您的支援要求，Azure 支援會與您連絡。 請注意，完成要求可以最多 2 個工作日。

## <a name="related-topics"></a>相關的主題

* [建立 Azure 批次帳戶使用 Azure 入口網站](batch-account-create-portal.md)

* [Azure 批次功能概觀](batch-api-basics.md)

* [Azure 訂閱及服務限制，配額和限制](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
