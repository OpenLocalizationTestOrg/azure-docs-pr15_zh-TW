<properties
    pageTitle="訂閱和帳戶準則 |Microsoft Azure"
    description="深入了解重要的設計及實作方針的訂閱和 Azure 帳戶。"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="subscription-and-accounts-guidelines"></a>訂閱與帳戶的指導方針

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

本文著重於了解如何為您的環境的訂閱與帳戶管理和使用者基底成長。


## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>實作方針訂閱與帳戶

決策︰

- 訂閱及帳戶必須裝載您的 IT 工作量或基礎結構的哪些設定？
- 如何以符合您的組織階層中斷？

工作︰

- 當您想要管理其從訂閱層級，請定義邏輯組織階層。
- 比對此邏輯階層，定義所需的帳戶和每個帳戶的訂閱。
- 建立的訂閱及帳戶使用您的命名慣例。


## <a name="subscriptions-and-accounts"></a>訂閱與帳戶

若要使用 Azure，您需要一個或多個 Azure 訂閱。 資源像是虛擬機器 (Vm) 或虛擬網路中存在的訂閱。

- 企業客戶通常會有企業版註冊，這是在階層中，最上層的資源，一或多個帳戶相關聯。
- 消費者與客戶不企業註冊，最上層的資源是帳戶。
- 訂閱相關聯的帳戶，而可以有一或多個訂閱，每個帳戶。 Azure 記錄付款的訂閱層級的資訊。

帳戶/訂閱關係的兩個階層層級的限制，因為是對齊的帳戶和帳單需求的訂閱的命名慣例。 例如，如果全域的公司使用 Azure，他們可能會有一個帳戶，每個地區，選擇並在有訂閱管理區域層級︰

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

例如，您可能會使用下列結構︰

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

如果地區決定有與相關聯的特定群組的多個訂閱的命名慣例應該併入編碼額外的資料，在 [帳戶] 或 [訂閱名稱的方式。 此組織允許 massaging 產生新階層層級的帳單的報表期間的付款資料︰

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

組織可能看起來如下︰

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

我們在企業合約提供詳細的計費透過單一帳戶，或針對所有帳戶下載的檔案。


## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 