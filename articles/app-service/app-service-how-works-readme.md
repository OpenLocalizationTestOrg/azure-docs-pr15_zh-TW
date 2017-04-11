<properties 
    pageTitle="Azure 應用程式服務的運作方式" 
    description="進一步瞭解應用程式服務的運作方式" 
    keywords="應用程式 azure 服務應用程式服務，可調整應用程式服務方案、 小數位數，應用程式服務成本"
    services="app-service" 
    documentationCenter="" 
    authors="yochay" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="02/10/2016" 
    ms.author="yochay"/>

# <a name="how-app-service-works"></a>應用程式 Service 的運作方式

Azure 應用程式服務是設計用來解決今天對工程師實務問題的雲端服務。 應用程式服務將焦點放在提供更好的開發人員生產力，而不會危害需要進行的應用程式在雲端上。 應用程式服務也會提供的功能和架構必要時支援具有最常用的開發語言 （.NET，Java、 PHP、 Node.JS 和 Python） 開發人員撰寫企業線條的商務應用程式。
服務應用程式開發人員可以︰

* 建立彈性的 Web 應用程式
* 快速建立 Mobile 應用程式後端易於使用行動裝置的功能，例如資料後端的一組，使用行動應用程式的使用者驗證]，然後推入通知。 
* 實作、 部署及發佈 Api API 應用程式。
* 將商務應用程式在一起的繫結到工作流程，及轉換邏輯應用程式的資料。

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

所有的應用程式類型依賴可調整和彈性 Web App 的平台可讓開發人員從應用程式的設計最佳化完整的生命週期體驗應用程式進行的維修作業。 允許的生命週期功能︰

* 快速應用程式建立-從頭開始，或選擇 [從 Azure Marketplace 的 [OS 套件。 
* 連續部署-自動部署新的程式碼，從常用的來源控制解決方案，例如 TFS、 GitHub BitBucket 和同步處理 OneDrive 和 DropBox 等線上儲存空間服務的內容。
* 生產環境中測試-順利建立測試生產環境及管理流量移至這些部分。 在雲端時所需也擲回如果找不到問題時，才偵錯。
* 執行非同步工作和批次作業-背景程序中執行的程式碼或啟動您的程式碼根據事件 （例如登陸 Azure 儲存佇列中的郵件），並排定的時間 (CRON)。
* 按比例縮放的應用程式-其中許多的選項，不按比例縮放水平及垂直自動根據流量和資源使用量您服務使用。 設定您的應用程式專用的私人環境   
* 維護的應用程式-利用許多偵錯和診斷功能保持在問題並有效率地解決方法請即時 （含自動修復和 live 偵錯等功能） 或事後分析記錄檔和記憶體傾印
 
應用程式服務功能放在一起，讓開發人員著重於其程式碼，並快速達到穩定、 彈性生產狀態。 使用的 API 及邏輯應用程式的功能，開發人員可以建立橋障礙以及內部部署至雲端整合之間商務解決方案的實際企業應用程式。  

[AZURE.INCLUDE [app-service-blueprint-how-app-service-works](../../includes/app-service-blueprint-how-app-service-works.md)]
