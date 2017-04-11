<properties
    pageTitle="Azure 應用程式服務中的 WebJobs"
    description="瞭解如何建立 WebJobs 執行背景測試、 儲存和服務匯流排等服務進行互動並建立排程的任務。"
    services="app-service"
    documentationCenter=""
    authors="christopheranderson"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="chrande"/>

# <a name="using-webjobs-in-azure-app-service"></a>Azure 應用程式服務中使用 WebJobs

本文以瞭解如何使用 Azure WebJobs 和 Azure WebJobs SDK 的文件資源連結。 Azure WebJobs 提供容易地在[應用程式服務 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)上執行指令碼或程式為背景處理程序。 您可以上傳並 cmd，以執行可執行檔，例如棍執行檔 (.NET)，ps1，sh，php，py，js 和 jar。 下列程式的執行為 WebJobs (cron) 的排程或持續。

WebJobs SDK 可以讓您更輕鬆地使用 Azure 儲存體。 WebJobs SDK 具備繫結及搭配 Microsoft Azure 儲存體二進位大型物件、 佇列和表格，以及服務匯流排佇列觸發程序系統。

建立、 部署及管理 WebJobs 是使用整合式的工具，在 Visual Studio 順暢。 您可以從範本建立 WebJobs、 發佈及管理 （執行/停止/監視器/偵錯） 它們。

Azure 入口網站中的 [WebJobs 儀表板提供的功能強大的管理功能，讓您執行的 WebJobs，包括可叫用 WebJobs 中的個別函數的完全控制權。 函數執行階段和記錄輸出，也會顯示儀表板。

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]
