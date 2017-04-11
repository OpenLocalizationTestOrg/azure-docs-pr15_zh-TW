<properties
    pageTitle="管理 Azure 服務匯流排使用 Azure 自動化 |Microsoft Azure"
    description="瞭解如何使用 Azure 自動化服務來管理 Azure 服務匯流排。"
    services="service-bus, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

# <a name="managing-azure-service-bus-using-azure-automation"></a>管理 Azure 服務匯流排使用 Azure 自動化

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化的 Azure 服務匯流排管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？

[Azure 自動化](../automation/automation-intro.md)是簡化透過程序自動化及想要的狀態設定雲端管理 Azure 服務。 使用 Azure 自動化，手動重複、 時間執行，和出錯工作可以自動化增加可靠性效率，與貴組織的時間值。

Azure 自動化提供縮放以符合您需求的高度可靠的高度可用的工作流程執行引擎。 在 Azure 自動化程序可以會開始手動，3rd 廠商系統或在排程的時間間隔，好讓完全在需要時，會發生的工作。

減少操作費用，釋放 IT 與 DevOps 聚焦於商務價值移動雲端管理工作，以 Azure 自動化自動執行的工作。

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Azure 自動化如何協助管理 Azure 服務匯流排？

您可以使用[服務匯流排 REST API](https://msdn.microsoft.com/library/azure/mt639375.aspx)來管理服務匯流排 Azure 自動化。 Azure 自動化內，您可以執行來執行許多服務匯流排工作使用 REST API 的 PowerShell 指令碼。 您也可以配對這些 REST API 呼叫中 Azure 自動化透過 cmdlet 如需其他 Azure 服務，便可自動化 Azure 服務以及第 3 廠商系統的複雜的工作。

以下是使用 PowerShell 來管理 Azure 服務匯流排一些範例︰

* [自訂的 PowerShell cmdlet 來管理 Azure 服務匯流排佇列](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [如何建立服務匯流排佇列、 主題及使用 PowerShell 指令碼的訂閱](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [建立使用 PowerShell 的 Azure 服務匯流排命名空間](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

使用中自動化 runbooks Azure 服務匯流排 PowerShell 模組可透過[PowerShell 庫](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0)下載。


## <a name="next-steps"></a>後續步驟

現在，您學到的 Azure 自動化，以及如何使用它來管理 Azure 服務匯流排基本概念，請遵循這些連結，深入瞭解 Azure 自動化。

* 請參閱 Azure 自動化[開始教學課程](../automation/automation-first-runbook-graphical.md)
* 請參閱如何[管理服務匯流排使用 PowerShell](service-bus-powershell-how-to-provision.md)
