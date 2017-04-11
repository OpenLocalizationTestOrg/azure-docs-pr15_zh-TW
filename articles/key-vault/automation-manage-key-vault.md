<properties
    pageTitle="管理 Azure 金鑰保存庫使用 Azure 自動化 |Microsoft Azure"
    description="深入了解如何 Azure 自動化服務可用於管理 Azure 金鑰保存庫。"
    services="Key-Vault, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-key-vault-using-azure-automation"></a>使用 Azure 自動化管理 Azure 金鑰保存庫

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化的索引鍵和 Azure 金鑰保存庫中的機密的管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？

[Azure 自動化](../automation/automation-intro.md)是簡化透過程序自動化及想要的狀態設定雲端管理 Azure 服務。 使用 Azure 自動化，手動重複、 時間執行，和出錯工作可以自動化增加可靠性效率，與貴組織的時間值。

Azure 自動化提供縮放以符合您需求的高度可靠的高度可用的工作流程執行引擎。 在 Azure 自動化程序可以會開始手動，3rd 廠商系統或在排程的時間間隔，好讓完全在需要時，會發生的工作。

減少操作費用，釋放 IT 與 DevOps 聚焦於商務價值移動雲端管理工作，以 Azure 自動化自動執行的工作。


## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Azure 自動化如何協助管理 Azure 金鑰保存庫？

可以使用 [AzureRM 金鑰保存庫 cmdlet] 中選擇 Azure 自動化管理金鑰保存庫 (https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) 和[Azure 傳統金鑰保存庫 cmdlet](https://msdn.microsoft.com/library/azure/dn868052.aspx)。 Azure 模組管理傳統的金鑰保存庫自動提供 Azure 自動化，而您可以將匯入[AzureRM KeyVault 模組](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4)Azure 自動化，讓您可以執行許多您在服務中的金鑰保存庫管理工作。 您也可以配對 Azure 自動化這些 cmdlet，以用於其他 Azure 服務，便可自動化複雜的工作 Azure 服務以及第 3 廠商系統 cmdlet。

透過 Azure 金鑰保存庫 cmdlet，您可以執行其他人之間這些工作︰ 

- 建立及設定金鑰保存庫
- 建立或匯入索引鍵
- 建立或更新密碼
- 更新索引鍵的屬性
- 取得金鑰或密碼
- 刪除索引鍵或密碼

以下是一些範例使用 PowerShell 來管理金鑰保存庫︰  

* [Azure 鍵保存庫-Step by Step](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [安裝及設定 Azure 的金鑰保存庫](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)


## <a name="next-steps"></a>後續步驟

現在，您學到 Azure 自動化，以及如何使用它來管理 Azure 金鑰保存庫的基本概念，請遵循這些連結，深入瞭解 Azure 自動化。

* 請參閱 Azure 自動化[開始教學課程](../automation/automation-first-runbook-graphical.md)。
* 請參閱[Azure 金鑰保存庫 PowerShell 指令碼](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5)。
