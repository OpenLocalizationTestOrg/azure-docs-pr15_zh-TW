<properties
   pageTitle="服務布料的轉印圖樣概觀 |Microsoft Azure"
   description="服務布料的轉印圖樣，應用程式位置組成提供小數位數和彈性的許多 microservices 概觀。 服務布料的轉印圖樣是分散式的系統平台用來建立可調整、 可靠，且輕鬆地管理雲端應用程式"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="mfussell"/>

# <a name="overview-of-service-fabric"></a>服務布料的轉印圖樣的概觀
服務布料的轉印圖樣是使其易於封裝、 部署及管理可調整和可靠 microservices 分散式的系統平台。 服務布料的轉印圖樣也地址開發及管理雲端應用程式中的重大問題。 開發人員和管理員，可避免在實作關鍵、 要求負載知道他們可調整、 可靠且更容易管理改為解決複雜的基礎結構的問題與焦點。 服務布料的轉印圖樣代表建立與管理這些企業層級，第 1 層雲端比例應用程式的下一步] 產生介軟體平台。

本[短片](https://aka.ms/servicefabricvideo)介紹服務布料的轉印圖樣及 microservices。


## <a name="applications-composed-of-microservices"></a>組成 microservices 應用程式
服務布料的轉印圖樣可讓您建立並管理共用資料庫的 （又稱為叢集） 的電腦上執行時更高的密度 microservices 所組成的調整和可靠應用程式。 建置分散式、 調整沒有狀態和狀態 microservices 提供複雜的執行階段。 它也會佈建、 部署、 監控、 升級/修補，提供完整的應用程式管理功能，並刪除部署應用程式。

為什麼 microservices 方法很重要？ 兩個主要原因如下︰

1. 他們可讓您根據其需求的應用程式的不同部分不按比例縮放。

2. 開發小組能夠會更靈活復原變更並因此更快且更頻繁地提供給您的客戶功能。

服務布料的轉印圖樣力量許多 Microsoft 服務，包括 Azure SQL 資料庫、 Azure DocumentDB、 Cortana，Power BI、 Microsoft Intune、 Azure 事件集線器、 Azure IoT、 商務用 Skype，及許多核心 Azure 服務。

服務布料的轉印圖樣適合用於建立雲端原生服務可以啟動小，視需要和成長龐大的縮放比例與數百個或千分位的電腦。

今天的網際網路比例服務內建的 microservices。 範例 microservices 包含通訊協定閘道器，使用者設定檔，購物車，庫存處理、 佇列，並快取。 服務布料的轉印圖樣是 microservices 平台，讓每個 microservice 可以無狀態或狀態的唯一名稱。

服務架構提供全面涵蓋所有內容執行階段及生命週期管理功能所組成這些 microservices 的應用程式。 它會裝載 microservices 內部部署及啟動服務布料的轉印圖樣叢集容器。 從移 Vm 至容器會讓可能順序的強度增加密度中。 同樣地，另一個差距密度中可以從容器移至 microservices 成為可能。 例如，單一 Azure SQL 資料庫叢集組成下的數百個機器執行個容器裝載數百個資料庫總計。 服務布料的轉印圖樣的狀態 microservice 每個資料庫。 相同是對的其他服務先前所述，即為 「 hyperscale 」 一詞用來描述服務布料的轉印圖樣功能為何。 如果容器提供密度，然後 microservices 讓您 hyperscale。

如需 microservices 方法的詳細資訊，請閱讀[建置應用程式的 microservices 方法為何？](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>容器部署及協調流程
為機器叢集的 microservices [orchestrator](service-fabric-cluster-resource-manager-introduction.md)服務布料的轉印圖樣。 使用部署[來賓可執行檔](service-fabric-deploy-existing-app.md)[服務布料的轉印圖樣程式設計模型](service-fabric-choose-framework.md)的多種方式可以進行開發 Microservices。 服務布料的轉印圖樣可以部署容器圖像的服務與重要的是您可以在其中混合程序中的服務和相同的應用程式在一起的容器中的服務。 如果您只是要[部署及管理容器圖像](service-fabric-containers-overview.md)跨叢集的電腦，服務布料的轉印圖樣是完美的選擇。


## <a name="create-service-fabric-clusters-anywhere"></a>建立任何一處服務布料的轉印圖樣叢集
您可以建立服務布料的轉印圖樣叢集，您的環境中，包括 Azure 或內部部署、 Windows Server 或 Linux。 此外，在 SDK 的開發環境相同，即不涉及模擬器生產環境。 換句話說，如果在您的本機開發叢集上執行其部署至其他環境中相同的叢集。

如需有關建立叢集內部部署，請閱讀[建立 Windows Server 或 Linux 叢集](service-fabric-deploy-anywhere.md)或 Azure 建立叢集[透過 Azure 入口網站](service-fabric-cluster-creation-via-portal.md)。

![服務布料的轉印圖樣平台][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>沒有狀態和狀態服務布料的轉印圖樣 microservices

服務布料的轉印圖樣可讓您能夠建立組成 microservices 應用程式。 無 microservices （通訊協定閘道、 web proxy 等） 不會維持以外任何指定的要求和從服務其回應的可變動狀態。 Azure 的雲端服務工作者角色為無服務的範例。 使用者帳戶、 資料庫 (裝置，購物車、 佇列等） 的狀態 microservices 維護可變動、 代表性狀態超出要求和其回應。 沒有狀態和狀態 microservices 組合包含今天的網際網路比例應用程式。

為什麼有狀態 microservices 以及無狀態的文件？ 兩個主要原因如下︰

1. 建立處理量最高低延遲、 失敗容錯線上交易處理 (OLTP) 服務保留在同一部電腦上的程式碼和關閉資料的能力。 部分範例是互動式店面、 搜尋、 網際網路的項目 (IoT) 系統、 交易系統、 信用卡處理和網路詐騙偵測系統和個人的記錄管理。

2. 應用程式設計簡化。 狀態 microservices 移除額外的佇列需要快取，傳統必要地址完全沒有狀態的應用程式的可用性及延遲的需求。 高可用性和低延遲，減少整個應用程式中管理變動的自然所狀態的服務。

如需詳細資訊，在應用程式與服務布料的轉印圖樣，閱讀您的服務[應用程式案例](service-fabric-application-scenarios.md)和[選擇程式設計的模型架構](service-fabric-choose-framework.md)的圖樣

## <a name="application-lifecycle-management"></a>應用程式生命週期管理
服務架構提供完整的應用程式生命週期管理 (ALM) 的雲端應用程式-從開發到部署、 每日管理，以及進行的維修作業最終解除委任最高級支援。

服務布料的轉印圖樣 ALM 功能可讓應用程式管理員 / IT 運算子使用簡單、 低觸控式工作流程進行佈建，部署修補程式，並監控應用程式。 這些內建的工作流程大幅降低負擔 IT 運算子持續可用的應用程式。

大部分的應用程式包含沒有狀態和狀態 microservices 與一起部署其他可執行檔/執行階段的組合。 有應用程式和封裝的 microservices 強式類型，服務布料的轉印圖樣啟用多個應用程式執行個體的部署。 每個執行個體管理，獨立升級。 重要的是服務布料的轉印圖樣 is able to 部署*任何*可執行檔或執行階段，並使其可靠的。 例如，服務布料的轉印圖樣部署 ASP.NET 核心 1、 Node.js、 Java Vm、 指令碼，或其他項目，讓應用程式。

應用程式生命週期管理的詳細資訊，請閱讀[應用程式生命週期](service-fabric-application-lifecycle.md)並部署任何程式碼，請參閱[部署可執行以來賓](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>主要功能
藉由使用服務布料的轉印圖樣，您可以︰

- 開發自我修復的人連線可調整應用程式。

- 開發應用程式使用的服務布料的轉印圖樣程式設計模型 microservices 所組成。 或只要主機來賓可執行檔，您的選擇，例如 ASP.NET 核心 1 或 Node.js 的其他應用程式架構。

- 開發可靠的狀態和狀態 microservices。

- 部署及協調容器跨叢集包含 Windows 容器及 Docker 容器。 這些容器可以容器來賓可執行檔或可靠的狀態和狀態 microservices。  在任一情況下，您會收到主機連接埠對應、 容器可搜尋性和自動化容錯移轉容器連接埠。

- 使用 [快取和佇列取代狀態 microservices 簡化應用程式的設計。

- 部署至 Azure 或零的程式碼變更執行 Windows Server 或 Linux 的內部部署雲朵。 撰寫一次，然後部署至任何服務布料的轉印圖樣叢集的任何位置。

- 開發 「 您的電腦上的資料中心 」 的方法。 在本機的開發環境是 Azure 資料中心的執行相同的程式碼。

- 將應用程式部署秒數。

- 部署較高的密度部署數百或數以千計的每部電腦的應用程式的虛擬機器比在應用程式。

- 部署不同版本的並排、 每個獨立可升級的同一個應用程式。

- 管理沒有任何停機時間，包括中斷和不分行升級狀態應用程式的生命週期。

- 管理應用程式使用.NET Api、 Java (Linux)、 PowerShell、 Azure CLI (Linux) 或其他介面。

- 升級後獨立修補 microservices 應用程式中。

- 監控診斷健全狀況的應用程式並設定執行自動修復的原則。

- 擴展或縮放比例中叢集，以及向上調整的節點數目或向下調整大小的每個節點，瞭解您的應用程式自動不按比例縮放，並根據 [可用的資源分佈的能力。

- 線上觀看自我修復資源平衡器叢集協調重新發佈應用程式。 服務布料的轉印圖樣從失敗復原，最佳化的載入根據 [可用的資源。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

* 如需詳細資訊︰
    * [為什麼 microservices 方式來建立應用程式？](service-fabric-overview-microservices.md)
    * [術語概觀](service-fabric-technical-overview.md)
* 設定您的服務布料的轉印圖樣[的開發環境](service-fabric-get-started.md)  
* [選擇程式設計的模型架構](service-fabric-choose-framework.md)服務

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
