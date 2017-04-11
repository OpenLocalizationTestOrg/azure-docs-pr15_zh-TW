<properties
   pageTitle="使用 Linux Azure 診斷收集記錄 |Microsoft Azure"
   description="本文將說明如何設定 Azure 診斷記錄收集服務布料的轉印圖樣 Linux 叢集 Azure 中執行。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>使用 Azure 診斷收集記錄

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

當您執行的 Azure 服務布料的轉印圖樣叢集時，最好從中央位置中的所有節點收集記錄。 無法在一個中央位置的記錄可讓您輕鬆分析和疑難排解問題，不管是您的服務，您的應用程式或叢集本身。 上傳並收集記錄的其中一個方法是使用 Azure 診斷分機] 上, 傳至 Azure 儲存體的記錄。 您可以讀取儲存空間，並將其置於產品，例如[彈性的搜尋](service-fabric-diagnostic-how-to-use-elasticsearch.md)或另一種記錄剖析的解決方案。

## <a name="log-sources-that-you-might-want-to-collect"></a>您可能想要收集的記錄來源
- **服務布料的轉印圖樣記錄**︰ 發出透過[LTTng](http://lttng.org)的平台和上傳至您儲存的帳戶。 記錄可以作業事件或執行階段事件的平台發出。 這些記錄會儲存在叢集資訊清單指定的位置。 （若要取得儲存帳戶詳細資料，搜尋的標籤**AzureTableWinFabETWQueryable**並尋找**StoreConnectionString**）。
- **應用程式的事件**︰ 發出從您的服務的程式碼。 您可以使用任何的撰寫文字為基礎的記錄檔，例如 LTTng 記錄解決方案。 如需詳細資訊，請參閱追蹤應用程式 LTTng 說明文件。  


## <a name="deploy-the-diagnostics-extension"></a>部署診斷副檔名
收集記錄的第一個步驟是部署每個服務布料的轉印圖樣叢集 Vm 診斷副檔名。 診斷副檔名為每個 VM 用來收集相關記錄，並將它們上載至您指定的儲存空間帳戶。 步驟會根據您使用的是 Azure 入口網站或 Azure 資源管理員而定。

若要部署至叢集 Vm 的診斷副檔名，做為叢集建立的一部分，將**診斷**為**上**。 建立叢集之後，您無法使用入口網站變更這項設定。

然後，設定 Linux Azure 診斷 （小） 收集檔案，並將其放置到您儲存的帳戶。 此程序說明案例 3 （上傳您自己的記錄檔]），請參閱[使用監控和診斷 Linux Vm 的小](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md)。 下列程序取得您存取權的追蹤。 您可以追蹤上傳至您所選擇的視覺化檢視中。

您也可以使用 Azure 資源管理員部署診斷副檔名。 程序 for Windows 和 Linux 類似，並會說明[如何收集 Azure 診斷記錄檔](service-fabric-diagnostics-how-to-setup-wad.md)中的 Windows 叢集。

您也可以使用作業管理套件，[與 Linux 作業管理套件記錄分析](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/)所述。

這項設定之後，請小代理程式監視指定的記錄檔。 當新的一行附加至該檔案時，它會建立會傳送至您指定的儲存空間的系統項目。


## <a name="next-steps"></a>後續步驟
若要瞭解什麼事件，您應該檢查問題疑難排解的更多詳細資料，請參閱[LTTng 文件](http://lttng.org/docs)並[使用小](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md)。
