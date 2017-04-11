<properties
   pageTitle="在 Windows Server 和 Linux 上建立 Azure 服務布料的轉印圖樣叢集 |Microsoft Azure"
   description="執行 Windows Server、 Linux，這表示您就可以部署及任何一處主機服務布料的轉印圖樣應用程式服務布料的轉印圖樣叢集您可以執行 Windows Server 或 Linux。"
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Windows Server 或 Linux 建立服務布料的轉印圖樣叢集

Azure 服務布料的轉印圖樣可讓您建立的任何 Vm 或執行 Windows Server 或 Linux 電腦上的服務布料的轉印圖樣叢集。 這表示您就可以部署與您有一組互相連接的 Windows Server 或 Linux 電腦任何環境中執行服務布料的轉印圖樣應用程式，可能是內部部署，Microsoft Azure 或任何雲端提供者。

##<a name="create-service-fabric-clusters-on-azure"></a>建立 Azure 服務布料的轉印圖樣叢集

Azure 上建立叢集是透過資源模型範本或 Azure 入口網站。 如需詳細資訊，請閱讀[建立使用資源管理員範本服務布料的轉印圖樣叢集](service-fabric-cluster-creation-via-arm.md)] 或 [[建立服務布料的轉印圖樣叢集從 Azure 入口網站](service-fabric-cluster-creation-via-portal.md)。

## <a name="supported-operating-systems-for-clusters-on-azure"></a>支援的作業系統上 Azure 叢集

您就可以建立叢集 Vm 執行這些作業系統上︰

* Windows Server 2012 R2
* 在 Windows Server 2016 （之後會通知為推出）
* Linux Ubuntu 16.04 （在公用預覽版本） 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>建立服務布料的轉印圖樣獨立叢集，內部部署或雲端中的任何提供者

服務布料的轉印圖樣提供的安裝套件，為您建立獨立版服務布料的轉印圖樣叢集內部部署或雲端中的任何提供者

如需在 Windows Server 上的獨立版服務布料的轉印圖樣叢集設定的詳細資訊，請閱讀[服務布料的轉印圖樣叢集建立的 Windows Server](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>內部部署與任何雲端部署
建立服務布料的轉印圖樣叢集內部部署的程序很類似的一組 Vm 您所選擇的任何雲端上建立叢集程序。 佈建 Vm 的初始步驟都受到雲端提供者或您使用的內部部署環境而定。 一旦您將一組 Vm 有啟用它們之間的網路連線時，請再的步驟，設定服務布料的轉印圖樣套件，編輯叢集設定] 中，執行叢集建立並管理指令碼完全相同。 這可確保您的知識和體驗的作業系統和管理服務布料的轉印圖樣叢集轉移當您選擇針對新的主機服務環境。

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>建立獨立版服務布料的轉印圖樣叢集的優點
* 您可以自由選擇裝載叢集任何雲端提供者。
* 服務布料的轉印圖樣，一次撰寫的應用程式，可以執行具有多個主控環境中最小沒有變更。
* 建立服務布料的轉印圖樣應用程式的知識庫執行會移到另一個主機服務環境到另一個。
* 執行及管理服務布料的轉印圖樣的操作經驗叢集，會移到另一個環境到另一個。
* 主要客戶範圍是未繫結主控環境的限制式。
* 額外的可靠性和廣泛中斷的保護層級存在，因為您可以將服務移至另一個部署環境如果資料中心或雲端提供者有轉黑。

## <a name="supported-operating-systems-for-standalone-clusters"></a>獨立叢集支援的作業系統
您就可以建立叢集 Vm 或執行這些作業系統的電腦上︰

* Windows Server 2012 R2
* 在 Windows Server 2016 （之後會通知為推出）
* Linux （即將推出）

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>透過獨立服務布料的轉印圖樣叢集 Azure 服務布料的轉印圖樣叢集的優點建立內部部署

Azure 上執行服務布料的轉印圖樣叢集會提供選項內部部署的優點，因此，如果您沒有特定的需求，為您用來執行叢集，然後建議他們執行 Azure 上。 在 Azure 中，我們會提供整合與其他的 Azure 功能可讓您作業及管理該叢集更輕鬆、 可靠的服務。

* **Azure 入口網站︰**Azure 入口網站可讓您更輕鬆地建立及管理叢集。

* **Azure 資源管理員︰**使用之 Azure 資源管理員可輕鬆地管理叢集做為單位的所有資源，並簡化成本追蹤及帳單。
* **服務布料的轉印圖樣叢集做為 Azure 資源**服務布料的轉印圖樣叢集是 ARM 資源，因此可以像 Azure 中的其他 ARM 資源模型。
* **Azure 基礎結構與整合**Azure 基礎的作業系統、 網路和其他升級改善可用性及可靠性應用程式與服務布料的轉印圖樣座標。  
* **診斷︰**在 Azure 中，我們會提供整合與 Azure 診斷記錄分析。
* **自動調整大小︰**為上 Azure 叢集，我們會提供自動調整大小的內建功能，因為虛擬機器縮放比例設定。 在內部部署和其他雲端環境中，您必須建立您自己自動縮放功能或使用縮放比例叢集的服務布料的轉印圖樣公開的 Api 手動的小數位數。

## <a name="next-steps"></a>後續步驟
建立叢集 Vm 或執行 Windows Server 的電腦上︰[服務布料的轉印圖樣叢集建立 Windows 伺服器](service-fabric-cluster-creation-for-windows-server.md)

建立叢集 Vm 或執行 Linux 電腦上︰[服務 linux 布料的轉印圖樣](service-fabric-linux-overview.md)
