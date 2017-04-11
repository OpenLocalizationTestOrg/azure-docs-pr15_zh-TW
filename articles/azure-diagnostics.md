<properties
    pageTitle="Azure 診斷概觀 |Microsoft Azure"
    description="Azure 診斷用於偵錯、 衡量效能監視，在雲端服務、 虛擬機器和服務布料的轉印圖樣的流量分析"
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/02/2016"
    ms.author="robb"/>


# <a name="what-is-microsoft-azure-diagnostics"></a>什麼是 Microsoft Azure 診斷


Azure 診斷是啟用診斷上部署的應用程式的資料收集的 Azure 中的功能。 您可以使用的診斷副檔名從多個不同來源。 目前支援是 Azure 雲端服務 Web 和工作者角色 Azure 虛擬機器執行 Microsoft Windows 和服務布料的轉印圖樣。 其他 Azure 服務會有自己的個別診斷。

## <a name="data-you-can-collect"></a>您可以收集的資料

Azure 診斷會收集下列類型的資料︰

資料來源|描述
---|---
效能計數器 | 作業系統和自訂效能計數器
應用程式的記錄     | 追蹤您的應用程式所撰寫的郵件
Windows 事件記錄檔   | 傳送到 Windows 事件記錄系統資訊
.NET 事件來源    | 撰寫使用.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)類別的事件的程式碼
IIS 記錄檔             | IIS 網站的相關資訊
資訊清單以 ETW   | 任何處理程序所產生的事件追蹤的 Windows 事件
損毀傾印          | 應用程式當機事件程序的狀態相關資訊
自訂錯誤記錄檔    | 建立的應用程式或服務的記錄
Azure 診斷基礎結構的記錄|診斷本身的相關資訊

Azure 診斷副檔名可以 Azure 儲存體帳戶傳送此資料，或將其傳送給[應用程式的深入見解](./application-insights/app-insights-cloudservices.md)等服務。 您可以使用資料的偵錯時，及疑難排解、 衡量效能、 監視資源使用狀況、 流量分析及容量計劃，及稽核。


## <a name="versioning"></a>版本設定
請參閱[Azure 診斷版本歷程記錄](azure-diagnostics-versioning-history.md)。

## <a name="next-steps"></a>後續步驟
選擇要收集診斷使用下列文章，即可開始使用的服務]。 使用一般 Azure 診斷連結特定任務的參照。

## <a name="web-apps"></a>Web 應用程式
請注意，Web 應用程式不會使用 Azure 診斷。 在[Web 應用程式](./app-service-web/web-sites-enable-diagnostic-log.md)找到相同的資訊

## <a name="cloud-services-using-azure-diagnostics"></a>使用 Azure 診斷雲端服務
- 如果使用 Visual Studio，請參閱[使用 Visual Studio 追蹤雲端服務應用程式](./vs-azure-tools-debug-cloud-services-virtual-machines.md)，即可開始使用。 否則，請參閱
- [如何監視使用 Azure 診斷雲端服務](./cloud-services/cloud-services-how-to-monitor.md)
- [在雲端服務應用程式中的 Azure 診斷設定](./cloud-services/cloud-services-dotnet-diagnostics.md)

更多進階的主題，請參閱

- [雲端服務中使用應用程式的深入見解 Azure 診斷程式](./application-insights/app-insights-cloudservices.md)
- [追蹤與 Azure 診斷在雲端服務應用程式的流程](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [使用 PowerShell 來設定診斷在雲端服務](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## <a name="virtual-machines-using-azure-diagnostics"></a>使用診斷 Azure 虛擬機器
- 如果使用 Visual Studio，請參閱[使用 Visual Studio 追蹤 Azure 虛擬機器](./vs-azure-tools-debug-cloud-services-virtual-machines.md)，即可開始使用。 否則，請參閱
- [設定 Azure 診斷上 Azure 虛擬機器](./virtual-machines-dotnet-diagnostics.md)

更多進階的主題，請參閱

- [使用 PowerShell 來設定診斷 Azure 虛擬機器上的商務連絡人](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [監控和範本 Azure 資源管理員的診斷與建立 Windows 虛擬機器](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="service-fabric-using-azure-diagnostics"></a>使用 Azure 診斷服務結構
開始在[監視服務布料的轉印圖樣應用程式](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 移至本文，就可以使用左側導覽樹狀目錄中有許多其他服務布料的轉印圖樣診斷程式的文件。

## <a name="general-azure-diagnostics-articles"></a>一般 Azure 診斷文章
- [Azure 診斷結構描述設定](https://msdn.microsoft.com/library/azure/mt634524.aspx)-瞭解如何變更用來收集及路由診斷資料的結構描述檔案。 請注意，您也可以使用 Visual Studio 若要變更的結構描述檔案。
- [如何 Azure 診斷，資料會儲存在 Azure 儲存體](./cloud-services/cloud-services-dotnet-diagnostics-storage.md)-知道資料表和寫入診斷資料的位置的二進位大型物件的名稱。
- 瞭解如何[使用中 Azure 診斷效能計數器](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md)。
- 瞭解如何[傳送 Azure 應用程式獲得深入見解的診斷資訊](./azure-diagnostics-configure-applicationinsights.md)
- 如果您有診斷啟動或 Azure 儲存體表格中尋找您的資料有問題，請參閱[疑難排解 Azure 診斷程式](./azure-diagnostics-troubleshooting.md)
