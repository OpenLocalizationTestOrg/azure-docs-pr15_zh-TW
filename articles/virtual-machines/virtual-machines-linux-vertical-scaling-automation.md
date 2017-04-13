<properties
    pageTitle="垂直調整 Azure 自動化 Azure 虛擬機器 |Microsoft Azure"
    description="垂直不按比例縮放 Linux 虛擬機器來監控通知 Azure 自動化回應的方式"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/29/2016"
    ms.author="singhkay"/>

# <a name="vertically-scale-azure-virtual-machine-with-azure-automation"></a>垂直調整 Azure 自動化 Azure 虛擬機器

垂直縮放比例是增加或減少工作量來回應機器的資源的程序。 Azure 中這可藉由變更虛擬機器的大小。 在下列情況，這可協助

- 如果沒有經常使用虛擬機器，可以調整至較小的大小，以減少您每月的成本
- 如果虛擬機器會看到的最大使用量載入時，可以調整至較大的增加其容量

若要完成此步驟的外框做為下方

1. 若要存取您的虛擬機器設定 Azure 自動化
2. Azure 自動化垂直刻度 runbooks 匯入您的訂閱
3. 新增您 runbook webhook
4. 加入虛擬機器中的通知

> [AZURE.NOTE] 由於的第一個的虛擬機器大小，可以調整大小可能是因為部署目前的虛擬機器中的其他大小叢集可用性有限。 在本文中使用已發佈的自動化 runbooks 我們會處理這種情況下，並只縮放內 VM 大小配對] 下方。 這表示突然不會調整，以 Standard_G5 或縮小，以 Basic_A0 Standard_D1v2 虛擬機器。

>| 縮放比例組 VM 大小 |   |
|---|---|
|  Basic_A0 |  Basic_A4 |
|  Standard_A0 | Standard_A4 |
|  Standard_A5 | Standard_A7  |
|  Standard_A8 | Standard_A9  |
|  Standard_A10 |  Standard_A11 |
|  Standard_D1 |  Standard_D4 |
|  Standard_D11 | Standard_D14  |
|  Standard_DS1 |  Standard_DS4 |
|  Standard_DS11 | Standard_DS14  |
|  Standard_D1v2 |  Standard_D5v2 |
|  Standard_D11v2 |  Standard_D14v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>若要存取您的虛擬機器設定 Azure 自動化

首先您需要執行的建立裝載用來縮放 VM 縮放比例設定執行個體 runbooks Azure 自動化帳戶。 最近的自動化服務推出 」 執行為帳戶 」 功能讓設定服務主體設定自動執行 runbooks 使用者的代表很容易。 您可以閱讀更多關於此，請參閱下列︰

* [Azure 執行為帳戶驗證方法 Runbooks](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure 自動化垂直刻度 runbooks 匯入您的訂閱

所需的垂直調整您的虛擬機器 runbooks 已發佈 Azure 自動化 Runbook 圖庫中。 您必須將其匯入您的訂閱。 您可以瞭解如何匯入 runbooks 閱讀下列文章。

* [Azure 自動化 Runbook 和模組組件庫](../automation/automation-runbook-gallery.md)

需要匯入 runbooks 會顯示在下圖中

![匯入 runbooks](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>新增您 runbook webhook

一旦您已匯入您將需要 runbooks 加入 webhook runbook，以便從虛擬機器提醒可以觸發。 建立您 Runbook webhook 的詳細資料，可以閱讀

* [Azure 自動化 webhooks](../automation/automation-webhooks.md)

請確定您複製 webhook 之前關閉 webhook] 對話方塊，您會需要此的下一節。

## <a name="add-an-alert-to-your-virtual-machine"></a>加入虛擬機器中的通知

1. 選取 [虛擬機器設定
2. 選取 「 通知規則 」
3. 選取 [新增提醒]
4. 選取 [公制上啟動提醒
5. 選取 [條件，時完成會導致啟動提醒
6. 在步驟 5 中選取條件的臨界值。 若要完成
7. 在步驟 5 和 6] 中選取的範圍監視服務會檢查的條件和臨界值
8. 貼上您從上一節複製 webhook。

![新增提醒至虛擬機器 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![新增提醒至虛擬機器 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)