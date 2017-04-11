<properties
   pageTitle="服務布料的轉印圖樣服務的可用性 |Microsoft Azure"
   description="偵測錯誤、 錯誤移轉及復原服務描述"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="availability-of-service-fabric-services"></a>服務布料的轉印圖樣服務的可用性
[狀態] 或 [無狀態，可以是 azure 服務布料的轉印圖樣服務。 本文提供的服務布料的轉印圖樣如何保持顯示狀態服務發生失敗的概觀。

## <a name="availability-of-service-fabric-stateless-services"></a>服務布料的轉印圖樣無服務的可用性
無服務是沒有任何[本機常設狀態](service-fabric-concepts-state.md)的應用程式服務。

建立無服務需要定義的執行個體計數，這是沒有狀態服務應該執行叢集的執行個體數目。 這是應用程式邏輯會產生叢集份數。 增加的執行個體數目是按比例縮放設定的狀態服務的建議的方式。

無服務的任何執行個體上偵測到錯誤，在叢集一些其他合格節點就會建立新的執行個體。

## <a name="availability-of-service-fabric-stateful-services"></a>服務布料的轉印圖樣狀態服務的可用性
設定狀態服務有一些與其相關聯的狀態。 服務結構中的狀態服務都為複本的一組建立模型。 每個複本是有一份狀態服務的程式碼的執行個體。 已讀取和寫入的操作被執行一個複本 （稱為 「 主要 」）。 若要從狀態變更撰寫作業會*複製*到多個其他複本 （稱為作用中的次要連結）。 主要與作用中的次要複本的組合是複本集的服務。

可以只有一個主要複本服務讀取與寫入要求，但可以有多個作用中的次要複本。 作用中的次要複本數目可設定，而有更多的複本可容許更大的數字，同時軟體及硬體失敗。

在錯誤 （時的主要複本當機） 的事件，服務布料的轉印圖樣可以讓您使用中的次要複本的其中一個新的主要複本。 這個作用中的次要複本已有更新的版本的狀態 （透過*複寫*），且其可以繼續處理進一步讀取和寫入作業。

此概念-複本，在 [主要] 或 [作用中的次要-稱為複本角色。

### <a name="replica-roles"></a>複本角色
複本的角色用來管理生命週期的受管理的複本的狀態。 其角色為主要服務複本讀取要求。 它也會更新的狀態，並將變更複寫到其複本集中作用中的次要連結服務寫入要求。 作用中的角色是次要的收到狀態變更複寫的主要複本，並更新的狀態的檢視。

>[AZURE.NOTE] 較高層級的程式設計模型，例如[可靠的動作項目架構](service-fabric-reliable-actors-introduction.md)離開抽象複本角色開發人員的概念。

## <a name="next-steps"></a>後續步驟

如需有關服務布料的轉印圖樣概念的詳細資訊，請參閱下列各項︰

- [服務布料的轉印圖樣服務的延展性](service-fabric-concepts-scalability.md)

- [分割服務布料的轉印圖樣服務](service-fabric-concepts-partitioning.md)

- [定義及管理狀態](service-fabric-concepts-state.md)
