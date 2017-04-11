<properties
    pageTitle="管理 Azure SQL 資料庫時使用 Azure 自動化 |Microsoft Azure"
    description="深入了解如何 Azure 自動化服務可用於管理在 Azure SQL 資料庫。"
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/26/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-sql-databases-using-azure-automation"></a>管理使用 Azure 自動化 Azure SQL 資料庫

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化管理 Azure SQL 資料庫。


## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？

[Azure 自動化](https://azure.microsoft.com/services/automation/)是簡化雲端管理程序自動化透過 Azure 服務。 使用 Azure 自動化，可以增加可靠性、 效率及時間] 的值為您的組織自動化長、 手動、 容易發生錯誤，並經常重複的工作。

Azure 自動化提供縮放以符合您的需求，當您的組織規模擴大時非常可靠和高度可用的工作流程執行引擎。 在 Azure 自動化程序可以會開始手動，3rd 廠商系統或在排程的時間間隔，好讓完全在需要時，會發生的工作。

降低操作費用，然後釋放 IT / DevOps 教職員焦點放在 [新增商務的工時值來移動雲端管理工作，以 Azure 自動化自動執行。


## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Azure 自動化如何協助管理 Azure SQL 資料庫？

可以使用[Azure SQL 資料庫 PowerShell 指令程式](https://msdn.microsoft.com/library/dn546723.aspx)所提供的[PowerShell 的 Azure 工具](https://msdn.microsoft.com/library/azure/jj156055.aspx)，在 Azure 自動化管理 azure SQL 資料庫。 Azure 自動化不在] 方塊中，有提供這些 Azure SQL 資料庫 PowerShell cmdlet，讓您可以執行所有服務中的 SQL DB 管理任務。 您也可以配對 Azure 自動化這些 cmdlet，以用於其他 Azure 服務，便可自動化複雜的工作 Azure 服務以及第 3 廠商系統 cmdlet。

Azure 自動化也是直接，通訊與 SQL server 發出使用 PowerShell SQL 命令的功能。

[Azure 自動化 runbook 圖庫](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/)中包含各種不同的產品小組和社群 runbooks，即可開始使用自動化的 Azure SQL 資料庫、 其他 Azure 服務與第 3 廠商系統管理。 圖庫 runbooks 包括︰

 * [執行 SQL Server 資料庫的 SQL 查詢](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [垂直不按比例縮放 （向上或向下） 的排程 Azure SQL 資料庫](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [如果資料庫接近大小上限，則捨去 SQL 表格](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [如果他們零散，索引 Azure SQL 資料庫資料表中](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>後續步驟

現在，您學到 Azure 自動化，以及如何使用它來管理 Azure SQL 資料庫的基本概念，請遵循這些連結，深入瞭解 Azure 自動化。

- [Azure 自動化概觀](../automation/automation-intro.md)
- [我的第一個 runbook](../automation/automation-first-runbook-graphical.md)
- [Azure 自動化學習地圖](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Azure 自動化︰ 您 SQL 代理程式在雲端](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 
