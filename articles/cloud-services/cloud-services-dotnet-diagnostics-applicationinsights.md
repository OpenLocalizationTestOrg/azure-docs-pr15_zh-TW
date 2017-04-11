<properties
   pageTitle="使用應用程式的深入見解的雲端服務進行疑難排解 |Microsoft Azure"
   description="瞭解如何使用應用程式的深入見解從 Azure 診斷程序資料，即可疑難排解在雲端服務問題。"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# <a name="troubleshoot-cloud-services-using-application-insights"></a>使用應用程式的深入見解的雲端服務進行疑難排解

[Azure SDK 2.8](https://azure.microsoft.com/downloads/)和 Azure 診斷副檔名為 1.5 您可傳送 Azure 診斷資料雲端服務直接與應用程式的深入見解。 將各種類型的記錄，包括應用程式的記錄，windows 事件記錄檔，Azure 診斷所收集 ETW 記錄和效能計數器可以立即傳送到應用程式的深入見解，應用程式的深入見解入口網站使用者介面中以視覺化方式檢視。 使用應用程式的深入見解 SDK 以及時，您現在可以取得獲得深入見解到指標，然後從您的應用程式，以及來自 Azure 診斷系統和基礎結構的層級資料的記錄。

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>設定 Azure 診斷傳送資料至應用程式的深入見解

請遵循下列步驟，設定您的雲端服務專案傳送 Azure 診斷資料至應用程式的深入見解。

1) Visual Studio 方案總管] 中以滑鼠右鍵按一下角色，然後選取 [**內容**]，開啟 [角色設計工具

![方案總管角色屬性][1]

2) 在 [診斷] 區段下的角色設計工具中，選取 [傳送**診斷應用程式獲得深入見解的資料**] 核取方塊

![角色設計工具應用程式獲得深入見解傳送診斷資料][2]

3) 在出現的對話方塊中選取您想要傳送 Azure 診斷資料至應用程式的深入見解資源。 對話方塊可讓您選取現有的應用程式的深入見解資源，從您的訂閱，或以手動方式，以指定應用程式的深入見解資源儀器鍵。 如果您沒有現有的應用程式的深入見解資源然後您可以建立在它將會開啟瀏覽器視窗 Azure 傳統入口網站，您可以在其中建立應用程式的深入見解資源的 [**建立新的資源**] 連結上即可。 建立應用程式的深入見解資源的相關資訊請參閱[建立新的應用程式的深入見解資源](../application-insights/app-insights-create-new-resource.md)

![選取應用程式的深入見解資源][3]

4) 一旦您已新增的應用程式的深入見解資源，儀器金鑰該資源的儲存為名稱**APPINSIGHTS_INSTRUMENTATIONKEY**服務設定的設定。 您可以從 [服務設定下拉式清單中選取不同的設定變更此設定的設定每個服務設定] 或 [環境向下並指定新儀器金鑰該設定。

![選取 [服務設定][4]

**APPINSIGHTS_INSTRUMENTATIONKEY**設定的設定 Visual studio 用於適當的應用程式的深入見解資源資訊發佈期間設定診斷副檔名。 設定的設定是方便您定義的不同的服務設定不同的儀器索引鍵。 Visual Studio 會翻譯該設定，並將其插入診斷延伸公用設定，當發佈。 若要簡化的使用 PowerShell 設定診斷副檔名程序，也輸出從 Visual Studio 套件包含具有適當的應用程式的深入見解儀器公用組態 XML 包含的金鑰。 公用設定檔案會建立在 [延伸] 資料夾，然後依照 PaaSDiagnostics 的模式。<RoleName>.PubConfig.xml。 任何 PowerShell 基礎部署可以使用這個模式對應至角色的每個設定。

5) 啟用**傳送診斷應用程式獲得深入見解的資料**將會自動設定 Azure 診斷若要傳送所有的效能計數器和錯誤會收集 Azure 診斷代理程式的應用程式獲得深入見解的層級記錄。 如果您想要進一步設定必須手動編輯每一個角色*diagnostics.wadcfgx*檔案，將會傳送至應用程式的深入見解的資料。 若要進一步瞭解手動更新組態[設定 Azure 診斷傳送應用程式獲得深入見解的資料](../azure-diagnostics-configure-applicationinsights.md)，請參閱。

雲端服務設定傳送 Azure 診斷資料，您可以將其部署到 Azure 像平常一樣的應用程式獲得深入見解之後確保 Azure 診斷副檔名為啟用。 請參閱[發佈使用 Visual Studio 雲端服務](../vs-azure-tools-publishing-a-cloud-service.md)。  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>在 [應用程式的深入見解中檢視 Azure 診斷資料
Azure 診斷遙測會顯示在 [設定您的雲端服務應用程式的深入見解資源。

以下是如何各種 Azure 診斷登類型地圖應用程式的深入見解的概念︰  

-  效能計數器會顯示在 [應用程式的深入見解的自訂指標
-  Windows 事件記錄檔會顯示為追蹤和應用程式的深入見解中的自訂事件
-  在應用程式的深入見解的追蹤以顯示應用程式的記錄、 ETW 記錄，以及任何診斷基礎結構的記錄檔。

若要檢視應用程式的深入見解 Azure 診斷資料︰

- 使用[指標總管] 中](../application-insights/app-insights-metrics-explorer.md)以視覺化方式呈現的任何自訂效能計數器或不同類型的 windows 事件記錄事件的計數。

![在計量總管] 中的自訂指標][5]

- 使用[搜尋](../application-insights/app-insights-diagnostic-search.md)在各種 Azure 診斷所傳送的追蹤記錄檔中搜尋。 範例，如果您在角色導致當機和資源資訊的角色無法處理的例外就會出現在*應用程式*頻道的*Windows 事件記錄檔*中。 若要查看的 Windows 事件記錄檔錯誤，並讓您尋找問題的根本原因的例外狀況，取得完整的堆疊追蹤，您可以使用搜尋功能。

![搜尋追蹤][6]

## <a name="next-steps"></a>後續步驟

- 若要從您的應用程式傳送邀請，例外狀況、 相依性，以及任何自訂遙測相關資料[新增至您的雲端服務應用程式的深入見解 SDK](../application-insights/app-insights-cloudservices.md) 。 結合 Azure 診斷資料您可以在相同的應用程式充分資源取得應用程式與系統的完整的檢視。  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
