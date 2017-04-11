<properties
   pageTitle="雲端服務與服務布料的轉印圖樣之間的差異 |Microsoft Azure"
   description="移轉概觀雲端服務的應用程式，以服務布料的轉印圖樣。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>深入了解雲端服務與服務布料的轉印圖樣之間的差異，再移轉應用程式。
Microsoft Azure 服務布料的轉印圖樣是下一步] 產生雲端應用程式的平台的彈性，可靠的分散式應用程式。 它會介紹許多包裝、 部署、 升級，及管理分散式的雲端應用程式的新功能。 

這是簡介指南移轉至雲端服務的應用程式，以服務布料的轉印圖樣。 它會解釋主要架構，並設計雲端服務與服務布料的轉印圖樣之間的差異。
 
## <a name="applications-and-infrastructure"></a>應用程式與基礎結構

基本雲端服務與服務布料的轉印圖樣之間的差別 Vm、 負載，與應用程式之間的關係。 您撰寫執行特定工作或提供服務的程式碼的定義的工作量。
 
 - **雲端服務的部署 Vm 為應用程式。** 您所撰寫的程式碼緊密 VM 執行個體，例如網頁或工作者角色。 若要部署雲端服務中的工作負載是部署執行工作負載的一或多個 VM 執行個體。 沒有分隔的應用程式和 Vm，還有，因此在應用程式沒有正式定義。 為一組的網頁或工作者角色的執行個體中的雲端服務部署或整個的雲端服務部署，您可以想像應用程式。 在此範例中，在應用程式會顯示為一組的角色執行個體。
 
![雲端服務應用程式與拓撲][1]

 - **服務結構的部署至現有的 Vm 或執行服務布料的轉印圖樣 Windows 或 Linux 電腦的應用程式。** 您所撰寫的服務會完全低耦合的基礎結構，離開的抽象服務布料的轉印圖樣應用程式平台，讓應用程式，可以部署到多個環境。 工作負載服務布料的轉印圖樣中稱為 「 服務]，然後一或多個服務一起執行服務布料的轉印圖樣應用程式平台的正式定義應用程式中。 多個應用程式可以部署到單一服務布料的轉印圖樣叢集。
 
![服務布料的轉印圖樣應用程式與拓撲][2]
 
服務結構本身是執行 Windows 或 Linux，應用程式的平台圖層，而雲端服務是附加的工作負載部署 Azure 管理 Vm 系統。
服務布料的轉印圖樣應用程式模型有許多優點︰

 - 快速部署時間。 建立 VM 執行個體可能會花時間。 在服務布料的轉印圖樣，Vm 只部署後，若要建立叢集主控服務布料的轉印圖樣應用程式平台。 從之後，應用程式套件可以部署叢集快速。
 - 高密度主機服務。 在雲端服務中工作者角色 VM 主控一個的工作量。 服務結構中的應用程式與分開 Vm 執行，也就是說，您可以部署大量的應用程式，以少數幾個 Vm，可以降低較大的部署的整體成本。
 - 服務布料的轉印圖樣的平台可執行任何一處，會有 Windows Server 或 Linux 電腦，無論您是 Azure 或內部部署。 因此在不同環境上執行應用程式，在平台的提供抽象層基礎結構。 
 - 分散式應用程式管理。 服務布料的轉印圖樣是不只裝載分散式應用程式，但也可協助管理其生命週期的主機服務 VM 亦或電腦生命週期的平台。

## <a name="application-architecture"></a>應用程式結構

雲端服務應用程式架構通常包含許多外部服務相依性，例如服務匯流排 Azure 資料表和 Blob 儲存體、 SQL、 意指，與其他人的狀態和應用程式和網頁和工作者角色之間的通訊雲端服務部署中的資料管理。 完整的雲端服務應用程式的範例可能看起來像這樣︰  

![雲端服務架構][9]

若要在完整的應用程式中使用相同的外部服務，也可以選擇服務布料的轉印圖樣應用程式。 使用此範例中雲端服務架構，最簡單移轉路徑雲端服務的服務布料的轉印圖樣是以雲端服務部署取代維持整體架構相同的服務布料的轉印圖樣應用程式。 Web 及工作者角色可以連接至服務布料的轉印圖樣無服務以最小的程式碼變更。

![簡單的移轉後的服務結構架構][10]

在此階段，系統應該可繼續使用之前相同。 當狀態服務適用之處，可以 internalized 運用服務布料的轉印圖樣的狀態的功能，外部狀態存放區。 這是更深入的體驗比的網頁和工作者角色的簡單移轉到服務布料的轉印圖樣無狀態服務，視需要撰寫自訂服務，提供您的應用程式的對等功能之前, 的外部服務一樣。 這樣做的優點包括︰ 

 - 移除外部相依性 
 - 統一部署、 管理和升級的模型。 
 
範例結果架構 internalizing 這些服務可能看起來像這樣︰

![完全移轉後的服務結構架構][11]

## <a name="communication-and-workflow"></a>通訊與工作流程

大部分的雲端服務應用程式包含多個層。 同樣地，服務布料的轉印圖樣應用程式包含一個以上的服務 （通常是許多服務）。 兩個常見的通訊模型會直接通訊和透過外部的長期儲存空間。

### <a name="direct-communication"></a>直接通訊

直接通訊，層可以直接透過所公開之每一層的端點進行通訊。 在沒有狀態環境中例如雲端服務，此所代表的意義隨機數選取執行個體的 VM 角色，或循環的負載平衡，及直接連線到其結束點。

![雲端服務直接通訊][5]

 直接通訊是常見的通訊模型中服務布料的轉印圖樣。 服務布料的轉印圖樣和雲端服務的主要差異是您連線至 VM，該在雲端服務，而您連線至服務中服務布料的轉印圖樣。 這幾個原因是重要的差異︰

 - 服務布料的轉印圖樣的服務未繫結至 Vm 裝載;服務中叢集，可能會移動，事實上，預計移動各種原因︰ 資源平衡、 容錯移轉、 應用程式和基礎架構升級和位置或載入的限制式。 這表示隨時都可以變更服務執行個體的地址。 
 - 在服務布料的轉印圖樣 VM 可以主控多個服務，兩者都有唯一的結束點。

服務架構提供服務探索機制，稱為命名服務，可以用來解決的服務端點位址。 

![服務布料的轉印圖樣直接通訊][6]

### <a name="queues"></a>佇列

常見的通訊機制等雲端服務的狀態環境中的層之間是長期儲存工作層級一到另一個使用外部儲存佇列。 常見的案例是 web 層傳送至 Azure 佇列中或服務匯流排位置工作者角色執行個體可以清除佇列及處理工作的工作。

![雲端服務佇列中通訊][7]

相同的通訊模型可以用於服務布料的轉印圖樣。 移轉現有的雲端服務應用程式服務布料的轉印圖樣時便十分實用。 

![服務布料的轉印圖樣直接通訊][8]
 
## <a name="next-steps"></a>後續步驟

服務布料的轉印圖樣至雲端服務的最簡單移轉路徑是雲端服務部署取代服務布料的轉印圖樣應用程式，讓您的應用程式的整體架構大致相同。 下列文章提供可協助轉換服務布料的轉印圖樣無狀態服務中的網頁或工作者角色指南。

 - [簡單的移轉︰ 將網頁或工作者角色轉換到服務布料的轉印圖樣無狀態服務](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
